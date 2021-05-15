<template>
    <div class="text-containter">
        <div class="text-containter__text">{{fieldText}}</div>
        <div id="user-text" class="text-containter__user-text">
            {{userText}}<span v-bind:class="[{error: isError}, 'current-letter']">{{fieldText[this.curLetterIndex]}}</span>
        </div>
    </div>
</template>

<script>
export default {
    $el: "user-text",
    name: "Field",
    props: {
        fieldText: String,
        fontSize: String,
    },
    data() {
        return {
            curLetterIndex: 0,
            userText: "",
            isError: false
        }
    },
    mounted: function() {
        let textStyles = document.getElementsByClassName("text-containter__text")[0].style;
        textStyles.fontSize = this.fontSize;
        this.startTrain();
    },
    methods: {
        startTrain() {
            document.addEventListener('keydown', (e)=>{
                if (/^[a-zA-Zа-яА-ЯЁё,.?!-:;() ]$/.test(e.key)&&(!this.isError)) {
                    this.checkLetter(e.key);
                } else if (e.key==="Backspace") {
                    this.isError = false;
                }
            });
        },
        checkLetter(userInputKey) {
            if (userInputKey === this.fieldText[this.curLetterIndex]) {
                this.updateMessage(userInputKey);
                this.curLetterIndex+=1;
            } else {
                this.isError = true;
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
