<template>
    <div class="modal-window">
        <div class="start-block">
            <div class="start-block__settings">
                <div class="settings__select">
                    <span>Выберите язык текста:</span>
                    <select id="language">
                        <option value="rus">Русский</option>
                        <option value="eng">English</option>
                    </select>
                </div>
                <div class="settings__select">
                    <span>Выберите размер шрифта:</span>
                    <select id="font-size">
                        <option value="16">16</option>
                        <option value="18">18</option>
                        <option value="20">20</option>
                        <option value="22">22</option>
                    </select>
                </div>
                <div class="settings__select">
                    <span>Выберите тип подсчета ошибок:</span>
                    <select id="error-counting-type">
                        <option value="easy">Робин Гуд</option>
                        <option value="medium">Ад часто ошибающихся</option>
                        <option value="hard">Ад не смотрящих в экран</option>
                        <option value="extreme">Путь самурая</option>
                    </select>
                </div>
            </div>
            <button class="start-block__btn" @click="startTrain">Начать</button>
        </div>
    </div>
</template>

<script>
export default {
    name: "StartWindow",
    methods: {
        startTrain() {
            let lang = document.getElementById("language").value;
            let fontSize = document.getElementById("font-size").value + "px";
            let errorCountingType = document.getElementById("error-counting-type").value;
            let settings = {
                language: lang,
                fontSize: fontSize,
                errorCountingType: errorCountingType
            };
            this.$emit('startTrain', settings);
        }
    }
}
</script>

<style lang="scss" scoped>
    $small-border: 2px solid black;
    @mixin centered-align-flex {
        display: flex;
        align-items: center;
    }

    .modal-window {
        @include centered-align-flex;
        justify-content: center;
        width: 100%;
        height: 100%;
        position: absolute;
        z-index: 1;
        background-color: rgba(128, 128, 128, 0.2);
    }

    .start-block {
        @include centered-align-flex;
        justify-content: space-evenly;
        flex-direction: column;
        width: 40%;
        min-width: 420px;
        height: 40%;
        border: $small-border;
        border-radius: 15px;
        background-color: #fff;
    }

    .start-block__settings {
        width: 80%;
        height: 30%;
        display: flex;
        flex-wrap: wrap;
    }

    .settings__select {
        width: 100%;
        display: flex;
        justify-content: space-between;
        margin-bottom: 5%;
    }

    .start-block__btn {
        margin-top: 5%;
        padding: 5px 10px;
        border: $small-border;
        border-radius: 5px;
        cursor: pointer;
        &:hover {
            background-color: rgba(0, 128, 0, 0.3);
        }
    }
</style>
