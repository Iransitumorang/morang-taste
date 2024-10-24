<template>
  <v-app id="inspire" :style="{ background: 'linear-gradient(to bottom, #ff7e5f, #feb47b)' }">
    <SideBare />
    <v-main>
      <v-container>
        <v-row>
          <v-col cols="12" sm="8">
            <v-text-field
              density="compact"
              placeholder="Search restaurant, Food, Cuisine or a Dish"
              append-inner-icon="mdi-magnify"
              variant="outlined"
              rounded
              class="text-white mx-8 placeholder"
              v-model="searchQuery" 
              @input="filterItems"
              style="color: white;" 
            ></v-text-field>

            <div v-if="(filteredFoods.length || filteredDishes.length) && searchQuery" class="d-flex justify-space-evenly mt-4" color="transparent">
              <div v-for="(food, i) in filteredFoods" :key="i" class="text-center">
                <v-avatar color="#424242" size="70">
                  <v-img :src="food.image" height="50"></v-img>
                </v-avatar>
                <div class="text-yellow">{{ food.name }}</div>
                <div class="text-white">{{ food.price }}</div>
                <v-btn @click="addToOrder(food)" color="orange" class="mt-2">Buy</v-btn>
              </div>
              <div v-for="(dishe, i) in filteredDishes" :key="i" class="text-center">
                <v-avatar color="#424242" size="70">
                  <v-img :src="dishe.image" height="50"></v-img>
                </v-avatar>
                <div class="text-yellow">{{ dishe.name }}</div>
                <div class="text-white">{{ dishe.money }}</div>
                <v-btn @click="addToOrder(dishe)" color="orange" class="mt-2">Buy</v-btn>
              </div>
            </div>

            <div class="d-flex justify-space-evenly my-5">
              <v-btn @click="surpriseMe" color="red" class="white--text rounded-lg elevation-2">Surprise me!</v-btn>
              <v-btn @click="filterByCategory('Dessert')" class="white--text rounded-lg elevation-2">Dessert</v-btn>
              <v-btn @click="filterByCategory('Italian food')" class="white--text rounded-lg elevation-2">Italian food</v-btn>
              <v-btn @click="filterByCategory('Fast food')" class="white--text rounded-lg elevation-2">Fast food</v-btn>
              <v-btn @click="filterByCategory('Asian food')" class="white--text rounded-lg elevation-2">Asian food</v-btn>
            </div>

            <v-toolbar color="transparent" class="pr-1 mt-2">
              <v-toolbar-title class="text-white">Categories</v-toolbar-title>
              <v-spacer></v-spacer>
              <span @click="showModal = true" class="text-caption text-white">View all</span>
              <v-btn density="compact" icon="mdi mdi-chevron-right-box" color="grey" @click="showModal = true"></v-btn>
            </v-toolbar>
            <h6 class="text-white ml-4 mt-n4">
              <span class="text-red">10+ new </span> Categories added this week
            </h6>

            <div class="d-flex justify-space-evenly mt-4" color="transparent">
              <div v-for="(food, i) in filteredFoods" :key="i" class="text-center">
                <v-avatar color="#424242" size="70">
                  <v-img :src="food.image" height="50"></v-img>
                </v-avatar>
                <div class="text-white">{{ food.name }}</div>
                <div class="text-white">{{ food.price }}</div>
                <v-btn @click="addToOrder(food)" color="orange" class="mt-2">Buy</v-btn>
              </div>
            </div>

            <v-dialog v-model="showModal" max-width="800px">
              <v-card class="bg-grey-900">
                <v-card-title class="text-h6">All Categories</v-card-title>
                <v-card-text>
                  <div class="d-flex flex-wrap justify-space-between">
                    <div v-for="(food, i) in filteredFoods" :key="i" class="text-center" style="margin: 10px; width: 120px; background-color: #424242; padding: 10px; border-radius: 8px;">
                      <v-avatar color="#424242" size="70">
                        <v-img :src="food.image" height="50"></v-img>
                      </v-avatar>
                      <div class="text-white">{{ food.name }}</div>
                      <div class="text-white">{{ food.price }}</div>
                      <v-btn @click="addToOrder(food)" color="orange" class="mt-2">Buy</v-btn>
                    </div>
                  </div>
                </v-card-text>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn text @click="showModal = false">Close</v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>

            <v-toolbar color="transparent" class="pr-1 my-2">
              <v-toolbar-title class="text-white">Popular Dishes</v-toolbar-title>
              <v-spacer></v-spacer>
              <span class="text-caption text-white" @click="showMoreDishes">View More</span>
              <v-btn density="compact" icon="mdi mdi-chevron-right-box" color="grey" @click="showMoreDishes"></v-btn>
            </v-toolbar>
            <h6 class="text-white ml-4 mt-n4">
              <span class="text-red">20+ new </span> dishes added this week
            </h6>

            <v-carousel hide-delimiter :cycle="!carouselPaused" :interval="3000" @mouseover="pauseCarousel" @mouseleave="resumeCarousel">
              <v-carousel-item v-for="(dishe, i) in filteredDishes" :key="i" :src="dishe.image">
                <v-card class="mt-n10" width="160" style="background: linear-gradient(to bottom, #feb47b, #ff7e5f);">
                  <v-card-item class="text-center">
                    <v-card-title class="mt-10">{{ dishe.name }}</v-card-title>
                    <v-card-subtitle>
                      <span class="me-1">Starting From</span>
                      <span>{{ dishe.money }}</span>
                    </v-card-subtitle>
                    <v-card-title>{{ dishe.money }}</v-card-title>
                    <v-btn @click="addToOrder(dishe)" color="orange">Buy</v-btn>
                  </v-card-item>
                  <v-card-text>
                    <v-row align="center" class="mx-0">
                      <v-icon color="yellow" icon="mdi mdi-star" size="small"></v-icon>
                      <div class="text-grey ms-4">
                        <h6>{{ dishe.star }}</h6>
                      </div>
                      <v-spacer></v-spacer>
                      <div class="text-grey ms-4">
                        1360
                        <h5>Total Sale</h5>
                      </div>
                    </v-row>
                  </v-card-text>
                </v-card>
              </v-carousel-item>
            </v-carousel>

            <v-toolbar color="transparent" class="pr-1 mt-5">
              <v-toolbar-title class="text-white">Order Reports</v-toolbar-title>
              <v-spacer></v-spacer>
              <span class="text-caption text-white">View all</span>
              <v-btn density="compact" icon="mdi mdi-chevron-right-box" color="grey"></v-btn>
            </v-toolbar>
            <h6 class="text-white ml-4 mt-n4">
              <span class="text-red">Wow 100+ new </span> Order got this week
            </h6>
            <v-card class="rounded-xl ma-2 pa-1" style="background: linear-gradient(to bottom, #feb47b, #ff7e5f);">
              <v-row>
                <v-col cols="12" sm="1"> </v-col>
                <v-col cols="12" sm="2" class="text-center"><span class="text-caption">Customer</span></v-col>
                <v-col cols="12" sm="2" class="text-center"><span class="text-caption">Order number</span></v-col>
                <v-col cols="12" sm="2" class="text-center"><span class="text-caption">address</span></v-col>
                <v-col cols="12" sm="2" class="text-center"><span class="text-caption">Amount</span></v-col>
                <v-col cols="12" sm="2" class="text-center"><span class="text-caption">Status</span></v-col>
                <v-col cols="12" sm="1" class="text-center"></v-col>
              </v-row>
            </v-card>
            <v-row>
              <v-col cols="12" sm="3"><v-avatar> <v-img src="20.jpg" alt="John"></v-img> </v-avatar><span class="text-caption text-white ml-1">Jenetta Sinaga</span></v-col>
              <v-col cols="12" sm="2" class="text-center mt-2"><span class="text-caption text-white">086537867736</span></v-col>
              <v-col cols="12" sm="2" class="text-center mt-2"><span class="text-caption text-white">Kuningan Barat</span></v-col>
              <v-col cols="12" sm="2" class="text-center mt-2"><span class="text-caption text-white">$120.45</span></v-col>
              <v-col cols="12" sm="2" class="text-center"><v-chip variant="flat" color="green"> Completed </v-chip></v-col>
              <v-col cols="12" sm="1" class="text-center"></v-col>
            </v-row>
            <v-row class="mt-n5">
              <v-col cols="12" sm="3"><v-avatar> <v-img src="30.jpg" alt="John"></v-img> </v-avatar><span class="text-caption text-white ml-1">Peter Situmorang</span></v-col>
              <v-col cols="12" sm="2" class="text-center mt-2"><span class="text-caption text-white">089787686556</span></v-col>
              <v-col cols="12" sm="2" class="text-center mt-2"><span class="text-caption text-white">Pancoran tugu</span></v-col>
              <v-col cols="12" sm="2" class="text-center mt-2"><span class="text-caption text-white">$140.45</span></v-col>
              <v-col cols="12" sm="2" class="text-center"><v-chip variant="flat" color="orange"> Pending </v-chip></v-col>
              <v-col cols="12" sm="1" class="text-center"></v-col>
            </v-row>
          </v-col>
          <v-col cols="12" sm="4">
            <v-card class="rounded-xl" style="background: linear-gradient(to bottom, #feb47b, #ff7e5f);" height="850">
              <v-card class="mx-2 mt-4 rounded-lg" max-width="344" style="background: linear-gradient(to bottom, #ff7e5f, #feb47b);">
                <v-card-item title="Iran Situmorang">
                  <template v-slot:subtitle>
                    <v-icon icon="mdi mdi-map-marker" size="18" class="me-1 pb-1"></v-icon>
                    Kuningan Barat, Jakarta Selatan
                  </template>
                </v-card-item>
                <v-card-text class="mt-n3">
                  <v-icon size="16" icon="mdi mdi-clock-outline" class="mr-2"></v-icon>20 min</v-card-text>
              </v-card>
              <v-toolbar color="transparent" class="pr-1 mt-n2">
                <v-btn icon class="hidden-xs-only">
                  <v-icon>mdi mdi-cart</v-icon>
                  <span v-if="orderedDishes.length > 0" class="text-caption" style="color: red; position: absolute; top: 0; right: 0; font-size: 0.75rem;">{{ orderedDishes.length }}</span>
                </v-btn>
                <v-toolbar-title class="text-white">My Order</v-toolbar-title>
                <v-spacer></v-spacer>
                <span class="text-caption text-white me-2">Order ID: #1099</span>
              </v-toolbar>
              <v-card class="ma-2 mt-n2" color="transparent" flat>
                <v-card-text class="ml-14 mt-n2" style="max-height: 350px; overflow-y: auto; margin-bottom: 20px;">
                  <div v-if="orderedDishes.length === 0" style="background-color: #424242; color: white; padding: 20px; border-radius: 8px; text-align: center; animation: fadeIn 1s;">
                    <div class="no-order-message">Tidak ada pesanan :(</div>
                    <div class="order-prompt">Ayo, pesan makanan favoritmu sekarang!</div>
                  </div>
                  <div v-else>
                    <div v-for="(item, index) in orderedDishes" :key="index" class="d-flex align-center mb-2">
                      <v-avatar class="mr-2" size="50">
                        <v-img :src="item.image" height="50" width="50" contain></v-img>
                      </v-avatar>
                      <div class="flex-grow-1">
                        <div class="text-white font-weight-bold text-h6">{{ item.name }} - {{ item.price }}</div>
                        <div class="d-flex align-center">
                          <v-icon @click="decreaseQuantity(index)" class="mr-2" x-small>mdi mdi-minus-circle-outline</v-icon>
                          <div class="text-white">{{ item.quantity }}</div>
                          <v-icon @click="increaseQuantity(index)" class="ml-2" x-small>mdi mdi-plus-circle-outline</v-icon>
                          <v-icon @click="() => confirmDelete(index)" class="ml-2" x-small>mdi mdi-delete</v-icon>
                        </div>
                      </div>
                    </div>
                  </div>
                </v-card-text>
              </v-card>
              <v-divider inset class="mt-n2"></v-divider>

              <v-card class="rounded-xl ma-2 pa-1 mt-n2" variant="" elevation="16">
                <v-chip variant="text" class="mRight"> Promotion Code </v-chip>
                <v-chip variant="flat" color="orange"> Purchase </v-chip>
              </v-card>
              <v-card color="transparent" class="ma-2 mt-5" flat style="max-height: 150px;">
                <v-list density="comfortable" class="text-white">
                  <v-list-item title="Sub Total">
                    <template v-slot:append>
                      <v-btn variant="text">{{ calculateSubTotal }}</v-btn>
                    </template>
                  </v-list-item>

                  <v-list-item title="Delivery Charge">
                    <template v-slot:append>
                      <v-btn variant="text">{{ orderedDishes.length === 0 ? '$0' : '$10' }}</v-btn>
                    </template>
                  </v-list-item>
                  <v-divider inset></v-divider>
                  <v-list-item title="TOTAL">
                    <template v-slot:append>
                      <v-btn variant="text">{{ orderedDishes.length === 0 ? '$0.00' : calculateTotal }}</v-btn>
                    </template>
                  </v-list-item>
                </v-list>
              </v-card>
              <v-chip 
                variant="flat" 
                color="black" 
                class="px-10 mLeft" 
                style="margin-top: 20px;" 
                @click="submitOrder"
                :disabled="orderedDishes.length === 0" 
                :class="{ 'hover-effect': orderedDishes.length > 0 }"
              >
                Submit Order
              </v-chip>

              <v-dialog v-model="showSubmitModal" max-width="400px" class="confirm-order-dialog" style="justify-content: center; align-items: center;">
                <v-card>
                  <v-card-title class="text-h6">Confirm Order</v-card-title>
                  <v-card-text>
                    <div v-for="(item, index) in orderedDishes" :key="index" class="d-flex justify-space-between item-row">
                      <span>{{ item.name }} - {{ item.price }} (x{{ item.quantity }})</span>
                    </div>
                    <div class="mt-2 total-price">
                      <strong>Total: ${{ calculateTotal }}</strong>
                    </div>
                  </v-card-text>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn text @click="showSubmitModal = false" class="hover-button">Batal</v-btn>
                    <v-btn color="orange" @click="confirmOrder" class="hover-button">Kirim</v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>

    <v-dialog v-model="showMoreModal" max-width="800px">
      <v-card>
        <v-card-title class="text-h6">More Popular Dishes</v-card-title>
        <v-card-text>
          <div class="d-flex flex-wrap" style="background-color: #424242; padding: 20px; border-radius: 8px;">
            <div v-for="(dish, i) in moreDishes" :key="i" class="text-center" style="margin: 10px; width: calc(25% - 20px);">
              <v-avatar color="#605850" size="70">
                <v-img :src="dish.image" height="50"></v-img>
              </v-avatar>
              <div class="text-white">{{ dish.name }}</div>
              <div class="text-white">{{ dish.money }}</div>
              <v-btn @click="addToOrder(dish)" color="primary" class="mt-2">Buy</v-btn>
            </div>
          </div>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn text @click="showMoreModal = false">Close</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="showPopup" max-width="400px" class="popup-dialog">
      <v-card>
        <v-card-title class="text-h6">{{ popupTitle }}</v-card-title>
        <v-card-text>
          <span v-if="popupTitle === 'Pesanan Dikirim!'">{{ popupMessage }}</span>
          <span v-else-if="popupTitle === 'Dihapus!'">{{ popupMessage }}</span>
          <span v-else> Tidak ada pesanan. 😢 </span>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn text @click="showPopup = false">Close</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <div v-if="orderedDishes.length === 0" class="no-order-message">
      Tidak ada pesanan. 😢
    </div>
  </v-app>
</template>

<script setup>
import { ref, computed } from 'vue';
import SideBare from "@/components/SideBar.vue";
import Swal from 'sweetalert2'; 

const searchQuery = ref('');
const filteredFoods = ref([]);
const filteredDishes = ref([]);
const foods = [
  { image: "2.png", name: "Burger", price: "$5.00", category: "Fast food" },
  { image: "3.png", name: "Pizza", price: "$8.00", category: "Italian food" },
  { image: "4.png", name: "Sushi", price: "$10.00", category: "Asian food" },
  { image: "5.png", name: "Pasta", price: "$7.00", category: "Italian food" },
  { image: "6.png", name: "Salad", price: "$4.00", category: "Dessert" },
  { image: "7.png", name: "Tacos", price: "$6.00", category: "Fast food" },
  { image: "8.png", name: "Steak", price: "$15.00", category: "Asian food" },
  { image: "2.png", name: "Ice Cream", price: "$3.00", category: "Dessert" },
  { image: "4.png", name: "Brownie", price: "$2.00", category: "Dessert" },
];

const dishes = [
  { image: "11.png", name: "Hamburger", money: "$10.00", star: "4.5", category: "Fast food" },
  { image: "22.png", name: "Pizza", money: "$25.00", star: "4.1", category: "Italian food" },
  { image: "33.png", name: "Sushi", money: "$15.00", star: "4.3", category: "Asian food" },
  { image: "44.png", name: "Gratin", money: "$23.00", star: "4.9", category: "Italian food" },
  { image: "2.png", name: "Pasta", money: "$12.00", star: "4.6", category: "Italian food" },
  { image: "3.png", name: "Salad", money: "$8.00", star: "4.2", category: "Dessert" },
  { image: "4.png", name: "Tacos", money: "$9.00", star: "4.4", category: "Fast food" },
  { image: "5.png", name: "Steak", money: "$30.00", star: "4.8", category: "Asian food" },
  { image: "6.png", name: "Ice Cream", money: "$5.00", star: "4.7", category: "Dessert" },
  { image: "7.png", name: "Brownie", money: "$6.00", star: "4.5", category: "Dessert" },
];

const showModal = ref(false);
const selectedDish = ref({ name: '', price: '', quantity: 1 });
const orderedDishes = ref([]);
const showMoreModal = ref(false);
const moreDishes = [
  { image: "11.png", name: "Hamburger", money: "$10.00" },
  { image: "22.png", name: "Pizza", money: "$25.00" },
  { image: "33.png", name: "Sushi", money: "$15.00" },
  { image: "44.png", name: "Gratin", money: "$23.00" },
  { image: "2.png", name: "Pasta", money: "$12.00" },
  { image: "3.png", name: "Salad", money: "$8.00" },
  { image: "4.png", name: "Tacos", money: "$9.00" },
  { image: "5.png", name: "Steak", money: "$30.00" },
];

const showSubmitModal = ref(false);
const carouselPaused = ref(false);
const showPopup = ref(false);
const popupMessage = ref('');
const popupTitle = ref('');
const confirmDeleteIndex = ref(null); // Menyimpan index item yang akan dihapus
const selectedCategory = ref(''); // Menyimpan kategori yang dipilih

const calculateSubTotal = computed(() => {
  return orderedDishes.value.reduce((total, item) => {
    return total + parseFloat(item.price.replace('$', '')) * item.quantity;
  }, 0).toFixed(2);
});

const calculateTotal = computed(() => {
  const deliveryCharge = 10;
  return (parseFloat(calculateSubTotal.value) + deliveryCharge).toFixed(2);
});

function addToOrder(dish) {
  const existingDish = orderedDishes.value.find(item => item.name === dish.name);
  if (existingDish) {
    existingDish.quantity++;
  } else {
    orderedDishes.value.push({ 
      name: dish.name, 
      price: dish.money || dish.price,
      quantity: 1
    });
  }
}

function increaseQuantity(index) {
  orderedDishes.value[index].quantity++;
}

function decreaseQuantity(index) {
  if (orderedDishes.value[index].quantity > 1) {
    orderedDishes.value[index].quantity--;
  } else {
    orderedDishes.value.splice(index, 1);
  }
}

function showMoreDishes() {
  showMoreModal.value = true;
}

function filterItems() {
  const query = searchQuery.value.toLowerCase();
  if (query === '') {
    filteredFoods.value = foods;
    filteredDishes.value = dishes;
  } else {
    filteredFoods.value = foods.filter(food => food.name.toLowerCase().includes(query));
    filteredDishes.value = dishes.filter(dish => dish.name.toLowerCase().includes(query));
  }
}

function removeFromOrder(index) {
  orderedDishes.value.splice(index, 1);
}

function submitOrder() {
  showSubmitModal.value = true;
}

function confirmOrder() {
  console.log("Pesanan telah dikirim:", orderedDishes.value);
  popupTitle.value = 'Pesanan Dikirim!';
  popupMessage.value = 'Pesanan Anda telah berhasil dikirim 😊😊😊'; // Menambahkan emot senang
  showPopup.value = true;
  orderedDishes.value = [];
  showSubmitModal.value = false;
}

function confirmDelete(index) {
  // Hapus item langsung tanpa konfirmasi
  removeFromOrder(index);
  popupTitle.value = 'Dihapus!';
  popupMessage.value = 'Item telah dihapus dari pesanan 😢😢😢'; // Menambahkan emot sedih
  showPopup.value = true;
}

function pauseCarousel() {
  carouselPaused.value = true;
}

function resumeCarousel() {
  carouselPaused.value = false;
}

function filterByCategory(category) {
  selectedCategory.value = category;
  filteredFoods.value = foods.filter(food => food.category === category);
  filteredDishes.value = dishes.filter(dish => dish.category === category);
}

// Inisialisasi filteredDishes dan filteredFoods dengan semua item pada awalnya
filteredFoods.value = foods;
filteredDishes.value = dishes;

function surpriseMe() {
  // Logika untuk menampilkan makanan acak
  filteredFoods.value = foods.sort(() => 0.5 - Math.random()).slice(0, 5); // Contoh: ambil 5 makanan acak
  filteredDishes.value = dishes.sort(() => 0.5 - Math.random()).slice(0, 5);
}
</script>

<script>
export default {
  data: () => ({}),
};
</script>

<style scoped>
.mRight {
  margin-right: 100px;
}
.v-list {
  background: transparent !important;
}
.mLeft {
  margin-left: 90px;
}
.imag {
  z-index: 9999 !important;
}
.text-h6 {
  font-size: 1.25rem;
}
.mb-2 {
  margin-bottom: 0.5rem;
}
.text-black {
  color: black;
}
.text-white {
  color: #000;
}
.text-yellow {
  color: #FFD700;
}
.placeholder {
  color: #FFD700;
}
.confirm-order-dialog .v-card {
  background: linear-gradient(to bottom, #ff7e5f, #feb47b);
  color: white;
  border-radius: 10px;
  max-width: 300px;
}
.confirm-order-dialog .v-card-title {
  font-weight: bold;
  text-align: center;
  color: black;
  font-size: 1.2rem;
}
.confirm-order-dialog .v-btn {
  font-weight: bold;
}
.item-row {
  background-color: rgba(255, 255, 255, 0.1);
  padding: 10px;
  border-radius: 5px;
  margin-bottom: 5px;
}
.total-price {
  font-size: 1.2rem;
  font-weight: bold;
  text-align: center;
  color: black;
}
.hover-button:hover {
  background-color: rgba(255, 255, 255, 0.2);
  color: #ff7e5f;
}
.hover-effect:hover {
  background-color: rgba(255, 255, 255, 0.2);
  color: #ff7e5f;
}
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
@keyframes bounce {
  0%, 20%, 50%, 80%, 100% {
    transform: translateY(0);
  }
  40% {
    transform: translateY(-10px);
  }
  60% {
    transform: translateY(-5px);
  }
}
.no-order-message {
  animation: fadeIn 1s;
}
.order-prompt {
  margin-top: 10px; 
  color: #FFD700; 
  font-weight: bold; 
  animation: bounce 1s infinite;
}
.popup-dialog .v-card {
  background: linear-gradient(to bottom, #ff7e5f, #feb47b);
  color: white;
  border-radius: 10px;
}
.popup-dialog .v-card-title {
  font-weight: bold;
  text-align: center;
  color: black;
  font-size: 1.2rem;
}
.popup-dialog .v-btn {
  font-weight: bold;
}
.no-order-message {
  color: white; /* Pastikan teks terlihat */
  text-align: center; /* Pusatkan teks */
  margin-top: 20px; /* Tambahkan margin untuk jarak */
}
</style>
