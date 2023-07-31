<template>
  <div>
    <nav class="navbar">
      <div class="text-xs-center">
        <button class="menu-button" @click="downloadXml">App-Menü laden</button>
        <button class="menu-button" @click="clearXml">übersichtliches App-Menü</button>
        <button class="menu-button" @click="downloadXml">App-Menü speichern</button>
        <button class="menu-button" @click="downloadXmlAs">App-Menü speichern unter...</button>
      </div>
    </nav>
  </div>
  <h1>XML Builder</h1>
  <div class="home" style="position: relative;">
    <input v-if="false" v-model="selectedNode" @keyup.enter="addNode" placeholder="Enter path">
    <button v-if="false" @click="toggleMenu">Add Path</button>

    <!--<div v-for="node in nodes" :key="node.id">-->
    <!--&lt;!&ndash; Other content related to the node &ndash;&gt;-->
    <!--<NodeTree :nodes="nodes" @remove-item="removeNode" />-->
    <!--</div>-->
    <div id="abc2">
      <div class="menu-container">
        <button class="menu-button" @click="toggleMenu">Hauptmenü</button>
        <!-- Menu -->
        <div v-if="showMenu" class="menu" :style="{ top:  + '10px', left:  '10px' }">
          <div @click="showPopupEditField" class="menu-item">Eintrag bearbeiten</div>
          <div @click="showPopup" class="menu-item">Eintrag einfügen</div>
          <div @click="removeNode(node)" class="menu-item">Eintrag löschen</div>
          <div @click="handleOptionClick(node)" class="menu-item">Bild einfügen</div>
          <div @click="handleOptionClick('Copy')" class="menu-item">PDF einfügen</div>
          <div @click="handleOptionClick('Copy')" class="menu-item">Einzelauswahl</div>
          <div @click="handleOptionClick('Copy')" class="menu-item">Keine Einzelauswahl</div>
          <div @click="handleOptionClick('Copy')" class="menu-item">In die Zwischenablage kopieren</div>
          <div @click="handleOptionClick('Copy')" class="menu-item">Aus Zwischenablage einfügen</div>
          <div @click="handleOptionClick('Copy')" class="menu-item">Farbe wählen</div>
        </div>
      </div>
    </div>

    <!-- Popup -->
    <div v-if="showPopupVisible" class="popup">
      <label>Wei soll der Eintrag heissen?</label><br/>
      <input v-model="textFieldValue" type="text" placeholder="Enter value"/><br/>
      <button class="add-button margin5" @click="addNode(textFieldValue)">OK</button>
      <button class="add-button margin5" @click="cancelNode(textFieldValue)">Cancel</button>
    </div>
    <div v-if="showPopupEdit" class="popup">
      <label>Wei soll der gewaehlte Punkt heissen?</label><br/>
      <input v-model="editText" type="text" placeholder=""/><br/>
      <button class="add-button margin5" @click="editNode(node,editText)">OK</button>
      <button class="add-button margin5" @click="cancelNode(editText)">Edit Button</button>
    </div>
    <NodeTree :nodes="nodes"/>


  </div>
</template>
<style>
.menu-container {
  position: relative;
  display: inline-block;
}

.add-button {
  cursor: pointer;
  padding: 8px 12px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 4px;
  margin-top: 8px;
}

.menu-item {
  cursor: pointer;
  padding: 8px 12px;
  border-bottom: 1px solid #ccc;
  width: 100%;

  /* Center the text horizontally using Flexbox */
  display: flex;
  justify-content: center;
}

.margin5 {
  margin: 5px;
}

.force-hide {
  display: none !important;
}

.navbar {
  /* background-color: #007bff;*/
  padding: 8px;
  color: #fff;
}

.menu-button {
  cursor: pointer;
  padding: 4px 12px;
  margin-top: 5px;
  background-color: transparent; /* Set background color to transparent */
  color: #007bff; /* Text color (you can adjust this as needed) */
  border-radius: 4px;
  border: 1px solid #007bff; /* Border color (you can adjust this as needed) */
}

.menu-button:last-child {
  margin-right: 0;
}

.menu {
  position: absolute;
  top: 40px;
  right: 0;
  background-color: #f1f1f1;
  border: 1px solid #ccc;
  border-radius: 4px;
  display: flex;
  flex-direction: column;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
  width: 250px;
  height: auto;
  overflow-y: clip;
  z-index: 9999;
  align-items: flex-end;
}

.popup {
  position: absolute;
  top: 100px;
  left: 50%;
  transform: translateX(-50%);
  background-color: #f1f1f1;
  border: 1px solid #ccc;
  border-radius: 4px;
  padding: 16px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
  width: 34%;
}

</style>
<script>
import NodeTree from "@/components/NodeTree";
import $ from "jquery";

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
      showPopupVisible: false,
      textFieldValue: '',
      childButtons: [],
      editText: '',
      showPopupEdit: false,
    };
  },
  methods: {
    toggleMenu() {
      $('[id^="node"]').addClass('force-hide');
      this.showMenu = !this.showMenu;
    },

    handleOptionClick(option) {
      // Handle the click of menu options here
      console.log('Clicked option:', option);
    },
    addNode(value) {
      console.log('Clicked add:');
      $('[id^="node"]').addClass('force-hide');
      this.showPopupVisible = false;
      this.selectedNode = value;
      if (value) {
        this.nodes.push({id: "node" + Date.now(), name: this.selectedNode, children: []});
        this.selectedNode = "";
      }
    },
    cancelNode() {
      this.showPopupVisible = false;
      this.selectedNode = "";
      this.textFieldValue = "";
      this.editText = "";
      this.showPopupEdit = false;
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
      xmlString += '  <menue source="xmlBuilder" version="2.3 ' + this.currentDateTime() + '">\n';
      xmlString += this.arrayToXml(this.nodes, 2);
      xmlString += '  </menue>\n';
      xmlString += this.footer();
      xmlString += '</dnas>';
      const blob = new Blob([xmlString], {type: "text/xml"});
      const url = window.URL.createObjectURL(blob);

      const a = document.createElement("a");
      a.href = url;
      a.download = "data.xml";
      a.click();

      window.URL.revokeObjectURL(url);
    },
    downloadXmlAs() {
      let xmlString = '<?xml version="1.0" encoding="UTF-8"?>\n';
      xmlString += '<dnas date="' + this.currentDateTime() + '">\n';
      xmlString += '  <menue source="xmlBuilder" version="2.3 ' + this.currentDateTime() + '">\n';
      xmlString += this.arrayToXml(this.nodes, 2);
      xmlString += '  </menue>\n';
      xmlString += this.footer();
      xmlString += '</dnas>';

      // Ask the user for the file name
      const fileName = prompt("Enter the file name for the XML file:", "custom_filename.xml");

      if (fileName) {
        const blob = new Blob([xmlString], {type: "text/xml"});
        const url = window.URL.createObjectURL(blob);

        const a = document.createElement("a");
        a.href = url;
        a.download = fileName;
        a.click();

        window.URL.revokeObjectURL(url);
      }
    },


    removeNode(idToRemove) {

      console.log(idToRemove);
      this.nodes = this.nodes.filter((item) => item.id !== idToRemove);
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

    showPopup() {
      this.showMenu = false;
      this.showPopupVisible = true;
      this.textFieldValue = '';
      this.editText = '';
    },

    showPopupEditField() {
      this.showPopupEdit = true;
      this.textFieldValue = '';
      this.editText = this.currentNode.name;
    },
    editNode(node, editText) {
      this.showPopupEdit = false;
      node.name = editText;
      this.textFieldValue = '';
      this.editText = '';
    },
    footer() {
      return '  <portusV database="demo_2018" version="3.291" user="admin" date="2020-04-17 10:13:29">\n' +
          '    <settings>\n' +
          '      <RequiredFields />\n' +
          '    </settings>\n' +
          '  </portusV>\n' +
          '  <cars database="demo_2018" version="3.291" user="admin" date="2020-05-20 14:00:34">\n' +
          '    <car id="100" name="Kehrmaschine" licensePlate="XX-WN 2206" code="WN-WN2206" />\n' +
          '    <car id="200" name="Toro Mäher" licensePlate="Toro" code="Toro" />\n' +
          '    <car id="300" name="Kontroll KFZ" licensePlate="XX-A 2447" code="FR-A2447" />\n' +
          '    <car id="400" name="XX-SH834" licensePlate="XX-SH834" code="OG-SH834" />\n' +
          '    <car id="500" name="Ladog" licensePlate="TS-2031" code="TS-2031" />\n' +
          '    <car id="800" name="MAN (XX PE 887)" licensePlate="XX-PE-887" code="BB-PE887" />\n' +
          '    <car id="900" name="Actros" licensePlate="XX-FU108" code="VS-FU108" />\n' +
          '    <car id="1000" name="MAN" licensePlate="Spühlwagen" code="GR26686" />\n' +
          '    <car id="1100" name="Müllwagen" licensePlate="Müllwagen" code="GR103038" />\n' +
          '    <car id="1500" name="Aufsitzrasenmäher" licensePlate="ZZ-ab01" code="ZZ-ab01" />\n' +
          '    <car id="2900" name="MAN-2002" licensePlate="MAN-2002" code="MAN-2002" />\n' +
          '    <car id="3000" name="MAN-Vorarlberg" licensePlate="DO-4XXXxx" code="DO-453DN" />\n' +
          '    <car id="3100" name="KEMA LKW Vorarlberg" licensePlate="DO 2XX xx" code="DO294CE" />\n' +
          '    <car id="3200" name="LKW WD Fremd Halb" licensePlate="DO-3" code="DOMAN3" />\n' +
          '    <car id="3301" name="KFZ Sinkkasten" licensePlate="XX E 4688" code="OF-E4688" />\n' +
          '    <car id="3302" name="Demo Koffer" licensePlate="FR-Koffer" code="FR-Koffer" />\n' +
          '    <car id="3303" name="Bucher-Schörling" licensePlate="XX-QX 9" code="TS-QX9" />\n' +
          '    <car id="3305" name="Passat" licensePlate="FR-IT1030" code="FR-IT1030" />\n' +
          '    <car id="3308" name="Schlepper Holder C 270" licensePlate="NK-ZB 111" code="NK-ZB 111" />\n' +
          '    <car id="3310" name="Schlepper Kommunal" licensePlate="DA-SJ 1900" code="SJ-1900" />\n' +
          '    <car id="3311" name="Tablet" licensePlate="Tablet" code="tablet" />\n' +
          '    <car id="3312" name="Tablet-Test" licensePlate="FR-Ebringen" code="HA-KOdemo" />\n' +
          '    <car id="3313" name="Iseki Schlepper" licensePlate="FR-N3116" code="Iseki Schlepper" />\n' +
          '    <car id="3314" name="Fendt Vorarlberg" licensePlate="DO9XXXxx" code="DO996AC" />\n' +
          '    <car id="3316" name="WLAN Test" licensePlate="Produktion" code="Produktion" />\n' +
          '    <car id="3317" name="Entwickl" licensePlate="Entwickl" code="Entwickl" />\n' +
          '  </cars>\n' +
          '  <server database="demo_2018" version="3.291" user="admin" date="2020-05-20 14:02:12">\n' +
          '    <address>testserver.dnas3.net</address>\n' +
          '    <port>2524</port>\n' +
          '    <database>demo_2018</database>\n' +
          '    <user>portus</user>\n' +
          '    <password>protuss</password>\n' +
          '  </server>\n' +
          '  <drivers database="demo_2018" version="3.291" user="admin" date="2020-05-20 14:02:12">\n' +
          '    <driver forename="Entwicklung" familyname="Entwicklung" idPerson="" chipcode="0074350963" codelevel="0" />\n' +
          '    <driver forename="Desfire-Test" familyname="Produktion" idPerson="04" chipcode="207d9a64d3" codelevel="0" />\n' +
          '    <driver forename="Legic-Test" familyname="Produktion" idPerson="234" chipcode="001ae42cfd" codelevel="0" />\n' +
          '    <driver forename="LF-Test" familyname="Produktion" idPerson="243" chipcode="04146e8eb6" codelevel="0" />\n' +
          '    <driver forename="Ha-Pe" familyname="Reeb" idPerson="600" chipcode="00148c0356" codelevel="0" />\n' +
          '    <driver forename="Christian" familyname="Schweitzer" idPerson="2707" chipcode="0058b9081a" codelevel="0" />\n' +
          '    <driver forename="Beifahrer" familyname="Tablet" idPerson="10" chipcode="0035e40631" codelevel="0" />\n' +
          '    <driver forename="Fahrer" familyname="Tablet" idPerson="11" chipcode="0024d00501" codelevel="0" />\n' +
          '  </drivers>\n' +
          '  <tours database="reichenbach" version="3.305" user="admin" date="2022-03-31 14:52:59">\n' +
          '    <tour id="40" name="Tour 500" />\n' +
          '  </tours>\n';
    },
  }
};
</script>