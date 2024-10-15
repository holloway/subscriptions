<template>
  <Table
    :columns="{
      id: '#Id',
      body: 'Content',
    }"
    :rows="[
      { id: 'sdfsdf', body: ['sdf'] },
      { id: 'sdfsdf', body: [0] },
      { id: 'sdffsdf', body: false },
    ]"
    :rowFormatters="{
      body: (val) => {
        if (typeof val === "boolean") {
            return val.toString()
        }
        return val.join(", ");
      },
    }"
  ></Table>
</template>
