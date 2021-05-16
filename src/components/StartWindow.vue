<template>
    <div class="modal-window">
        <div class="start-block">
            <div class="start-block__settings">
                <div class="settings__select">
                    <span class="setting-text">Выберите язык текста:</span>
                    <select id="language">
                        <option value="rus">Русский</option>
                        <option value="eng">English</option>
                    </select>
                </div>
                <div class="settings__select">
                    <span class="setting-text">Выберите размер шрифта:</span>
                    <select id="font-size">
                        <option value="16">16</option>
                        <option value="18">18</option>
                        <option value="20">20</option>
                        <option value="22">22</option>
                    </select>
                </div>
                <div class="settings__select">
                    <span class="setting-text">Выберите тип подсчета ошибок:</span>
                    <select id="error-counting-type">
                        <option value="easy">Доверительный</option>
                        <option value="medium">Справедливый</option>
                        <option value="hard">Препод по русскому</option>
                        <option value="extreme">Адский путь самурая</option>
                    </select>
                    <div class="help-icon">
                        <img src="../assets/help-mark.png" class="help-icon__image" alt="Подробнее"/>
                        <div class="help-icon__text">
                            <p>
                                Доверительный: Учитываем ваши ошибки относительно всего текста.
                                Ошибкой считается первый неправильно введенный символ. 
                                (точность = количество правильных символов всего текста/длину текста)
                            </p>
                            <br/>
                            <p>
                                Справедливый: Учитываем ваши ошибки относительно введенного текста. 
                                Исправленные символы СЧИТАЮТСЯ правильными. 
                                (точность = количество правильных символов/
                                <span class="red-text">количество нажатых клавиш</span>)
                            </p>
                            <br/>
                            <p>
                                Препод по русскому: Учитываем ваши ошибки относительно введенного текста. 
                                Исправленные символы <span class="red-text">не считаются</span> <span class="green-text">правильными</span>. 
                                (точность = количество правильных символов/количество всех ранее введенных 
                                <span class="green-text">правильных</span> символов)
                            </p>
                            <br/>
                            <p>
                                Адский путь самурая: Никаких пощад!
                                Учитываем ваши ошибки относительно введенного текста. 
                                Исправленные символы <span class="red-text">не считаются</span> <span class="green-text">правильными</span>. 
                                (точность = количество <span class="green-text">правильных</span> символов/
                                <span class="red-text">количество нажатых клавиш</span>)
                            </p>
                        </div>
                    </div>
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
    $small-border: 2px solid rgba(0, 0, 0, 0.8);
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
        min-height: 280px;
        border: $small-border;
        box-shadow: 0 0 30px 20px rgba(0,0,0,0.5);
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
        position: relative;
        width: 100%;
        display: flex;
        justify-content: space-between;
        margin-bottom: 5%;
    }

    .help-icon {
        position: absolute;
        cursor: pointer;
        right: -30px;
        width: 20px;
        height: 20px;
    }

    .start-block__btn {
        margin-top: 5%;
        padding: 5px 15px;
        border: $small-border;
        border-radius: 5px;
        cursor: pointer;
        &:hover {
            background-color: rgba(0, 128, 0, 0.4);
            box-shadow: 0 0 12px 4px rgb(128, 128, 128);
        }
    }

    select {
        cursor: pointer;
        padding: 5px;
        border-radius: 5px;
        border: 2px solid gray;
        &:hover {
            box-shadow: 0 0 10px 4px rgba(0, 0, 255, 0.3);
        }
    }

    .setting-text {
        @include centered-align-flex;
    }

    .help-icon {
        &:hover {
            .help-icon__text {
                display: block;
            }
        }
    }

    .help-icon__image {
        width: 100%;
    }

    .help-icon__text {
        position: absolute;
        display: none;
        top: 0;
        padding: 5px;
        left: 10px;
        width: 200px;
        height: 230px;
        border: $small-border;
        border-radius: 5px;
        overflow-y: scroll;
        background-color: rgb(170, 226, 248);
        &:hover {
            display: block;
        }
        &::-webkit-scrollbar {
            width: 0;
        }
    }

    .red-text {
        color: red;
    }

    .green-text {
        color: green;
    }

    @media only screen and (max-width: 480px) {
        * {
            font-size: 20px;
        }
        .start-block {
            min-width: 100%;
            height: 100%;
            border: none;
            border-radius: 0;
        }

        .start-block__settings {
            height: 60%;
        }

        .start-block__btn {
            width: 30%;
            height: 8%;
        }

        .settings__select {
            flex-flow: row wrap;
        }

        select {
            width: 100%
        }

        .help-icon {
            top: 15px
        }

        .help-icon__text {
            width: 300px;
            left: -300px;
        }
    }
</style>
