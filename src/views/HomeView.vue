<template>

  <body>

    <header>
      <img id="headerImg"
        src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcR3q0lX_-KpXS5pLcDqtdW8_zXJnKwZh_kNWA&usqp=CAU">
      <h1> Welcome to BurgerOnline</h1>
    </header>

    <main>
      <section id="selectBurger">
        <h2>Select Burger</h2>
        <p>This is where you execute burger selection</p>
        <div class="wrapper">
          <Burger v-for="burger in burgers" v-bind:burger="burger" v-bind:key="burger.name"
            v-on:orderedBurger="addToOrder($event)" />
        </div>
      </section>

      <section id="customerInfo">
        <h2>Customer Information</h2>



        <h3>Delivery Information</h3>


        <p>
          <label for="firstname">Full Name </label><br>
          <input type="text" id="First- and Last name" v-model="na" required="required"
            placeholder="First- and Last name">
        <p>{{ na }}</p>
        </p>

        <p>
          <label for="E-mail">E-mail </label><br>
          <input type="email" id="email" v-model="em" required="required" placeholder="E-mail address">
        <p>{{ em }}</p>
        </p>
       

        <div class="mapScroll">
          <div id="map" v-on:click="setLocation">
            
            <div id="dots">
            <div v-bind:style="{
              left: location.x + 'px',
              top: location.y + 'px'
            }">
              T
            </div>
          </div>
          </div>
        </div>
        <br>
        <label for="Gender">Gender</label><br>
        <input type="radio" id="male" v-model="gender" value="Male" required="required">
        <label for="Male">Male</label> <br>

        <input type="radio" id="Female" v-model="gender" value="Female" required="required">
        <label for="Female">Female</label> <br>

        <input type="radio" id="Undisclosed" v-model="gender" value="Undisclosed" required="required">
        <label for="Undisclosed">Undisclosed</label><br>

        <p>
          <label for="recipient">Payment options</label><br>
          <select id="recipient" v-model="pmt">
            <option value="Card">Credit card</option>
            <option value="Swish">Swish</option>
            <option value="Paypal">Paypal</option>
            <option value="Cash">Cash</option>
          </select>
        </p>

      </section>

      <button type="submitOrder" required v-on:click="submit">
        <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRMHn2kJfGL_po26BsKUhzyC20BTB8GzX8IAA&usqp=CAU"
          style="width: 50px">
        Place my order!
      </button>
    </main>

    <footer>
      <hr>
      &copy; 2022 Ydrewood Burgers Inc
    </footer>
  </body>


</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'

const socket = io();

//Object Constructor Function

import menu from '../assets/menu.json'

function MenuItem(burger, pic, kCal, glu, lac) {
  this.name = burger; // The *this* keyword refers to the object itself
  this.url = pic;
  this.calories = kCal;
  this.gluten = glu;
  this.lactose = lac;
}

// Objects are then instantiated using the *new* keyword
const burgers_list = [new MenuItem('Fire Burger', 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRR4AfmKtBcOqDJcCWWY3-qguPmlt0QBxQ2QQ&usqp=CAU', '750', 'Yes', 'Yes'), new MenuItem('Cheese Burger', 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQ36qM-nAlweinP0km3B5I4KQZNz2ant-WO7A&usqp=CAU', '600', 'Yes', 'Yes'), new MenuItem('Ydrewood Special', 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRg-7Bi7xnVDA6IQ-X9ncHs2aujJcRHbBcDmA&usqp=CAU', '550', 'Yes', 'Yes'), new MenuItem('Vegan Burger Special', 'https://biancazapatka.com/wp-content/uploads/2020/05/veganer-bohnen-burger.jpg', '430', 'No', 'No'), new MenuItem('Burger Surprise', 'https://media-cdn.tripadvisor.com/media/photo-s/1b/57/ab/58/sink-your-teeth-into.jpg', '550', 'No', 'Yes'), new MenuItem('Salmon Avocado Burger', 'https://www.seafoodexperts.com.au/wp-content/uploads/2018/01/SR-152-Avocado-salmon-salad-burger-CLEAN.jpg', '300', 'No', 'No')];
console.log(burgers_list); // Output: Maike


export default {
  name: 'HomeView',
  components: {
    Burger
  },

  data: function () {
    return {
      burgers: menu,
      na: '',
      em: '',
      st: '',
      ho: '',
      gender: '',
      pmt: 'Credit Card',
      orderedBurgers: {},
      location: {
        x: 0,
        y: 0
      }
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random() * 100000);
    },
    addOrder: function (event) {
      var offset = {
        x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top
      };

      this.location = {
        x: event.clientX - 10 - offset.x,
        y: event.clientY - 10 - offset.y

      };
      socket.emit("addOrder", {
        orderId: this.getOrderNumber(),
        details: {
          x: event.clientX - 10 - offset.x,
          y: event.clientY - 10 - offset.y
        },
        orderItems: ["Beans", "Curry"]
      }
      );
    },
    submit: function () {
      console.log(this.na, this.em, this.gender, this.pmt)
      console.log(this.orderedBurgers)
      
      socket.emit("addOrder", {
        orderId: this.getOrderNumber(),
        details: {
          x: this.location.x,
          y: this.location.y
        },
        orderItems: this.orderedBurgers,
        customerName: this.na,
        customerEmail: this.em,
        customerGender: this.gender,
        customerPayment: this.pmt
      }
      );
    },
    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
    },
    setLocation: function (event) {
      var offset = {
        x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top
      };

      this.location = {
        x: event.clientX - 10 - offset.x,
        y: event.clientY - 10 - offset.y

      };
    },

  }
}
</script>

<style>
@import 'https://fonts.googleapis.com/css?family=Pacifico|Dosis';


body {
  font-family: Arial, sans-serif;
  font-weight: normal;
  font-size: 100%;
}


section {
  margin: 2vw;
  padding: 2vh;
}

header {
  height: 20vw;
  margin: 2vw;
  overflow: hidden;
  font-size: 2vw;
}


#headerImg {
  opacity: 0.90;
  width: 92vw;
  height: auto;
  object-fit: cover;
  object-position: center 30%;
}


header>h1 {
  color: white;
  position: absolute;
  /* padding: 100px 100px;
  margin-top: -690px;
  margin-left: 290px;
  font-size: 80px; */
  padding-left: 22%;
  padding-top: 5%;
  margin-top: -50%;
}



.wrapper {
  display: grid;
  grid-gap: 10px;
  grid-template-columns: 33% 33% 33%;
  background-color: rgb(138, 129, 129);
  color: white;
  padding: 2vw;
  padding-right: 3vw;
}

.burger {
  background-color: rgb(97, 87, 87);
  color: black;
  border-radius: 20px;
  padding: 20px;
  font-size: 90%;
}

.ingredients {
  color: black;
}

#ingred {
  font-weight: bold;
  color: black;
}

#selectBurger {
  background-color: rgb(50, 40, 40);
  color: white;
  border: ridge burlywood;
}

button:hover {
  background-color: white;
  cursor: pointer;
}

#customerInfo {
  padding: 5px 10px;
  border: groove burlywood;
}

button {
  margin: 0px 20px 20px 20px;
  background-color: lightgray;
  color: black;
}

/* 
div {
  display: inline-block;
  margin: 15px;
  padding: 10px;
} */

#selectBurger img {
  opacity: 0.90;
  width: 90%;
  height: auto;
  overflow: hidden;
}


#map {
  width: 1920px;
  height: 1078px;
  background: url('../../public/img/polacks.jpg');
}

.mapScroll {
  width: 100%;
  height: 500px;
  overflow: scroll;
}


#dots {
    position: relative;
    margin: 0;
    padding: 0;
    background-repeat: no-repeat;
    width:1920px;
    height: 1078px;
    cursor: crosshair;
  }
  
  #dots div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }


</style>

