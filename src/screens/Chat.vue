<template>
  <nb-container :style="{ backgroundColor: '#fff' }">
    <KeyboardAwareScrollView
      :resetScrollToCoords="{ x: 0, y: 0 }"
      :scrollEnabled="false"
      >
    <nb-header class="gray" :style="{height: 60, 
        shadowOffset: {  height: 8 },
        shadowColor: 'black',
        shadowOpacity: .8,
        shadowRadius: 10}">
      <nb-left>
        <nb-button
          transparent
          :onPress="leaveChat"
        >
          <nb-icon class="cream" name="arrow-back" />
        </nb-button>
      </nb-left>
        <nb-body>
          <Image resizeMode="center" :style="{marginTop:65}" :source="headerIcon"  />
        </nb-body>
      <nb-right />
    </nb-header>
    <Image :source="chatImage" resizeMode="cover" class="imageContainerChat" />
    <View :style="{ height: 80}">
      <nb-left class="client thumb" :style="{alignSelf: 'flex-start', width:100}">
        <nb-thumbnail large :source="{uri: navigation.getParam('clientImageLink')}" />
      </nb-left>
      <nb-right class="artist thumb" :style="{alignSelf: 'flex-end', width:100}">
        <nb-thumbnail large :source="{uri: navigation.getParam('artistImageLink')}" />
      </nb-right>
    </View>
    <nb-content :style="{height:400}" >
      <scroll-view :style="{flex:1, marginTop: 10}" v-for="(comment, index) in chat" :key="index">
        <nb-left class="clientComment" :style="{alignSelf: 'flex-start'}" v-if="comment.chatClient">           
          <nb-text class="clienttext">
          {{comment.chatClient}}
          </nb-text>        
        </nb-left>
        <nb-right class="artistComment" :style="{alignSelf: 'flex-end'}" v-if="comment.chatArtist">        
          <nb-text class="artisttext">
            {{comment.chatArtist}}
          </nb-text>
        </nb-right>    
      </scroll-view> 
      </nb-content>
      <nb-form left :style="{flex:1, flexDirection: 'row', marginBottom:-425}">
        <nb-item left :style="{alignSelf: 'flex-start', width:280, backgroundColor: 'silver'}">
          <TextInput class="inputBg" :editable="true" :multiline="true" :style="{color:'black', height: 45, fontSize: 20}" v-model="comment.chat_client" type="text" placeholder="  message" />
        </nb-item>
        <nb-button dark id="bringUp" :onPress="submitComment" :style="{marginLeft: 6, alignSelf: 'flex-start'}">
          <nb-text>send</nb-text>
        </nb-button>
      </nb-form>
    </KeyboardAwareScrollView>
  </nb-container>
</template>

<script>
import { ScrollView, TextInput } from "react-native";
import { KeyboardAwareScrollView } from "react-native-keyboard-aware-scroll-view";
import chatImage from "../../assets/wallpaperbg.jpg";
import headerIcon from "../../assets/i.png";
import React from "react";
import store from "../../store";

export default {
  components: {
    KeyboardAwareScrollView
  },
  props: {
    navigation: {
      type: Object
    }
  },
  data: function() {
    return {
      headerIcon: headerIcon,
      CHAT_API_URL: "https://inkswell.herokuapp.com/chat",
      CHAT_API_URL2: "https://inkswell.herokuapp.com/matches/2/chat",
      chatImage: chatImage,
      chat: [],
      timer: "",
      comment: {
        match_id: 2,
        chat_client: "",
        chat_artist: null
      }
    };
  },
  mounted: async function() {
    this.timer = setInterval(() => this.getComments(), 900);
  },
  methods: {
    ref: function() {
      this.scrollView = ref;
    },
    getComments: async function() {
      fetch(this.CHAT_API_URL2)
        .then(res => res.json())
        .then(res => {
          this.chat = res;
          console.log("fetch loop active");
        });
    },
    cancelAutoUpdate: function() {
      clearInterval(this.timer);
    },
    submitComment: async function() {
      await this.chat.push(this.comment);
      this.postComment(this.comment);
      this.comment = {
        match_id: 2,
        chat_client: "",
        chat_artist: null
      };
    },
    postComment() {
      return fetch(this.CHAT_API_URL, {
        headers: new Headers({ "Content-Type": "application/json" }),
        method: "POST",
        body: JSON.stringify(this.comment)
      }).then(response => response.json());
      this.comment.match_id = 2;
      this.comment.chat_client = "";
      this.comment.chat_artist = null;
    },
    leaveChat: function() {
      clearInterval(this.timer);
      this.navigation.navigate("Matches");
    }
  }
};
</script>

<style>
.clienttext {
  color: #fffede;
  font-size: 19;
}
.artisttext {
  color: #383838;
  font-size: 19;
}
#bringUp {
  background-color: silver;
  z-index: 1000;
}
.thumb {
  margin-bottom: 65;
}
.artist {
  margin-top: -87;
  margin-right: 15;
}
.client {
  margin-top: -8;
  margin-left: 15;
}
.gray {
  background-color: #383838;
}
.clientComment {
  background-color: #202020;
  border-color: silver;
  border-width: 1;
  border-radius: 11;
  margin: 3;
  margin-left: 7;
  max-width: 70%;
  padding: 9;
}
.artistComment {
  background-color: #fffede;
  border-color: #202020;
  border-width: 2;
  border-radius: 11;
  margin: 3;
  margin-right: 7;
  max-width: 70%;
  padding: 9;
}
.imageContainerChat {
  margin-top: 60;
  position: absolute;
  z-index: -10;
}
.cream {
  color: #fffede;
}
</style>