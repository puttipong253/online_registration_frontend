<template>
  <Wrapper>
    <div>
      <v-row>
      <v-col cols="12" sm="12" md="12"><Header>การเข้าพักโรงแรม (เฉพาะเจ้าหน้าที่ สอจ.)</Header></v-col>

      <v-col cols="6" sm="6" md="6">
        <v-text-field
          class="text-custom"
          v-model="getHotel.Check_In"
          :rules="checkInRules"
          label="วันที่เช็คอิน"
          readonly
        ></v-text-field>
      </v-col>

      <v-col cols="6" sm="6" md="6">
        <v-text-field
          class="text-custom"
          v-model="getHotel.Check_Out"
          label="วันที่เช็คเอาท์"
          readonly
        ></v-text-field>
      </v-col>

      <v-col cols="12" sm="12" md="12"><Header>คู่พัก</Header></v-col>

      <v-dialog v-model="dialog" persistent max-width="500">
        <v-card>
          <v-card-title class="headline">คำอธิบายการเลือกคู่</v-card-title>
          <v-card-text class="text-body-1">1. กรณีเลือก "รอจับคู่พัก" เป็นการลงชื่อไว้ในระบบจับคู่ เพื่อให้คู่พักของท่านมาเลือกท่านทีหลัง</v-card-text>
          <v-card-text class="text-body-1">2. กรณีเลือก "เลือกคู่พัก" ให้ท่านเลือกจังหวัดของคู่พักของท่าน แล้วรายชื่อของผู้ลงทะเบียนในจังหวัดนั้นๆ จะแสดงออกมาให้ท่านเลือก</v-card-text>
          <v-card-text class="text-body-1">3. กรณีเลือก "ส่วนกลาง" เฉพาะเจ้าหน้าที่จากส่วนกลางเท่านั้น (กทม.)</v-card-text>
          <v-card-text class="red--text">****หมายเหตุ****</v-card-text>
          <v-card-text class="text-body-1">1.ถ้าหากคู่พักของท่านยังไม่ได้ลงทะเบียน ให้ท่านเลือก "กรณีที่ 1" เพื่อรอจับคู่</v-card-text>
          <v-card-text class="text-body-1">2.ถ้าหากท่านเลือก "กรณีที่ 2" คู่พักของท่านจะต้องลงทะเบียนไว้ก่อนแล้วเท่านั้น</v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="green darken-1" text @click="dialog = false">ยกเลิก</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <v-col cols="1" sm="1" md="1">
        <v-icon slot="prepend" color="blue" @click="popup">mdi-exclamation-thick</v-icon>
      </v-col>

      <v-col cols="4" sm="2" md="2">
        <v-tooltip v-model="show1" color="success" top>
          <template v-slot:activator="{ on }">  
            <div v-on="on">
              <v-radio-group v-model="getCustomer.Status" required row >                
                <v-radio class="mr-5" color="green" label="รอจับคู่พัก" :value="true"></v-radio>
              </v-radio-group>
            </div>            
          </template>          
          <v-card-text class="text-body-1">1. กรณีเลือก "รอจับคู่พัก" เป็นการลงชื่อไว้ในระบบจับคู่เพื่อให้คู่พักของท่านมาเลือกท่านภายหลัง</v-card-text>
          <v-card-text class="text-body-1">**หมายเหตุ** ถ้าหากคู่พักของท่านยังไม่ได้ลงทะเบียนให้ท่านเลือก ตัวเลือกนี้ เพื่อรอจับคู่</v-card-text>
        </v-tooltip>
      </v-col>

      <v-col cols="3" sm="2" md="2">
        <v-tooltip v-model="show2" color="success" top>
          <template v-slot:activator="{ on }">  
            <div v-on="on">
                <v-radio-group v-model="getCustomer.Status" required row >
                  <v-radio color="green" label="เลือกคู่พัก" :value="false"></v-radio>                    
                </v-radio-group>
            </div>            
          </template>          
          <v-card-text class="text-body-1">2. กรณีเลือก "เลือกคู่พัก" ให้ท่านเลือกจังหวัดของคู่พักของท่าน แล้วรายชื่อของผู้ลงทะเบียนในจังหวัดนั้นๆ จะแสดงออกมาให้ท่านเลือก</v-card-text>
          <v-card-text class="text-body-1">**หมายเหตุ** คู่พักของท่านจะต้องลงทะเบียนไว้ก่อนแล้วเท่านั้น รายชื่อถึงจะแสดง</v-card-text>
        </v-tooltip>
      </v-col>

      <v-col cols="4" sm="2" md="2">
        <v-tooltip v-model="show3" color="success" top>
          <template v-slot:activator="{ on }">  
            <div v-on="on">
                <v-radio-group v-model="getCustomer.Status" required row >
                  <v-radio color="green" label="ส่วนกลาง" :value="2"></v-radio>                    
                </v-radio-group>
            </div>            
          </template>          
          <v-card-text class="text-body-1">เฉพาะเจ้าหน้าที่จากส่วนกลางเท่านั้น (กทม.)</v-card-text>
        </v-tooltip>
      </v-col>

      <v-col cols="6" sm="3" md="2">
        <v-select
          class="text-custom"
          v-model="getHotel.Partner_Province"
          :items="getMyProvince"
          :disabled="getCustomer.Status"
          @change="setPartnerName"
          label="จังหวัดของผู้ร่วมพัก"
          item-text="th"
          clearable
        ></v-select>
      </v-col>

      <v-col cols="6" sm="2" md="3">
        <v-select
          class="text-custom"
          v-model="getRoom.Customer_2_ID"
          :items="getPartnerName"
          :disabled="getCustomer.Status"
          :item-text="text"
          item-value="Customer_ID"
          label="ชื่อของผู้ร่วมพัก"
          clearable
        ></v-select>
      </v-col>

      <v-col cols="12" sm="12" md="12">
        <v-textarea
          class="mx-2"
          v-model="getHotel.Note"
          label="หมายเหตุ"
          rows="1"
          prepend-icon="mdi-comment"
        ></v-textarea>
      </v-col>
    </v-row>
    </div>
  </Wrapper>
</template>

<script>
import { Wrapper, Header } from "./index.style";
export default {
  data: () => ({
    dialog: false,
    valid: false,
    show1: false,
    show2: false,
    show3: false,
    checkInRules: [(v) => !!v || "กรุณาเลือกวันที่เช็คอิน"],
    checkOutRules: [(v) => !!v || "กรุณาเลือกวันที่เช็คเอาท์"],
    partnerRules: [(v) => !!v || "กรุณาเลือกคู่พัก"],
  }),
  components: {
    Wrapper,
    Header
  },
  mounted(){
    this.getMyProvince.sort(this.compareItem)
  },
  computed: {
    getHotel() {
      return this.$store.getters.getHotel;
    },
    getMyProvince(){
      return this.$store.getters.myProvince
    },
    getPartnerName(){
      return this.$store.getters.getPartnerName
    },
    getCustomer() {
      return this.$store.getters.getCustomer
    },
    getRoom() {
      return this.$store.getters.getRoom;
    },
    getPartner() {
      return this.$store.getters.getPartner;
    },
  },
  methods: {
    setPartnerName(){
      this.$store.dispatch('setPartnerName') //เมื่อจังหวัดมีการเปลี่ยนแปลงจะทำการ get Customer ที่มี id จังหวัด ที่ตรงกับจังหวัดที่เลือก
    },
    popup(){
      this.dialog = true
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
    text: item => item.F_Name + ' ' + item.L_Name
  },
};
</script>