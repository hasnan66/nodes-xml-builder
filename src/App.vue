<template>
  <div class="text-xs-center">
    <button style="align-content: center;" @click="downloadXml">convert to xml</button>
    <button style="align-content: center;" @click="clearXml">clear</button>
  </div>
  <h1>XML Builder</h1>
  <div class="home" style="position: relative;">
    <input v-model="selectedNode" @keyup.enter="addNode" placeholder="Enter path">
        <button @click="addNode">Add Path</button>
<!--    <button @click="toggleMenu">hauptmenue+</button>-->

    <!-- Menu -->
    <div v-if="showMenu" class="menu" :style="{ top: buttonPosition.top + 'px', left: buttonPosition.left + 'px' }">
      <div @click="handleOptionClick('Add')">Add</div>
      <div @click="handleOptionClick('Copy')">Copy</div>
      <div @click="handleOptionClick('Edit')">Edit</div>
    </div>
    <NodeTree :nodes="nodes"/>
  </div>
</template>
<style>
/* Add your CSS styles for the menu here */
.menu {
  position: absolute;
  background-color: #f1f1f1;
  border: 1px solid #ccc;
  padding: 5px;
  display: flex;
  flex-direction: column;
  z-index: 999;
}

.menu div {
  cursor: pointer;
}
</style>
<script>
import NodeTree from "@/components/NodeTree";

export default {
  name: "App",
  components: {
    NodeTree
  },
  data() {
    return {
      counter: 1,
      selectedNode: "",
      nodes: [],
      showMenu: false,
    };
  },
  methods: {
    toggleMenu() {
      this.showMenu = !this.showMenu;
    },
    handleOptionClick(option) {
      // Handle the click of menu options here
      console.log('Clicked option:', option);
    },
    addNode() {
      if (this.selectedNode) {
        this.nodes.push({id: Date.now(), name: this.selectedNode, children: []});
        this.selectedNode = "";
      }
    },
    arrayToXml(arr, depth) {
      const indent = "  ".repeat(depth);
      let xml = "";

      for (const item of arr) {
        if (Array.isArray(item.children)) {
          if (item.children[0] != null) {
            xml += `${indent}<item name="${item.name}" id="${this.counter++}">\n`;
            xml += this.arrayToXml(item.children, depth + 1);
            xml += "  ".repeat(depth) + '</item>\n';
          } else {
            xml += `${indent}<item name="${item.name}" id="${this.counter++}"/>\n`;
          }
        } else {
          xml += `${indent}<item name="${item.name}" id="${this.counter++}"/>\n`;
        }
      }

      return xml;
    },
    downloadXml() {
      let xmlString = '<?xml version="1.0" encoding="UTF-8"?>\n';
      xmlString += '<dnas date="' + this.currentDateTime() + '">\n';
      xmlString += '  <menue source="xmlBuilder" version="2.3" ' + this.currentDateTime() + '">\n';
      xmlString += this.arrayToXml(this.nodes, 2);
      xmlString += '  </menue>\n';
      xmlString += '</dnas>';
      const blob = new Blob([xmlString], {type: "text/xml"});
      const url = window.URL.createObjectURL(blob);

      const a = document.createElement("a");
      a.href = url;
      a.download = "data.xml";
      a.click();

      window.URL.revokeObjectURL(url);
    },
    currentDateTime() {
      const now = new Date();
      const year = now.getFullYear();
      const month = String(now.getMonth() + 1).padStart(2, "0");
      const date = String(now.getDate()).padStart(2, "0");
      const hours = String(now.getHours()).padStart(2, "0");
      const minutes = String(now.getMinutes()).padStart(2, "0");
      const seconds = String(now.getSeconds()).padStart(2, "0");

      return `${year}-${month}-${date} ${hours}:${minutes}:${seconds}`;
    },
    clearXml() {
      this.nodes = [];
    },
  }
};
</script>
