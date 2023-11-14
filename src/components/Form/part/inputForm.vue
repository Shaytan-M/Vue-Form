<script>
export default {
    data() {
        return {
            simpleTypes: ['email', 'text', 'tel', 'number', 'date', 'file', 'password'],
            error: false,
            inputValue: '',
        };
    },
    props: {
        dataInput: Object,
    },
    methods: {
        // Подія @change на input і формування обєкту для таблиці
        changeInput(value) {
            if (value && this.dataInput.validation && this.dataInput.validation(value) == false) {
                this.error = true;
            } else {
                this.error = false;
            }
            let inputObj = {
                statusError: this.error,
                value: value,
                id: this.dataInput.id,
                labelName: this.dataInput.label,
            };
            this.$emit('inputChange', inputObj);
        },
        // Подія @input для скиндання помилки валідації
        inputEvent(value) {
            if (
                (this.dataInput.validation && this.dataInput.validation(value)) ||
                !this.dataInput.validation
            ) {
                this.error = false;
            }
        },
    },
    emits: ['inputChange', 'clickSubmit'],
};
</script>
<template>
    <label v-if="simpleTypes.includes(dataInput.inputType)">
        {{ dataInput.label }}
        <input
            :type="dataInput.inputType"
            :placeholder="dataInput.label"
            :required="dataInput.required ?? false"
            v-model="inputValue"
            @change="(e) => changeInput(e.target.value)"
            @input="(event) => inputEvent(event.target.value)"
        />
        <p class="errorText" v-if="error && inputValue.length > 0">
            {{ dataInput.errorText ?? 'Помилка валідації' }}
        </p>
    </label>
    <label v-else-if="dataInput.inputType == 'checkbox' && dataInput.checkbox">
        {{ dataInput.label }}
        <input
            type="checkbox"
            name="subscribe"
            :required="dataInput.required ?? false"
            @change="(e) => changeInput(e.target.value)"
            @input="(event) => inputEvent(event.target.value)"
        />
        {{ dataInput.checkbox?.text }}
    </label>
    <div v-else-if="dataInput.inputType == 'radio' && dataInput.radio" class="radio-wrapper">
        <p>{{ dataInput.label }}</p>
        <label v-for="button in dataInput.radio.buttons">
            <input
                type="radio"
                :name="dataInput.id"
                :value="button.value"
                :required="dataInput.required ?? false"
                @change="(e) => changeInput(e.target.value)"
                @input="(event) => inputEvent(event.target.value)"
            />
            {{ button.text }}
        </label>
    </div>
    <div v-else-if="dataInput.inputType == 'select' && dataInput.select" class="select-wrapper">
        <p>{{ dataInput.label }}</p>
        <select
            value=""
            @change="(e) => changeInput(e.target.value)"
            @input="(event) => inputEvent(event.target.value)"
            :required="dataInput.required ?? false"
        >
            <option value="">Оберіть варіант</option>
            <option v-for="option in dataInput.select.options" :value="option.value">
                {{ option.text }}
            </option>
        </select>
    </div>
    <label v-else-if="dataInput.inputType == 'submit' || 'reset'">
        <input class="btn" :type="dataInput.inputType" :value="dataInput.value" />
    </label>
</template>
<style scoped lang="scss">
.errorText {
    color: red;
}
input[type='text'],
input[type='email'],
input[type='tel'],
input[type='number'],
input[type='date'],
input[type='file'],
input[type='password'] {
    padding: 10px;
    border: 1px solid #444;
    border-radius: 6px;
    color: #fff;
    background-color: $a;
    transition: border-color 0.2s, box-shadow 0.2s;

    &:focus {
        border-color: $buttonColor;
        box-shadow: 0 0 5px rgba(0, 123, 255, 0.5);
        outline: none;
    }
}
.radio-wrapper {
    display: flex;
    align-items: center;
    justify-content: space-around;
    flex-wrap: wrap;
    gap: 10px;
    p {
        flex-basis: 100%;
        text-align: center;
    }
    label {
        flex-basis: 30%;
    }
}
.select-wrapper {
    display: flex;
    align-items: center;
    justify-content: flex-start;
    flex-wrap: wrap;
    p {
        flex-basis: 100%;
    }
    select {
        padding: 10px;
        font-size: 16px;
        border: 1px solid $a;
        border-radius: 5px;
        background-color: #444;
        color: #fff;
        width: 100%;
        option:checked {
            color: #fff;
        }

        option {
            font-size: 16px;
            padding: 10px;
            background-color: $a;
        }
    }
}
</style>
