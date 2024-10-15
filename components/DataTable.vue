<template>
  <table>
    <thead>
      <tr>
        <th v-for="columnKey in columnKeys" :key="columnKey">
          <Renderable :val="columns[columnKey]" />
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(row, index) in props.rows" :key="index">
        <td v-for="columnKey in columnKeys" :key="columnKey">
          <template v-if="rowFormatters[columnKey]">
            <Renderable
              :val="rowFormatters[columnKey](row[columnKey] as any)"
            />
          </template>
          <template v-else>
            <Renderable :val="row[columnKey]" />
          </template>
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
    ColumnKeys extends keyof Columns,
    Row extends Record<ColumnKeys, unknown>,
    RowFormatters extends { [I in ColumnKeys]?: (val: Row[I]) => VueRenderable }    
  "
>
type Props = {
  columns: Columns
  rows: Row[]
  rowFormatters: RowFormatters
}

const props = defineProps<Props>()

const columnKeys = Object.keys(props.columns) as ColumnKeys[]
</script>
