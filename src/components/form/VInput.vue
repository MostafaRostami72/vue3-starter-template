<template>
    <label v-if="$slots.label" class="fd-form-label" :for="id">
        <slot name="label"></slot>
    </label>

    <div :class="inputGroupClassNames">
        <span v-if="$slots.prepend" class="fd-input-group__addon">
            <slot name="prepend"></slot>
        </span>

        <input
            :value="modelValue"
            @input="handleInput"
            @blur="validate"
            :id="id"
            :type="type"
            :placeholder="placeholder"
            :disabled="disabled"
            class="fd-input fd-input-group__input"
        />

        <span v-if="$slots.append" class="fd-input-group__addon">
            <slot name="append"></slot>
        </span>
    </div>

    <div
        v-if="errors.length !== 0"
        class="fd-form-message fd-form-message--error"
    >{{ errors[0] }}</div>
</template>

<script>
import { computed, toRef, inject, onUnmounted } from 'vue';

// Utils
import { getUniqueId, isWritableFormElement } from '@/utils';

// Composables
import { useValidator } from '@/composables/validatation.composable';

// Components
import { FORM_INJECTION_KEY } from '@/components/form/VForm.vue';

export default {
    name: 'VInput',

    props: {
        modelValue: {
            default: null
        },
        id: {
            type: String,
            default: () => getUniqueId()
        },
        type: {
            type: String,
            default: 'text',
            validator: isWritableFormElement
        },
        placeholder: {
            type: String,
            default: null
        },
        disabled: {
            type: Boolean,
            default: false
        },
        rules: {
            type: Array,
            default: () => []
        }
    },

    emits: ['update:modelValue'],

    setup(props, context) {
        const value = toRef(props, 'modelValue');
        const rules = toRef(props, 'rules');

        const { errors, isValid, validate, resetValidation } = useValidator(value, rules);

        const formProvider = inject(FORM_INJECTION_KEY);
        const registrationId = formProvider.register({ validate, resetValidation });
        onUnmounted(() => formProvider.unregister(registrationId));

        const inputGroupClassNames = computed(() => {
            const classNames = ['fd-input-group'];

            if (props.disabled === true) {
                classNames.push('is-disabled');
            }

            if (isValid.value === false) {
                classNames.push('is-error');
            }

            return classNames;
        });

        function handleInput(event) {
            context.emit('update:modelValue', event.target.value);
        }

        return {
            errors,
            validate,

            inputGroupClassNames,

            handleInput
        };
    }
};
</script>

<style>
@import 'fundamental-styles/dist/input.css';
@import 'fundamental-styles/dist/input-group.css';
@import 'fundamental-styles/dist/form-message.css';
</style>
