<template>
    <CustomButton @click="exportPdf" btn-type="primary">
        Export resume pdf
    </CustomButton>
</template>

<script>
import CustomButton from './CustomButton.vue';

export default {
    components: {
        CustomButton
    },
    methods: {
        exportPdf() {
            var opt = {
                html2canvas: { scale: 1, useCORS: true, scrollY: 0 },
            };
            const resume = document.getElementById('resume');
            html2pdf().set(opt).from(resume).save();
        }
    }
}
</script>
<style>
.btn-primary {
    background-color: var(--color-black);
}

.btn-primary:hover {
    background-color: var(--color-black);
}

.btn-secondary {
    background-color: #3943B7;
    color: white;
}

.btn-secondary:hover {
    background-color: #3943B7;
    color: white;
}
</style>