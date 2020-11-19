<template>
    <div class="container">
        <div>
            <div class="panel top-left-panel" ref="tlp"
                 @click="panelClicked($event.target)"></div>
            <div class="panel top-right-panel" ref="trp"
                 @click="panelClicked($event.target)"></div>
        </div>
        <div>
            <div class="panel bottom-left-panel" ref="blp"
                 @click="panelClicked($event.target)"></div>
            <div class="panel bottom-right-panel" ref="brp"
                 @click="panelClicked($event.target)"></div>
        </div>
        <div>
            <h2>Round:
                <span>{{ round }}</span>
            </h2>
        </div>
        <div class="game-options">
            <h2 class="level-selection">Select Level:</h2>
            <label>
                <input type="radio" value="1500" v-model="timeout">Easy
            </label>
            <br>
            <label>
                <input type="radio" value="1000" v-model="timeout">Medium
            </label>
            <br>
            <label>
                <input type="radio" value="400" v-model="timeout">Difficult
            </label>
        </div>
        <div>
            <button @click="getGamingSequence">Start</button>
        </div>
    </div>
</template>

<script>
    export default {
        name: "GameBoard",
        data() {
            return {
                timeout: '1500',
                need_timeout: true,
                round: 0,
                clickAllowed: false,
                sequence: null,
                copy: null,
                panels: []
            }
        },
        methods: {
            // функция для получения случайной кнопки
            getRandomPanel() {
                return this.panels[Math.floor(Math.random() * 4)];
            },

            // функция для озвучивания
            playSound(sound) {
                let audio = new Audio(sound);
                audio.play();
            },

            // функция, которая соотносит кнопку с характерным звуком
            mapPanelWithSound(panel) {
                if (panel === this.$refs.tlp) {
                    return require(`@/assets/sounds/1.mp3`)
                } else if (panel === this.$refs.trp) {
                    return require(`@/assets/sounds/2.mp3`)
                } else if (panel === this.$refs.blp) {
                    return require(`@/assets/sounds/3.mp3`)
                } else {
                    return require(`@/assets/sounds/4.mp3`)
                }
            },

            // функция для подсвечивания кнопок
            doSomeFlashing(panel, timeout) {
                timeout = (this.need_timeout) ? timeout = Number(this.timeout) : 250;
                return new Promise(resolve => {
                    panel.className += ' active';
                    setTimeout(() => {
                        panel.className = panel.className.replace(
                            ' active',
                            ''
                        );
                        setTimeout(() => {
                            resolve();
                        }, 250)
                    }, timeout);
                })
            },

            // функция, срабатывающая при нажатии на квадрат
            panelClicked(event) {
                if (!this.clickAllowed) return;
                this.need_timeout = false;
                let expectedPanel = this.copy.shift();
                this.playSound(this.mapPanelWithSound(event));
                this.doSomeFlashing(event);
                if (expectedPanel === event) {
                    if (this.copy.length === 0) {
                        //начинается новый раунд
                        this.round += 1;
                        this.sequence.push(this.getRandomPanel());
                        this.copy = [...this.sequence];
                        setTimeout(() => {
                            this.getGamingSequence();
                        }, 1000);
                    }
                } else {
                    // выводим сообщение о проигрыше
                    setTimeout(() => {
                        alert('Game over!');
                    }, 500);
                    this.round = 0;
                    this.sequence = [this.getRandomPanel()];
                    this.copy = [...this.sequence];
                }
                this.need_timeout = true;
            },

            // асинхронная функция для получения игровой последовательности
            async getGamingSequence() {
                this.clickAllowed = false;
                try {
                    for (let panel of this.sequence) {
                        this.playSound(this.mapPanelWithSound(panel));
                        await this.doSomeFlashing(panel);
                    }
                    this.clickAllowed = true;
                } catch (e) {
                    console.log(e)
                }
            }
        },

        // используем хук mounted(), чтобы избежать ошибки определения
        // переменной с использованием функции и элементов DOM дерева
        // до их непосредственной инициализации
        mounted() {
            this.panels.push(this.$refs.tlp, this.$refs.trp,
                this.$refs.blp, this.$refs.brp);
            this.sequence = [this.getRandomPanel()];
            this.copy = [...this.sequence];
        }
    }
</script>

<style scoped>
    input[type="radio"] {
        margin-right: 10px;
    }

    button {
        margin-top: 30px;
        width: 100px;
        height: 40px;
        cursor: pointer;
        background: #6DABE8;
        border: none;
        border-radius: 10px 10px 10px 10px;
        font-size: 20px;
    }

    .level-selection {
        margin-bottom: 5px;
    }

    .game-board {
        margin-right: 300px;
    }

    .panel {
        width: 150px;
        height: 150px;
        opacity: 0.6;
        box-shadow: 0 0 3px 0 black;
        display: inline-block;
        cursor: pointer;
    }

    .panel:hover {
        box-shadow: inset 0 0 0 3px black;
        box-sizing: border-box;
    }

    .bottom-left-panel {
        border-bottom-left-radius: 200px;
        background-color: #FEEF33;
    }

    .bottom-right-panel {
        border-bottom-right-radius: 200px;
        background-color: #BEDE15;
    }

    .top-left-panel {
        border-top-left-radius: 200px;
        background-color: dodgerblue;
    }

    .top-right-panel {
        border-top-right-radius: 200px;
        background-color: #FF5643;
    }

    .active {
        background-color: white;
    }
</style>
