<template>
    <div class="stats-block">
        <div class="radial-progress" :data-progress="accuracy.toFixed()">
            <div class="circle">
                <div class="mask full">
                    <div :class="[{
                    'red-background': accuracy.toFixed(2)<50,
                    'green-background': accuracy.toFixed(2)>75}, 
                    'fill']"></div>
                </div>
                <div class="mask half">
                    <div :class="[{
                    'red-background': accuracy.toFixed(2)<50,
                    'green-background': accuracy.toFixed(2)>75}, 
                    'fill']"></div>
                     <div :class="[{
                    'red-background': accuracy.toFixed(2)<50,
                    'green-background': accuracy.toFixed(2)>75}, 
                    'fill', 'fix']"></div>
                </div>
                <div class="shadow"></div>
            </div>
            <div class="inset">
                <div :class="[{
                    'red-font': accuracy.toFixed(2)<50,
                    'green-font': accuracy.toFixed(2)>75}, 
                    'percentage']"
                >
                    {{accuracy.toFixed(2)}}%
                </div>
            </div>
        </div>
        <div class="input-speed">Символов в минуту:
            <div class="input-speed-number">{{inputSpeed}}</div>
        </div>
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
        countEasyAccuracy(info) {//(точность = количество правильных символов всего текста/длину текста)
            return (info.fullTextLength-info.firstTimeErrorAmount)/info.fullTextLength*100;
        },
        countMediumAccuracy(info) {//(точность = количество правильных символов/количество нажатых клавиш)
            return ((this.userRightTextLength+info.totalErrorAmount) !== 0) 
                ? (this.userRightTextLength/(this.userRightTextLength+info.totalErrorAmount))*100 
                : 100;
        },
        countHardAccuracy(info) {//(точность = количество правильных символов/количество всех ранее введенных правильных символов)
            return ((info.firstTimeRightAmount + info.firstTimeErrorAmount) !== 0) 
                ? info.firstTimeRightAmount/(info.firstTimeRightAmount + info.firstTimeErrorAmount)*100 
                : 100;
        },
        countExtremeAccuracy(info) {//(точность = количество правильных символов/количество нажатых клавиш)
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
            if (this.userRightTextLength<=1) return 0;
            return speed;
        }
    }
}
</script>

<style lang="scss" scoped>
    /*Progress-bar для точности взят из интернета, 
    мною он переписан из less в scss и 
    переписана логика его связывания с точностью*/
	$stats-font-size: 22px;
    .radial-progress {
	$circle-size: 120px;
	$circle-background: #d6dadc;
	$circle-color: #97a71d;
	$inset-size: 90px;
	$inset-color: #fbfbfb;
	$transition-length: 1s;
	$shadow: 6px 6px 10px rgba(0,0,0,0.2);
	$percentage-color: #97a71d;
	$percentage-text-width: 85px;

	width:  $circle-size;
	height: $circle-size;

	background-color: $circle-background;
	border-radius: 50%;
	.circle {
		.mask, .fill, .shadow {
			width:    $circle-size;
			height:   $circle-size;
			position: absolute;
			border-radius: 50%;
		}
		.shadow {
			box-shadow: $shadow inset;
		}
		.mask, .fill {
			-webkit-backface-visibility: hidden;
			transition: -webkit-transform $transition-length;
			transition: -ms-transform $transition-length;
			transition: transform $transition-length;
			border-radius: 50%;
		}
		.mask {
			clip: rect(0px, $circle-size, $circle-size, $circle-size/2);
			.fill {
				clip: rect(0px, $circle-size/2, $circle-size, 0px);
				background-color: $circle-color;
			}
		}
	}
	.inset {
		width:       $inset-size;
		height:      $inset-size;
		position:    absolute;
		margin-left: ($circle-size - $inset-size)/2;
		margin-top:  ($circle-size - $inset-size)/2;

		background-color: $inset-color;
		border-radius: 50%;
		box-shadow: $shadow;
		.percentage {
			width:       $percentage-text-width;
			position:    absolute;
			top:         ($inset-size - $stats-font-size) / 2;
			left:        ($inset-size - $percentage-text-width) / 2;

			line-height: 1;
			text-align:  center;

			font-family: "Lato", "Helvetica Neue", Helvetica, Arial, sans-serif;
			color:       $percentage-color;
			font-weight: 800;
			font-size:   $stats-font-size;
		}
	}

	$i: 0;
	$increment: 180deg / 100;

	@while ($i <= 100) {
		&[data-progress="#{$i}"] {
			.circle {
				.mask.full, .fill {
					-webkit-transform: rotate($increment * $i);
					-ms-transform: rotate($increment * $i);
					transform: rotate($increment * $i);
				}	
				.fill.fix {
					-webkit-transform: rotate($increment * $i * 2);
					-ms-transform: rotate($increment * $i * 2);
					transform: rotate($increment * $i * 2);
				}
			}
		}
		$i: $i + 1;
	}
}

.red-font {
    color: red !important;
}

.red-background {
    background-color: red !important;
}

.green-font {
    color: green !important;
}

.green-background {
    background-color: green !important;
}

.input-speed {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    font-size: $stats-font-size;
    color: rgb(89, 108, 219);
}

.input-speed-number {
    font-size: $stats-font-size*1.5;
}

.stats-block {
    display: flex;
    justify-content: space-evenly;
    margin-top: 5%;
}
</style>
