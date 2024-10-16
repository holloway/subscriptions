<template>
  <div>
    <NuxtRouteAnnouncer />

    <h1>Email addresses</h1>

    <DataTable
      :columns="{
        name: formattedColumnName,
        body: 'Content'
      }"
      :rows="[
        { name: 'Jeff', body: ['sdf'] },
        { name: 'Kevin', body: [0] },
        { name: 'Bob', body: false }
      ]"
      :cell-formatters="{
        body: (val) => {
          if (typeof val === 'boolean') {
            return `FORMATTED [${val.toString()}]`
          }
          return `FORMATTED [${val.join('|')}]`
        },
        name: (val) => {
          return h('h1', val)
        }
      }"
      :sortable-columns="['body']"
      :row-link="
        (row) => {
          return h('a', {
            class: 'absolutfe inset-0',
            href: `/fdf/${row.body}/`
          })
        }
      "
    />
  </div>
</template>
<script setup lang="ts">
const formattedColumnName = h('b', 'My id')
</script>
