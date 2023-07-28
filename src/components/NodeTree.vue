<template>
  <div>
    <ul>
        <li v-for="node in nodes" :key="node.id">
          {{ node.name }}
          <button @click="addChildNode(node)">Add Path</button>
          <input v-model="newChildNode" @keyup.enter="addChildNode(node)" placeholder="Enter path">
          <NodeTree :nodes="node.children" />
        </li>
    </ul>
  </div>
</template>

<script>
export default {
  props: {
    nodes: Array
  },
  data() {
    return {
      newChildNode: ""
    };
  },
  methods: {
    addChildNode(node) {
      if (this.newChildNode) {
        if (!node.children) {
          this.$set(node, "children", []);
        }
        node.children.push({ id: Date.now(), name: this.newChildNode, children: [] });
        this.newChildNode = "";
      }
    }
  }
};
</script>
