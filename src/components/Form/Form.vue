<script>
import inputForm from './part/inputForm.vue';
export default {
    data() {
        return {
            formFieldArray: [],
            config: {
                fields: [
                    {
                        id: 'first_name',
                        label: 'First Name',
                        inputType: 'text',
                    },
                    {
                        id: 'last_name',
                        label: 'Last Name',
                        inputType: 'text',
                    },
                    {
                        id: 'phone',
                        label: 'Phone',
                        inputType: 'tel',
                        errorText: 'Поле повинно містити мінімум 8 символи',
                        validation: (value) => value.length > 8,
                    },
                    { id: 'email', label: 'Email', inputType: 'email' },
                    {
                        id: 'gender',
                        label: 'Gender',
                        inputType: 'select',
                        select: {
                            options: [
                                { value: 'Male', text: 'Male' },
                                { value: 'Female', text: 'Female' },
                            ],
                        },
                    },
                    {
                        id: 'phone1',
                        label: 'Phone1',
                        inputType: 'tel',
                        errorText: 'Поле повинно містити мінімум 8 символи',
                        validation: (value) => value.length > 8,
                    },
                    {
                        id: 'submit',
                        inputType: 'submit',
                        value: 'Надіслати',
                    },
                ],
            },
        };
    },
    emits: ['listForming', 'errorFunc'],
    methods: {
        // Функція яка спрацьовує в момент заповення філда
        // Виконує заповнення масиву formFieldArray
        inputChange(obj) {
            let index = this.formFieldArray.findIndex((i) => i.id == obj.id);
            if (obj.value.length == 0) {
                this.formFieldArray.splice(index, 1);
            } else {
                if (index == -1) {
                    this.formFieldArray.push(obj);
                } else {
                    this.formFieldArray.splice(index, 1, obj);
                }
            }
        },
        // Функція яка спрацьовує на відправеленні форми
        // Відправляє дані в таблицю і перевіряє чи форма заповнена правильно
        clickSubmit(e) {
            e.preventDefault();
            if (this.formFieldArray.length > 0) {
                let indexError = this.formFieldArray.findIndex((i) => i.statusError == true);
                if (indexError == -1) {
                    let objCol = this.formFieldArray.map((i) => {
                        return {
                            labelName: i.labelName,
                            field_ID: i.id,
                        };
                    });
                    let objRow = this.formFieldArray.map((i) => {
                        return {
                            value: i.value,
                            field_ID: i.id,
                        };
                    });
                    let objForSend = {
                        col: objCol,
                        row: objRow,
                    };
                    this.$emit('listForming', objForSend);
                    this.formFieldArray = [];
                    this.$refs.form.reset();
                } else if (indexError >= 0) {
                    this.$emit('errorFunc', this.formFieldArray[indexError]);
                }
            }
        },
    },
    components: {
        inputForm,
    },
};
</script>
<template>
    <div class="row">
        <div class="form-wrapper">
            <form ref="form" :onsubmit="(e) => clickSubmit(e)">
                <div class="input-wrapper">
                    <inputForm
                        v-for="item in this.config.fields"
                        :dataInput="item"
                        @inputChange="inputChange"
                    />
                </div>
            </form>
        </div>
    </div>
</template>
<style scoped lang="scss">
.form-wrapper {
    width: 100%;
    max-width: 600px;
    margin: 0 auto;
    background-color: #333;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
    form {
        width: 100%;
    }

    .input-wrapper {
        display: grid;
        grid-template-columns: repeat(2, 1fr);
        grid-gap: 15px;
    }

    .input-wrapper label {
        display: flex;
        flex-direction: column;
        color: $text;
        justify-content: flex-end;
    }
}
</style>
