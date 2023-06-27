<template>
  <div class="tang-container" id="tang-container">
  </div>
</template>

<script setup>
import * as d3 from 'd3'
import { onMounted } from 'vue';


let promptBox,body,width,height
const nodeRadius = 6

onMounted(() => {
  draw()
})

function draw() {
  
  width = document.getElementById('tang-container').offsetWidth*0.9
  height = document.getElementById('tang-container').offsetHeight*0.9
  promptBox = d3.select("#tooltip");
  body = d3.select(".tang-container");

  const svg = body
    .append("svg")
    .attr("width", width)
    .attr("height", height);
  function drag(simulation) {
    function dragStart(e, d) {
      console.log(e.active)
      simulation.alphaTarget(0.8).restart()
      d.fx = d.x
      d.fy = d.y
    }
    function dragging(e, d) {
      d.fx = e.x
      d.fy = e.y
    }
    function dragEnd(e, d) {
      simulation.alphaTarget(0).restart()
      d.fx = null
      d.fy = null
    }
    return d3.drag().on('start', dragStart).on('drag', dragging).on('end', dragEnd)
  }
  function ticked(links, nodesChart) {
    // console.log('ticked');
    links
      .attr('x1', function (d) {
        return d.source.x
        // console.log('line',d)
      })
      .attr('y1', function (d) {
        return d.source.y
      })
      .attr('x2', function (d) {
        return d.target.x
      })
      .attr('y2', function (d) {
        return d.target.y
      })

    // linksText
    //     .attr('x', function (d) {
    //         return (d.source.x + d.target.x) / 2
    //     })
    //     .attr('y', function (d) {
    //         return (d.source.y + d.target.y) / 2
    //     })

    nodesChart.attr('transform', function (d) {
      return 'translate(' + d.x + ',' + d.y + ')'
    })
  }
  function addEvent(selection) {
    selection
      .on("mouseover.over", function (event, d) {
        let [posX, posY] = d3.pointer(event, body);
        promptBox
          .attr("class", "prompt-show")
          .style("top", posY - 50 + "px")
          .style("left", posX + 15 + "px")
          .html(
            // `被引次数:${d3.select(this).attr('level')}<br>论文数量:${d.length}`
            `title:${d.title}<br>引用量:${d.citation}`
          );
      })
      .on("mousemove", function (event) {
        let [posX, posY] = d3.pointer(event, body);
        promptBox.style("top", posY - 50 + "px").style("left", posX + 15 + "px");
      })
      .on("mouseout.out", function () {
        promptBox.attr("class", "prompt-hide");
      })
    // .on('click', function (_, d) {
    //     updatePaperList(d)
    // })
  }

  d3.json('/src/data/唐_data.json').then(data => {

    const nodes = data.nodes
    const links = data.edges
    console.log(nodes.length);

    const simulation = d3.forceSimulation(nodes)
      .force("link", d3.forceLink(links).id(d => d.name))
      .force("charge", d3.forceManyBody().strength(-100))
      .force('collide', d3.forceCollide(nodeRadius))
      // .force("center", d3.forceCenter(width / 2, height / 2))
      // .force('radial', d3.forceRadial(height / 4, width / 2, height / 2).strength(0.2))
      .force('x', d3.forceX(width / 2).strength(0.1))
      .force('y', d3.forceY(height / 2).strength(0.1))
      .on("tick", () => ticked(edges, nodesChart))

    const line = svg.append('g').selectAll().data(links).enter().append('g')
    const edges = line.append('line').attr('stroke', '#ccc').attr('stroke-width', 1)

    const nodesChart = svg
      .append('g')
      .selectAll()
      .data(nodes)
      .join('g')
      .attr('transform', function (d, i) {
        // console.log('g-circle',d)
        return 'translate(' + d.x + ',' + d.y + ')'
      })
    nodesChart
      .append('circle')
      .on('mouseover', function (e, d) {
        promptBox.text(d.text)
      })
      // .on('mouseout',() => {promptBox.text('')})
      .on('mousemove', function (e, d) {

        // let currentX = d3.pointer(e,svg.node())[0]
        // let currentY = d3.pointer(e,svg.node())[1]

        // let textW = promptBox.node().getBoundingClientRect().width

        // if(currentX + textW >= width){
        //     // console.log(currentX + textW,width);
        //     if(textW - currentX <= 0) currentX -= textW
        //     else currentX -= textW/2
        // }
        // // console.log(d3.pointer(e)[0],d3.pointer(e)[1])
        // promptBox.attr('x',currentX+10).attr('y',currentY-10)
      })
      // .call(addEvent)
      .attr('r', function (d, i) {
        // 半径
        return nodeRadius
      })
      .attr('fill', function (d, i) {
        return "blue"
      })

    nodesChart
      .append('text')
      .attr('x', -40)
      .attr('y', -5)
      .attr('dy', 10)
      .attr('font-size', 10)
      .text(function (d) {
        return d.name
      })
      .attr('fill', '#000')
      .attr('pointer-events', 'none')
      .style('user-select', 'none')

    nodesChart
      .call(drag(simulation))
  })

}

</script>
  
<style>
#tang-container{
  width: 100%;
  height: 100%;
}
</style>
  