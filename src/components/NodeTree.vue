<template>
  <div>
    <ul>
      <li v-for="node in nodes" :key="node.id">

        <button v-if="false" @click="toggleMenu1">Add Path</button>

        <div id="abc3">
          <div class="menu-container">
            <button class="menu-button" @click="toggleMenu1(node)" :style="{ color:  node.color, 'border': '1px solid '+ node.color}">{{ node.name }}</button>
            <!-- Menu -->
            <div :id="node.id" class="menu force-hide" :style="{ top:  + '20px', left:  '10px' }">
              <div @click="showPopupChildEdit" class="menu-item">Eintrag bearbeiten</div>
              <div @click="showChildPopup" class="menu-item">Eintrag einfügen</div>
              <div @click="removeItem()" class="menu-item">Eintrag löschen</div>
              <label class="menu-item">
                <input type="file" style="display: none" accept=".jpeg, .jpg, .png" @change="handleImageUpload" />
                Bild einfügen
              </label>
              <label class="menu-item">
                <input type="file" style="display: none" accept=".pdf" @change="handleFileUpload" />
                PDF einfügen
              </label>
              <div @click="embedSingleSelection" class="menu-item">Einzelauswahl</div>
              <div @click="removeSingleSelection" class="menu-item">Keine Einzelauswahl</div>
              <div @click="copySelectedNodes()" class="menu-item">In die Zwischenablage kopieren</div>
              <div @click="pasteOnSelectedNodes()" class="menu-item">Aus Zwischenablage einfügen</div>
              <div @click="openColorSelectedNodePopup" class="menu-item">Farbe wählen</div>
            </div>
          </div>
        </div>
        <!-- Popup -->
        <div v-if="showChildPopupText" class="popup">
          <label>Wei soll der Eintrag heissen?</label><br/>
          <input v-model="childTextField" type="text" placeholder="Enter value"/><br/>
          <button class="add-button margin5" @click="addChildNode(node,childTextField)">OK</button>
          <button class="add-button margin5" @click="cancelNode(childTextField)">Cancel</button>
        </div>

        <div v-if="showPopupEditChild" class="popup">
          <label>Wei soll der gewaehlte Punkt heissen?</label><br/>
          <input v-model="editChildText" type="text" placeholder="Enter value"/><br/>
          <button class="add-button margin5" @click="editChildNode(node,editChildText)">OK</button>
          <button class="add-button margin5" @click="cancelNode(editChildText)">Edit Button</button>
        </div>
        <div v-if="showPopupColorChild"  class="popup popupColor">
          <label>Bitte wähle eine Farbe aus:</label><br/>
          <button class="add-button margin5" @click="colorSelectedNode(node,'#000000')">Scharz</button>
          <button class="add-button margin5" @click="colorSelectedNode(node,'#808080')">Grau</button>
          <button class="add-button margin5" @click="colorSelectedNode(node,'#FF0000')">Rot</button>
          <button class="add-button margin5" @click="colorSelectedNode(node,'#008000')">Grün</button>
          <button class="add-button margin5" @click="colorSelectedNode(node,'#0000FF')">Blau</button>
          <button class="add-button margin5" @click="colorSelectedNode(node,'#00FFFF')">Cyan</button>
          <button class="add-button margin5" @click="colorSelectedNode(node,'#FF00FF')">Magenta</button>
          <button class="add-button margin5" @click="colorSelectedNode(node,'#FFFF00')">Gelb</button>
          <button class="add-button margin5" @click="colorSelectedNode(node,'#088F8F')">Blau Grün</button>
          <button class="add-button margin5" @click="colorSelectedNode(node,'#87CEEB')">Himmelblau</button>
          <button class="add-button margin5" @click="colorSelectedNode(node,'#7F00FF')">Violett</button>
          <button class="add-button margin5" @click="colorSelectedNode(node,'#FF007F')">Rose</button>
          <button class="add-button margin5" @click="colorSelectedNode(node,'#FFA500')">Orange</button>
          <button class="add-button margin5" @click="colorSelectedNode(node,'#9ACD32')">Gelbgrun</button>
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
    nodes: Array,
  },
  data() {
    // let id;
    // let children;
    return {
      newChildNode: "",
      showChildMenu: false,
      showChildPopupText: false,
      showPopupEditChild: false,
      childTextField: '',
      editChildText: '',
      currentNode: [],
      showPopupColorChild: false,
      copyNodes: [],
    };
  },
  methods: {
    addChildNode(node, childTextField) {
      this.showChildPopupText = false;
      this.showPopupEditChild = false;
      this.showChildMenu = !this.showChildMenu;
      this.newChildNode = childTextField;
      node = this.currentNode;
      if (this.newChildNode) {

        if(childTextField == "pdfFileParam" && node.fileType != 'pdfs' && node.fileType != 'images'){
          let children = {id: "node" + Date.now(), name: this.fileName, children: [], selection: false, fileType:"pdf"};
          let child = [];
          child.push(children);

          if (!node.children) {
            this.$set(node, "children", []);
          }
          node.children.push({id: "node" + Date.now(), name: "PDF", children: child, selection: false, fileType:"pdfs"});

        } else if(childTextField == "imageFileParam" && node.fileType != 'pdfs' && node.fileType != 'images'){
          let children = {id: "node" + Date.now(), name: this.fileName, children: [], selection: false, fileType:"image"};
          let child = [];
          child.push(children);

          if (!node.children) {
            this.$set(node, "children", []);
          }
          node.children.push({id: "node" + Date.now(), name: "Bilder", children: child, selection: false, fileType:"images"});

        } else{
          if (!node.children) {
            this.$set(node, "children", [] , "selection", false, "fileType","none");
          }
          node.children.push({id: "node" + Date.now(), name: this.newChildNode, children: [], selection: false, fileType:"none"});
        }

        this.newChildNode = "";
      }
    },
    showChildPopup() {
      this.closeMenu();
      this.showChildPopupText = true;
      this.childTextField = '';
      this.editChildText = '';
    },
    handleFileUpload(event) {
      this.closeMenu();
      const file = event.target.files[0];
      // You can now access the uploaded file and handle it as needed
      console.log('Uploaded file:', file);

      if(file.type != 'application/pdf'){
        alert('Please Upload Image File only!!')
      } else{
        this.fileName = file.name;
        this.addChildNode((JSON.parse(JSON.stringify(this.currentNode))),"pdfFileParam");
      }
    },
    handleImageUpload(event) {
      this.closeMenu();
      const file = event.target.files[0];

      if(file.type != 'image/jpeg' && file.type != 'image/jpg' && file.type != 'image/png'){
        alert('Please Upload Image File only!!')
      } else{
        this.fileName = file.name;
        this.addChildNode((JSON.parse(JSON.stringify(this.currentNode))),"imageFileParam");
      }
    },
    showPopupChildEdit() {
      this.closeMenu();
      this.showPopupEditChild = true;
      this.childTextField = '';
      this.editChildText = this.currentNode.name;
    },
    toggleMenu1(node) {
      console.log(node);
      this.showPopupColorChild = false;
      if ($('#' + node.id).hasClass('force-hide')) {
        this.closeMenu();
        $('#' + node.id).removeClass('force-hide');
        this.currentNode = node;
      } else {

        this.closeMenu();
        this.currentNode = node;
      }
      this.saveCurrentNodeToLocalStorage();
      this.showChildMenu = !this.showChildMenu;
      this.childTextField = '';
      this.editChildText = '';
    },
    editChildNode(node, editChildText) {
      this.showPopupEditChild = false;
      this.currentNode.name = editChildText;
      this.childTextField = '';
      this.editChildText = '';
    },
    removeItem: function () {
      for (let i = 0; i < this.$parent.nodes.length; i++) {
        this.closeMenu();
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
    },
    cancelNode() {
      this.showPopupEditChild = false;
      this.showChildPopupText = false;
      this.selectedNode = "";
      this.childTextField = "";
      this.editChildText = "";
    },
    embedSingleSelection(){
      if (!this.currentNode.name.includes("- Einzelauswahl")) {
        this.currentNode.selection = true;
        this.currentNode.name += " - Einzelauswahl";
      }
      this.closeMenu();
    },
    removeSingleSelection(){
      if (this.currentNode.name.includes("- Einzelauswahl")) {
        this.currentNode.selection = false;
        this.currentNode.name = this.currentNode.name.replace(" - Einzelauswahl", "");
      }
      this.closeMenu();
    },
    openColorSelectedNodePopup(){
      this.showChildMenu = false;
      this.showPopupColorChild = true;
      this.closeMenu();
    },
    closeMenu(){
      $('[id^="node"]').addClass('force-hide');
    },
    colorSelectedNode(node,color){
      this.showPopupColorChild = false;
      this.currentNode.color = color
    },
    copySelectedNodes() {
      if (this.currentNode) {
        this.copyNodes = this.currentNode;
        this.saveCopyNodeToLocalStorage();
      }
      this.closeMenu();
    },
    printNames(obj) {
      const modifiedObj = { ...obj }; // Create a copy of the object with a new ID
      const uniqueId = new Date().getTime() + Math.random().toString(36).substr(2, 9);
      modifiedObj.id = "node" + uniqueId; // Modify the ID with the specified prefix and unique ID


      if (modifiedObj.children && modifiedObj.children.length > 0) {
        modifiedObj.children = modifiedObj.children.map((child) =>
            this.printNames(child)
        );
      }

      return modifiedObj;
    },
    pasteOnSelectedNodes: function () {

      this.loadCopyNodeFromLocalStorage();
      this.loadCurrentNodeFromLocalStorage();
      this.copyNodes = this.printNames(this.copyNodes);
      this.saveCopyNodeToLocalStorage();
      console.log(this.$parent.nodes.length);
      for (let i = 0; i < this.$parent.nodes.length; i++) {
        this.closeMenu();
        if (this.$parent.nodes[i].id == this.currentNode.id) {
          this.$parent.copyNode(this.currentNode.id, this.copyNodes);
        } else {
          for (let j = 0; j < this.$parent.nodes[i].children.length; j++) {

            if (this.$parent.nodes[i].children[j].id == this.currentNode.id) {
              console.log(this.$parent.nodes[i].children[j].name);
              this.$parent.nodes[i].children[j].children.push(this.copyNodes);
              break;
            }
          }
        }
      }
      this.closeMenu();
    },
    handleDocumentClick(event) {
      // Check if the clicked element is inside the menu or its parent container
      const isInsideMenu = event.target.closest(".menu-container");
      if (!isInsideMenu) {
        this.closeMenu();
      }
    },
    saveCurrentNodeToLocalStorage() {
      localStorage.setItem('currentNode', JSON.stringify(this.currentNode));
    },
    loadCurrentNodeFromLocalStorage() {
      const storedCurrentNode = localStorage.getItem('currentNode');
      if (storedCurrentNode) {
        this.currentNode = JSON.parse(storedCurrentNode);
      }
    },
    saveCopyNodeToLocalStorage() {
      localStorage.setItem('copyNodes', JSON.stringify(this.copyNodes));
    },
    loadCopyNodeFromLocalStorage() {
      const storedCopyNode = localStorage.getItem('copyNodes');
      if (storedCopyNode) {
        this.copyNodes = JSON.parse(storedCopyNode);
      }
    },
  },
  created() {
    this.loadCurrentNodeFromLocalStorage(); // Load currentNode from localStorage
  },
  watch: {
    nodes: {
      deep: true,
      handler: 'saveNodesToLocalStorage',
    },
    currentNode: {
      deep: true,
      handler: 'saveCurrentNodeToLocalStorage',
    },
    copyNodes: {
      deep: true,
      handler: 'saveCopyNodeToLocalStorage',
    },
  },
  mounted() {
    document.addEventListener("click", this.handleDocumentClick);
  },

  beforeUnmount() {
    document.removeEventListener("click", this.handleDocumentClick);
  },
};
</script>