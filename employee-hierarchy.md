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

