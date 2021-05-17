<template>
    <div class="wrapper">
        <div class="text-container">
            <div  v-bind:class="{fixStringField: displayType == 'stringField'}">
                <div id="user-text" v-bind:class="[
                    {'text-containter__user-text': displayType == 'textField'}, 
                    {'string-text-containter__user-text': displayType == 'stringField'}]"
                >{{userText}}<span v-bind:class="[                //Странные переносы в разметке,
                                        {error: isErrorSpanStyle},//т.к. переносы строк считаются за пробельный символ
                                        'current-letter',
                                        {'stringLetterSpan': displayType == 'stringField'}]"
                             >{{fieldText[this.curLetterIndex]}}</span>
                </div>
            </div>
            <div v-bind:class="[
                {'text-containter__text': displayType == 'textField'}, 
                {'string-text-containter__text': displayType == 'stringField'}]"
            >{{curFieldText}}</div>
        </div>
        <div class="stats-block-container">
            <StatsBlock 
            :statsInfo="statsInfo"
            :userRightTextLength="curLetterIndex"
            />
        </div>
        <input type="text" id="trigger-mobile-keyboard" autofocus/>
    </div>
</template>

<script>
import StatsBlock from "./StatsBlock.vue";
export default {
    $el: "user-text",
    name: "Field",
    components: {
        StatsBlock
    },
    props: {
        displayType: String,
        fieldText: String,
        fontSize: String,
        errorCountingType: String
    },
    data() {
        return {
            fieldType: this.displayType,
            curLetterIndex: 0,
            userText: "",
            inputText: "",
            previousText: "",
            curFieldText: this.fieldText,
            isErrorSpanStyle: false,
            isFirstAttempt: true,//Для подсчета "ошибок первого раза"
            statsInfo: {
                fullTextLength: this.fieldText.length,//длина всего текста
                firstTimeErrorAmount: 0,//"ошибка первого раза" - как только ошиблись, счетчик+=1, при дальнейших ошибках не увеличиваем
                totalErrorAmount: 0,//количество ошибок всего (учитывая, что ошибка после неправильно введенного символа - тоже ошибка)
                errorCountingType: this.errorCountingType,//способ, по которому считаем точность
                firstTimeRightAmount: 0,
                startInputTime: 0,
                rightInputTimestamp: 0
            }
        }
    },
    mounted: function() {
        let textStyles = document.getElementsByClassName("text-container")[0];
        textStyles.style.fontSize = this.fontSize;
        const isMobile = /Mobile|webOS|BlackBerry|IEMobile|MeeGo|mini|Fennec|Windows Phone|Android|iP(ad|od|hone)/i.test(navigator.userAgent);//детектим мобилку
        this.startTraining(isMobile);
    },
    methods: {
        startTraining(isMobile) {
            let inputTag = document.getElementById("trigger-mobile-keyboard");
            inputTag.addEventListener('compositionupdate', (e) => {//костыль для очистки автодополнения на телефоне
                if (this.statsInfo.startInputTime == 0) {          //Нужен, ибо никакие события адекватно не отслеживают этот ввод...
                    this.statsInfo.startInputTime = Date.now();
                }
                this.checkLetter(e.data);
                inputTag.blur();
                window.getSelection().removeAllRanges();
                setTimeout(() => {
                    inputTag.focus();
                }, 10);
            });
            let eventType = isMobile ? "textInput" : "keydown";//для мобилок keydown/keypress - баганый (всегда 229 код символа)
            document.addEventListener("click", function() {
                document.getElementById("trigger-mobile-keyboard").focus();//триггер клавиатуры
            });
            document.addEventListener(eventType, (e)=>{
                if (this.statsInfo.startInputTime == 0) {
                    this.statsInfo.startInputTime = Date.now();
                }
                if (eventType=="keydown"&&/^[a-zA-Zа-яА-ЯЁё,.?!-:;() ]$/.test(e.key)) {//ввели букву на десктопе
                    this.checkLetter(e.key);
                } else if (e.key==="Backspace") {//блок исправления стиля span
                    this.isErrorSpanStyle = false;
                } else if (eventType=="textInput"&&/^[a-zA-Zа-яА-ЯЁё,.?!-:;() ]$/.test(e.data)){//буква с мобилки
                    this.checkLetter(e.data);
                }
            });
        },
        isCorrectLetter(userInputKey) {
            return userInputKey === this.fieldText[this.curLetterIndex] ? true : false;
        },
        addFirstTimeAmount(isCurrentLetterCorrect) {//логика отслеживания первой попытки ввода символа пользователем
            let info = this.statsInfo;//нужна для подсчета "ошибок первого ввода"
            isCurrentLetterCorrect ? info.firstTimeRightAmount += 1 : info.firstTimeErrorAmount += 1;
        },
        rightUserInputAction(userInputKey) {
            this.isErrorSpanStyle = false;
            this.updateMessage(userInputKey);
            this.curLetterIndex+=1;
            this.isFirstAttempt = true;//обновляем "первая попытка?" для следующего символа
        },
        wrongUserInputAction() {
            this.isErrorSpanStyle = true;
            this.statsInfo.totalErrorAmount += 1;
            this.isFirstAttempt = false;
        },
        checkLetter(userInputKey) {
            let isCurrentLetterCorrect = this.isCorrectLetter(userInputKey);
            if (this.isFirstAttempt) {
                this.addFirstTimeAmount(isCurrentLetterCorrect);
            }
            if (isCurrentLetterCorrect) {
                this.statsInfo.rightInputTimestamp = Date.now();
                this.rightUserInputAction(userInputKey);
            } else {
                this.wrongUserInputAction();
            }
        },
        updateMessage: async function (userInputKey) {
            if (this.displayType == "stringField") this.curFieldText = this.curFieldText.slice(1);
            this.userText += userInputKey;
            await this.$nextTick()
        }
    }
}
</script>

<style lang="scss" scoped>
    @mixin fontStyle {
        font-family: Arial,Tahoma,Verdana,sans-serif;
        line-height: 30px;
        font-weight: 700;
        letter-spacing: 3px;
        word-break: break-all;
    }

    $textBlockPadding: 15px;

    .wrapper {
        display: flex;
        flex-flow: row wrap;
        justify-content: center;
        margin-top: 10%;
    }

    .text-container {
        position: relative;
        display: flex;
        width: 80%;
        border: 2px solid gray;
        border-radius: 15px;
        box-shadow: 0px 0px 10px 5px gray;
        background-color: white;
        padding: $textBlockPadding;
    }

    .text-containter__text {
        @include fontStyle;
        opacity: 0.3;
    }

    .string-text-containter__text {
        @include fontStyle;
        white-space: pre;
        width: 50%;
        overflow: hidden;
        opacity: 0.3;
    }

    .string-text-containter__user-text {
        @include fontStyle;
        white-space: pre;
        float: right;
        color: green;
    }

    .text-containter__user-text {
        @include fontStyle;
        color: green;
        width: 100%;
        height: 100%;
        position: absolute;
        top: $textBlockPadding;
        padding-right: $textBlockPadding*2;
        z-index: 1;
    }

    .current-letter {
        background-color: green;
        color: white;
        white-space: pre-wrap;
        display: inline-block;
        letter-spacing: 0;
        padding: 0px 1px;
        margin-right: 1px;
    }

    .error {
        background-color: red;
    }

    .stringLetterSpan {
        position: absolute;
        transform: translateX(-1px);
    }

    .fixStringField {
        @include fontStyle;
        width: 50%;
        overflow-x: hidden;
        text-align: end;
    }

    .stats-block-container {
        flex-grow: 1;
    }

    #trigger-mobile-keyboard {
        opacity: 0;
        width: 0px;
        height: 0px;
        overflow: hidden;
    }
</style>
