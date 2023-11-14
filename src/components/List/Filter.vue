<script>
import inputForm from '@/components/Form/part/inputForm.vue';
import CloseIcon from '@/image/close.vue';
export default {
    data() {
        return {
            tags: {
                include: [],
                exclude: [],
            },
            formFieldArray: [],
            filterRules: {
                include: [],
                exclude: [],
            },
            fields: [
                {
                    id: 'rulesType',
                    label: 'Rules Type:',
                    inputType: 'select',
                    required: true,
                    select: {
                        options: [
                            { value: 'include', text: 'Include' },
                            { value: 'exclude', text: 'Exclude' },
                        ],
                    },
                },
                {
                    id: 'submit',
                    inputType: 'submit',
                    value: 'Надіслати',
                },
            ],
        };
    },
    props: {
        colItems: Array,
    },
    watch: {
        colItems(value) {
            value.forEach((element) => {
                let obj = {
                    id: element.field_ID,
                    label: element.labelName,
                    inputType: 'text',
                };
                let index = this.fields.findIndex((i) => i.id == obj.id);
                if (index == -1) {
                    this.fields.splice(this.fields.length - 1, 0, obj);
                }
            });
        },
    },
    emits: ['getFilterObj'],
    methods: {
        clickSubmit(e) {
            e.preventDefault();
            let obj = Object(
                this.formFieldArray.reduce((acc, currentItem) => {
                    acc[currentItem.id] = currentItem.value;
                    return acc;
                }, {}),
            );
            if (obj.rulesType == 'include') {
                delete obj.rulesType;
                if (Object.keys(obj).length > 0) {
                    this.filterRules.include.push(obj);
                }
            } else if (obj.rulesType == 'exclude') {
                delete obj.rulesType;
                if (Object.keys(obj).length > 0) {
                    this.filterRules.exclude.push(obj);
                }
            }
            this.$emit('getFilterObj', Object(this.filterRules));
            this.formFieldArray = [];
            this.$refs.form.reset();
        },
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
        deleteTag(index, arrName) {
            if (arrName == 'include') {
                this.filterRules.include.splice(index, 1);
            } else {
                this.filterRules.exclude.splice(index, 1);
            }
            this.$emit('getFilterObj', Object(this.filterRules));
        },
    },
    components: {
        inputForm: inputForm,
        CloseIcon: CloseIcon,
    },
};
</script>
<template>
    <div class="form-wrapper">
        <form ref="form" :onsubmit="(e) => clickSubmit(e)">
            <div class="input-wrapper">
                <inputForm v-for="item in fields" :dataInput="item" @inputChange="inputChange" />
            </div>
        </form>
        <div class="filter-tags">
            <div class="include" v-if="filterRules.include.length > 0">
                <h4>Include:</h4>
                <div
                    v-for="item in fields.filter(
                        (element) => element.id !== 'submit' && element.id !== 'rulesType',
                    )"
                >
                    <div v-for="(element, indexEl) in filterRules.include">
                        <p class="tag" v-if="element[item.id]">
                            {{ item.label }}:
                            {{ element[item.id] }}
                            <CloseIcon @click="(e) => deleteTag(indexEl, 'include')" />
                        </p>
                    </div>
                </div>
            </div>
            <div class="exclude" v-if="filterRules.exclude.length > 0">
                <h4>Exclude:</h4>
                <div
                    v-for="item in fields.filter(
                        (element) => element.id !== 'submit' && element.id !== 'rulesType',
                    )"
                >
                    <div v-for="(element, indexEl) in filterRules.exclude">
                        <p class="tag" v-if="element[item.id]">
                            {{ item.label }}:
                            {{ element[item.id] }}
                            <CloseIcon @click="(e) => deleteTag(indexEl, 'exclude')" />
                        </p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<style scoped lang="scss">
.filter-tags {
    .include,
    .exclude {
        .tag {
            background-color: #442222;
            border-radius: 15px;
            display: inline-block;
            padding: 5px 10px;
            margin-bottom: 5px;
            display: inline-flex;
            align-items: center;
            svg {
                margin-left: 5px;
                cursor: pointer;
            }
        }
    }
}
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
    }
}
</style>
