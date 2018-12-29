<template>
    <div id="audio-template">
        <img :src="songArtwork" v-show="songArtwork!=''">
        <br/>
        <audio controls preload="none" style="width:480px;" id='zikListener' ref='zikListener'>
            <source :src="songUrl" type="audio/mp4" />
            <p>Your browser does not support HTML5 audio.</p>
        </audio>
    <br/>
    <button v-on:click="previousSong"><<</button> <button v-on:click="nextSong">>></button>
    <br/>
        <button v-on:click="backToResult">Back</button>
    </div>
</template>

<script>
import Vue from 'vue';

export default {
  name: 'audioplayer',
  template: '#audio-template',
  props: {
      songArtwork: String,
      songUrl: String,
      songName: String,
      songArtist: String
  },
  methods: {
      initSong: function(){
        var self = this;
        Vue.nextTick(function () {
            self.$refs['zikListener'].load();
            self.$refs['zikListener'].play();
        });
      },
      previousSong: function(){
        this.$emit('prevSong');
        this.initSong();
      },
      nextSong: function(){
        this.$emit('nextSong');
        this.initSong();

      },
      backToResult: function(){
          this.$emit('back');
          this.$refs['zikListener'].pause();
      }
  }
}
</script>
