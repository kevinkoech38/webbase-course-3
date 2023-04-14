<template>
  <div id="app">
    <navbar :cartItemsCount="cartItemCount" @toggle-checkout="toggleCheckout" />
    <div class="container">
      <LessonList
        v-if="!displayForm"
        @add-to-cart="addLesson"
        :products="products"
      />
      <LessonCheckout
        v-if="displayForm"
        :cart="cart"
        @remove-from-cart="remove"
        @back-to-lessons="displayLessons"
      />
    </div>
  </div>
</template>

<script>
import LessonList from "./components/Lessons.vue";
import LessonCheckout from "./components/LessonCheckout.vue";
import navbar from "./components/Navbar.vue";
import { products } from "./products.js";

export default {
  name: "App",
  components: {
    LessonList,
    LessonCheckout,
    navbar,
  },
  data() {
    return {
      products: products,
      cart: [],
      displayForm: false,
    };
  },
  computed: {
    cartItemCount() {
      let count = 0;
      for (const item of this.cart) {
        count += item.quantity;
      }
      return count;
    },
  },
  methods: {
    addLesson(productId) {
      const product = this.products.find((p) => p.id === productId);
      if (product.availableInventory > 0) {
        const cartItem = this.cart.find((item) => item.id === productId);
        if (cartItem) {
          cartItem.quantity++;
        } else {
          this.cart.push({ ...product, quantity: 1 });
        }
        product.availableInventory--;
      }
    },
    remove(productId) {
      const index = this.cart.findIndex((item) => item.id === productId);
      if (index !== -1) {
        const cartItem = this.cart[index];
        cartItem.quantity--;
        if (cartItem.quantity <= 0) {
          this.cart.splice(index, 1);
        }
        const product = this.products.find((p) => p.id === productId);
        product.availableInventory++;
      }
    },
    toggleCheckout() {
      this.displayForm = !this.displayForm;
    },
    displayLessons() {
      this.displayForm = false;
    },
  },
};
</script>
<style>
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background-image: url("./assets/bg.jpg");
  color: #ffffff;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 15px;
}

button {
  cursor: pointer;
}

input,
select {
  display: block;
  width: 100%;
  padding: 6px 12px;
  margin-bottom: 10px;
  font-size: 14px;
  line-height: 1.42857143;
  color: #ffffff;
  background-color: #f21d99;
  background-image: none;
  border: 1px solid #592e55;
  border-radius: 4px;
}
</style>
