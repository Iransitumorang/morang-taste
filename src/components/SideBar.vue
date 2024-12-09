<template>
  <v-navigation-drawer v-model="drawer" class="gradient-background">
    <div color="" class="pa-4">
      <v-img
        src="/src/assets/favicon.ico"
        alt="food"
        width="150"
        height="150"
        class="ml-9"
      ></v-img>
    </div>
    <v-card class="rounded-e-xl me" width="60" height="400" color="#388e3c"></v-card>
    <v-list class="top">
      <v-list-item
        v-for="(item, i) in links"
        :key="i"
        :value="item"
        class="mb-5"
        color="#FF6259"
        variant="plain"
      >
        <template v-slot:append="{ isActive }">
          <div v-if="isActive" class="a"></div>
        </template>
        <template v-slot:prepend>
          <v-icon :icon="item.icon"></v-icon>
        </template>
        <v-list-item-title v-text="item.text" class="text-color"></v-list-item-title>
      </v-list-item>
    </v-list>
    <v-div
      style="
        position: absolute;
        bottom: 20px;
        margin-left: auto;
        margin-right: auto;
        left: 0;
        right: 0;
        text-align: center;
      "
    >
      <v-btn
        icon="mdi mdi-cog"
        size="small"
        class="mr-2 gradient-button"
        color="white"
        @click="showSettingsDialog"
      ></v-btn>
      <v-badge dot color="#FF6259">
        <v-btn icon="mdi mdi-bell" size="small" color="#5F3B39" @click="showDialog"></v-btn>
      </v-badge>
    </v-div>

    <v-dialog v-model="settingsDialog" max-width="600px">
      <v-card class="gradient-background">
        <v-card-title class="headline">Pengaturan</v-card-title>
        <v-card-text>
          <p>Di sini Anda dapat mengatur preferensi aplikasi Anda. Sesuaikan pengaturan sesuai kebutuhan Anda untuk pengalaman yang lebih baik.</p>
        </v-card-text>
        <v-card-actions>
          <div style="margin-left: auto;">
            <v-btn class="gradient-button" @click="settingsDialog = false">Close</v-btn>
          </div>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="dialog" max-width="600px">
      <v-card class="gradient-background">
        <v-card-title class="headline">Notifikasi</v-card-title>
        <v-card-text>
          <p>🎉 Dapatkan penawaran spesial hari ini! Jangan lewatkan kesempatan untuk membeli produk unggulan kami dengan diskon menarik!</p>
        </v-card-text>
        <v-card-actions>
          <div style="margin-left: auto;">
            <v-btn class="gradient-button" @click="dialog = false">Close</v-btn>
          </div>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-app id="inspire" :style="{ background: 'linear-gradient(to right, #388e3c, #a5d6a7)' }" :class="{ 'blur-background': showSettingsDialog || dialog }">
      ...
    </v-app>
  </v-navigation-drawer>
</template>

<script>
import { ref } from "vue";

export default {
  data: () => ({
    links: [
      { text: "Dashboard", icon: "mdi mdi-view-dashboard" },
      { text: "Orders", icon: "mdi mdi-phone-classic" },
      { text: "Restaurants", icon: "mdi mdi-map-marker-radius" },
      { text: "Finance", icon: "mdi mdi-finance" },
      { text: "Logout", icon: "mdi mdi-logout" },
    ],
    cards: ["Today", "Yesterday"],
    drawer: null,
    dialog: false,
    settingsDialog: false,
  }),
  methods: {
    showDialog() {
      this.dialog = true;
    },
    showSettingsDialog() {
      this.settingsDialog = true;
    },
  },
};
</script>

<style scoped>
.v-list-item.v-list-item--active.v-list-item--link.border.v-theme--light.text-red.v-list-item--density-default.v-list-item--one-line.v-list-item--variant-text {
  color: white !important;
}
.top {
  margin-top: 60px;
}
.me {
  position: absolute;
  margin-top: 28px;
}
.v-locale--is-ltr .rounded-e-xl {
  border-top-right-radius: 44px !important;
  border-bottom-right-radius: 44px !important;
}
.a {
  top: 32%;
  left: 20.5%;
  position: absolute;
  height: 15px;
  width: 15px;
  background: linear-gradient(-90deg, #545454, #545454 50%, rgb(151, 185, 86) 50%);
  border-radius: 50%;
}
.gradient-background {
  background: #388e3c;
}
.gradient-button {
  background: #388e3c;
  color: white;
}
.v-card {
  background: #388e3c;
}
.text-color {
  color: black;
}
.v-img:hover {
  transform: scale(0.9);
  transition: transform 0.3s ease;
}
.v-application {
    display: flex;
    background: #388e3c !important;
    color: rgba(var(--v-theme-on-background), var(--v-high-emphasis-opacity));
}
</style>
