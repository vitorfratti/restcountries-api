<template>

    <div id="app">
        <header>
            <h1>Where in the world?</h1>
            <button @click="switchTheme">
                <img v-if="this.mode === 'Dark Mode'" src="./assets/moon-light.png" alt="moon">
                <img v-else src="./assets/sun.png" alt="moon">
                {{ mode }}
            </button>
        </header>
        <nav>
            <section v-if="this.section === 1" class="filters__container">
                <div class="search__container">
                    <button @click="buscar"><img src="./assets/search.png" alt="search"></button>
                    <input @keypress="buscar" @keyup.enter="buscar" type="text" v-model="this.input" placeholder="Search for a country...">
                </div>
                <select @change="filtrar" v-model="select">
                    <option value="" disabled selected>Filter by Region</option>
                    <option value="All">All</option>
                    <option value="Africa">Africa</option>
                    <option value="America">America</option>
                    <option value="Asia">Asia</option>
                    <option value="Europe">Europe</option>
                    <option value="Oceania">Oceania</option>
                </select>
            </section>
            <section v-if="this.section === 2" class="back__container">
                <button @click="closeDetails">
                    <img v-if="this.mode === 'Dark Mode'" src="./assets/back-light.png" class="light-back" alt="back">
                    <img v-else src="./assets/back.png" class="dark-back" alt="back">
                    Back
                </button>
            </section>
        </nav>
        <main>
            <section v-if="this.section === 1" class="countries__container">
                <div @click="openDetails(index)" v-for="(country, index) in countries" :key="country" :index="index" class="country">
                    <div class="image__container">
                        <img :src="country.flags.png" alt="flag">
                    </div>
                    <div class="info__container">
                        <h1>{{ country.name }}</h1>
                        <h2>Population: {{ country.population.toLocaleString('pt-BR') }}</h2>
                        <h2>Region: {{ country.region }}</h2>
                        <h2 v-if="country.capital !== undefined">Capital: {{ country.capital }}</h2>
                        <h2 v-else>Capital: No capital</h2>
                    </div>
                </div>
            </section>
            <section v-if="this.section === 2" class="details__container">
                <div v-for="(country, index) in details" :key="country" :index="index" class="details__content">
                    <div class="details__content__flag">
                        <img :src="country.flags.png" alt="flag">
                    </div>
                    <div class="details__content__infos">
                        <h1>{{ country.name }}</h1>
                        <span>
                            <div>
                                <h3>Native Name: {{ country.nativeName }}</h3>
                                <h3>Population: {{ country.population.toLocaleString('pt-BR') }}</h3>
                                <h3>Region: {{ country.region }}</h3>
                                <h3>Sub Region: {{ country.subregion }}</h3>
                                <h3 v-if="country.capital !== undefined">Capital: {{ country.capital }}</h3>
                                <h3 v-else>Capital: No capital</h3>
                            </div>
                            <div>
                                <h3>Top Level Domain: {{ country.topLevelDomain[0] }}</h3>
                                <h3 v-for="country in country.currencies" :key="country">Currencies: {{ country.code }}</h3>
                                <h3>Languages: {{ country.languages[0].name }}</h3>
                            </div>
                        </span>
                        <div>
                            <h3>Border Countries: </h3><h3 class="borders" v-for="country in country.borders" :key="country">{{ country }}</h3>
                        </div>
                    </div>
                </div>
            </section>
        </main>
    </div>
  
</template>

<script>

import axios from "axios"

export default {
    name: 'App',
    data() {
        return {
            input: null,
            select: '',
            countries: [],
            details: [],
            section: 1,
            url: null,
            mode: 'Dark Mode'
        }
    },
    methods: {
        puxarDados() {
            this.url = 'https://restcountries.com/v2/all/'
            axios.get(this.url).then(res => {
                this.countries = res.data
                this.countries.forEach(countries => {
                    if(countries.borders === undefined) {
                        countries.borders = '0'
                    } else {
                        return countries
                    }
                })
            })
        },
        buscar() {
            if(this.input !== '') {
                this.url = 'https://restcountries.com/v2/name/'+this.input
                axios.get(this.url).then(res => {
                    this.countries = res.data
                })
            } else {
                this.url = 'https://restcountries.com/v2/all'
                axios.get(this.url).then(res => {
                    this.countries = res.data
                })
            }
        },
        filtrar() {
            this.url = 'https://restcountries.com/v2/region/'+this.select
            if(this.select === 'All') {
                this.url = 'https://restcountries.com/v2/all/'
            }
            axios.get(this.url).then(res => {
                this.countries = res.data
            })
        },
        openDetails(index) {
            this.details.push(this.countries[index])
            this.section = 2
        },
        closeDetails() {
            this.details = []
            this.section = 1
        },
        switchTheme() {
            document.querySelector('html').classList.toggle('light-mode')
            if(this.mode === 'Dark Mode') {
                this.mode = 'Light Mode'
            } else if(this.mode === 'Light Mode') {
                this.mode = 'Dark Mode'
            }
        }
    },
    mounted() {
        this.puxarDados()
    }
}

</script>

<style lang="scss">

@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@100;300;400;500;700;900&display=swap');

:root {
    --primaryBackground: #212e37;
    --primaryText: #fbffff;
    --primaryInput: #101920;
    --primaryElements: #2b3743;
}

.light-mode:root {
    --primaryBackground: #fafafa;
    --primaryText: #1a1a1a;
    --primaryInput: #505050;
    --primaryElements: #ffffff;
}

* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: 'Roboto', sans-serif;
}

body {
    background: var(--primaryBackground);
}

header {
    width: 100%;
    height: 6rem;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 5%;
    box-shadow: 0 0 1px var(--primaryInput);
    background: var(--primaryElements);
    position: fixed;
    z-index: 10;

    h1 {
        color: var(--primaryText);
        font-size: 1.8rem;
        font-weight: 800;
    }

    button {
        width: 7.8rem;
        display: flex;
        justify-content: space-between;
        align-items: center;
        background: none;
        border: none;
        font-size: 1.1rem;
        font-weight: 600;
        cursor: pointer;
        transition: 0.2s;
        color: var(--primaryText);

        &:hover {
            opacity: 0.7;
            transition: 0.2s;
        }

        img {
            width: 1.5rem;
        }
    }
}

nav {
    width: 100%;
    padding: 9rem 5% 3rem 5%;
    background: var(--primaryBackground);

    section.filters__container {
        display: flex;
        justify-content: space-between;
        align-items: center;

        .search__container {
            width: 30rem;
            height: 3.5rem;
            display: flex;
            justify-content: flex-start;
            box-shadow: 0 0 1px var(--primaryInput);
            border-radius: 8px;
            background: var(--primaryElements);
            padding-right: 1rem;

            button {
                display: flex;
                justify-content: center;
                align-items: center;
                background: none;
                border: none;
                cursor: pointer;
                transition:0.2s;
                width: 15%;
                color: var(--primaryText);

                &:hover {
                    opacity: 0.7;
                    transition: 0.2s;
                }

                img {
                    width: 1.1rem;
                }
            }

            input {
                width: 85%;
                border: none;
                font-size: 1rem;
                font-weight: 500;
                border-radius: 8px;
                color: var(--primaryText);
                background: var(--primaryElements);

                &:focus {
                    outline: none;
                }

                &::placeholder {
                    font-weight: 400;
                }
            }
        }

        select {
            width: 15rem;
            height: 3.5rem;
            padding: 1rem;
            border: none;
            box-shadow: 0 0 1px var(--primaryInput);
            border-radius: 8px;
            border: none;
            font-size: 1rem;
            font-weight: 500;
            color: var(--primaryText);
            background: var(--primaryElements);
        }
    }

    section.back__container {
        button {
            background: var(--primaryElements);
            width: 8rem;
            height: 3.5rem;
            padding: 0 1.5rem;
            border: none;
            box-shadow: 0 0 1px var(--primaryInput);
            border-radius: 8px;
            border: none;
            font-size: 1rem;
            font-weight: 500;
            color: var(--primaryText);
            display: flex;
            justify-content: space-between;
            align-items: center;
            cursor: pointer;

            img.dark-back {
                width: 1.5rem;
                transform: rotate(180deg);
            }

            img.light-back {
                width: 1.5rem;
                transform: rotate(90deg);
            }
        }
    }
}

main {
    padding: 0 5%;
    background: var(--primaryBackground);

    section.countries__container {
        display: flex;
        justify-content: space-between;
        align-items: center;
        flex-wrap: wrap;

        .country {
            width: 23rem;
            background: var(--primaryElements);
            margin: 0 0 5rem 0;
            border-radius: 8px;
            box-shadow: 0 0 1px var(--primaryInput);
            transition: 0.2s;

            &:hover {
                cursor: pointer;
                transition: 0.2s;
            }


            .image__container {
                width: 100%;
                height: 12rem;
                border-radius: 8px 8px 0 0;

                img {
                    width: 100%;
                    height: 100%;
                    border-radius: 8px 8px 0 0;
                }
            }

            .info__container {
                padding: 1.8rem;
                height: 55%;

                h1 {
                    color: var(--primaryText);
                    font-size: 1.5rem;
                    font-weight: 800;
                    margin-bottom: 1.5rem;
                }

                h2 {
                    color: var(--primaryText);
                    font-size: 1rem;
                    font-weight: 400;
                    margin: 0.8rem 0;
                }
            }
        }
    }

    section.details__container {
        width: 100%;
        display: flex;
        justify-content: space-between;
        align-items: center;

        .details__content {
            display: flex;
            justify-content: space-between;
            width: 100%;

            .details__content__flag {
                width: 35%;
                height: 25rem;
                box-shadow: 0 0 1px var(--primaryInput);

                img {
                    width: 100%;
                    height: 100%;
                }
            }

            .details__content__infos {
                display: grid;
                width: 55%;
                height: 25rem;

                h1 {
                    font-size: 2.5rem;
                    color: var(--primaryText);
                }

                span {
                    display: flex;
                    justify-content: space-between;

                    div {
                        display: block;
                        width: 45%;

                        h3 {
                            font-size: 1.1rem;
                            font-weight: 400;
                            color: var(--primaryText);
                            margin: 2rem 0;
                        }
                    }
                }

                div {
                    display: flex;
                    justify-content: flex-start;
                    align-items: center;
                    flex-wrap: wrap;

                    h3 {
                        color: var(--primaryText);
                    }

                    h3.borders {
                        cursor: default;
                        margin-left: 1rem;
                        padding: 0.6rem;
                        border: 1px solid var(--primaryText);
                        border-radius: 10px;
                        color: var(--primaryText);
                        margin: 0.5rem 0 0.5rem 1rem;
                    }
                }
            }
        }
    }
}

@media only screen and (max-width: 835px) {

    section.countries__container {
        justify-content: center !important;
    }

    section.filters__container {
        display: block !important;

        .search__container {
            width: 100% !important;
            margin-bottom: 1rem !important;   
        }
    } 

}

@media only screen and (max-width: 975px) {

    section.details__container {
        padding-bottom: 12rem !important;
    }

    .details__content {
        display: block !important;

        .details__content__flag {
            width: 100% !important;
            height: 15rem !important;
        }

        .details__content__infos {
            width: 100% !important;
            margin-top: 3rem !important;
        }
    }

}

@media only screen and (max-width: 550px) {

    section.details__container {
        padding-bottom: 20rem !important;

        .details__content__infos {
            span {
                display: block !important;

                div {
                    width: 100% !important;
                }
            }
        }
    }

}

@media only screen and (max-width: 480px) {

    header {
        h1 {
            font-size: 1.5rem !important;
        }
    }

}

</style>
