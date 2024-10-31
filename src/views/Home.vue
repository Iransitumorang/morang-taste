<template>
  <v-app id="inspire" :style="{ background: 'linear-gradient(to right, #388e3c, #a5d6a7)' }" :class="{ 'blur-background': showPopup || showSubmitModal || showPromoModal || showFoodModal || showMoreModal }">
    <SideBare />
    <v-main>
      <v-container>
        <v-row>
          <v-col cols="12" sm="8" class="d-flex flex-column">
            <div class="d-flex justify-space-between my-5 flex-wrap">
              <v-text-field
                density="compact"
                placeholder="Search restaurant, Food, Cuisine or a Dish"
                append-inner-icon="mdi-magnify"
                variant="outlined"
                rounded
                class="text-white mx-1 placeholder flex-grow-1"
                v-model="searchQuery" 
                @input="filterItems"
                style="color: white;" 
              ></v-text-field>

              <v-btn v-if="searchQuery" @click="clearSearch" icon class="mx-1" style="padding: 0; background: none;">
                <v-icon>mdi-close</v-icon>
              </v-btn>

              <v-select
                v-model="sortOption"
                :items="sortOptions"
                label="Sort by"
                class="white--text custom-select" 
                @change="sortItems"
                style="max-width: 150px; height: 25px;" 
              ></v-select>
            </div>

            <div v-if="filteredFoods.length === 0 && searchQuery" class="text-center text-black mt-4" style="font-size: 2.5rem; background-color: rgba(0, 0, 0, 0.7); padding: 20px; border-radius: 8px;">
              <h6>Tidak ada item yang ditemukan untuk "{{ searchQuery }}".</h6>
              <v-img src="/src/assets/ue.png" alt="No items found" height="300" class="mt-2"></v-img>
            </div>

            <div v-if="filteredFoods.length > 0" class="d-flex justify-start my-5 flex-wrap">
              <v-btn @click="surpriseMe" class="black--text rounded-lg elevation-2 mx-1" style="background: linear-gradient(to right, #388e3c, #a5d6a7);">Surprise me!</v-btn>
              <v-btn @click="filterByCategory('Dessert')" class="black--text rounded-lg elevation-2 mx-1" style="background: linear-gradient(to right, #388e3c, #a5d6a7);">Dessert</v-btn>
              <v-btn @click="filterByCategory('Italian food')" class="black--text rounded-lg elevation-2 mx-1" style="background: linear-gradient(to right, #388e3c, #a5d6a7);">Italian food</v-btn>
              <v-btn @click="filterByCategory('Fast food')" class="black--text rounded-lg elevation-2 mx-1" style="background: linear-gradient(to right, #388e3c, #a5d6a7);">Fast food</v-btn>
              <v-btn @click="filterByCategory('Asian food')" class="black--text rounded-lg elevation-2 mx-1" style="background: linear-gradient(to right, #388e3c, #a5d6a7);">Asian food</v-btn>
            </div>

            <div v-if="filteredFoods.length > 0">
              <v-toolbar color="transparent" class="pr-1 mt-n2">
                <v-toolbar-title class="text-black">Categories</v-toolbar-title>
                <v-spacer></v-spacer>
                <span @click="showAllFoodCategories" class="text-caption text-black hover-effect">View all</span>
                <v-btn density="compact" icon="mdi mdi-chevron-right-box" color="grey" @click="showAllFoodCategories" class="hover-effect"></v-btn>
              </v-toolbar>
              <h6 class="text-black ml-4 mt-n4">
                <span class="text-red">{{ filteredFoods.length }} new </span> Categories added this week
              </h6>

              <div class="d-flex justify-space-evenly mt-4 flex-wrap">
                <div v-for="(food, i) in displayedFoods" :key="i" class="text-center">
                  <v-avatar color="#424242" size="70" @click="openFoodModal(food)">
                    <v-img :src="getImage(food.image)" height="50" class="zoom-out" @error="handleImageError"></v-img>
                  </v-avatar>
                  <div class="text-black font-weight-bold">{{ food.name }}</div>
                  <div class="text-black">{{ food.price }}</div>
                  <v-btn @click="addToOrder(food)" color="orange" class="mt-2" style="background: linear-gradient(to right, #388e3c, #a5d6a7);">Order</v-btn>
                </div>
              </div>

              <v-toolbar color="transparent" class="pr-1 my-2">
                <v-toolbar-title class="text-black">Popular Dishes</v-toolbar-title>
                <v-spacer></v-spacer>
                <span class="text-caption text-black hover-effect" @click="showMoreDishes">View More</span>
                <v-btn density="compact" icon="mdi mdi-chevron-right-box" color="grey" @click="showMoreDishes" class="hover-effect"></v-btn>
              </v-toolbar>
              <h6 class="text-black ml-4 mt-n4">
                <span class="text-red">{{ filteredDishes.length }} new </span> dishes added this week
              </h6>

              <v-carousel hide-delimiter :cycle="!carouselPaused" :interval="3000" @mouseover="pauseCarousel" @mouseleave="resumeCarousel">
                <v-carousel-item v-for="(dishe, i) in filteredDishes" :key="i" :src="getImage(dishe.image)">
                  <div class="carousel-content">
                    <v-card class="mt-n10" width="250" style="background: linear-gradient(to bottom, #388e3c, #a5d6a7); padding: 20px; text-align: center;"> 
                      <v-card-item>
                        <v-card-title class="mt-2">{{ dishe.name }}</v-card-title>
                        <v-card-subtitle>
                          <span class="me-1">Starting From</span>
                          <span>{{ dishe.money }}</span>
                        </v-card-subtitle>
                        <v-card-title class="mt-1">{{ dishe.money }}</v-card-title>
                        <v-btn @click="addToOrder(dishe)" color="orange" style="background: linear-gradient(to right, #388e3c, #a5d6a7); margin-top: 10px;">Order</v-btn>
                        <div class="mt-2">
                          <span class="text-yellow">{{ dishe.star }} ⭐</span> 
                          <span class="text-white">({{ Math.floor(Math.random() * 100) + 1 }} ratings)</span>
                        </div>
                        <div class="mt-1">
                          <span class="text-white">Ordered: {{ orderedDishes.length }} Dishes</span>
                        </div>
                        <v-btn @click="openFoodModal(dishe)" color="orange" class="mt-2" style="background: linear-gradient(to right, #388e3c, #a5d6a7);">View Details</v-btn> 
                      </v-card-item>
                    </v-card>
                  </div>
                </v-carousel-item>
              </v-carousel>

              <v-toolbar color="transparent" class="pr-1 mt-5">
                <v-toolbar-title class="text-black">Order Reports</v-toolbar-title>
                <v-spacer></v-spacer>
                <span class="text-caption text-black">View all</span>
                <v-btn density="compact" icon="mdi mdi-chevron-right-box" color="grey"></v-btn>
              </v-toolbar>
              <h6 class="text-black ml-4 mt-n4">
                <span class="text-red">Wow {{ orderedDishes.length }} new </span> Order got this week
              </h6>
              <v-card class="rounded-xl ma-2 pa-1" style="background: linear-gradient(to bottom, #388e3c, #a5d6a7);">
                <v-row>
                  <v-col cols="12" sm="1"> </v-col>
                  <v-col cols="12" sm="2" class="text-center"><span class="text-caption text-black">Customer</span></v-col>
                  <v-col cols="12" sm="2" class="text-center"><span class="text-caption text-black">Order number</span></v-col>
                  <v-col cols="12" sm="2" class="text-center"><span class="text-caption text-black">Address</span></v-col>
                  <v-col cols="12" sm="2" class="text-center"><span class="text-caption text-black">Amount</span></v-col>
                  <v-col cols="12" sm="2" class="text-center"><span class="text-caption text-black">Status</span></v-col>
                  <v-col cols="12" sm="1" class="text-center"></v-col>
                </v-row>
              </v-card>
              <v-row class="mt-2 ms-3">
                <v-col cols="12" sm="3" class="mt-2"> 
                  <v-avatar> <v-img src="/src/assets/20.jpg" alt="John"></v-img> </v-avatar>
                  <span class="text-caption text-black ml-1">Jenetta Sinaga</span>
                </v-col>
                <v-col cols="12" sm="2" class="text-center mt-2">
                  <span class="text-caption text-black">086537867736</span>
                </v-col>
                <v-col cols="12" sm="2" class="text-center mt-2">
                  <span class="text-caption text-black">Kuningan Barat</span>
                </v-col>
                <v-col cols="12" sm="2" class="text-center mt-2">
                  <span class="text-caption text-black">$120.45</span>
                </v-col>
                <v-col cols="12" sm="2" class="text-center">
                  <v-chip variant="flat" color="green" class="status-chip"> Completed </v-chip>
                </v-col>
                <v-col cols="12" sm="1" class="text-center"></v-col>
              </v-row>
              <v-row class="mt-1 ms-3">
                <v-col cols="12" sm="3" class="mt-2"> 
                  <v-avatar> <v-img src="/src/assets/30.jpg" alt="John"></v-img> </v-avatar>
                  <span class="text-caption text-black ml-1">Peter Situmorang</span>
                </v-col>
                <v-col cols="12" sm="2" class="text-center mt-2">
                  <span class="text-caption text-black">089787686556</span>
                </v-col>
                <v-col cols="12" sm="2" class="text-center mt-2">
                  <span class="text-caption text-black">Pancoran tugu</span>
                </v-col>
                <v-col cols="12" sm="2" class="text-center mt-2">
                  <span class="text-caption text-black">$140.45</span>
                </v-col>
                <v-col cols="12" sm="2" class="text-center">
                  <v-chip variant="flat" color="orange" class="status-chip"> Pending </v-chip>
                </v-col>
                <v-col cols="12" sm="1" class="text-center"></v-col>
              </v-row>
            </div>
          </v-col>
          <v-col cols="12" sm="4">
            <v-card class="rounded-xl" style="background: linear-gradient(to right, #388e3c, #a5d6a7);" height="850">
              <v-card class="mx-2 mt-4 rounded-lg" max-width="344" style="background: linear-gradient(to right, #388e3c, #a5d6a7);">
                <v-card-item title="Morang's Taste">
                  <template v-slot:subtitle>
                    <v-icon icon="mdi mdi-map-marker" size="18" class="me-1 pb-1"></v-icon>
                    Kuningan Barat, Jakarta Selatan
                  </template>
                </v-card-item>
                <v-card-text class="mt-n3">
                  <v-icon size="16" icon="mdi mdi-clock-outline" class="mr-2"></v-icon>{{ getRandomTime() }} min</v-card-text>
              </v-card>
              <v-toolbar color="transparent" class="pr-1 mt-n2">
                <v-btn icon class="hidden-xs-only" style="position: relative;">
                  <v-icon>mdi mdi-cart</v-icon>
                  <span v-if="orderedDishes.length > 0" class="text-caption cart-count">{{ orderedDishes.length }}</span>
                </v-btn>
                <v-toolbar-title class="text-black" style="background: linear-gradient(to right, #388e3c, #a5d6a7);">My Order</v-toolbar-title>
                <v-spacer></v-spacer>
                <span class="text-caption text-black me-2">Order ID: #{{ randomOrderId }}</span>
              </v-toolbar>
              <v-card class="ma-2 mt-n2" color="transparent" flat>
                <v-card-text class="ml-14 mt-n2" style="max-height: 350px; overflow-y: auto; margin-bottom: 20px;">
                  <div v-if="orderedDishes.length === 0" style="background: linear-gradient(to right, #388e3c, #a5d6a7); color: black; padding: 30px; border-radius: 8px; text-align: center; animation: fadeIn 1s;">
                    <div class="no-order-message" style="color: black;">Tidak ada pesanan :(</div>
                    <div class="order-prompt" style="color: black; font-weight: bold; animation: bounce 1s infinite;">Ayo, pesan makanan favoritmu sekarang!</div>
                  </div>
                  <div v-else>
                    <div v-for="(item, index) in orderedDishes" :key="index" class="d-flex align-center mb-2" style="background: linear-gradient(to right, #388e3c, #a5d6a7); border-radius: 8px; padding: 10px;">
                      <v-avatar class="mr-2" size="50">
                        <v-img :src="getImage(item.image)" height="50" width="50" contain></v-img>
                      </v-avatar>
                      <div class="flex-grow-1">
                        <div class="text-black font-weight-bold text-h6">{{ item.name }} - {{ item.price }}</div>
                        <div class="d-flex align-center">
                          <v-icon @click="decreaseQuantity(index)" class="mr-2" x-small>mdi mdi-minus-circle-outline</v-icon>
                          <div class="text-black font-weight-bold" style="font-size: 1.2rem;">{{ item.quantity }}</div>
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
                <v-chip 
                  variant="flat" 
                  color="orange" 
                  style="background: linear-gradient(to right, #388e3c, #a5d6a7);" 
                  @click="showPromoModal = true" :disabled="orderedDishes.length === 0" 
                >
                  Purchase 
                </v-chip>
              </v-card>

              <v-dialog v-model="showPromoModal" max-width="400px">
                <v-card :style="{ background: 'linear-gradient(to bottom, #388e3c, #a5d6a7)' }">
                  <v-card-title class="text-h6">Enter Promo Code</v-card-title>
                  <v-card-text>
                    <v-text-field v-model="promoCode" label="Promo Code" />
                    <div class="hashtags">
                      <span class="hashtag" @click="promoCode = 'DISCOUNT50'">#DISCOUNT50</span>
                      <span class="hashtag" @click="promoCode = 'SAVE10'">#SAVE10</span>
                      <span class="hashtag" @click="promoCode = 'FREESHIP'">#FREESHIP</span>
                    </div>
                  </v-card-text>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn text @click="applyPromoAndClear">Apply</v-btn>
                    <v-btn text @click="showPromoModal = false" class="hover-button" style="color: red;">Close</v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>

              <div v-if="orderedDishes.length === 0" style="background: linear-gradient(to right, #388e3c, #a5d6a7); color: black; padding: 30px; border-radius: 8px; text-align: center; animation: fadeIn 1s;">
                <div class="text-caption" style="color: black; font-size: 1.5rem;">Cobalah makanan lezat kami!</div>
                <div class="text-caption" style="color: black; font-size: 1.2rem;">Nikmati pengalaman kuliner yang tak terlupakan, sekarang juga.</div>
                <div class="text-caption" style="color: black; font-size: 1.2rem;">Pesan sekarang dan rasakan kelezatannya!</div>
              </div>

              <div v-if="orderedDishes.length > 0">
                <v-list-item title="Sub Total">
                  <template v-slot:append>
                    <v-btn variant="text">{{ '$' + calculateSubTotal }}</v-btn>
                  </template>
                </v-list-item>

                <v-list-item title="Discount">
                  <template v-slot:append>
                    <v-btn variant="text">{{ discountAmount }}</v-btn>
                  </template>
                </v-list-item>

                <v-list-item title="Service Charge">
                  <template v-slot:append>
                    <v-btn variant="text">{{ '$' + (calculateSubTotal * 0.10).toFixed(2) }}</v-btn>
                  </template>
                </v-list-item>

                <v-list-item title="TOTAL">
                  <template v-slot:append>
                    <v-btn variant="text">{{ '$' + calculateTotalWithDiscount }}</v-btn>
                  </template>
                </v-list-item>
              </div>

              <v-chip 
                variant="flat" 
                color="black" 
                class="px-10 mLeft" 
                style="margin-top: 20px; background: linear-gradient(to right, #388e3c, #a5d6a7); font-weight: bold; color: #FFD700;" 
                @click="submitOrder"
                :disabled="orderedDishes.length === 0" 
                :class="{ 'hover-effect': orderedDishes.length > 0 }"
              >
                Submit Order
              </v-chip>

              <v-dialog v-model="showSubmitModal" max-width="800px" class="confirm-order-dialog" style="display: flex; justify-content: center; align-items: center; width: 800px; height: 600px;">
                <v-card style="margin: auto;">
                  <v-card-title class="text-h6 text-center" style="font-weight: bold; background: linear-gradient(to right, #ff7e5f, #feb47b); color: black; padding: 16px;">Confirm Order</v-card-title>
                  <v-card-text>
                    <div v-for="(item, index) in orderedDishes" :key="index" class="d-flex justify-space-between item-row">
                      <span>{{ item.name }} - {{ item.price }} (x{{ item.quantity }})</span>
                    </div>
                    <v-divider class="my-"></v-divider>
                    <div class="mt-5 total-price d-flex justify-space-between">
                      <strong>Sub Total</strong>
                      <span class="text-black confirm-total">${{ calculateSubTotal }}</span>
                    </div>
                    <div class="mt-2 total-price d-flex justify-space-between">
                      <strong>Discount</strong>
                      <span class="text-black confirm-discount">{{ discountAmount }}</span>
                    </div>
                    <div class="mt-2 total-price d-flex justify-space-between">
                      <strong>Service Charge</strong>
                      <span class="text-black confirm-total">$10</span>
                    </div>
                    <div class="mt-2 total-price d-flex justify-space-between">
                      <strong>Total After Disc</strong>
                      <span class="text-black confirm-total">${{ calculateTotalWithDiscount }}</span>
                    </div>
                  </v-card-text>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn text @click="showSubmitModal = false" class="hover-button" style="color: red;">Batal</v-btn>
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
      <v-card :style="{ background: 'linear-gradient(to bottom, #388e3c, #a5d6a7)' }">
        <v-card-title class="text-h6">More Popular Dishes</v-card-title>
        <v-card-text>
          <div class="d-flex flex-wrap" style="background-color: #388e3c; padding: 20px; border-radius: 8px;">
            <div v-for="(dish, i) in moreDishes" :key="i" class="text-center" style="margin: 10px; width: calc(25% - 20px);">
              <v-avatar color="#605850" size="70" @click="openFoodModal(dish)">
                <v-img :src="getImage(dish.image)" height="50" class="zoom-out" @error="handleImageError"></v-img>
              </v-avatar>
              <div class="text-white">{{ dish.name }}</div>
              <div class="text-white">{{ dish.money }}</div>
              <v-btn @click="addToOrder(dish)" color="primary" class="mt-2" style="background: linear-gradient(to right, #388e3c, #a5d6a7);">Order</v-btn>
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
      <v-card :style="{ background: 'linear-gradient(to right, #388e3c, #a5d6a7)' }">
        <v-card-title class="text-h6">{{ popupTitle }}</v-card-title>
        <v-card-text>
          <span v-if="popupTitle === 'Pesanan Dikirim!'">{{ popupMessage }}</span>
          <span v-else-if="popupTitle === 'Dihapus!'">{{ popupMessage }}</span>
          <span v-if="popupTitle === 'Promo Applied Successfully!'">You've received a discount of {{ discountAmount }}. It's time to shop smarter!</span>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn text @click="showPopup = false">Close</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="showFoodModal" max-width="400px">
      <v-card :style="{ background: 'linear-gradient(to bottom, #388e3c, #a5d6a7)' }">
        <v-card-title class="d-flex justify-between align-center">
          <span class="text-h6">Food Detail</span>
          <span @click="showFoodModal = false" class="text-white hover-button" style="cursor: pointer; margin-left: auto;">Close</span>
        </v-card-title>
        <v-card-subtitle class="text-white text-center font-weight-bold mb-2" style="font-size: 1.5rem;">{{ selectedFood.name }}</v-card-subtitle>
        <v-card-subtitle class="text-white text-center">Discover the delightful flavors and unique <br/> ingredients of your chosen dish!</v-card-subtitle>
        <v-card-text>
          <v-img :src="getImage(selectedFood.image)" height="150" class="mb-2 zoom-out" contain></v-img>
          <div class="text-white text-center">Price: {{ selectedFood.price }}</div>
          <div class="d-flex justify-center align-center mt-2">
            <v-text-field v-model="quantity" type="number" min="1" label="Quantity" class="mt-2" style="width: 60px;"></v-text-field>
            <v-btn color="orange" @click="addToOrderWithQuantity(selectedFood)" class="ml-2" style="background: linear-gradient(to right, #388e3c, #a5d6a7);">Order</v-btn>
          </div>
        </v-card-text>
      </v-card>
    </v-dialog>

    <v-dialog v-model="showModal" max-width="800px">
      <v-card :style="{ background: 'linear-gradient(to bottom, #4caf50, #a5d6a7)' }">
        <v-card-title class="text-h6">All Popular Food Categories</v-card-title>
        <v-card-text>
          <div class="d-flex flex-wrap" style="background-color: #388e3c; padding: 20px; border-radius: 8px;">
            <div v-for="(dish, i) in filteredFoods.slice(0, filteredFoods.length - 1)" :key="dish.id" class="text-center" style="margin: 10px; width: calc(25% - 20px);">
              <v-avatar color="#605850" size="70" @click="openFoodModal(dish)">
                <v-img :src="getImage(dish.image)" height="50" class="zoom-out"></v-img>
              </v-avatar>
              <div class="text-white">{{ dish.name }}</div>
              <div class="text-white">{{ dish.price }}</div>
              <v-btn @click="addToOrder(dish)" color="primary" class="mt-2" style="background: linear-gradient(to right, #388e3c, #a5d6a7);">Order</v-btn>
            </div>
          </div>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn text @click="showModal = false">Close</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-app>
</template>

<script setup>
import { ref, computed } from 'vue';
import SideBare from "@/components/SideBar.vue";
import Swal from 'sweetalert2'; 

const searchQuery = ref('');
const filteredFoods = ref([]);
const filteredDishes = ref([]);
const sortOption = ref('All Items');
const sortOptions = ['All Items', 'Price: Low to High', 'Price: High to Low'];
const foods = [
  { image: "2.png", name: "Burger", price: "$5.00", category: "Fast food" },
  { image: "3.png", name: "Pizza", price: "$8.00", category: "Italian food" },
  { image: "4.png", name: "Sushi", price: "$10.00", category: "Asian food" },
  { image: "5.png", name: "Pasta", price: "$7.00", category: "Italian food" },
  { image: "6.png", name: "Salad", price: "$4.00", category: "Dessert" },
  { image: "7.png", name: "Tacos", price: "$6.00", category: "Fast food" },
  { image: "8.png", name: "Steak", price: "$15.00", category: "Asian food" },
  { image: "6.png", name: "Ice Cream", price: "$3.00", category: "Dessert" },
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
  { image: "8.png", name: "Sushi Roll", money: "$12.00", star: "4.6", category: "Asian food" },
  { image: "9.png", name: "Pasta Primavera", money: "$14.00", star: "4.4", category: "Italian food" },
];

const showModal = ref(false);
const selectedDish = ref({ name: '', price: '', quantity: 1 });
const orderedDishes = ref([]);
const showMoreModal = ref(false);
const moreDishes = [
  { image: "11.png", name: "Hamburger", money: "$10.00" },
  { image: "22.png", name: "Pizza", money: "$25.00" },
  { image: "33.png", name: "Sushi", money: "$15.00" },
  { image: "11.png", name: "Hamburger", money: "$10.00" },
  { image: "22.png", name: "Pizza", money: "$25.00" },
  { image: "33.png", name: "Sushi", money: "$15.00" },
  { image: "11.png", name: "Hamburger", money: "$10.00" },
  { image: "22.png", name: "Pizza", money: "$25.00" },
];

const showSubmitModal = ref(false);
const carouselPaused = ref(false);
const showPopup = ref(false);
const popupMessage = ref('');
const popupTitle = ref('');
const confirmDeleteIndex = ref(null);
const selectedCategory = ref('');
const showFoodModal = ref(false);
const selectedFood = ref({});
const quantity = ref(1);
const showPromoModal = ref(false);
const promoCode = ref('');
const discountAmount = ref('$0.00');
const categories = ['Dessert', 'Italian food', 'Fast food', 'Asian food'];

const discountPercentage = 0.05;
const deliveryChargePercentage = 0.10;
const serviceChargePercentage = 0.10;

const randomOrderId = ref(Math.floor(Math.random() * 10000));

const calculateSubTotal = computed(() => {
  return orderedDishes.value.reduce((total, item) => {
    return total + parseFloat(item.price.replace('$', '')) * item.quantity;
  }, 0).toFixed(2);
});

const calculateTotal = computed(() => {
  const serviceCharge = parseFloat(calculateSubTotal.value) * serviceChargePercentage;
  const deliveryCharge = parseFloat(calculateSubTotal.value) * deliveryChargePercentage;
  return (parseFloat(calculateSubTotal.value) + serviceCharge + deliveryCharge).toFixed(2);
});

const calculateTotalWithDiscount = computed(() => {
  const discount = parseFloat(discountAmount.value.replace('$', '')) || 0;
  const total = parseFloat(calculateTotal.value) + discount;
  return total.toFixed(2);
});

function addToOrder(dish) {
  const existingDish = orderedDishes.value.find(item => item.name === dish.name);
  if (existingDish) {
    existingDish.quantity++;
  } else {
    orderedDishes.value.push({ 
      name: dish.name, 
      price: dish.money || dish.price,
      quantity: 1,
      image: dish.image
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
  popupTitle.value = 'Pesanan Dikirim!';
  popupMessage.value = 'Pesanan Anda telah berhasil dikirim 😊😊😊';
  showPopup.value = true;
  orderedDishes.value = [];
  discountAmount.value = '$0.00';
  showSubmitModal.value = false; 
}

function confirmDelete(index) {
  removeFromOrder(index);
  popupTitle.value = 'Dihapus!';
  popupMessage.value = 'Item telah dihapus dari pesanan 😢';
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

filteredFoods.value = foods;
filteredDishes.value = dishes;

function surpriseMe() {
  filteredFoods.value = foods.sort(() => 0.5 - Math.random()).slice(0, 5);
  filteredDishes.value = dishes.sort(() => 0.5 - Math.random()).slice(0, 5);
}

function sortItems() {
  if (sortOption.value === 'All Items') {
    filteredFoods.value = foods.slice();
  } else if (sortOption.value === 'Price: Low to High') {
    filteredFoods.value.sort((a, b) => parseFloat(a.price.replace('$', '')) - parseFloat(b.price.replace('$', '')));
  } else if (sortOption.value === 'Price: High to Low') {
    filteredFoods.value.sort((a, b) => parseFloat(b.price.replace('$', '')) - parseFloat(a.price.replace('$', '')));
  }
  filteredFoods.value = filteredFoods.value.slice(0, 8);
}

const sortedFilteredFoods = computed(() => {
  return [...filteredFoods.value].sort((a, b) => parseFloat(a.price.replace('$', '')) - parseFloat(b.price.replace('$', '')));
});

const displayedFoods = computed(() => {
  let foodsToDisplay = [...filteredFoods.value];
  if (sortOption.value === 'Price: Low to High') {
    foodsToDisplay.sort((a, b) => parseFloat(a.price.replace('$', '')) - parseFloat(b.price.replace('$', '')));
  } else if (sortOption.value === 'Price: High to Low') {
    foodsToDisplay.sort((a, b) => parseFloat(b.price.replace('$', '')) - parseFloat(a.price.replace('$', '')));
  }
  return foodsToDisplay.slice(0, 8);
});

function openFoodModal(food) {
  selectedFood.value = food;
  showFoodModal.value = true;
}

function addToOrderWithQuantity(food) {
  const orderItem = { 
    name: food.name, 
    price: food.price, 
    quantity: quantity.value 
  };
  orderedDishes.value.push(orderItem);
  showFoodModal.value = false;
  quantity.value = 1;
}

function applyPromo() {
  // Potongan 0.5% untuk setiap kode promo yang dimasukkan
  if (promoCode.value) {
    const discount = calculateSubTotal.value * discountPercentage;
    discountAmount.value = `-$${discount.toFixed(2)}`;
    popupTitle.value = 'Promo Applied Successfully!';
    popupMessage.value = `You've received a discount of ${discountAmount.value}! It's time to shop smarter!`;
    
    showPopup.value = true;
    showPromoModal.value = false;
  }
}

function clearSearch() {
  searchQuery.value = '';
  filterItems();
}

function getRandomTime() {
  return Math.floor(Math.random() * 60) + 1;
}

function clearPromoCode() {
  promoCode.value = '';
  showPromoModal.value = false;
}

function applyPromoAndClear() {
  applyPromo();
  promoCode.value = ''; 
}

const getImage = (imagePath) => {
  try {
    return new URL(`../assets/${imagePath}`, import.meta.url).href;
  } catch (error) {
    console.error("Gambar tidak ditemukan:", error);
    return ''; // Kembalikan string kosong jika gambar tidak ditemukan
  }
};

function showAllFoodCategories() {
  filteredFoods.value = foods; // Mengisi dengan semua makanan
  showModal.value = true; // Menampilkan modal
}
</script>

<script>
export default {
  data: () => ({}),
  methods: {
    handleImageError(event) {
      event.target.src = '/path/to/fallback/image.png'; // Ganti dengan path gambar fallback
    }
  }
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
  background: linear-gradient(to right, #388e3c, #a5d6a7);
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
  font-size: 1rem;
  font-weight: 500;
  color: white;
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
  background: linear-gradient(to right, #388e3c, #a5d6a7);
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
  color: white;
  text-align: center;
  margin-top: 20px;
}
.bg-grey-900 {
  background-color: #424242;
}

.v-select {
  max-width: 150px;
  margin-left: 0;
}

.v-select .v-input__control {
  height: 50px;
  background: linear-gradient(to right, #ffccbc, #ffab91);
}

.v-select .v-input__slot {
  padding: 0 16px;
}

.custom-select .v-input__control {
  border-radius: 8px;
}

.custom-select .v-input__control:focus {
  background: linear-gradient(to right, #ffccbc, #ffab91);
}

.category-item {
  margin: 10px;
  width: 120px;
  background: linear-gradient(to bottom, #ff7e5f, #feb47b);
  padding: 10px;
  border-radius: 8px;
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  transition: transform 0.3s; 
}

.category-item:hover {
  transform: scale(0.9);
}

@media (max-width: 768px) {
  .mRight {
    margin-right: 20px;
  }
  .mLeft {
    margin-left: 20px;
  }
  .text-h6 {
    font-size: 1.1rem;
  }
  .category-item {
    width: 100%;
    margin: 5px 0; 
  }
  .d-flex {
    flex-direction: column; 
  }
}

.carousel-content {
  display: none; 
  position: absolute;
  top: 50%; 
  left: 50%;
  transform: translate(-50%, -50%);
}

.v-carousel-item:hover .carousel-content {
  display: block; 
}

.status-chip {
  width: 100%; 
  text-align: center;
}
.mt-n5 {
  margin-top: 20px;
}
.mt-3 {
  margin-top: 15px; 
}
.mt-4 {
  margin-top: 20px; 
}
.mt-2 {
  margin-top: 10px;
}
.hover-effect:hover {
  color: #ff7e5f;
}
.zoom-out {
  transition: transform 0.3s;
}

.zoom-out:hover {
  transform: scale(0.8);
}

.dish-item {
  margin: 0; 
  padding: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.dishes-grid {
  display: grid; 
  grid-template-columns: repeat(4, 1fr);
  gap: 10px;
}

.dish-item {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.blur-background {
  filter: blur(5px);
  transition: filter 0.3s ease;
}

.hashtags {
  margin-top: 10px;
}

.hashtag {
  cursor: pointer;
  color: #ff7e5f;
  margin-right: 10px;
  font-weight: bold;
}

.cart-count {
  position: absolute;
  top: 4px;
  right: 6px;
  font-size: 0.75rem;
  font-weight: bold;
  color: red;
}
</style>




