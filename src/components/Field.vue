<template>
    <div class="hello">
        <span class="field-text">{{fieldText}}</span>
    </div>
</template>

<script>
export default {
    name: 'Field',
    props: {
        fieldText: String,
        fontSize: String
    },
    mounted: function() {
        let textStyles = document.getElementsByClassName("field-text")[0].style;
        textStyles.fontSize = this.fontSize;
    }
}
</script>

<style lang="scss" scoped>
    .field-text {
        opacity: 0.4;
        font-family: Arial,Tahoma,Verdana,sans-serif;
        line-height: 30px;
        letter-spacing: 2px;
    }
</style>
