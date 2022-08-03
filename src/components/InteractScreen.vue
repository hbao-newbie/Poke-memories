<template>
    <div class="screen">
        <div class="screen__inner"
            :style="{
                width: `${ 
                    ((((920 - (16 * 4)) / Math.sqrt(cardsContext.length) - 16) * 3) / 4 + 16)
                    * Math.sqrt(cardsContext.length)
                }px`
            }"
        >
            <CardVue 
                v-for="(card, index) in cardsContext"
                :key="index"
                :imgBackFaceUrl="`images/${card}.png`"
                ref="card"
                :card="{index, value: card}"
                :cardsContext="cardsContext"
                @onFlip="checkRules($event)"
            />
        </div>
    </div>
</template>

<script>
    import CardVue from "./Card.vue";
    export default {
        props: {
            cardsContext: {
                type: Array,
                default: function() {
                    return [];
                }
            }
        },
        components: {
            CardVue,
        },
        data() {
            return {
                rules: [],
            }
        },
        methods: {
            checkRules(card) {
                if (this.rules.length === 2) return false;
                this.rules.push(card);
                if (
                    this.rules.length === 2
                    && this.rules[0].value === this.rules[1].value
                ) {
                    console.log('Right...');
                    // add class disable into component card
                    this.$refs.card[this.rules[0].index].onEnbleDisableMode();
                    this.$refs.card[this.rules[1].index].onEnbleDisableMode();

                    const disableElements = document.querySelectorAll(".screen .card.disabled");

                    if (disableElements && disableElements.length === this.cardsContext.length - 2 ){
                        setTimeout(() => {
                            this.$emit("onFinish");
                        }, 1000);
                    }
                    // reset rules become 0
                    this.rules = [];
                }
                else if (
                    this.rules.length === 2
                    && this.rules[0].value !== this.rules[1].value
                ) {
                    // close two cards
                    console.log('Wrong...');
                    // create timeout
                    setTimeout(() => {
                        this.$refs.card[this.rules[0].index].onFlipBackCard();
                        this.$refs.card[this.rules[1].index].onFlipBackCard();
                        // reset rules become 0
                        this.rules = [];
                    }, 800);
                    // Một chút chỉnh sửa cú pháp cho hợp lệ với vue 3.2

                } else return false;
            }
        }
    }
</script>

<style>
    .screen {
        width: 100%;
        height: 100vh;
        position: absolute;
        top: 0;
        left: 0;
        z-index: 2;
        background-color: var(--dark);
        color: var(--light);
    }

    .screen__inner {
        display: flex;
        flex-wrap: wrap;
        margin: 2rem auto;
    }
</style>