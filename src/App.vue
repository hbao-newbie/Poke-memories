<template>
    <MainScreenVue 
        v-if="statusMatch === 'default'" 
        @onStart="onHandleBeforeStart($event)"
    />
    <InteractScreenVue 
        v-if="statusMatch === 'match' "
        :cardsContext="settings.cardsContext"
        @onFinish="onGetResult"
    />
    <ResultScreenVue
        v-if="statusMatch === 'result'"
        :timer="timer"
        @onStartAgain="statusMatch = 'default'"
    />
    <p class="copyright">
        This is game code by vue 3 of RHP team - Edit and fix vue 3.2 by Hoai Bao
    </p>
</template>

<script>
    /**
    *  Có hướng dẫn sử dụng netlify để đẩy code  
    * 
    */


    import { shuffled } from "./utils/array.js";

    import MainScreenVue from "./components/MainScreen.vue";
    import InteractScreenVue from "./components/InteractScreen.vue";
    import ResultScreenVue from "./components/ResultScreen.vue";
    export default {
        components: {
            MainScreenVue,
            InteractScreenVue,
            ResultScreenVue,
        },
        data() {
            return {
                settings: {
                    totalOfBlock: 0,
                    cardsContext: [],
                    startedAt: null,
                },
                statusMatch: "default",
                timer: 0,
            }
        },
        methods: {
            onHandleBeforeStart(config) {
                this.settings.totalOfBlock = config.totalOfBlock;

                const firstCards = Array.from(
                    {length: this.settings.totalOfBlock / 2},
                    (_,i) => i + 1
                );
                const secondCards = [...firstCards];
                const Cards = [...firstCards,...secondCards];
                this.settings.cardsContext = shuffled(shuffled(shuffled(shuffled(Cards))));
                this.settings.startedAt = new Date().getTime();

                this.statusMatch = 'match';
            },
            onGetResult() {
                // get timer
                this.timer = new Date().getTime() - this.settings.startedAt;
                // switch to result component
                this.statusMatch = 'result';
            }
        }
    }
</script>

<style scoped>
    .copyright {
        position: fixed;
        left: 50%;
        transform: translateX(-50%);
        bottom: 1.5rem;
        color: var(--light);
        z-index: 3;
        font-size: 1.5rem;
    }
    .copyright a {
        color: #f4dc26;
    }
</style>