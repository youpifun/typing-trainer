<template>
    <div class="text-containter">
        <div class="text-container">
            <div class="text-containter__text">{{fieldText}}</div>
            <div id="user-text" class="text-containter__user-text">
                {{userText}}<span v-bind:class="[{error: isErrorSpanStyle}, 'current-letter']">{{fieldText[this.curLetterIndex]}}</span>
            </div>
        </div>
        <div class="stats-block-container">
            <StatsBlock 
            :statsInfo="statsInfo"
            :userRightTextLength="curLetterIndex"
            />
        </div>
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
        fieldText: String,
        fontSize: String,
        errorCountingType: String
    },
    data() {
        return {
            curLetterIndex: 0,
            userText: "",
            isErrorSpanStyle: false,
            isFirstAttempt: true,//Для подсчета "ошибок первого раза"
            statsInfo: {
                fullTextLength: this.fieldText.length,//длина всего текста
                firstTimeErrorAmount: 0,//"ошибка первого раза" - как только ошиблись, счетчик+=1, при дальнейших ошибках не увеличиваем
                totalErrorAmount: 0,//количество ошибок всего (учитывая, что ошибка после неправильно введенного символа - тоже ошибка)
                errorCountingType: this.errorCountingType,//способ, по которому считаем точность
                firstTimeRightAmount: 0
            }
        }
    },
    mounted: function() {
        let textStyles = document.getElementsByClassName("text-containter__text")[0].style;
        textStyles.fontSize = this.fontSize;
        this.startTraining();
    },
    methods: {
        startTraining() {
            document.addEventListener('keydown', (e)=>{
                if (/^[a-zA-Zа-яА-ЯЁё,.?!-:;() ]$/.test(e.key)) {//ввели букву
                    this.checkLetter(e.key);
                } else if (e.key==="Backspace") {//блок исправления стиля span
                    this.isErrorSpanStyle = false;
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
                this.rightUserInputAction(userInputKey);
            } else {
                this.wrongUserInputAction();
            }
        },
        updateMessage: async function (userInputKey) {
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

    .text-containter {
        position: relative;
    }

    .text-containter__text {
        @include fontStyle;
        opacity: 0.3;
    }

    .text-containter__user-text {
        @include fontStyle;
        color: green;
        width: 100%;
        height: 100%;
        position: absolute;
        top: 0;
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
</style>
