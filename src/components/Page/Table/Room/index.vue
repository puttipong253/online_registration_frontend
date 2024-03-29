<template>
  <Wrapper>
    <v-card-title>
      รายชื่อห้องพัก
      <v-btn class="ml-5 info" @click="downloadRoom">ดาวน์โหลด</v-btn>
      <v-spacer></v-spacer>
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
      ></v-text-field>
    </v-card-title>

    <div v-if="customerRoom == ''">
      <v-data-table
        item-key="name"
        class="elevation-1"
        loading
        loading-text="Loading... Please wait"
        :headers="headers"
        :search="search"
      ></v-data-table>
    </div>

    <div v-else>
      <v-data-table
        :headers="headers"
        :items="customerRoom"
        :search="search"        
      >
      <template #[`item.full_name1`]="{ item }">{{ item.UID1 }} {{ item.PF_1 }} {{ item.F_1 }} {{ item.L_1 }}</template>
      <template #[`item.full_name2`]="{ item }">{{ item.UID2 }} {{ item.PF_2 }} {{ item.F_2 }} {{ item.L_2 }}</template>

      <template v-slot:top>
        <v-dialog v-model="dialog" persistent max-width="500px">
          <v-card>
            <v-card-title>
              <span class="headline"><h5>แก้ไขเลขห้อง</h5></span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="6" sm="6" md="6">
                    <v-select
                      class="text-custom"
                      v-model="getRoom.Province_1"
                      :items="myProvince" 
                      @change="updateCustomerRoom1"
                      item-text="th"
                      label="จังหวัด"
                    ></v-select>
                  </v-col>

                  <v-col cols="6" sm="6" md="6">
                    <v-select
                      class="text-custom"
                      v-model="getRoom.Customer_1_ID"
                      :items="getUpdateCustomerRoom1" 
                      :item-text="text"
                      item-value="Customer_ID"
                      label="ชื่อคนที่ 1"
                    ></v-select>
                  </v-col>

                  <v-col cols="6" sm="6" md="6">
                    <v-select
                      class="text-custom"
                      v-model="getRoom.Province_2"
                      :items="myProvince" 
                      @change="updateCustomerRoom2"
                      item-text="th"
                      label="จังหวัด"
                    ></v-select>
                  </v-col>

                  <v-col cols="6" sm="6" md="6">
                    <v-select
                      class="text-custom"
                      v-model="getRoom.Customer_2_ID"
                      :items="getUpdateCustomerRoom2" 
                      :item-text="text"
                      item-value="Customer_ID"
                      label="ชื่อคนที่ 2"
                    ></v-select>
                  </v-col>

                  <v-col cols="12" sm="12" md="12">
                    <v-text-field v-model="getRoom.Room_Number" label="หมายเลขห้อง"></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text :disabled="disabled" @click="save">บันทึก</v-btn>
              <v-btn color="blue darken-1" text :disabled="disabled" @click="close">ยกเลิก</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </template>
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon
          small
          class="mr-2"
          @click="editRoom(item)"
        >
          mdi-pencil
        </v-icon>
        <v-icon
          small
          color="red"
          @click="deleteRoom(item)"
        >
          mdi-delete
        </v-icon>
      </template>
      </v-data-table>

    </div>
  </Wrapper>
</template>

<script>
import { Wrapper } from './index.style'
  export default {
    data(){
      return{
          disabled: false,
          dialog: false,
          search: "",
          headers: [
            { text: 'ID', value: 'Room_ID' },
            { text: 'ชื่อคนที่ 1', value: 'full_name1' },
            { text: 'จังหวัด', value: 'PV_1' },
            { text: 'ชื่อคนที่ 2', value: 'full_name2' },
            { text: 'จังหวัด', value: 'PV_2' },
            { text: 'หมายเลขห้อง', value: 'Room_Number' },
            { text: 'แก้ไขเลขห้อง', value: 'actions', sortable: false }
          ],
      }
    },
    components:{
      Wrapper
    },
    mounted() {
      this.$store.dispatch('setCustomerRoom');   
      this.myProvince.sort(this.compareItem)  
    },
    watch: {
      overlay (val) {
        val && setTimeout(() => {
          this.overlay = false
        }, 3000)
      },
    },
    computed: {
      customerRoom() {
        return this.$store.getters.getCustomerRoom;
      },
      getRoom() {
        return this.$store.getters.getRoom;
      },
      getTmp() {
        return this.$store.getters.getTmp;
      },
      customer() {
        return this.$store.getters.getShowCustomer;
      },
      myProvince() {
        return this.$store.getters.myProvince;
      },
      getUpdateCustomerRoom1(){
        return this.$store.getters.getUpdateCustomerRoom1;
      },
      getUpdateCustomerRoom2(){
        return this.$store.getters.getUpdateCustomerRoom2;
      }
    },
    methods: {
      editRoom (item) {    
        this.disabled = false,          
        this.$store.dispatch('updateCustomerRoom1')
        this.$store.dispatch('updateCustomerRoom2')
        this.getRoom.Room_Number = item.Room_Number
        this.getRoom.Customer_1_ID = item.UID1
        this.getRoom.Customer_2_ID = item.UID2
        this.getTmp.Customer_1_ID = item.UID1
        this.getTmp.Customer_2_ID = item.UID2
        this.getRoom.Province_1 = item.PV_1
        this.getRoom.Province_2 = item.PV_2
        this.getRoom.Room_ID = item.Room_ID
        this.$store.state.roomItems = item
        this.dialog = true
      },
      close () {
        this.getRoom.Room_Number = ''
        this.getRoom.Customer_1_ID = ''
        this.getRoom.Customer_2_ID = ''
        this.getTmp.Customer_1_ID = ''
        this.getTmp.Customer_2_ID = ''
        this.getRoom.Province_1 = ''
        this.getRoom.Province_2 = ''
        this.getRoom.Room_ID = ''
        this.$store.state.roomItems = ''
        this.dialog = false
      },
      async save() {      
        this.disabled = true,     
        await this.$store.dispatch('resetData')             
        await this.$store.dispatch('updateRoom')
        await this.$store.dispatch('partnerRoomHotel')   
        await this.$store.dispatch('setCustomerRoom')      
        await this.$store.dispatch('setCustomersHotel');  
        await this.$store.dispatch('matching')
        await this.close()
      },
      updateCustomerRoom1(){
        this.$store.dispatch('updateCustomerRoom1')
      },
      updateCustomerRoom2(){
        this.$store.dispatch('updateCustomerRoom2')
      },
      compareItem(a, b){
        if(a.th < b.th){
                return -1;
        }else if(a.th > b.th){
                return 1;
        }else{
                return 0;
        }
      },
      downloadRoom(){
        this.$store.dispatch('downloadRoom')
      },
      async deleteRoom(item){
          this.$store.state.roomItems = item
          this.getTmp.Customer_1_ID = item.UID1
          this.getTmp.Customer_2_ID = item.UID2
          var con = confirm("ต้องการลบห้องพักนี้ใช่หรือไม่ ?");      
          if (con) {
            await this.$store.dispatch('deleteRoom');
            setTimeout(() => {
                this.$store.dispatch('setCustomerRoom');   
                this.$store.dispatch('setShowCustomer');
                this.$store.dispatch('setCustomerHotel');
                this.$store.dispatch('matching')
                this.$store.dispatch('countAllCustomer')
                this.$store.dispatch('countCustomerMatch')
                this.$store.dispatch('countCustomerNotMatch')
                this.$store.dispatch('countCustomerRoom')
            }, 1500);
            
          }        
          else{
            return false;
          }   
      },
      text: item => item.F_Name + ' ' +  item.L_Name
    },
  }
</script>