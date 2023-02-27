<script>

    import axios from 'axios'

    export default {
        data() {
            return {
                "restaurants": null,
                "menus": null,
                "today": false,
                "choice": null,
                "dayIndex": null,
                "contacts": null,
                "hu": {
                    "monday": "Hétfő",
                    "tuesday": "Kedd",
                    "wednesday":"Szerda",
                    "thursday": "Csütörtök",
                    "friday": "Péntek",
                    "saturday": "Szombat",
                    "sunday": "Vasárnap"
                }
            }
        },

        mounted() {
            axios.get('/data.json')
                .then(response => {
                    let t = new Date();
                    let n = t.getDay();
                    let today = false;


                    if (!this.choice) {
                        this.choice = response.data.restaurants[0].key;
                    }

                    // @TODO delete this 
                    
                    if (n >= 1 && n <= 5) {
                        today = response.data.menus[this.choice][n-1];
                    }

                    this.dayIndex = n-1;
                    this.today = today;
                    this.restaurants = response.data.restaurants;
                    this.menus = response.data.menus;
                    this.contacts = response.data.contacts;
                })
                .catch(error => {
                    console.log(error)
                })
        },

        methods: {
            press(k) {
                this.choice = k;
            }
        }
    }
</script>

<template>
    <div class="container-fluid border-start border-end border-dark" style="max-width: 768px;">
        <div class="row px-3">
            <div class="col-12 mt-5">
                <div class="d-flex justify-content-center align-items-center">
                    <img src="/img/icons/android-chrome-512x512.png" alt="" class="w-25 rounded-circle float-end">
                    <div class="d-flex align-items-center ms-2 h-100">
                        <h3>Éhes paraszt</h3>
                    </div>
                </div>
            </div>
        </div>

        <div class="row px-3">
            <div class="col pt-5">
                <div class="d-flex justify-content-center align-items baseline">
                    <div class="ps-3" v-for="(e, i) in restaurants" v-if="restaurants !== null" :key="i">
                        <button v-if="e.key == choice"
                            class="btn btn-success p-1" 
                            @click="press(e.key)" 
                            id="{{ e.key }}" 
                            name="{{ e.key }}">{{ e.name }}
                        </button>

                        <button v-else
                            class="btn btn-secondary p-1" 
                            @click="press(e.key)" 
                            id="{{ e.key }}" 
                            name="{{ e.key }}">{{ e.name }}
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <div class="row px-3">
            <div class="col pt-3">
                <div class="d-flex justify-content-center gap-4">
                    <div>
                        <h3>Napi menü</h3>
                    </div>
                    <div class="d-flex flex-column">
                        <a v-bind:href="'tel:' + this.contacts[this.choice]" v-if="contacts !== null">
                            <button class="btn btn-danger">
                                Rendelés
                            </button>
                        </a>
                        <a v-bind:href="'tel:' + this.contacts[this.choice]" v-if="contacts !== null">{{  this.contacts[this.choice] }}</a>
                    </div>
                </div>
            </div>
        </div>

        <div class="row px-3">
            <div class="col-12 col-md-8 pt-3 mx-auto text-center">
                <div v-html="menus[choice][dayIndex].meal" v-if="menus != null && menus[choice][dayIndex] !== undefined"></div>
            </div>
        </div>

        <div class="row px-3">
            <div class="col pt-3">
                <table class="table table-striped">
                    <thead>
                        <tr>
                            <td class="text-center">Nap</td>
                            <td class="text-center">Menü</td>
                        </tr>
                    </thead>

                    <tbody>
                        <tr v-for="(menu, index) in menus[choice].slice(dayIndex+1, 5)" v-if="menus !== null" :key="index">
                            <td>{{ hu[menu.day] }}</td>
                            <td v-html="menu.meal"></td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>