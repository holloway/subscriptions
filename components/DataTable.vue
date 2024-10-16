<template>
  <table :class="tableClass">
    <thead :class="theadClass">
      <tr :class="trClass">
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
          :class="thClass"
        >
          <template v-if="sortableColumns?.includes(columnKey)">
            <button
              type="button"
              @click="sortBy(columnKey)"
              :class="sortButtonClass"
              :aria-pressed="state.sortColumn === columnKey"
            >
              <Renderable :val="columns[columnKey]" />
              <template v-if="state.sortColumn === columnKey">
                <template v-if="state.sortDirection === 'asc'">
                  <Renderable :val="sortAscIcon" />
                </template>
                <template v-else-if="state.sortDirection === 'desc'">
                  <Renderable :val="sortDescIcon" />
                </template>
              </template>
              <template v-else>
                <Renderable :val="unsortedIcon" />
              </template>
            </button>
          </template>
          <template v-else>
            <Renderable :val="columns[columnKey]" />
          </template>
        </th>
      </tr>
    </thead>
    <tbody :class="tbodyClass">
      <tr v-for="(row, index) in sortedRows" :key="index" :class="trClass">
        <td
          v-for="(columnKey, columnKeyIndex) in columnKeys"
          :key="columnKey"
          :class="tdClass"
        >
          <component
            :is="columnKeyIndex === 0 && rowLink ? rowLink(row) : VFragment"
          >
            <template v-if="cellFormatters?.[columnKey]">
              <Renderable
                :val="cellFormatters[columnKey](row[columnKey] as any, row)"
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
    ColumnKeys extends ColumnKey[],
    Row extends Record<ColumnKey, unknown>,
    CellFormatters extends { [I in ColumnKey]?: (cell: Row[I], row: Row) => VueRenderable },
    RowLink extends (row: Row) => ReturnType<typeof h>
  "
>
import { orderBy } from 'lodash-es'
import VFragment from './VFragment.vue'

type Props = {
  /**
   * Definitions of columns and their labels
   **/
  columns: Columns
  /**
   * The data of the table
   **/
  rows: Row[]
  /**
   * Formatters per cell in a row
   */
  cellFormatters?: CellFormatters
  /**
   * An optional wrapper link per row around the first cell.
   *
   * Devs should return a conventional link or an SPA link for the row.
   *
   * The link can be made to cover the whole row through CSS. Set `trClass` to a
   * class resolving to `position: relative` (tailwind: `relative`) and return a link
   * with a class resolving to `position: absolute; inset: 0` (tailwind: `absolute inset-0`)
   *
   * Troubleshooting: don't include 'children' in your returned link (3rd arg in `h()`)
   *
   * Usage:
   * ```
   * :rowLink="(row) => h('a', { href: `/{row.something}/info` })"
   * ```
   */
  rowLink?: RowLink
  /**
   * A list of columns which can be sorted clientside.
   **/
  sortableColumns?: ColumnKeys
  tableClass?: string
  trClass?: string
  theadClass?: string
  thClass?: string
  tbodyClass?: string
  tdClass?: string
  sortButtonClass?: string
  sortAscIcon?: string | ReturnType<typeof h>
  sortDescIcon?: string | ReturnType<typeof h>
  unsortedIcon?: string | ReturnType<typeof h>
}

const props = withDefaults(defineProps<Props>(), {
  trClass: 'relative even:bg-gray-50 odd:bg-white',
  buttonClass: 'bg-transparent p-0 border-none',
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
</script>
