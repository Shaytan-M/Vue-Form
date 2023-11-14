<script>
export default {
    data() {
        return {};
    },
    props: {
        colItems: Array,
        rowItems: Array,
    },
};
</script>
<template>
    <table>
        <thead>
            <tr>
                <th v-for="item in this.colItems">
                    {{ item.labelName }}
                </th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="(itemRow, index) in this.rowItems">
                <td v-for="(itemCol, index) in this.colItems">
                    {{ itemRow[itemCol.field_ID] }}
                </td>
            </tr>
        </tbody>
    </table>
</template>
<style lang="scss">
table {
    width: 100%;
    border-collapse: collapse;
    margin-bottom: 20px;

    tr {
        border-bottom: 2px solid $a;
    }
    th,
    td {
        padding: 12px;
        text-align: left;
        border-left: 2px solid $a;
        color: $text;
        &:first-child {
            border-left: 0;
        }
    }

    th {
        background-color: #333;
    }

    tbody {
        tr {
            background-color: #262626;
        }
        tr:nth-child(even) {
            background-color: #444;
        }
    }
}
</style>
