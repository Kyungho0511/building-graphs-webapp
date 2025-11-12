<template>
  <div id="3d-graph"></div> 
</template>

<script setup>
import ForceGraph3D from '3d-force-graph';
import { onMounted } from 'vue'
import {convertToGraphFormat} from "@/utils/index"

onMounted(async () => {

  //Step 1: Get the nodes and edges
  const [nodesRes, edgesRes] = await Promise.all([
      fetch('/nodes.json'),
      fetch('/edges.json')
    ]);

  const nodeData = await nodesRes.json();
  const edgeData = await edgesRes.json();

  //Step 2: Format the data into the structure expected by ForceGraph3D and store it globally
  let formattedGraphJson = convertToGraphFormat(nodeData, edgeData)
  
  // Step 3: Assign positions using Centroid
  formattedGraphJson.nodes.forEach(node => {
    if (node.Centroid && node.Centroid.length === 3) {
      node.x = node.Centroid[0];
      node.y = node.Centroid[2];  // switch y and z because y is up in ForceGraph3D
      node.z = node.Centroid[1];
    }
  });

  //Step 4: Get the HTML element where we want to render the graph
  let graphDiv = document.getElementById('3d-graph')

  //Step 5: Create the ForceGraph3D (https://github.com/vasturiano/3d-force-graph)
  const Graph = new ForceGraph3D(graphDiv)
    .graphData(formattedGraphJson)
    .nodeLabel('nodeLabel')
    .nodeAutoColorBy('IfcType')
})
</script>

<style scoped>

</style>
