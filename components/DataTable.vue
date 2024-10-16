<template>
  <table>
    <thead>
      <tr>
        <th
          v-for="columnKey in columnKeys"
          :key="columnKey"
          :aria-sort="
            state.sortColumn === columnKey
              ? state.sortDirection === 'asc'
                ? 'ascending'
                : 'descending'
              : undefined
          "
        >
          <template v-if="sortableColumns?.includes(columnKey)">
            <button @click="sortBy(columnKey)" :class="buttonClass">
              <Renderable :val="columns[columnKey]" />
              <template v-if="state.sortColumn === columnKey">
                <template v-if="state.sortDirection === 'asc'">
                  <Renderable :val="props.sortAscIcon" />
                </template>
                <template v-else-if="state.sortDirection === 'desc'">
                  <Renderable :val="props.sortDescIcon" />
                </template>
              </template>
              <template v-else>
                <Renderable :val="props.unsortedIcon" />
              </template>
            </button>
          </template>
          <template v-else>
            <Renderable :val="columns[columnKey]" />
          </template>
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(row, index) in sortedRows" :key="index" :class="trClass">
        <td v-for="columnKey in columnKeys" :key="columnKey" :class="tdClass">
          <component :is="rowLink ? rowLink(row) : spanFactory()">
            <template v-if="rowFormatters?.[columnKey]">
              <Renderable
                :val="rowFormatters[columnKey](row[columnKey] as any)"
              />
            </template>
            <template v-else>
              <Renderable :val="row[columnKey]" />
            </template>
          </component>
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
    ColumnKey extends keyof Columns,
    Row extends Record<ColumnKey, unknown>,
    RowFormatters extends { [I in ColumnKey]?: (val: Row[I]) => VueRenderable }   
  "
>
import { orderBy } from 'lodash-es'

type Props = {
  columns: Columns
  rows: Row[]
  rowFormatters?: RowFormatters
  rowLink?: (val: Row) => string
  sortableColumns?: ColumnKey[]
  trClass?: string
  tdClass?: string
  buttonClass?: string
  sortAscIcon?: string | ReturnType<typeof h>
  sortDescIcon?: string | ReturnType<typeof h>
  unsortedIcon?: string | ReturnType<typeof h>
}

const props = withDefaults(defineProps<Props>(), {
  trClass: 'relative',
  buttonClass: 'bg-transparent p-0',
  sortAscIcon: '\u2193',
  sortDescIcon: '\u2191',
  unsortedIcon: ' '
})

const columnKeys = Object.keys(props.columns) as ColumnKey[]

const state = reactive<{
  sortColumn?: ColumnKey
  sortDirection: 'asc' | 'desc'
}>({
  sortDirection: 'asc'
})

function sortBy(columnKey: ColumnKey) {
  state.sortColumn = columnKey as (typeof state)['sortColumn']

  if (state.sortColumn === columnKey) {
    state.sortDirection = state.sortDirection === 'asc' ? 'desc' : 'asc'
  } else {
    state.sortDirection = 'asc'
  }
}

const sortedRows = computed<Row[]>(() => {
  if (state.sortColumn) {
    return orderBy(props.rows, [state.sortColumn], [state.sortDirection])
  } else {
    return props.rows
  }
})

function spanFactory() {
  return h('span')
}
</script>
