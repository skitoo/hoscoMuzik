<template>
    <div id="app">
        <div v-show="!player">
            <p>{{ message }}</p>
            <input v-model="searched">
            <button v-on:click="searchOnItunes" v-on:keyup.enter="searchOnItunes">OK</button>
            <p> {{ resultMessage }}</p>
        </div>
        <grid
            :data="result"
            :columns="gridColumns"
            v-on:listen="changeSong($event)"
            v-on:sort="changeOrder($event)"
            v-show="!player && result.length > 0">
        </grid>
        <audioplayer
            :songArtwork="songArtwork"
            :songUrl="songUrl"
            :songName="songUrl"
            :songArtist="songUrl"
            v-show="player"
            v-on:nextSong="nextSong"
            v-on:prevSong="previousSong"
            v-on:back="backToResult"
            ref="audioplayer">
        </audioplayer>
    </div>
</template>

<script>
import Vue from 'vue';
import axios from 'axios';
import grid from './components/Table';
import audioplayer from './components/AudioPlayer';

export default  {
    name: 'app',
    data () {
        return {
            message: 'Enter your search !',
            searched: '',
            result: [],
            resultCount: 0,
            gridColumns: ['artistName','trackName','collectionName'],
            songUrl: '',
            songArtwork: '',
            songRang: 0,
            audio: '',
            player: false
        }
    },
    methods:{
        searchOnItunes: function()
            {
                var self = this;
                axios.get('https://itunes.apple.com/search?term='+this.searchedTerms)
                .then(function (response) {
                    self.result = [];
                    response.data.results.forEach(song => {
                        self.result.push(song);
                    });
                self.resultCount = response.data.resultCount;
                })
                .catch(function (error) {
                    self.result = error;
                })
            },
        changeSong: function (rang) {
            this.songUrl = this.result[rang].previewUrl;
            this.songArtwork = this.result[rang].artworkUrl100;
            this.songRang = rang;

            var self = this;
            console.log(this.result[rang].trackName);

            this.player = true;
            this.$refs['audioplayer'].initSong();
        },
        changeOrder: function(filteredData){
            this.result = filteredData;
        },
        nextSong: function()
        {
            if (this.songRang < (this.result.length-1))
            {
                this.songRang += 1;
            }
            else{
                this.songRang = 0;
            }
            this.changeSong(this.songRang)
        },
        previousSong: function()
        {
            if (this.songRang > 0)
            {
                this.songRang -= 1;
            }
            else{
                this.songRang = this.result.length-1;
            }
            this.changeSong(this.songRang)
        },
        backToResult: function(){
            this.player = false;
            this.$refs['zikListener'].pause();
        }
    },
    computed: {
        searchedTerms: function () {
            return this.searched.replace(' ','+');
        },
        resultMessage: function () {
            var message = '';
            if (this.resultCount == 0)
            {
                message = 'There is no result.';
            }
            else{
                var plural = '';
                if (this.resultCount > 1)
                {
                    plural ='s';
                }
                message = 'There is '+this.resultCount+ ' result'+plural;
            }
            return message;
        }
    },
    components: {
        grid,
        audioplayer
    }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
