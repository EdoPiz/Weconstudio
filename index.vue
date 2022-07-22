<template>
  <v-row justify="center">
    <v-col cols="12" sm="8" md="12">
      <v-card class="logo py-4 d-flex justify-center"></v-card>
      <v-card>
        <v-card-title class="headline">
          22/7/22
        </v-card-title>
        <div style ="height: 500px; width: 100%">
          <l-map id="map" ref="map" style="height: 100%; width: 100%;" :zoom="zoom" :center="center" @dblclick="dblClickMap"  >
            <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
            
          </l-map>
        </div>
      </v-card>
    </v-col>
    <div id="formAll" justify="center" >
      <v-dialog persistent max-width="400px" style="text-align: center !important">
        <template v-slot:activator="{ on, attrs }" hidden>
          <button id="btnModify" hidden style="border: 1px solid black; box-shadow: 2px 2px 1px 1px rgba(0, 0, 0 , 0.4); background-color: lightslategrey; padding:5px; border-radius: 5px;" v-bind="attrs" v-on="on"> MODIFICA </button>
        </template>
        <v-card id="form" style="background-color:white !important; text-align: center !important;">
          <v-card-title>
            <span class="text-h5" style="color:black !important">Informazioni</span>
          </v-card-title>
          <v-card-text>
            <v-container>    
              <v-row style="margin:15px; padding:15px; height:50px">
                <input placeholder="Nome marker..." type="text" class="field" required/>
              </v-row>
              <v-row style="margin:15px; padding:15px; height:50px">
                <input placeholder="Descrizione..." type="text" class="field" required/>
              </v-row>
              <v-row style="margin:15px; padding:15px; height:50px">
                <input placeholder="Raggio..." type="number" class="field" required/>
              </v-row>
              <v-row style="margin:15px; padding:15px; height:50px;">
                  <input placeholder="Colore..." type="color" class="field" style="height: 50px !important; width:200px !important" required>
              </v-row>  
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn id="btnClose" @click="close" style="border: 1px solid black; box-shadow: 2px 2px 1px 1px rgba(0, 0, 0 , 0.4); background-color: darkred; padding:5px; border-radius: 5px;">CLOSE</v-btn>
            <v-btn id="btnSave" @click="save" style="border: 1px solid black; box-shadow: 2px 2px 1px 1px rgba(0, 0, 0 , 0.4); background-color: green; padding:5px; border-radius: 5px;">SAVE</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
      <div id="snackbar"></div>
    </div>
  </v-row>
</template>


<style>
  .field{
    width:200px;
    height:30px; 
    padding:5px; 
    color:black; 
    border:1px solid black;
    border-radius:5px;
    line-height:30px; 
  }
  #snackbar {
  visibility: hidden;
  min-width: 250px;
  margin-left: -125px;
  text-align: center;
  border-radius: 2px;
  padding: 16px;
  position: fixed;
  z-index: 1;
  left: 50%;
  bottom: 30px;
  font-size: 17px;
}

#snackbar.show {
  visibility: visible;
  -webkit-animation: fadein 0.5s, fadeout 0.5s 2.5s;
  animation: fadein 0.5s, fadeout 0.5s 2.5s;
}

@-webkit-keyframes fadein {
  from {bottom: 0; opacity: 0;} 
  to {bottom: 30px; opacity: 1;}
}

@keyframes fadein {
  from {bottom: 0; opacity: 0;}
  to {bottom: 30px; opacity: 1;}
}

@-webkit-keyframes fadeout {
  from {bottom: 30px; opacity: 1;} 
  to {bottom: 0; opacity: 0;}
}

@keyframes fadeout {
  from {bottom: 30px; opacity: 1;}
  to {bottom: 0; opacity: 0;}
}
</style>

<script src="https://unpkg.com/leaflet@1.8.0/dist/leaflet.js" integrity="sha512-BB3hKbKWOc9Ez/TAwyWxNXeoV9c1v6FIeYiBieIWkpLjauysF18NzgR1MBNBXf8/KABdlkX68nAhlwcDFLGPCQ=="crossorigin=""></script>
<script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>

<script>
  import L from 'leaflet';
  import {LMap, LTileLayer, LMarker, LCircle, LPopup, LFeatureGroup, LControl} from 'vue2-leaflet';

  export default {
    components: {
      LMap,
      LTileLayer,
      LMarker,
      LCircle,
      LPopup,
      LFeatureGroup,
      LControl,
    },
    data () {
      return {
        url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
        attribution:
          '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
        zoom: 6,
        center: [42.5664377,11.5314529],
        bra: { //sede Weconstudio come marker di default
          city: 'Bra',
          lat:'44.6956071',
          long:'7.8433976',
          radius:'100'
        },
      };
    },
    methods: {
      save () {
        /*let name = document.getElementsByClassName("field")[0]
        let description=document.getElementsByClassName("field")[1]
        let radius=document.getElementsByClassName("field")[2]*/
        let fields=document.getElementsByClassName("field")
        let name=fields[0].value
        let description=fields[1].value
        let radius=parseInt(fields[2].value)
        console.log("name: "+name+"\ndesc: "+description+"\nradius: "+radius+"\ncolor: "+fields[3].value)

        let correct=false
        if(name!="" && !isNaN(radius)) correct=true
        else correct=false

        if(correct){
          L.circle([this.position.lat, this.position.lng], { color:fields[3].value, fillColor: fields[3].value, fillOpacity: 0.3, radius: radius }).addTo(this.$refs.map.mapObject);
          var snackbar = document.getElementById("snackbar")
          snackbar.className = "show"
          snackbar.innerText="Posizione salvata con successo!"
          snackbar.style.color="green";
          snackbar.style.backgroundColor="lightgreen"
          setTimeout(function(){ snackbar.className = snackbar.className.replace("show", ""); }, 3000);

          var btn=document.getElementById("btnModify")
          var overlayS=document.getElementsByClassName("v-overlay__scrim")[0]
          var overlay=document.getElementsByClassName("v-overlay")[0]
          overlay.style.display="none"
          overlayS.style.display="none"
          btn.hidden=true
          form.style.display="none"
        }
        else{
          var snackbar = document.getElementById("snackbar");
          snackbar.className = "show";
          snackbar.innerText="Compila correttamente i campi..."
          snackbar.style.color="darkred";
          snackbar.style.backgroundColor="red"
          setTimeout(function(){ snackbar.className = snackbar.className.replace("show", ""); }, 3000);
        }
        // da aggiungere API
      },
      async dblClickMap (value) { //creazione marker
        this.position=value.latlng
        var btn=document.getElementById("btnModify")
        var map=document.getElementById("map")
        btn.hidden=true
        map.style.zIndex="0"
        L.marker([this.position.lat, this.position.lng]).addTo(this.$refs.map.mapObject).on('click', function(){ 
          btn.hidden=false
        })
      },
      close(){
        var btn=document.getElementById("btnModify")
        var form=document.getElementById("form")
        var overlayS=document.getElementsByClassName("v-overlay__scrim")[0]
        var overlay=document.getElementsByClassName("v-overlay")[0]
        var overlayY=document.getElementsByClassName("overflow-y-hidden")[0]
        overlayY.className=overlayY.className.replace("overflow-y-hidden","")
        overlay.style.display="none"
        overlayS.style.display="none"
        btn.hidden=true
        form.style.display="none"
      },
  }
}
</script>