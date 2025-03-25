<script>
    const API = "https://api.github.com/users/"

    export default {
        data(){
            return {
                serach: null,
                result: null,
                error: null,
                favorites: new Map()
            };
        },
        computed:{
            isFavorite(){
                return this.favorites.has(this.result.id)
            },
            allFavorites(){
                // para no tener en cuenta el key
                return Array.from(this.favorites.values())
            }
        },
        methods: {
            async doSearch(){
                this.result = this.error = null
                try{
                    const response = await fetch(API + this.search);
                    if (!response.ok) throw new Error("User not found")
                    const data = await response.json();
                    this.result = data;
                }catch(error){
                    this.error = error
                }finally{
                    this.search = null
                }
            },

            addFavorites(){
                this.favorites.set(this.result.id, this.result)
            },

            removeFavorites(){
                this.favorites.delete(this.result.id)
            },

        }
    }
</script>

<template>
    <!-- Fa<vorites -->
    <div id="favorites" class="w3-container">
        <!-- <div v-for="[, favorite] in favorites"> -->
        <span v-for="favorite in allFavorites">
            <a v-bind:href="favorite.blog" target="_blank">
                <img v-bind:src="favorite.avatar_url" class="w3-round" v-bind:alt="favorite.name">
            </a>
        </span>
    </div>
    <br>
    <main class="w3-display-container">
      <div class="w3-container w3-card-4 w3-light-grey">
        <h3>Search User</h3>
        <form v-on:submit.prevent="doSearch">
            <div class="w3-row">
                <div class="w3-threequarter">
                    <input v-model="search" type="text" class="w3-input w3-border w3-round" required placeholder="Users...">
                </div>
                <div class="w3-quarter">
                    <input type="submit" class="w3-button w3-block w3-blue w3-ripple w3-padding" value="Search">
                </div>
            </div><br>

            <!-- Result -->
            <div v-if="result">
                <div class="w3-card-4 w3-blue">
                    <div class="w3-container ">
                        <a v-if="!isFavorite" href="#" class="w3-button w3-right" @click="addFavorites">Add Favorite <span class="w3-yellow  w3-badge w3-small">+</span></a>
                        <a v-else href="#" class="w3-button w3-right" @click="removeFavorites">Remove Favorite <span class="w3-red w3-badge w3-small">-</span></a>
                        <h4>{{ result.name }}</h4>
                    </div>
                    <div class="w3-container w3-row">
                        <img v-bind:src="result.avatar_url" class="w3-round w3-quarter" v-bind:alt="result.name">
                        <p class="w3-threequarter">{{ result.bio }}</p>
                    </div>
                    <div class="w3-container w3-blue">
                        <h5><a v-bind:href="result.blog" target="_blank">{{ result.blog }}</a></h5>
                    </div>
                </div><br>
           </div>
            <!-- Error -->
            <div id="error" class="w3-red" v-if="error">{{error}}</div>
         </form>
      </div>
    </main>
</template>

<style scoped>
    img {
        height: 100px;
        width: 18%;
    }
</style>
