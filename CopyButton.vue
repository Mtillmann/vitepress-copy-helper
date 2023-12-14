<script>
const defaults = {
    position: 'auto',
    target: 'auto',
    message: 'copied',
    label: '',
    classes: 'copy-btn'
}

export const copyHelperDefaultSettings = defaults;
</script>

<script setup>
import copyToClipboard from "./copyToClipboard";
import { ref, onMounted } from "vue";

const btn = ref(null);
const codeElement = ref(null);
const style = ref({})
let mode = 'code-inline';


const props = defineProps({
    position: {
        // start, end, auto
        type: String,
        default: () => defaults.position
    },
    target: {
        // before, after, auto
        type: String,
        default: () => defaults.target
    },
    message: {
        type: String,
        default: () => defaults.message
    },
    label: {
        type: String,
        default: () => defaults.label
    },
    classes: {
        type: String,
        default: () => defaults.classes
    }
});



onMounted(() => {

    style.value = {
        '--message': props.message
    }

    const elementBefore = btn.value.previousElementSibling?.tagName === 'CODE' ? btn.value.previousElementSibling : null;
    const elementAfter = btn.value.nextElementSibling?.tagName === 'CODE' ? btn.value.nextElementSibling : null;

    if (props.target === 'auto') {
        codeElement.value = elementBefore || elementAfter;
    } else if (props.target === 'before') {
        codeElement.value = elementBefore;
    } else if (props.target === 'after') {
        codeElement.value = elementAfter;
    }

    if (!codeElement.value) {
        return;
    }

    let insertPosition = 'beforeend';
    if (props.position === 'auto') {
        if (elementBefore) {
            insertPosition = 'beforeend';
        } else if (elementAfter) {
            insertPosition = 'afterbegin';
        }
    } else if (props.position === 'start') {
        insertPosition = 'afterbegin';
    } else if (props.position === 'end') {
        insertPosition = 'beforeend';
    }

    

    const text = codeElement.value.innerText;
    codeElement.value.innerText = '';
    codeElement.value.insertAdjacentHTML('beforeend', `<span>${text}</span>`);




    codeElement.value.insertAdjacentElement(insertPosition, btn.value);
    btn.value.classList.add('copy-btn', `copy-btn-${insertPosition}`);
});

async function copy() {
    await copyToClipboard(codeElement.value.querySelector('span').innerText);
    btn.value.classList.add('copied');
    setTimeout(() => {
        btn.value.classList.remove('copied');
    }, 1000);
}

</script>
<template>
    <span :class="classes" ref="btn" @click="copy" :data-copied-message="message">{{ label }}</span>
</template>
<style scoped>
.copy-btn {
    position: relative;
    vertical-align: middle;
    border: 1px solid var(--vp-code-copy-code-border-color);
    border-radius: 4px;
    display: inline-block;
    width: 18px;
    height: 18px;
    background-color: var(--vp-code-copy-code-bg);

    cursor: pointer;
    background-image: var(--vp-icon-copy);
    background-position: 50%;
    background-size: 20px;
    background-repeat: no-repeat;

    &.copy-btn-beforeend {
        margin-left: 5px;
    }

    &.copy-btn-afterbegin {
        margin-right: 5px;
    }

    &.copied::after {
        content: attr(data-copied-message);
        position: absolute;
        top: 0;
        left:50%;
        color:var(--vp-c-text-1);
        opacity:0;
        animation: notify 1s ease-out;

        border: 1px solid var(--vp-code-copy-code-border-color);
        border-radius: 4px;
        padding: 2px 4px;
        font-size: small;
        display: inline-block;
        background-color: var(--vp-code-copy-code-bg);

        
        white-space: nowrap;

    }
}

@keyframes notify {
    0% {
        opacity: 0;
        transform:translate(-50%, -100%);
    }

    50% {
        opacity: 1;
        transform:translate(-50%, -150%);
    }

    100% {
        opacity: 0;
        transform:translate(-50%, -200%);
    }
}
</style>