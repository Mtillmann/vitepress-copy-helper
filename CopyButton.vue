<script>
const defaults = {
    position: 'auto',
    target: 'auto',
    message: 'copied',
    label: null,
    classes: 'copy-btn',
    preferSibling: 'previous'
}

export const copyHelperDefaultSettings = defaults;
</script>

<script setup>
import copyToClipboard from "./copyToClipboard";
import { ref, onMounted, useSlots } from "vue";
const slots = useSlots();



const btn = ref(null);
const codeElement = ref(null);
const show = ref(true);
const props = defineProps({
    position: {
        // start, end, auto
        type: String,
        default: () => defaults.position
    },
    target: {
        // previous, next, auto
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
    },
    content: {
        type: String,
        default: null
    },
});


const labelString = props.label ? props.label : slots.default?.()[0]?.children;

onMounted(() => {


    const elementBefore = btn.value.previousElementSibling?.tagName === 'CODE' ? btn.value.previousElementSibling : null;
    const elementAfter = btn.value.nextElementSibling?.tagName === 'CODE' ? btn.value.nextElementSibling : null;

    if(!elementBefore && !elementAfter && !props.content){
        show.value = false;
        return;
    }


    if(props.content){
       return; 
    }

    // attempt inject button into code element

    let preliminaryPosition = props.position;
    if (props.target === 'auto') {
        if (elementBefore && elementAfter) {
            if (defaults.preferSibling === 'previous') {
                codeElement.value = elementBefore;
                if (preliminaryPosition === 'auto') {
                    preliminaryPosition = 'end';
                }
            } else if (defaults.preferSibling === 'next') {
                codeElement.value = elementAfter;
                if (preliminaryPosition === 'auto') {
                    preliminaryPosition = 'start';
                }
            }
        } else {
            codeElement.value = elementBefore || elementAfter;
        }
    } else if (props.target === 'previous') {
        codeElement.value = elementBefore;
        if (preliminaryPosition === 'auto') {
            preliminaryPosition = 'end';
        }

    } else if (props.target === 'next') {
        codeElement.value = elementAfter;
        if (preliminaryPosition === 'auto') {
            preliminaryPosition = 'start';
        }
    }

    if (!codeElement.value) {
        return;
    }

    let insertPosition = 'beforeend';
    if (preliminaryPosition === 'auto') {
        if (elementBefore) {
            insertPosition = 'beforeend';
        } else if (elementAfter) {
            insertPosition = 'afterbegin';
        }
    } else if (preliminaryPosition === 'start') {
        insertPosition = 'afterbegin';
    } else if (preliminaryPosition === 'end') {
        insertPosition = 'beforeend';
    }


    const text = codeElement.value.innerText;
    codeElement.value.innerText = '';
    codeElement.value.insertAdjacentHTML('beforeend', `<span>${text}</span>`);




    codeElement.value.insertAdjacentElement(insertPosition, btn.value);
    btn.value.classList.add(`copy-btn-${insertPosition}`);
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
    <span v-if="show" :class="classes" ref="btn" @click="copy" :data-message="message" :data-label="labelString"></span>
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

    line-height: 1;

    &.copy-btn-beforeend {
        margin-left: 5px;
    }

    &.copy-btn-afterbegin {
        margin-right: 5px;
    }

    &[data-label] {
        width: auto;
        padding-left: 22px;
        padding-right: 4px;
        background-position-x: 2px;
        padding-top: 0;

        &::after {
            content: attr(data-label);
            line-height: 1.2;
        }
    }

    &[data-message].copied::before {
        content: attr(data-message);
        position: absolute;
        top: 0;
        left: 50%;
        color: var(--vp-c-text-1);
        opacity: 0;
        animation: showmessage 1s ease-out;

        border: 1px solid var(--vp-code-copy-code-border-color);
        border-radius: 4px;
        padding: 2px 4px;
        font-size: small;
        display: inline-block;
        background-color: var(--vp-code-copy-code-bg);


        white-space: nowrap;

        line-height: 1;

    }
}

@keyframes showmessage {
    0% {
        opacity: 0;
        transform: translate(-50%, -100%);
    }

    50% {
        opacity: 1;
        transform: translate(-50%, -150%);
    }

    100% {
        opacity: 0;
        transform: translate(-50%, -200%);
    }
}
</style>