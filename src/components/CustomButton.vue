<template>
    <button class="btn" :class="customClass">
        <slot></slot>
    </button>
</template>

<script>
export default {
    props: {
        btnType: {
            type: String,
            default: ""
        }
    },
    computed: {
        customClass() {
            if (this.btnType) {
                return 'btn-' + this.btnType
            }
        }
    }
}
</script>

<style scoped>
.btn{
    border-radius: 4px;
    padding: 8px 10px;
    border: none;
    font-size: 16px;
   margin-bottom: 10px; 
    background-color: #82C0CC;
    
}
.btn:hover{
    background-color: #73aab5;
    cursor: pointer;
}

</style>