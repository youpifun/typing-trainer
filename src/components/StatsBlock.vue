<template>
    <div class="stats-block">
        <div class="accuracy">{{accuracy.toFixed(2)}}%</div>
        <div class="input-speed">{{inputSpeed}} Символов в минуту</div>
    </div>
</template>

<script>
export default {
    name: 'StatsBlock',
    props: {
        userRightTextLength: Number,
        statsInfo: {
            fullTextLength: Number,
            firstTimeErrorAmount: Number,
            totalErrorAmount: Number,
            errorCountingType: String,
            firstTimeRightAmount: Number,
            startInputTime: Number,
            rightInputTimestamp: Number
        }
    },
    data() {
        return {
        }
    },
    methods: {
        countEasyAccuracy(info) {//Учитываем ваши ошибки относительно всего текста. Ошибкой считается первый неправильно введенный символ. (точность = количество правильных символов всего текста/длину текста)
            return (info.fullTextLength-info.firstTimeErrorAmount)/info.fullTextLength*100;
        },
        countMediumAccuracy(info) {//Учитываем ваши ошибки относительно введенного текста. Исправленные символы СЧИТАЮТСЯ правильными. (точность = количество правильных символов/количество нажатых клавиш)
            return ((this.userRightTextLength+info.totalErrorAmount) !== 0) 
                ? (this.userRightTextLength/(this.userRightTextLength+info.totalErrorAmount))*100 
                : 100;
        },
        countHardAccuracy(info) {//Учитываем ваши ошибки относительно введенного текста. Исправленные символы НЕ СЧИТАЮТСЯ правильными. (точность = количество правильных символов/количество всех ранее введенных правильных символов)
            return ((info.firstTimeRightAmount + info.firstTimeErrorAmount) !== 0) 
                ? info.firstTimeRightAmount/(info.firstTimeRightAmount + info.firstTimeErrorAmount)*100 
                : 100;
        },
        countExtremeAccuracy(info) {//Учитываем ваши ошибки относительно введенного текста. Исправленные символы НЕ СЧИТАЮТСЯ правильными. (точность = количество правильных символов/количество нажатых клавиш)
            return ((this.userRightTextLength+info.totalErrorAmount) !== 0) 
                ? (info.firstTimeRightAmount/(this.userRightTextLength+info.totalErrorAmount))*100 
                : 100;
        }
    },
    computed: {
        accuracy: function() {
            let info = this.statsInfo;
            switch (info.errorCountingType) {
                case "easy": {
                    return this.countEasyAccuracy(info);
                }
                case "medium": {
                    return this.countMediumAccuracy(info);
                }
                case "hard": {
                    return this.countHardAccuracy(info);
                }
                case "extreme": {
                    return this.countExtremeAccuracy(info);
                }
                default: return 100;
            }
        },
        inputSpeed: function () {
            let info = this.statsInfo;
            let speed = Math.round(60*this.userRightTextLength/(info.rightInputTimestamp-info.startInputTime)*1000);
            return isNaN(speed) ? 0 : speed;
        }
    }
}
</script>
