# Build Employee Hierarchy on ORACLE APEX
	
![Employee Hierarchy on ORACLE APEX](/img/employee-hierarchy.PNG)

we will use [d3.js org chart ](https://observablehq.com/@bangingrocks/d3-v5-org-chart) libary 

1- on javascript on APEX page we will add this two javascript file on `File URLs`
<br>
[d3.v5.min.js](/employee-hierarchy/src/d3.v5.min.js)<br>
[index.js](/employee-hierarchy/src/index.js)

2- we will add draw function on Function and `Global Variable Declaration`

```javascript
function draw() {
        d3.json('https://gist.githubusercontent.com/bumbeishvili/dc0d47bc95ef359fdc75b63cd65edaf2/raw/c33a3a1ef4ba927e3e92b81600c8c6ada345c64b/orgChart.json')
        .then(data => {
            new Chart()
                .container('.chart-container')
                .data(data)
                .svgWidth(window.innerWidth)
                .svgHeight('88vh')
                .backgroundColor('#ffffff00')
                .initialZoom(0.6)
                .onNodeClick(d => openPage(d))
                .render()

        })
}
```

3- we will call draw funvtion on `Execute when Page Loads`
```javascript
draw()
```
4- we will add some css to `Inline` CSS on page
```css
#main {
    background-image: url(https://i2.wp.com/onaircode.com/wp-content/uploads/2017/11/Tesselation-Transition.jpg);
    background-size: cover;
    background-attachment: fixed;
    margin: 0;
    padding: 0
}

footer.t-Footer {
    display: none
}

#main>.t-Body-contentInner {
    padding: 16px 0
}

circle.node-button-circle {
    fill: #e7e4e4
}
```
5- we need to create static region to drow the hierarchy on this region 
just set `chart-container` as `Column CSS Classes` on `Layout` for this region

we can save and the hierarchy will appear on the page as image above 

## Now we will generate our json file from query 

1- create `Ajax Callback` on page process <br> 
Name it `getData` <br>
Type `PL/SQL Code`
Source PL/SQL and put the code below on source 

```sql
DECLARE
  L_FIRSTROW BOOLEAN := TRUE;
BEGIN
  APEX_JSON.OPEN_ARRAY; -- , [
  --htp.p ( '[' );
  FOR I IN (SELECT ID,
                   PARENT_ID,
                   NAME,
                   TOOLTIP,
                   '#' AS LINK,
                   COLOR,
                   EMP_STATUS,
                   EMPLOYEE_NUMBER,
                   GRADE
            FROM   EMPLOYEES
            START  WITH PARENT_ID = :P0_PERSON_ID
            CONNECT BY PRIOR ID = PARENT_ID
            UNION
            SELECT ID,
                   PARENT_ID,
                   NAME,
                   TOOLTIP,
                   '#' AS LINK,
                   COLOR,
                   EMP_STATUS,
                   EMPLOYEE_NUMBER,
                   GRADE
            FROM   EMPLOYEES
            WHERE  ID = :P0_PERSON_ID
            ORDER  BY PARENT_ID) LOOP
    APEX_JSON.OPEN_OBJECT; -- {
    APEX_JSON.WRITE('nodeId',
                    '' || I.ID || '');
    APEX_JSON.WRITE('parentNodeId',
                    '' || I.PARENT_ID || '');
    APEX_JSON.WRITE('width',
                    342);
    APEX_JSON.WRITE('height',
                    146);
    APEX_JSON.WRITE('borderWidth',
                    1);
    APEX_JSON.WRITE('borderRadius',
                    5);
    APEX_JSON.OPEN_OBJECT('borderColor'); -- "borderColor" : {
    APEX_JSON.WRITE('red',
                    146);
    APEX_JSON.WRITE('green',
                    1);
    APEX_JSON.WRITE('blue',
                    5);
    APEX_JSON.WRITE('alpha',
                    1);
    APEX_JSON.CLOSE_OBJECT; -- }
    APEX_JSON.OPEN_OBJECT('nodeImage'); -- "nodeImage" : {
    APEX_JSON.WRITE('url',
                    'JAMAPEX.JAM_DISPLAY_IMAGE?P_ID=' || I.ID || '&P_TYPE=EMP_DISPLAY_IMAGE');
    APEX_JSON.WRITE('width',
                    100);
    APEX_JSON.WRITE('height',
                    100);
    APEX_JSON.WRITE('centerTopDistance',
                    0);
    APEX_JSON.WRITE('centerLeftDistance',
                    0);
    APEX_JSON.WRITE('cornerShape',
                    'CIRCLE');
    APEX_JSON.WRITE('shadow',
                    FALSE);
    APEX_JSON.WRITE('borderWidth',
                    0);
    APEX_JSON.OPEN_OBJECT('borderColor'); -- "borderColor" : {
    APEX_JSON.WRITE('red',
                    19);
    APEX_JSON.WRITE('green',
                    123);
    APEX_JSON.WRITE('blue',
                    128);
    APEX_JSON.WRITE('alpha',
                    1);
    APEX_JSON.CLOSE_OBJECT; -- }
    APEX_JSON.CLOSE_OBJECT; -- }     
    APEX_JSON.OPEN_OBJECT('nodeIcon'); -- "nodeIcon" : {
    APEX_JSON.WRITE('icon',
                    '#APP_IMAGES#icon/tree.png');
    APEX_JSON.WRITE('size',
                    30);
    APEX_JSON.CLOSE_OBJECT; -- }   
    APEX_JSON.WRITE('template',
                    '<div> <div style="margin-left:70px; margin-top:10px; font-size:20px; font-weight:bold; ">' ||
                    I.NAME ||
                    ' </div> <div style="margin-left:70px; margin-top:3px; font-size:16px; ">' ||
                    I.TOOLTIP ||
                    ' </div> <div style="margin-left:70px; margin-top:3px; font-size:14px; ">' ||
                    I.EMPLOYEE_NUMBER ||
                    '</div> <div style="margin-left:190.5px; margin-top:15px; font-size:13px; position:absolute; bottom:5px; "> <div>' ||
                    I.EMP_STATUS ||
                    '</div> <div style="margin-top:5px" title="Corporate"></div>Grade:' ||
                    I.GRADE ||
                    '</div> </div>');
    APEX_JSON.OPEN_OBJECT('connectorLineColor'); -- "connectorLineColor" : {
    APEX_JSON.WRITE('red',
                    220);
    APEX_JSON.WRITE('green',
                    189);
    APEX_JSON.WRITE('blue',
                    207);
    APEX_JSON.WRITE('alpha',
                    1);
    APEX_JSON.CLOSE_OBJECT; -- }      
    APEX_JSON.WRITE('connectorLineWidth',
                    5);
    APEX_JSON.WRITE('dashArray',
                    '');
    APEX_JSON.WRITE('expanded',
                    FALSE);
    APEX_JSON.WRITE('directSubordinates',
                    0);
    APEX_JSON.WRITE('totalSubordinates',
                    812);
    APEX_JSON.CLOSE_OBJECT; -- }
  END LOOP;
  APEX_JSON.CLOSE_ARRAY; -- ]
END;
```

2- go to page and change `draw` function on `Function and Global Variable Declaration`
by new json sourec come from AJAX Process 
```javascript
 d3.json(window.location.origin + '/ords/f?p=&APP_ID.:&APP_PAGE_ID.:&SESSION.:APPLICATION_PROCESS=getData:::')
      /**d3.json('https://gist.githubusercontent.com/bumbeishvili/dc0d47bc95ef359fdc75b63cd65edaf2/raw/c33a3a1ef4ba927e3e92b81600c8c6ada345c64b/orgChart.json')**/
```

now you will have Employee Hierarchy coming from your table 

Thanks for [Mr.Moamin Shadid (moamin.suleiman@al-majid.com) to guidance and support 
