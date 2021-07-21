<template>
    <base-container title="Base Project">
        <div class="spinner" v-show="spinner == true">
            <div class="cube1"></div>
            <div class="cube2"></div>
        </div>
        <input type="text" width="200" v-model="search"> Search for some text here
        <div v-for="result in searchedResults" :key="result.id">
            <p>Title: {{ result.title }} <br> Body: {{ result.body }} </p>
            <input type="checkbox" :value="result.title" v-model="checkedBox"> Check
        </div>
    </base-container>
    <base-container title="Checked posts">
        <p v-show="checkedBox.length < 1">No checked item...</p>
        <p v-for="(checked, index) in checkedBox" :key="checked.id">{{ index + 1 }} <strong>Title of selected post:</strong> {{ checked }}<br></p>
        <button @click="saveData">Save this data</button>
    </base-container>
    <base-container title="Show saved post">
        <div style="width: 100%; display: flex; justify-content: space-between;" v-for="(saved,index) in savedPosts" :key="saved.id">
            <p style="width: 90%">{{ index + 1}} {{ saved.title.join(', ') }}</p>
        </div>
        <button @click="getSavedPost">Load posts</button>
    </base-container>
</template>

<script>

import BaseContainer from './components/BaseContainer.vue';
import axios from 'axios';

export default {
    components: {
        BaseContainer,
    },
    data() {
        return {
            results: [],
            search: '',
            searchedWord: '',
            checkedBox: [],
            savedPosts: [],
            spinner: false,
        }
    },
    computed: {
        searchedResults() {
            const matchedWord = this.results.filter(el => el.title.toLowerCase().includes(this.search.toLowerCase()) || el.body.toLowerCase().includes(this.search.toLowerCase()));
            return matchedWord;
        },
    },
    methods: {
        saveData() { 
            axios.post('https://api-placeholder-default-rtdb.europe-west1.firebasedatabase.app/posts.json', this.checkedBox)
                .then(response => {
                    console.log(response.data);
                    this.checkedBox = [];
                })
                .catch(e => {
                    console.log(e);
                })
        },
        gettingPost(res) {
                this.savedPosts = [];
                this.spinner = false;
                const data = res.data;
                const results = [];
                for(const id in data) {
                    results.push(data[id]);
                }
                results.forEach((el,index) => {
                    this.savedPosts.push({
                        id: index,
                        title: el
                    });
                })
                console.log(this.savedPosts);
        },
        getSavedPost() {
            this.spinner = true;            
            axios.get('https://api-placeholder-default-rtdb.europe-west1.firebasedatabase.app/posts.json')
                .then(response => {
                    this.gettingPost(response);
                })
                .catch(e => {
                    console.log(e);
                })
        },
    },
    created() {
        this.checkedBox = [];
        this.spinner = true;
        axios.get('https://jsonplaceholder.typicode.com/posts')
            .then(response => {
                this.spinner = false;
                const data = response.data;
                const results = [];
                for(const id in data) {
                    results.push({
                        title: data[id].title,
                        body: data[id].body
                    })
                }
                this.results = results;
            })
            .catch(e => {
                console.log(e);
            })
        axios.get('https://api-placeholder-default-rtdb.europe-west1.firebasedatabase.app/posts.json')
                .then(response => {
                    this.gettingPost(response);
                })
                .catch(e => {
                    console.log(e);
                })
    }
}
</script>

<style>
* {
  box-sizing: border-box;
}

html {
  font-family: sans-serif;
}

body {
  margin: 0;
}
.deleteBtn{
    width: 10%;
    height: 30px;
    border-radius: 10px;
    border: 1px solid red;
    background-color: white;
    color: red;
    margin: 16px 0px;
    transition: .5s ease-in-out;
}
.deleteBtn:hover{
    background-color: red;
    color: white;
    border: 1px solid white;
}
.spinner {
  margin: 100px auto;
  width: 40px;
  height: 40px;
  position: fixed;
}

.cube1, .cube2 {
  background-color: dodgerblue;
  width: 15px;
  height: 15px;
  position: fixed;
  top: 50%;
  left: 50%;
  
  -webkit-animation: sk-cubemove 1.8s infinite ease-in-out;
  animation: sk-cubemove 1.8s infinite ease-in-out;
}

.cube2 {
  -webkit-animation-delay: -0.9s;
  animation-delay: -0.9s;
}

@-webkit-keyframes sk-cubemove {
  25% { -webkit-transform: translateX(42px) rotate(-90deg) scale(0.5) }
  50% { -webkit-transform: translateX(42px) translateY(42px) rotate(-180deg) }
  75% { -webkit-transform: translateX(0px) translateY(42px) rotate(-270deg) scale(0.5) }
  100% { -webkit-transform: rotate(-360deg) }
}

@keyframes sk-cubemove {
  25% { 
    transform: translateX(42px) rotate(-90deg) scale(0.5);
    -webkit-transform: translateX(42px) rotate(-90deg) scale(0.5);
  } 50% { 
    transform: translateX(42px) translateY(42px) rotate(-179deg);
    -webkit-transform: translateX(42px) translateY(42px) rotate(-179deg);
  } 50.1% { 
    transform: translateX(42px) translateY(42px) rotate(-180deg);
    -webkit-transform: translateX(42px) translateY(42px) rotate(-180deg);
  } 75% { 
    transform: translateX(0px) translateY(42px) rotate(-270deg) scale(0.5);
    -webkit-transform: translateX(0px) translateY(42px) rotate(-270deg) scale(0.5);
  } 100% { 
    transform: rotate(-360deg);
    -webkit-transform: rotate(-360deg);
  }
}

</style>