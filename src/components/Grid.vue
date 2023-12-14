<template>
    <div>
      <div v-for="(row, rowIndex) in grid" :key="rowIndex" class="row">
        <Cell v-for="(cell, cellIndex) in row" :key="cellIndex" :cell="cell" @open-cell="openCell(rowIndex, cellIndex)"  @show="showMines()"/>
      </div>
    </div>
  </template>
  
  <script>
  import Cell from './Cell.vue';
  
  export default {
    components: {
      Cell,
    },
    props: {
      grid: {
        type: Array,
        required: true,
      },
    },
    methods: {
    showMines(){
      this.grid.flat().filter((cell) => 
      {
        if(cell.mine){
          cell.show = !cell.show;
        }
      });
    },
      openCell(row, col) {
        this.$emit('open-cell', row, col);
      },
    },
  };
  </script>
  
  <style scoped>
  .row {
    display: flex;
  }
  </style>