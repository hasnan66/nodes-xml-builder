<template>
  <div>
    <ul>
      <li v-for="node in nodes" :key="node.id">

        <button v-if="false" @click="toggleMenu1">Add Path</button>

        <div id="abc3">
          <div class="menu-container">
            <button class="menu-button" @click="toggleMenu1(node)">{{ node.name }}</button>
            <!-- Menu -->
            <div :id="node.id" class="menu force-hide" :style="{ top:  + '20px', left:  '10px' }">
              <div @click="showPopupChildEdit" class="menu-item">Eintrag bearbeiten</div>
              <div @click="showChildPopup" class="menu-item">Eintrag einfügen</div>
              <div @click="removeItem()" class="menu-item">Eintrag löschen</div>
              <div @click="handleOptionClick(node)" class="menu-item">Bild einfügen</div>
              <div @click="handleOptionClick('Copy')" class="menu-item">PDF einfügen</div>
              <div @click="handleOptionClick('Copy')" class="menu-item">Einzelauswahl</div>
              <div @click="handleOptionClick('Copy')" class="menu-item">Keine Einzelauswahl</div>
              <div @click="copySelectedNodes()" class="menu-item">In die Zwischenablage kopieren</div>
              <div @click="pasteOnSelectedNodes()" class="menu-item">Aus Zwischenablage einfügen</div>
              <div @click="handleOptionClick('Copy')" class="menu-item">Farbe wählen</div>
            </div>
          </div>
        </div>
        <!-- Popup -->
        <div v-if="showPopupVisible2" class="popup">
          <label>Wei soll der Eintrag heissen?</label><br/>
          <input v-model="textFieldValue2" type="text" placeholder="Enter value"/><br/>
          <button class="add-button margin5" @click="addChildNode(node,textFieldValue2)">OK</button>
          <button class="add-button margin5" @click="cancelNode(textFieldValue2)">Cancel</button>
        </div>

        <div v-if="showPopupEditChild" class="popup">
          <label>Wei soll der gewaehlte Punkt heissen?</label><br/>
          <input v-model="editChildText" type="text" placeholder="Enter value"/><br/>
          <button class="add-button margin5" @click="editChildNode(node,editChildText)">OK</button>
          <button class="add-button margin5" @click="cancelNode(editChildText)">Edit Button</button>
        </div>
        <input v-if="false" v-model="newChildNode" @keyup.enter="addChildNode(node)" placeholder="Enter path">
        <NodeTree :nodes="node.children"/>
      </li>
    </ul>
  </div>
</template>

<script>
import $ from 'jquery';

export default {
  props: {
    nodes: Array
  },
  data() {
    let id;
    let children;
    return {
      newChildNode: "",
      showMenu2: false,
      showPopupVisible2: false,
      showPopupEditChild: false,
      textFieldValue2: '',
      editChildText: '',
      currentNode: [],
      copyNodes: {id, name, children},
    };
  },
  methods: {
    addChildNode(node, textFieldValue2) {
      this.showPopupVisible2 = false;
      this.showPopupEditChild = false;
      this.showMenu2 = !this.showMenu2;
      this.newChildNode = textFieldValue2;
      node = this.currentNode;
      if (this.newChildNode) {
        if (!node.children) {
          this.$set(node, "children", []);
        }
        node.children.push({id: "node" + Date.now(), name: this.newChildNode, children: []});
        this.newChildNode = "";
      }
    },
    showChildPopup() {
      $('[id^="node"]').addClass('force-hide');
      this.showPopupVisible2 = true;
      this.textFieldValue2 = '';
      this.editChildText = '';
    },
    showPopupChildEdit() {
      $('[id^="node"]').addClass('force-hide');
      this.showPopupEditChild = true;
      this.textFieldValue2 = '';
      this.editChildText = this.currentNode.name;
    },
    toggleMenu1(node) {
      console.log(node);
      if ($('#' + node.id).hasClass('force-hide')) {
        $('[id^="node"]').addClass('force-hide');
        $('#' + node.id).removeClass('force-hide');
        this.currentNode = node;
      } else {

        $('[id^="node"]').addClass('force-hide');
        this.currentNode = node;
      }

      this.showMenu2 = !this.showMenu2;
      this.textFieldValue2 = '';
      this.editChildText = '';
    },
    editChildNode(node, editChildText) {
      this.showPopupEditChild = false;
      node.name = editChildText;
      this.textFieldValue2 = '';
      this.editChildText = '';
    },
    removeItem: function () {
      // console.log(this.currentNode);
      // this.$emit("update:nodes", this.nodes.filter(item => item.id !== this.currentNode.id));
      // this.$parent.removeNode(this.currentNode.id);
      // this.$emit('remove-item', this.currentNode.id);

      for (let i = 0; i < this.$parent.nodes.length; i++) {
        $('[id^="node"]').addClass('force-hide');
        if (this.$parent.nodes[i].id == this.currentNode.id) {
          this.$parent.removeNode(this.currentNode.id);
        } else {
          for (let j = 0; j < this.$parent.nodes[i].children.length; j++) {

            if (this.$parent.nodes[i].children[j].id == this.currentNode.id) {
              console.log(this.$parent.nodes[i].children[j].name);
              this.$parent.nodes[i].children.splice(j, 1);
            }
          }
        }

      }
      // this.showMenu2 = false;
    },
    cancelNode() {
      this.showPopupEditChild = false;
      this.showPopupVisible2 = false;
      this.selectedNode = "";
      this.textFieldValue2 = "";
      this.editChildText = "";
    },
    copySelectedNodes() {
      // eslint-disable-next-line no-debugger
      debugger;
      this.copyNodes.id = this.currentNode.id;
      this.copyNodes.name = this.currentNode.name;
      if (this.currentNode.children) {
        this.copyNodes.children = JSON.parse(JSON.stringify(this.currentNode.children));
      }
      // eslint-disable-next-line no-debugger
      debugger;
    },
    pasteOnSelectedNodes() {
      // eslint-disable-next-line no-debugger
      debugger;
      if(this.copyNodes.length > 0 ){
        this.currentNode.children.push(this.copyNodes);
      }
      // eslint-disable-next-line no-debugger
      debugger;
    },
  }
};
</script>
