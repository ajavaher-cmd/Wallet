<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
     
    </v-app-bar>

    <v-content>
    <v-container>
      <v-layout row>
        <v-flex lg4 hidden-sm-and-down>
        <v-sheet width='350px' class="mt-5"> 
        
              <v-card>
                <v-card-title>
                  Estimated Balance:
                </v-card-title>
                <v-card-subtitle>{{balance}} USD</v-card-subtitle>
              </v-card>
            
          <v-card v-for="item in wallet" :key="item.name">
            <v-card-title @click="adjust(item.name)">
               <v-img :src='`${item.src}`' max-height='20' max-width='20' class="mr-2" ></v-img>
                {{item.name}}
                <v-spacer></v-spacer>
                <div>
                  <v-layout column>
                  <span class="text-right">{{item.amount}} {{item.id}}</span>
                  <span class="caption text-right">{{item.amount*item.exchangeRate}} USD</span>
                  </v-layout>
                </div>
            </v-card-title>
              <div v-show="item.show" >
                <div class="text-center">
                  <qrcode-vue :value="item.address" :size="150" level="H"></qrcode-vue>
                </div>
                <v-card-text class="text-center">{{item.address}}</v-card-text>
                <v-card-actions  >
                  <v-container>
                  <v-layout justify='space-around'>
                    <v-flex><v-btn @click='tab=2' class="blue lighten-5">send</v-btn></v-flex><v-flex><v-btn @click='tab=3' class="blue lighten-5">buy</v-btn></v-flex><v-flex><v-btn @click='tab=4' class="blue lighten-5">exchange</v-btn></v-flex>
                  </v-layout>
                  </v-container>
                </v-card-actions>
              </div>
          </v-card>
        </v-sheet>
        </v-flex>
    
  <v-flex xs12 md8 lg8>
  <v-sheet class="mt-5">      
  <v-card width='800px'>
    
    <v-tabs vertical v-model="tab">
      <v-tab>
        History
      </v-tab>
      <v-tab active-class>
      Recieve
      </v-tab>
      <v-tab>
        Send
      </v-tab>
      <v-tab>
        Buy
      </v-tab>
      <v-tab>
        Exchange
      </v-tab>

      <v-tab-item>
        <v-card flat>
          <v-card-text>
            <p>
              Sed aliquam ultrices mauris. Donec posuere vulputate arcu. Morbi ac felis. Etiam feugiat lorem non metus. Sed a libero.
            </p>
          </v-card-text>
        </v-card>
      </v-tab-item>
      <v-tab-item>
        <v-card flat>
          <p class="blue--text mt-5 ml-5">Your wallet:</p>
          <v-autocomplete class="mx-5" :items="wallet" item-text="name" label="currency" v-model='choice'></v-autocomplete>
          <v-card-text>
            <p class="blue--text">Information:</p>
            <div>
                  <qrcode-vue :value="wallet[number].address" :size="150" level="H"></qrcode-vue>
                </div>
            <p>wallet address</p>
            <p>{{wallet[0].address}}</p>
          </v-card-text>
          <v-card-actions>
            <v-btn class="blue lighten-5 ml-16">Send</v-btn><v-btn class="blue lighten-5 ml-10">Exchange</v-btn>
          </v-card-actions>
        </v-card>
        <v-divider></v-divider>
        <p class="blue--text mt-5">{{wallet[number].name}} price:</p>
        <v-btn @click="monthChart=true,weekChart=false" class="blue lighten-5 ml-16 mb-3">Month</v-btn>
        <v-btn @click="weekChart=true,monthChart=false" class="blue lighten-5 ml-4 mb-3">Week</v-btn>
        <area-chart v-show="monthChart" :data="{'2017-01-01 00:00:00 -0800': 2, '2017-01-01 00:01:00 -0800': 5}"></area-chart>
        <area-chart v-show="weekChart" :data="{'2017-01-01 00:00:00 -0800': 5, '2017-01-01 00:01:00 -0800': 2}"></area-chart>
      </v-tab-item>  
      <v-tab-item>
        <v-card flat>
          <p class="blue--text mt-5 mx-5">From:</p>
          <v-autocomplete class="mx-5" :items="wallet" item-text="name" label="currency" v-model="choice">
          </v-autocomplete>
          <p class="blue--text mt-5 ml-5">To:</p>
          
         <v-text-field v-model="result">
           <template v-slot:append>            
            <v-btn @click="qrcodeShow=!qrcodeShow"><v-icon>mdi-qrcode-scan</v-icon></v-btn>
            <qrcode-stream v-show="qrcodeShow" @decode="onDecode" @init="onInit" />            
           </template>           
         </v-text-field>
        
          <p class="blue--text mt-5 ml-5">Amount</p>
          <v-text-field suffix="USD" class="mx-5"></v-text-field>
          <v-card-text>
          <p>Available amount: {{balance}}USD</p>
          </v-card-text>
          <v-card-actions>
            <v-btn class="blue lighten-5 ml-16">Next</v-btn>
          </v-card-actions>
        </v-card>
      </v-tab-item>
      <v-tab-item>
        <v-card flat>
          <p class="blue--text mt-5 ml-5">Amount</p>
          <v-text-field class="mx-5" suffix="USD"></v-text-field>
          <p class="blue--text mt-5 ml-5">Your wallet:</p>
          <v-autocomplete class="mx-5" :items="wallet" item-text="name" label="currency" v-model="choice" ></v-autocomplete>
          <v-card-text>
           <p>Min amount: 50USD</p>
           <p>Max amount: 2500USD</p>
           <p>Will Recieve:{{exchangeRate[2]}}</p>
          </v-card-text>
          <v-card-actions>
            <v-btn class="blue lighten-5 ml-16">Buy</v-btn>
          </v-card-actions>
        </v-card>
      </v-tab-item>
      <v-tab-item>
        <v-card flat>
          <p class="blue--text mt-5 mx-5">From:</p>
          <v-autocomplete class="mx-5" :items="wallet" item-text="name" label="currency" v-model="choice">
          </v-autocomplete>
          <p class="blue--text mt-5 ml-5">To:</p>
          <v-autocomplete class="mx-5" :items="wallet" item-text="name" label="currency" v-model="choice">
          </v-autocomplete>
          <p class="blue--text mt-5 ml-5">Amount</p>
          <v-text-field class="mx-5" suffix='USD' color='black'></v-text-field>
          <v-card-text>
            <p>Available: {{wallet[1].amount}}</p>
           <p>Mix amount: 2500USD</p>
           <p>Will Recieve:{{exchangeRate[1]*wallet[1].amount}}</p>
          </v-card-text>
          <v-card-actions>
            <v-btn class="blue lighten-5 ml-16">Buy</v-btn>
          </v-card-actions>
        </v-card>
      </v-tab-item>
    </v-tabs>
  </v-card>
  </v-sheet>
  </v-flex>
  </v-layout>
  </v-container>


    </v-content>
  </v-app>
</template>

<script>
import QrcodeVue from 'qrcode.vue'

export default {
  name: 'App',
components: {
      QrcodeVue,
    },
  
  data: () => ({
    total:0,
    number:0,
    choice:'',
    weekChart: true,
    monthChart: false,
    tab:1,
    pics: ['./assets/ion.png'],
    result: '',
    error: '',
    qrcodeShow:false,
    wallet: [
      {name:'bitcoin',id:'btc',amount:0.1, src: '/ion.png',show:false,exchangeRate:919,address:'tyui7ururururururururururururururururnchrncuruudhghjuu'},
      {name:'ethereum',id:'eth',amount:1, src: '/btc.png',show:false,exchangeRate:68,address:'tyui7udhghjuu'},
      {name:'dash',id:'dash',amount:2, src: '/btx.png',show:false,exchangeRate:233,address:'tyui7udhghjuu'},
      {name:'litcoin',id:'ltc',amount:5, src: '/burst.png',show:false,exchangeRate:42,address:'tyui7udhghjuu'},
      {name:'cardano',id:'ada',amount:1, src: '/call.png',show:false,exchangeRate:0.1,address:'tyui7udhghjuu'},
    ],
    exchangeRate: [919,68,233,42,0.1]
  }),
  methods: {
      adjust:function(obj) {
      for(let index = 0; index < this.wallet.length; index++) {
        if (this.wallet[index].name === obj) {
          this.wallet[index].show=!this.wallet[index].show
          this.choice=this.wallet[index].name
          this.number=index
        }
        else {this.wallet[index].show=false}
      }
       console.log(this.choice)
    },
    onDecode (result) {
      this.result = result
    },

    async onInit (promise) {
      try {
        await promise
      } catch (error) {
        if (error.name === 'NotAllowedError') {
          this.error = "ERROR: you need to grant camera access permisson"
        } else if (error.name === 'NotFoundError') {
          this.error = "ERROR: no camera on this device"
        } else if (error.name === 'NotSupportedError') {
          this.error = "ERROR: secure context required (HTTPS, localhost)"
        } else if (error.name === 'NotReadableError') {
          this.error = "ERROR: is the camera already in use?"
        } else if (error.name === 'OverconstrainedError') {
          this.error = "ERROR: installed cameras are not suitable"
        } else if (error.name === 'StreamApiNotSupportedError') {
          this.error = "ERROR: Stream API is not supported in this browser"
        }
      }
    }
  },

  computed: {
    balance:function() {
      
        let final=0;
        for(let index = 0; index < this.wallet.length; index++) {
         final+= this.wallet[index].amount*this.exchangeRate[index]  
        
      } 
      return final
    }
   
  }
 

};
</script>
