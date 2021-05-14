<template>
    <div id="app">
        <StartWindow v-if="isModalActive" @startTrain="startTrain"/>
        <Field :fieldText="text"/>
    </div>
</template>

<script>
import Field from './components/Field.vue'
import StartWindow from "./components/StartWindow.vue";

export default {
    name: 'App',
    components: {
        Field,
        StartWindow
    },
    data() { 
        return {
            isModalActive: true,
            text: '',
        }
    },
    methods: {
        startTrain(settings) {
            console.log(settings);
            this.getTextFromSource()
            .then(res => {
                this.text = res;
                this.isModalActive = false
                })
            .catch(err => alert(err));
        },
        getTextFromSource() {
            return new Promise((resolve, reject) =>{
                let xhr = new XMLHttpRequest();
                xhr.open('GET','https://baconipsum.com/api/?type=all-meat&sentences=5&format=text', true);
                xhr.responseType = 'text';
                xhr.onload = () => {
                    if (xhr.status === 200) {
                        resolve(xhr.response);
                    } else {
                        reject(xhr.response);
                    }
                }
                xhr.onerror = () => {
                    reject(xhr.response);
                }
                xhr.send();
            })
        },
    }
}
</script>

<style>
    * {
        margin: 0;
        box-sizing: border-box;
    }
</style>
