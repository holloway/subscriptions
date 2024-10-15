<template>
  <Table
    :columns="{
      id: {
        label: 'Id I guess',
        dataType: '',
      },
      body: {
        label: 'Id I guess',
        dataType: [''],
      },
    }"
    :rows="[{ id: 'sdfsdf', body: [] }]"
    :rowFormatters="{
      id: (val) => {
        return val;
      },
      body: (val) => {
        return '';
      },
    }"
  ></Table>
</template>
