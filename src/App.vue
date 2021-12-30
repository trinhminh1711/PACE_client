<template>
  <v-app style="padding: 50px">
    <h2 style="text-align: center; padding: 10px; margin-bottom: 20px">
      CHƯƠNG TRÌNH ĐỌC EPASSPORT
    </h2>
    <v-container class="">
      <v-row no-gutters>
        <v-col class="pl-10" cols="12" sm="6" md="6">
          <v-row>
            <v-col cols="12" sm="6" md="4">
              <label style="font-size: 14px" for="Tổ chức phát hành"
                >Mã cá nhân / Personal ID</label
              >
              <v-text-field
                disabled
                style="margin-top: 10px"
                :label="customer.id_code"
                dense
              ></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" md="4">
              <label style="font-size: 14px" for="Tổ chức phát hành"
                >Số hộ chiếu</label
              >
              <v-text-field
                disabled
                style="margin-top: 10px"
                :label="customer.epassport_number"
                dense
              ></v-text-field>
            </v-col>

            <v-col cols="12" sm="6" md="12">
              <label style="font-size: 14px" for="Tổ chức phát hành">Họ</label>
              <v-text-field
                disabled
                style="margin-top: 10px"
                :label="customer.first_name"
                dense
              ></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" md="12">
              <label style="font-size: 14px" for="Tổ chức phát hành">Tên</label>
              <v-text-field
                disabled
                style="margin-top: 10px"
                :label="customer.full_name"
                dense
              ></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" md="4">
              <label style="font-size: 14px" for="Tổ chức phát hành"
                >Quốc tịch</label
              >
              <v-text-field
                disabled
                style="margin-top: 10px"
                :label="customer.nationality"
                dense
              ></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" md="4">
              <label style="font-size: 14px" for="Tổ chức phát hành"
                >Giới tính</label
              >
              <v-text-field
                disabled
                style="margin-top: 10px"
                :label="customer.sex"
                dense
              ></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" md="4">
              <label style="font-size: 14px" for="Tổ chức phát hành"
                >Ngày sinh</label
              >
              <v-text-field
                disabled
                style="margin-top: 10px"
                :label="customer.birthday"
                dense
              >
              </v-text-field>
            </v-col>
          </v-row>
        </v-col>
        <v-col class="pl-10" cols="12" sm="6" md="6">
          <div>
            <h2>logs :</h2>
            <div style="background-color: red; padding: 10px; margin: 20px 0px">
              <p>
                Tiến hành thỏa thuận khóa Diffie–Hellman Chọn p = 23 , g = 5
                ,<br />
                Chọn số bí mật a = 6 ;
                <br />A = g<sup>a</sup> mod p = 5<sup>6</sup> mod 23 = 8
                <br />Gửi p , g , A cho server <br />Nhận được B: {{ B }}
                <br />s = {{ B }}<sup>6</sup> mod 23
              </p>
              <p style="font-size: 30px">
                Khóa chung s = <span style="font-weight: bold">{{ s }}</span>
              </p>
            </div>
            <p>Tạo chuỗi ngẫu nhiên s : {{ logTaoRandom }}</p>
            <p>Mã hóa : {{ logMaHoa }}</p>
          </div>
        </v-col>
      </v-row>
    </v-container>
    <v-container>
      <v-row>
        <v-col class="pl-10" cols="12" sm="6" md="8">
          <p v-if="show" style="font-size: 30px; letter-spacing: 5px">
            IDCZESPECIMEN&lt;&lt;VZOR&lt;&lt;&lt;&lt;&lt;&lt;&lt;&lt;&lt;&lt;&lt;&lt;&lt;&lt;&lt;&lt;
            <br />9900005164CZE6802295F1011027449&lt;&lt;&lt;9
          </p>
        </v-col>
      </v-row>
      <label for="avatar">Choose a profile picture:</label>
      <input
        type="file"
        id="avatar"
        name="avatar"
        accept="image/png, image/jpeg"
      />
      <v-btn @click="sharePrivateKey()" depressed color="error"> Scan </v-btn>
    </v-container>
  </v-app>
</template>

<script>
const axios = require("axios");
var CryptoJS = require("crypto-js");
//import Base64 from 'crypto-js/enc-base64';
export default {
  name: "App",
  components: {},
  data() {
    return {
      customer: {
        first_name: "",
        full_name: "",
        id_code: "",
        expireday: "",
        issued_place: "",
        nationality: "",
        reg_day: "",
        sex: "",
        link_avart: "",
      },
      show: false,
      logTaoRandom: "",
      logMaHoa: "",
      B: "0",
      s: "",
    };
  },
  methods: {
    async renderAPI() {
      var data = await this.callAPI();
      this.customer = data;
      const myArray = this.customer.full_name.split(" ");
      this.customer.first_name = myArray[0];
    },
    createKey() {
      this.logTaoRandom = "hvktmm";
      var ciphertext = CryptoJS.AES.encrypt(
        this.logTaoRandom,
        "secret key 123"
      ).toString();
      this.logMaHoa = ciphertext;
    },
    async sharePrivateKey() {
      this.show = true;
      var p = 23,
        g = 5,
        A = 8;
      const res = axios({
        method: "get",
        url: "http://localhost:5000/key/" + p + "/" + g + "/" + A,
      });
      const dataRes = await res;
      this.B = dataRes.data;
      /*
      Thỏa thuận khóa :
      p=23 , g=5
      chọn a = 6 -> Tính
      A = 5^6 mod 23
      A = 15.625 mod 23
      A = 8
      => Chuyển cho server 8
      + Nhận 19 từ server -> Tính s =  19^6 mod 23 = 2
      Khóa chia sẻ 2
      */
      this.s = 2;
      var T = CryptoJS.HmacSHA256(this.s, "secret").toString(CryptoJS.enc.Hex);
      const authentication = axios({
        method: "get",
        url: "http://localhost:5000/T/" + T,
      });
      const dataResponse = await authentication;
      var TR = CryptoJS.HmacSHA256("hvktmm", "secret").toString(
        CryptoJS.enc.Hex
      );
      if (dataResponse.data == TR) {
        this.renderAPI();
      }
      this.createKey();
    },
    makeid(length) {
      var result = "";
      var characters =
        "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
      var charactersLength = characters.length;
      for (var i = 0; i < length; i++) {
        result += characters.charAt(
          Math.floor(Math.random() * charactersLength)
        );
      }
      return result;
    },
    async callAPI() {
      const res = axios({
        method: "get",
        url: "http://localhost:5000/user/990000516/" + this.logMaHoa,
      });
      const dataRes = await res;
      return dataRes.data[0];
    },
  },
};
</script>
<style scoped>
.avatar {
  width: 200px;
}
.loader {
  border: 16px solid #f3f3f3;
  border-radius: 50%;
  border-top: 16px solid #3498db;
  width: 60px;
  height: 60px;
  -webkit-animation: spin 2s linear infinite; /* Safari */
  animation: spin 2s linear infinite;
}

/* Safari */
@-webkit-keyframes spin {
  0% {
    -webkit-transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
  }
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>