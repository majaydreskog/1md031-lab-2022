<template>

  <div class="burger">
    <h2> {{ burger.name }}</h2>
    <img v-bind:src="burger.url">
    <ul>
      <section class="ingredients">
        <li>{{ burger.calories }} kCal</li>
        <li v-if="burger.lactose" >Contains <span class="ingredients"> lactose </span> </li>
        <li v-if="burger.gluten">Contains <span class="ingredients"> gluten </span> </li>
        
      </section>
    </ul>
    <p>Amount:
      <button v-on:click="removeBurger" style="width: 25px"> - </button>
      {{ amountOrdered }}
      <button  v-on:click="addBurger" style="width: 25px"> + </button>
    </p>
  </div>

</template>
  
<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data: function () {
    return {
      amountOrdered: 0,
    }
  },
  methods: {
    addBurger: function () {
      this.amountOrdered += 1;
      this.$emit('orderedBurger', {
        name: this.burger.name,
        amount: this.amountOrdered
      }
      );
    },

    removeBurger: function () {
      if(amountOrdered>0){
        this.amountOrdered -= 1 ;
      }
      this.$emit('orderedBurger', {
        name: this.burger.name,
        amount: this.amountOrdered
      }
      );
    },



  }

}
</script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
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
</style>
  