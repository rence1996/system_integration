<template>
  <v-app>
      <v-navigation-drawer
        expand-on-hover
        permanent
        rail
      >
        <v-list>
          <v-list-item
            prepend-avatar="https://randomuser.me/api/portraits/women/85.jpg"
            subtitle="sandra_a88@gmailcom"
            title="Sandra Adams"
          ></v-list-item>
        </v-list>

        <v-divider></v-divider>

        <v-list density="compact" nav>
          <v-list-item prepend-icon="mdi-folder" title="My Files" value="myfiles"></v-list-item>
          <v-list-item prepend-icon="mdi-qrcode" title="QR Scanner" value="qr-scanner"></v-list-item>
          <v-list-item prepend-icon="mdi-weather-sunny" title="Weather" value="weather"></v-list-item>
        </v-list>
      </v-navigation-drawer>

      <v-app-bar>
  <template v-slot:prepend>
    <v-app-bar-nav-icon></v-app-bar-nav-icon>
  </template>

  <v-app-bar-title>Application Bar</v-app-bar-title>
</v-app-bar>
      <slot />
      <v-main style="height: 250px"></v-main>
</v-app>
</template>