<template>
    <div id="app">
        <StartWindow v-if="isModalActive" @startTrain="startTrain"/>
        <Field
            :displayType="displayType"
            :fieldText="text"
            :fontSize="fontSize"
            :errorCountingType="errorCountingType"
            v-if="isTextFieldActive"
        />
    </div>
</template>

<script>
import Field from "./components/Field.vue"
import StartWindow from "./components/StartWindow.vue";

export default {
    name: "App",
    components: {
        Field,
        StartWindow
    },
    data() { 
        return {
            isModalActive: true,
            isTextFieldActive: false,
            displayType: "",
            text: "",
            fontSize: 0,
            errorCountingType: ""
        }
    },
    methods: {
        startTrain(settings) {
            let language;
            [this.displayType, language, this.fontSize, this.errorCountingType] = [settings.displayType, settings.language, settings.fontSize, settings.errorCountingType]
            this.getTextFromSource(language)
            .then(res => {
                this.text = res;
                this.isModalActive = false,
                this.isTextFieldActive = true
                })
            .catch(err => alert(err));
        },
        getTextFromSource(language) {
            return new Promise((resolve, reject) =>{
                let xhr = new XMLHttpRequest();
                let url = (language==="eng") ? "https://baconipsum.com/api/?type=all-meat&sentences=6" : "https://fish-text.ru/get?number=2";
                xhr.open("GET", url, true);
                xhr.responseType = "json";
                xhr.onload = () => {
                    let [answerText, isSuccessfulResult] = this.formatAnswerFromAPI(xhr, language);
                    isSuccessfulResult ? resolve(answerText) : reject(answerText);
                }
                xhr.onerror = () => {
                    reject(xhr.response);
                }
                xhr.send();
            })
        },
        formatAnswerFromAPI(xhr, language) {
            let result = xhr.response;
            if (language==="eng"){
                return (xhr.status === 200) ? [result[0], true] : [result, false];
            } else {
                return (xhr.status === 200) ? [result.text, true] : [result.text, false];
            }
        }
    }
}
</script>

<style>
    * {
        margin: 0;
        box-sizing: border-box;
    }

    body {
        background-color: whitesmoke;
    }
</style>
