<template>
  <nb-container :style="{ backgroundColor: '#fff' }">
    <nb-header class="gray" :style="{height: 70, shadowOffset: {  height: 8 },
        shadowColor: 'black',
        shadowOpacity: .8,
        shadowRadius: 10}">
      <nb-left>
        <nb-button
          transparent
          :onPress="() => this.props.navigation.navigate('DrawerOpen')"
        >
          <nb-icon class="cream" name="arrow-back" />
        </nb-button>
      </nb-left>
      <nb-body>
        <Image class="front" resizeMode="center" :style="{marginTop:65}" :source="headerIcon"  />
      </nb-body>
      <nb-right />
    </nb-header>    
    <Image :style="{flex:1, width: 400, height: 775}" :source="{uri: clients.client_image_link}" class="imageContainer" />
    <View class="shade">
      <nb-h1 class="cream pads">{{clients.client_name}}</nb-h1>
      <text class="cream pads more">budget: {{clients.budget}}</text>
    </View>
    <View class="description">
      <nb-h2 class="cream">{{clients.client_name}}'s idea:</nb-h2>
      <Text class="cream" :style="{fontSize: 17}">{{clients.description}}</Text>
    </View>
  </nb-container>
</template>

<script>
import { Animated, ScrollView, Image, View, Text } from "react-native";
import Carousel from "react-native-snap-carousel";
import headerIcon from "../../assets/i.png";
import React from "react";
import store from "../../store";

export default {
  data() {
    return {
      headerIcon: headerIcon,
      clients: {}
    };
  },
  props: {
    navigation: {
      type: Object
    }
  },
  mounted: async function() {
    await store.dispatch("getClientUsers");
    this.clients = store.state.clients.clients[3];
  }
};
</script>

<style>
.more {
  margin-left: 100;
}
.pads {
  margin-left: 50;
}
.shade {
  background-color: rgba(34, 34, 34, 0.5);
  margin-top: 80;
  margin-left: -40;
  position: absolute;
  transform: rotate(-11deg);
  width: 160%;
}
.description {
  background-color: rgba(34, 34, 34, 0.5);
  border-radius: 4;
  margin-top: 475;
  margin-left: 90;
  padding: 4;
  position: absolute;
  width: 75%;
}
.front {
  z-index: 1000;
}
.cream {
  color: #fffede;
}
.gray {
  background-color: #383838;
  z-index: 10;
}
.imageContainer {
  margin-top: 50;
  position: absolute;
  z-index: -100;
}
</style>