<script>
import Form from '@/components/Form/Form.vue';
import List from '@/components/List/List.vue';
import Filter from '@/components/List/Filter.vue';
export default {
    data() {
        return {
            colItems: [],
            rowItems: [],
            error: {
                status: false,
                fieldName: '',
            },
            filterRules: {
                include: [],
                exclude: [],
            },
        };
    },
    components: {
        Form: Form,
        List: List,
        Filter: Filter,
    },
    watch: {
        'error.status'(value) {
            if (value) {
                setTimeout(() => {
                    this.error.status = false;
                }, 2000);
            }
        },
    },
    computed: {
        filterRowItem() {
            if (this.filterRules.include.length > 0) {
                return this.rowItems.filter((person) => {
                    return this.filterRules.include.some((rule) => {
                        return Object.keys(rule).every((key) => {
                            if (key in person) {
                                return person[key]
                                    .toLowerCase()
                                    .startsWith(rule[key].toLowerCase());
                            }
                            return false;
                        });
                    });
                });
            } else if (this.filterRules.exclude.length > 0) {
                return this.rowItems.filter((person) => {
                    return this.filterRules.exclude.some((rule) => {
                        return Object.keys(rule).every((key) => {
                            if (key in person) {
                                return !person[key]
                                    .toLowerCase()
                                    .startsWith(rule[key].toLowerCase());
                            }
                            return false;
                        });
                    });
                });
            } else if (this.filterRules.exclude.length > 0 && this.filterRules.include.length > 0) {
                return this.rowItems.filter((person) => {
                    return (
                        this.filterRules.include.some((rule) => {
                            return Object.keys(rule).every((key) => {
                                if (key in person) {
                                    return person[key]
                                        .toLowerCase()
                                        .startsWith(rule[key].toLowerCase());
                                }
                                return false;
                            });
                        }) &&
                        this.filterRules.exclude.some((rule) => {
                            return Object.keys(rule).every((key) => {
                                if (key in person) {
                                    return !person[key]
                                        .toLowerCase()
                                        .startsWith(rule[key].toLowerCase());
                                }
                                return false;
                            });
                        })
                    );
                });
            } else {
                return this.rowItems;
            }
        },
    },
    methods: {
        listForming(obj) {
            this.colItems = [...this.colItems, ...obj.col].reduce((acc, currentItem) => {
                if (!acc.find((item) => item.field_ID === currentItem.field_ID)) {
                    acc.push(currentItem);
                }
                return acc;
            }, []);
            this.rowItems.push(
                obj.row.reduce((acc, currentItem) => {
                    acc[currentItem.field_ID] = currentItem.value;
                    return acc;
                }, {}),
            );
        },
        getFilterObj(obj) {
            this.filterRules.exclude = obj.exclude;
            this.filterRules.include = obj.include;
        },
        errorFunc(objError) {
            (this.error.status = true), (this.error.fieldName = objError.labelName);
        },
    },
};
</script>
<template>
    <div class="section-wrapper">
        <TransitionGroup name="list">
            <div class="block-error" v-if="error.status">
                Поле "{{ error.fieldName }}" заповнено некоректно
            </div>
        </TransitionGroup>
        <div class="form-section">
            <Form @listForming="listForming" @errorFunc="errorFunc" />
        </div>
        <div class="list-section">
            <Filter :colItems="colItems" @getFilterObj="getFilterObj" />
            <div class="list-wrapper">
                <List :colItems="colItems" :rowItems="filterRowItem" />
            </div>
        </div>
    </div>
</template>
<style lang="scss">
.list-enter-active,
.list-leave-active {
    transition: all 0.5s ease;
}
.list-enter-from,
.list-leave-to {
    opacity: 0;
    transform: translateX(30px);
}
.list-section {
    display: flex;
    justify-content: space-between;
    gap: 20px;
    .list-wrapper {
        flex: 1;
        table {
            border-radius: 8px;
            overflow: hidden;
        }
    }
    .form-wrapper {
        flex-basis: 300px;
        .input-wrapper {
            display: grid;
            grid-template-columns: repeat(1, 1fr);
            grid-gap: 15px;
        }
    }
}
.section-wrapper {
    padding: 40px 0;
    position: relative;
    .block-error {
        position: absolute;
        right: 20px;
        top: 20px;
        padding: 16px;
        border-radius: 15px;
        border: 1px solid #444;
        color: rgb(151, 4, 4);
        font-size: 16px;
        font-weight: 600;
        background-color: #19191e;
    }
    .form-section {
        padding: 40px 0;
    }
}
</style>
