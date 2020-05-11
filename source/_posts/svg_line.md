---
title: SVG绘画折线图
date: 2020-05-11 10:20:58
tags: html5
categories: 前端开发 
thumbnail: http://img5.imgtn.bdimg.com/it/u=1647314705,3126772480&fm=26&gp=0.jpg
---

### 代码如下：
1.html代码：
```
<svg viewBox="0 0 200 100" id="chart">
  <g transform="translate(0, 100) scale(1, -1)" id="path-container">
    <path fill="none" stroke-width="3" stroke-linecap="round" stroke-linejoin="round" id="line" />
  </g>
  <text text-anchor="middle" alignment-baseline="middle" x="100" y="50" class="loading">
    Loading...
  </text>
</svg>
```
2，css代码：
```
body {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}

#chart {
  background-color: #f6f6f6;
  width: 100%;
  max-width: 1000px;
  
  --stroke-length: 0;
}

#chart #line {
  stroke: #9F3AF0;
  stroke-dasharray: var(--stroke-length);
  stroke-dashoffset: var(--stroke-length);
}

.name, .caption {
  font-family: Helvetica;
  font-size: 4px;
  font-weight: bold;
  fill: #999;
  
  animation: .5s fade-in ease-in-out;
  animation-fill-mode: forwards;
}

.values {
  font-family: Helvetica;
  font-size: 3px;
}

@keyframes animate-line {
  from {
    stroke-dashoffset: var(--stroke-length);
  }
  to {
    stroke-dashoffset: 0;
  }
}

#chart.animate #line {
  animation: 3s animate-line linear;
  animation-fill-mode: forwards;
  animation-delay: .3s;
}

@keyframes zoom-in {
  from {
    transform: scale(0);
  }
  to {
    transform: scale(1);
  }
}

@keyframes fade-in {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

#chart .point {
  opacity: 0;
}

#chart.animate .point {
  animation: .5s fade-in ease-in-out, .5s zoom-in ease-in-out;
  animation-fill-mode: forwards;
  animation-delay: var(--delay);
}

#chart .values {
  opacity: 0;
}

#chart.animate .values {
  animation: .5s fade-in ease-in-out;
  animation-fill-mode: forwards;
  animation-delay: var(--delay);
}

.loading {
  font-family: Helvetica;
  font-size: 5px;
  fill: #999;
  
  transition: .25s opacity ease-in-out;
}

#chart.animate .loading {
  opacity: 0;
}
```
3,js代码：
```
let padding = 15
let start_x = padding
let svg_width = document.querySelector('#chart').viewBox.baseVal.width

function loadData() {
  fetch(`https://codingstartup.com/assets/svg-chart/appl.php`)
    .then((response) => {
      return response.json()
    })
    .then((data) => {
      console.log(`api data`, data)
      drawChart(data)
    })
    .catch((err) => {
      console.log(err)
    })
}

setTimeout(() => {
  loadData()
}, 1000)

function drawChart(api_data) {
  let name = document.createElementNS('http://www.w3.org/2000/svg', 'text')
  name.setAttribute('text-anchor', 'middle')
  name.setAttribute('alignment-baseline', 'middle')
  name.setAttribute('x', svg_width / 2)
  name.setAttribute('y', 6)
  name.classList.add('name')
  name.appendChild(document.createTextNode(api_data['stock_name']))
  document.querySelector('#chart').appendChild(name)
  
  let stock_data = api_data['data']
  let path_data = []
  
  for (let i in stock_data) {
    path_data.push(`${start_x}, ${stock_data[i]['normalized']}`)
    
    let caption = document.createElementNS('http://www.w3.org/2000/svg', 'text')
    caption.setAttribute('text-anchor', 'middle')
    caption.setAttribute('alignment-baseline', 'middle')
    caption.setAttribute('x', start_x)
    caption.setAttribute('y', 96)
    caption.classList.add('caption')
    caption.appendChild(document.createTextNode(stock_data[i]['date']))
    document.querySelector('#chart').appendChild(caption)
    
    let circle = document.createElementNS('http://www.w3.org/2000/svg', 'circle')
    circle.setAttribute('cx', start_x)
    circle.setAttribute('cy', stock_data[i]['normalized'])
    circle.setAttribute('r', 3)
    circle.setAttribute('stroke', '#9F3AF0')
    circle.setAttribute('stroke-width', 2)
    circle.setAttribute('fill', 'white')
    circle.setAttribute('transform-origin', `${start_x} ${stock_data[i]['normalized']}`)
    circle.style.setProperty('--delay', `${(3 * parseInt(i) / stock_data.length)}s`)
    circle.classList.add('point')
    document.querySelector('#path-container').appendChild(circle)
    
    let value = document.createElementNS('http://www.w3.org/2000/svg', 'text')
    value.setAttribute('text-anchor', 'middle')
    value.setAttribute('alignment-baseline', 'middle')
    value.setAttribute('x', start_x)
    value.setAttribute('y', stock_data[i]['normalized'])
    value.setAttribute('transform', `translate(0, ${stock_data[i]['normalized'] * 2 - 8}) scale(1, -1)`)
    value.style.setProperty('--delay', `${(3 * parseInt(i) / stock_data.length)}s`)
    value.classList.add('values')
    value.appendChild(document.createTextNode(stock_data[i]['index']))
    document.querySelector('#path-container').appendChild(value)
    
    start_x += (svg_width - padding * 2) / (stock_data.length - 1)
  }
  
  let line = document.querySelector('#line')
  line.setAttribute('d', `M${path_data.join(' ')}`)
  let strokeLength = Math.ceil(line.getTotalLength())
  document.querySelector('#chart').style.setProperty('--stroke-length', strokeLength)
  
  document.querySelector('#chart').classList.add('animate')
}
```