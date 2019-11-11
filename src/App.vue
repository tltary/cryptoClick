<template>
  <section>
    <section class="menu">
      <div class="container">
        <div class="menu__block">
          <div class="row">
            <div class="col-12">
              <span class="menu__logo">
                Crypto Click
              </span>
            </div>
          </div>
        </div>
      </div>
    </section>
    <section class="mining">
      <div class="container">
        <div class="row">
          <div class="col-lg-6 col-md-6 col-sm-6 col-xs-12">
            <button v-on:click="miningClick" class="mining__btn">
              <img v-bind:src="require(`./assets/button.svg`)">
            </button>
          </div>
          <div class="col-lg-6 col-md-6 col-sm-6 col-xs-12">
            <div class="text__block">
              <p class="text__item">
                <span class="text__icon">
                  <img v-bind:src="require(`./assets/coin.svg`)">
                </span>
                Mined coin - {{crypto}}</p>
              <p class="text__item">
                <span class="text__icon">
                  <img v-bind:src="require(`./assets/coins.svg`)">
                </span>
                Your money - {{money}}</p>
              <p class="text__item">
                <span class="text__icon">
                  <img v-bind:src="require(`./assets/rates.svg`)">
                </span>
                Rates - {{currency}}
                <span class="text__carret" :class="[{'up' : currencyUp},{'down' : currencyDown}]">
                  <svg version="1.1" id="Capa_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
                     width="292.362px" height="292.361px" viewBox="0 0 292.362 292.361" style="enable-background:new 0 0 292.362 292.361;"
                     xml:space="preserve">
                    <path d="M286.935,197.287L159.028,69.381c-3.613-3.617-7.895-5.424-12.847-5.424s-9.233,1.807-12.85,5.424L5.424,197.287
                      C1.807,200.904,0,205.186,0,210.134s1.807,9.233,5.424,12.847c3.621,3.617,7.902,5.425,12.85,5.425h255.813
                      c4.949,0,9.233-1.808,12.848-5.425c3.613-3.613,5.427-7.898,5.427-12.847S290.548,200.904,286.935,197.287z"/>
                  </svg>
                </span>
              </p>
            </div>
            <div class="exchange__block">
              <button class="exchange__btn" v-on:click="exchangeModal">
                <span class="exchange__icon">
                  <img v-bind:src="require(`./assets/get-money.svg`)">
                </span> exchange
              </button>
            </div>
            <div v-if="hardware[0].count > 0 || hardware[1].count > 0 || hardware[2].count > 0" class="item__block">
              <p class="item__title">
                Your hardware
              </p>
              <div class="row">
                <div v-for="(item, key) in hardware" v-if="item.count > 0" class="col-lg-4 col-md-4 col-sm-6 col-xs-12">
                  <div class="item__hardware__block">
                    <span class="item__hardware__logo">
                      <img v-bind:src="require(`./assets/${item.img}.svg`)">
                    </span>
                    <p class="item__hardware__title">
                      {{item.name}}
                    </p>
                    <p class="item__hardware__count">
                      Count: {{item.count}}
                    </p>
                    <p class="item__hardware__mined">
                      Power:  {{item.power * item.count}}
                    </p>
                    <button class="btn btn-danger item__hardware__btn--one" v-on:click="sellOne(key)">
                      Sell one<br>{{item.price / 2}}
                    </button>
                    <button v-if="item.count > 1" class="btn btn-danger item__hardware__btn--all" v-on:click="sellAll(key)">
                      Sell all<br>{{(item.price / 2) * item.count}}
                    </button>
                  </div>
                </div>
              </div>
            </div>
            <div class="buy__block">
              <p class="buy__title">
                Shop
              </p>
              <div class="row">
                <div class="col-lg-4 col-md-4 col-sm-6 col-xs-12" v-for="(item, key) in hardware">
                  <button v-if="money >= item.price && item.count < 20" v-on:click="buyClick(key)" class="buy__btn">
                    <span class="buy__btn__logo">
                      <img v-bind:src="require(`./assets/${item.img}.svg`)">
                    </span>
                    <p class="buy__btn__title">Buy</p>
                    <p class="buy__btn__name">{{item.name}}</p>
                    <p class="buy__btn__power">Power: {{item.power}}</p>
                    <p class="buy__btn__price">Price:<br>{{item.price}} money</p>
                  </button>
                  <button v-else-if="item.count < 20" class="buy__btn notSell">
                    <span class="buy__btn__logo">
                      <img v-bind:src="require(`./assets/${item.img}.svg`)">
                    </span>
                    <p class="buy__btn__title">Not enough money</p>
                    <p class="buy__btn__name">{{item.name}}</p>
                    <p class="buy__btn__power">Power: {{item.power}}</p>
                    <p class="buy__btn__price">Price:<br>{{item.price}} money</p>
                  </button>
                  <button v-else class="buy__btn notSell">
                    <span class="buy__btn__logo">
                      <img v-bind:src="require(`./assets/${item.img}.svg`)">
                    </span>
                    <p class="buy__btn__title">You can not buy more than 20</p>
                    <p class="buy__btn__name">{{item.name}}</p>
                    <p class="buy__btn__power">Power: {{item.power}}</p>
                    <p class="buy__btn__price">Price:<br>{{item.price}} money</p>
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
    <b-modal id="exchange" centered>
      <div class="exchange__modal__block">
        <p class="exchange__modal__text">You earned {{crypto}} coin</p>
        <input class="exchange__modal__input" type="number" v-model="exchangeSum">
        <p class="exchange__modal__text">You give {{exchangeSum}} - You get {{exchangeSum * currency}}</p>
        <button class="btn btn-success exchange__modal__btn exchange__modal__btn--left" v-on:click="okModal">Exchange</button>
        <button class="btn btn-danger exchange__modal__btn exchange__modal__btn--right" v-on:click="cancelModal">Cancel</button>
      </div>
    </b-modal>
  </section>
</template>

<script>

import { secretKeyHardware, secretKeyCrypto, secretKeyCurrency, secretKeyMoney} from './tuple';

export default {
  name: 'app',
  data() {
    return {
      secretKeyHardware: secretKeyHardware,
      secretKeyCrypto: secretKeyCrypto,
      secretKeyCurrency: secretKeyCurrency,
      secretKeyMoney: secretKeyMoney,
      crypto: 0,
      currency: 10,
      money: 0,
      exchangeSum: 0,
      exchange: false,
      currencyUp: false,
      currencyDown: false,
      hardware: [
        {
          name: 'computer',
          count: 0,
          price: 500,
          power: 1,
          img: 'laptop',
        },
        {
          name: 'graphic',
          count: 0,
          price: 5000,
          power: 3,
          img: 'graphics-card',
        },
        {
          name: 'ASIC',
          count: 0,
          price: 10000,
          power: 5,
          img: 'server',
        },
      ]
    }
  },
  mounted() {
    this.initGame();
  },
  methods: {
    initGame() {
      if (localStorage.hardware) {
        let bytes = this.CryptoJS.AES.decrypt(localStorage.hardware, this.secretKeyHardware);
        let original = bytes.toString(this.CryptoJS.enc.Utf8);
        this.hardware = JSON.parse(original)
      } else {
        this.hardware = [{name: 'computer',count: 0,price: 500,power: 1,img: 'laptop',},{name: 'graphic',count: 0,price: 5000,power: 3,img: 'graphics-card',},{name: 'ASIC',count: 0,price: 10000,power: 5,img: 'server',}];
      }
      if (localStorage.crypto) {
        let bytes = this.CryptoJS.AES.decrypt(localStorage.crypto, this.secretKeyCrypto);
        this.crypto = parseInt(bytes.toString(this.CryptoJS.enc.Utf8));
      } else {
        this.crypto = 0;
      }
      if (localStorage.currency) {
        let bytes = this.CryptoJS.AES.decrypt(localStorage.currency, this.secretKeyCurrency);
        this.currency = parseInt(bytes.toString(this.CryptoJS.enc.Utf8));
      } else {
        this.currency = 10;
      }
      if (localStorage.money) {
        let bytes = this.CryptoJS.AES.decrypt(localStorage.money, this.secretKeyMoney);
        this.money = parseInt(bytes.toString(this.CryptoJS.enc.Utf8));
      } else {
        this.money = 0;
      }
      this.gameWatch()
    },
    miningClick() {
      this.crypto = this.crypto + 1;
    },
    random(min, max) {
      return parseInt(min + Math.random() * (max - min));
    },
    sellOne(item) {
      this.money = this.money + (this.hardware[item].price / 2);
      this.hardware[item].count = this.hardware[item].count - 1;
      localStorage.hardware = this.CryptoJS.AES.encrypt(JSON.stringify(this.hardware), this.secretKeyHardware).toString();
    },
    sellAll(item) {
      this.money = this.money + ((this.hardware[item].price / 2) * this.hardware[item].count);
      this.hardware[item].count = 0;
      localStorage.hardware = this.CryptoJS.AES.encrypt(JSON.stringify(this.hardware), this.secretKeyHardware).toString();
    },
    buyClick(item) {
      if (this.money >= this.hardware[item].price) {
        this.money = this.money - this.hardware[item].price;
        this.hardware[item].count = this.hardware[item].count + 1;
        localStorage.hardware = this.CryptoJS.AES.encrypt(JSON.stringify(this.hardware), this.secretKeyHardware).toString();
      }
    },
    gameWatch() {
      let tick = 0;
      setInterval(() => {
        if (!this.exchange) {
          for (let key in this.hardware) {
            if (this.hardware[key].count > 0) {
              this.minedHardware()  
              break;
            }
          }
          if (tick < 10) {
            tick = tick + 1;
          } else {
            this.changeCurrency();
            tick = 0;
          }
        }
      }, 2500);
    },
    exchangeModal() {
      this.exchangeSum = 0;
      this.$bvModal.show('exchange')
      this.exchange = true;
    },
    minedHardware() {
      let profit = 0;
      this.hardware.forEach((i) => {
        if (i.count > 0) {
          profit = profit + (i.count * i.power)
        }
      })
      this.crypto = this.crypto + profit;
    },
    changeCurrency() {
      if (this.random(1, 10) < 5) {
        this.currency = parseInt(this.currency - (this.random(1, 1000) / 100));
        this.currencyUp = false;
        this.currencyDown = true;
        if (this.currency <= 0) {
          this.currency = 1;
        }
      } else {
        this.currency = parseInt(this.currency + (this.random(1, 1000) / 100));
        this.currencyUp = true;
        this.currencyDown = false;
      }
    },
    okModal() {
      this.crypto = parseInt(this.crypto) - parseInt(this.exchangeSum);
      this.money = this.money + parseInt(this.exchangeSum * this.currency);
      this.$bvModal.hide('exchange')
      this.exchange = false;
    },
    cancelModal() {
      this.$bvModal.hide('exchange')
      this.exchange = false;
    },
  },
  watch: {
    crypto(newVal) {
      localStorage.crypto = this.CryptoJS.AES.encrypt(newVal.toString(), this.secretKeyCrypto).toString();
    },
    currency(newVal) {
      localStorage.currency = this.CryptoJS.AES.encrypt(newVal.toString(), this.secretKeyCurrency).toString();
    },
    money(newVal) {
      localStorage.money = this.CryptoJS.AES.encrypt(newVal.toString(), this.secretKeyMoney).toString();
    },
    exchangeSum(newVal) {
      if (newVal > this.crypto) {
        this.exchangeSum = this.crypto;
      } else if (newVal < 0) {
        this.exchangeSum = 0;
      }
    }
  }
}
</script>

<style lang="scss">
  @import url('https://fonts.googleapis.com/css?family=Press+Start+2P&subset=cyrillic');

  body {
    font-family: 'Press Start 2P', cursive !important;
    background-color: #1f5af6 !important;
    color: #fff !important;
  }

  .modal-footer, .modal-header {
    display: none !important;
  }

  .menu {
    &__block {
      padding: 15px 0px;
    }
    &__logo {
      font-size: 16px;
      font-weight: bold;
    }
  }

  .mining {
    padding: 40px 0px;
    &__btn {
      border-radius: 50px;
      border: 1px #3e3e3e solid;
      background-color: transparent;
      outline: none !important;
      padding: 0px;
      background-color: #fff;
      img {
        width: 100%;
      }
      &:active {
        img {
          transform: scale(-0.9);
        }
      }
    }
  }

  .exchange {
    &__block {
      margin-bottom: 20px;
    }
    &__btn {
      padding: 10px 25px;
      background-color: #e3e3e3;
      border: none;
      outline: none !important;
    }
    &__icon {
      width: 30px;
      display: inline-block;
      svg {
        width: 100%;
        height: auto;
      }
    }
    &__modal {
      &__block {
        padding: 15px;
        text-align: center;
      }
      &__text {
        color: #3e3e3e
      }
      &__input {
        padding: 15px 10px;
        margin-bottom: 15px;
        width: 100%;
        border: 2px #e3e3e3 solid;
      }
      &__btn {
        &--left {
          margin-right: 15px;
        }
        &--right {
          margin-left: 15px;
        }
      }
    }
  }

  .item {
    &__block {
      margin-bottom: 20px;
    }
    &__title {
      font-size: 16px;
      margin-top: 0px;
      margin-bottom: 10px;
    }
    &__hardware {
      &__btn {
        &--one {
          width: 100% !important;
          border-radius: 0px !important;
        }
        &--all {
          width: 100% !important;
          border-top-right-radius: 0px !important;
          border-top-left-radius: 0px !important;
        }
      }
      &__block {
        padding: 10px 10px 0px 10px;
        border: 1px #e3e3e3 solid;
        border-radius: 5px;
        margin-bottom: 30px;
      }
      &__logo {
        display: inline-block;
        width: 100%;
        margin-bottom: 15px;
        img {
          width: 100%;
          height: auto;
        }
      }
      &__title {
        font-size: 14px;
        margin-bottom: 10px;
        margin-top: 0px
      }
      &__count {
        font-size: 12px;
        margin-bottom: 10px;
        margin-top: 0px
      }
      &__mined {
        font-size: 12px;
        margin-bottom: 10px;
        margin-top: 0px
      }
    }
  }

  .buy {
    &__block {

    }
    &__title {
      font-size: 16px;
      margin-top: 0px;
      margin-bottom: 10px;
    }
    &__btn {
      padding: 10px;
      border-radius: 5px;
      outline: none !important;
      background: transparent;
      box-shadow: none;
      border: 1px #e3e3e3 solid;
      margin-bottom: 30px;
      transition: color .3s ease-in-out, background-color.3s ease-in-out;
      &.notSell {
        background-color: #3e3e3e;
        color: #fff;
        &:hover {
          background-color: #3e3e3e;
          color: #fff;
        }
        &:active {
          img {
            transform: scale(1);
          }
        }
      }
      &:hover {
        color: #fff;
        background-color: #28a745;
      }
      &__logo {
        display: inline-block;
        width: 100%;
        margin-bottom: 15px;
        img {
          width: 100%;
          height: auto;
        }
      }
      &__title {
        margin-top: 0px;
        font-size: 14px;
        margin-bottom: 10px;
      }
      &__name {
        margin-top: 0px;
        font-size: 14px;
        margin-bottom: 10px;
      }
      &__power {
        margin-top: 0px;
        font-size: 12px;
        margin-bottom: 10px;
      }
      &__price {
        margin-top: 0px;
        font-size: 12px;
      }
    }
  }

  .text {
    &__icon {
      width: 20px;
      display: inline-block;
      img {
        width: 100%;
        height: auto;
      }
    }
    &__carret {
      width: 22px;
      display: none;
      svg {
        width: 100%;
        height: auto;
      }
      &.up {
        display: inline-block;
        position: absolute;
        margin: -3px 0px 0px 10px;
        svg {
          path {
            fill: #28a745;
          }
        }
      }
      &.down {
        display: inline-block;
        position: absolute;
        margin: 0px 0px 0px 10px;
        transform: rotate(180deg);
        svg {
          path {
            fill: #dc3545;
          }
        }
      }
    }
    &__block {
      margin-bottom: 20px;
    }
    &__item {
      margin-bottom: 10px;
      margin-top: 0px;
      &:last-child {
        margin-bottom: 0px;
      }
    }
  }

  @media screen and (max-width: 760px) {
    .mining {
      &__btn {
        border-radius: 20px;
        margin-bottom: 30px;
      }
    }
    .exchange {
      &__modal {
        &__btn {
          width: 100%;
          &--left {
            margin: 0px 0px 15px 0px;
          }
          &--right {
            margin: 0px 0px 0px 0px;
          }
        }
      }
    }
  }
</style>
