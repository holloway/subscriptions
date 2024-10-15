<template>
  <table>
    <thead>
      <tr>
        <th v-for="columnsKey in columnKeys">
          {{ columns[columnsKey].label }}
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(row, index) in props.rows" :key="index">
        <td v-for="columnKey in columnKeys">
          {{ row[columnKey] }}
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script
  setup
  lang="ts"
  generic="
    Columns extends Record<string, { label: string | ReturnType<typeof h>; dataType: unknown }>,
    Row extends { [I in keyof Columns]: Columns[I]['dataType'] },
    RowFormatters extends { [I in keyof Columns]?: (val: Columns[I]['dataType']) => string | ReturnType<typeof h> }    
  "
>
type Props = {
  columns: Columns;
  rows: Row[];
  rowFormatters?: RowFormatters;
};

const props = defineProps<Props>();

const columnKeys = Object.keys(props.columns) as (keyof Columns)[];
</script>
