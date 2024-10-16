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
      :rowFormatters="{
        body: (val) => {
          if (typeof val === 'boolean') {
            return `FORMATTED [${val.toString()}]`
          }
          return `FORMATTED [${val.join('|')}]`
        }
      }"
      :sortableColumns="['body']"
    />
  </div>
</template>
<script setup lang="ts">
const formattedColumnName = h('b', 'My id')
</script>
