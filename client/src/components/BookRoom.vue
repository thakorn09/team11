<template>
<html>
  <Navbar/>
  <body background="blue-and-silver-stetoscope-40568.jpg">
    <center>
      <br>
      <body class="card1">
    
    <br>
    <center>
        
         <h1> จองห้องสำหรับแพทย์</h1>
        
          
    <div>
      <div class="md-layout-item">
      <md-field>
          <label>แพทย์ผู้ทำการจองห้อง</label>
          <md-select id="doc" class="profileSelect" v-model="profileSelect">
                <md-option id="doc" v-for="profile in profiles" :key="profile.profile_id" :value="profile.profile_id">{{profile.name}} </md-option>
          </md-select>
  </md-field>
      </div>

      <div class="md-layout-item">
     <md-field>
          <label >ชื่อคนไข้</label>
          <md-select id="pat" class="patSelect"  v-model="patSelect">
                 <md-option id="pat" v-for="pat in pats" :key="pat.patient_id" :value="pat.patient_id">{{pat.name}} </md-option>
          </md-select>
    </md-field>
      </div>
       <div class="md-layout-item">
      <md-field>
          <label>ห้อง</label>
          <md-select id="room" class="roomSelect" v-model="roomSelect">
                 <md-option id="room" v-for="room in rooms" :key="room.room_id" :value="room.room_id">{{room.room}} </md-option>
          </md-select>
  </md-field>
      </div>
      <v-col cols="12" lg="5">
                <v-menu
                  id="menu"
                  class="dateS"
                  ref="menu"
                  v-model="menu"
                  :close-on-content-click="false"
                  :return-value.sync="date"
                  transition="scale-transition"
                  offset-y
                  full-width
                  min-width="290px"
                >
                  <template v-slot:activator="{ on }">
                    <v-text-field id="dateS"
                      v-model="dateS"
                      label="เลือกวันที่ทำการจอง"
                      prepend-icon="event"
                      readonly
                      v-on="on"
                    ></v-text-field>
                  </template>
                  <v-date-picker id ="date" v-model="dateS" locale="th" no-title scrollable>
                    <v-spacer></v-spacer>
                    <v-btn text color="primary" @click="menu = false">Cancel</v-btn>
                    <v-btn text color="primary" @click="$refs.menu.save(dateS),menu = false">OK</v-btn>
                  </v-date-picker>
                </v-menu>
              </v-col>            
        
          <v-col cols="12" lg="5">
      <v-menu
        id="time1"
        ref="menu"
        class="time1"
        v-model="menu2"
        :close-on-content-click="false"
        :nudge-right="40"
        :return-value.sync="time"
        transition="scale-transition"
        offset-y
        max-width="290px"
        min-width="290px"
      >
        <template v-slot:activator="{ on }">
          <v-text-field
            id="timeS"
            v-model="timestart"
            label="Picker in menu"
            prepend-icon="access_time"
            readonly
            v-on="on"
          ></v-text-field>
        </template>
        <v-time-picker
        id ="menu2"
        class="time2"
          v-if="menu2"
          v-model="timestart"
          full-width
          @click:minute="$refs.menu.save(time)"
        ></v-time-picker>
        <v-btn text color="primary" @click="menu2 = false">Ok</v-btn>
      </v-menu>
    </v-col>
    <v-col cols="12" lg="5" >
      <v-menu
        ref="menu"
        id ="modal2"
        v-model="modal2"
        :close-on-content-click="false"
        :nudge-right="40"
        :return-value.sync="time"
        transition="scale-transition"
        offset-y
        max-width="290px"
        min-width="290px"
      >
        <template v-slot:activator="{ on }">
          <v-text-field
          id ="timee"
            v-model="timeend"
            label="Picker in menu"
            prepend-icon="access_time"
            readonly
            v-on="on"
          ></v-text-field>
        </template>
        <v-time-picker
          v-if="modal2"
          v-model="timeend"
          full-width
          @click:minute="$refs.menu.save(time)"
        ></v-time-picker>
        <v-btn text color="primary" @click="modal2 = false">Ok</v-btn>
      </v-menu>
    </v-col>      
       <md-field>

      <label>บันทึกอาการและการรักษา (ห้ามมีเครื่องหมาย /)</label>
      <md-textarea id ="des" v-model="description"></md-textarea>
      <md-icon>description</md-icon>
    </md-field>
         <center>
          <md-button id="btsave" class="md-raised md-primary" @click = "savedata()">บันทึก</md-button> 
          <md-button id="btdialog" class="md-raised md-primary" @click = "dialog = true">แสดงข้อมูลการจอง</md-button> 
        </center>
        
    </div>
    
  <v-dialog
      v-model="dialog"
      max-width="1000"
      max-height="500"
    >
      <v-card class="blue lighten-5">
        <v-card-title class="headline">ข้อมูลการจองห้องภายในโรงพยาบาล</v-card-title>
        <v-card >
         <v-data-table 
            :headers="headers"
            :items="items"
            :items-per-page="5"
            class="elevation-1  blue lighten-5"
  ></v-data-table>
        </v-card>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="green darken-1"
            text
            @click="dialog = false">
            ปิด
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    
    </center>
    </body>
</center>
  </body>

  
</html>
</template>
<script>
import Navbar from '../components/Navbar'
import http from "../http-common";
export default {
  components: {
    Navbar
  },
data() {
    return {
      headers: [
      //{ text: "ID", value: "id" },
        {
          text: "ID",
          align: "left",
          sortable: false,
          value: "id"
        },
        { text: "ชื่อแพทย์", value: "profile.name" },
        { text: "ชื่อผู้ป่วย", value: "patientManagement.name" },
        { text: "ห้อง", value: "room.room" },
        { text: "วันที่จอง", value: "dateOfBook" },
        { text: "เวลาเริ่มใช้ห้อง", value: "timeOfStart" },
        { text: "เวลาสิ้นสุดการใช้ห้อง", value: "timeOfEnd" }
      ],
      items: [],
      dialog: false,
      profiles: null,
      profileSelect : null,
      patSelect: null,
      pats : null,
      rooms : null,
      roomSelect : null,
      dateS : new Date().toISOString().substr(0, 10),
      menu : false,
      timestart : null,
        menu2: false,
        modal2: false,
      timeend : null,
     description : null,

    };
  },
  methods: {
        /* eslint-disable no-console */
     getBookRoom() {
      http
        .get("/Bookroom")
        .then(response => {
          this.items = response.data;
          console.log(this.items);
        })
        .catch(e => {
          console.log(e);
        });

    },
    getProfile() {
      http
        .get("/profile")
        .then(response => {
          this.profiles = response.data;
          console.log(response.data);
        })
        .catch(e => {
          console.log(e);
        });

    },
    getPatient() {
      http
        .get("/patientmanagement")
        .then(response => {   
          this.pats = response.data;
          console.log(response.data);
        })
        .catch(e => {
          console.log(e);
        });
          },
     
    getRoom(){
        http
            .get("/Room")
            .then(response =>{
                this.rooms = response.data;
                console.log(response.data);
            })
            .catch(e => {
            console.log(e);
            });
            },
    
    savedata(){
          http
            .post(
                "/Bookroom/" +
                this.description +
                "/" + 
                this.dateS+
                "/" + 
                this.timestart +
                "/" +
                this.timeend +
                "/" +
                this.roomSelect +
                "/" +
                this.profileSelect+
                "/" +
                this.patSelect
                    )
                    .then(response => {
                      console.log(response);
                      this.$alert(
                               "บันทึกข้อมูลสำเร็จ",
                               "Success",
                               "success"
                   ).then(() => console.log("Closed",location.reload()));
                    })
                    .catch(e => {
                      console.log(e);
                       this.$alert(
                           "บันทึกข้อมูลไม่สำเร็จ",
                           "Warning",
                           "warning"
                        ).then(() => console.log("Closed",location.reload()));
                        
                    });
                        
        console.log(this.profileSelect,this.patSelect,this.roomSelect,this.dateS,this.timestart,this.timeend,this.description);
        }
  },
    mounted() {
      this.getProfile();
      this.getPatient();
      this.getRoom();
      this.getBookRoom();
  },

}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>

.md-field{
  max-width: 400px;
}
.card1 {
  width: 40%;
  height: 50%;

  background-color: #E3F2FD;
}


</style>