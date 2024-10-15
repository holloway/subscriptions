<template>
  <Table
    :columns="{
      name: 'My id',
      body: 'Content',
    }"
    :rows="[
      { name: 'Jeff', body: ['sdf'] },
      { name: 'Kevin', body: [0] },
      { name: 'Bob', body: false },
    ]"
    :rowFormatters="{
      body: (val) => {
        if (typeof val === 'boolean') {
          return val.toString();
        }
        return val.join(', ');
      },
    }"
  ></Table>
</template>
