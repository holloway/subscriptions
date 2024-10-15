<template>
  <table>
    <thead>
      <tr>
        <th v-for="columnsKey in columnKeys">
          {{
            columns[columnsKey]
            // TODO handle h()
          }}
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(row, index) in props.rows" :key="index">
        <td v-for="columnKey in columnKeys">
          {{
            row[columnKey]
            // TODO handle h()
          }}
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script
  setup
  lang="ts"
  generic="
    VueRenderable extends string | ReturnType<typeof h>,
    Columns extends Record<string, VueRenderable>,
    Row extends Record<keyof Columns, unknown>,
    RowFormatters extends { [I in keyof Columns]?: (val: Row[I]) => VueRenderable }    
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
