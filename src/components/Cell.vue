<template>
    <div :class="{ 'cell': true, 'open': cell.open, 'mine': cell.mine, 'marked': cell.marked, 'show': cell.show }" @click="handleClick"
        @contextmenu.prevent="markCell">
        <template v-if="cell.open && !cell.mine && cell.surroundingMines !== 0">
            <span class="number">{{ cell.surroundingMines }}</span>
        </template>
    </div>
</template>
  
<script>
export default {
    props: {
        cell: {
            type: Object,
            required: true,
        },
    },
    methods: {
        handleClick() {
            if (!this.cell.open && !this.cell.marked) {
                if (this.cell.mine) {
                    alert('Вы проиграли! Нажмите кнопку "Рестарт" для начала новой игры.');
                    this.$emit('show');
                    return;
                }
                this.$emit('open-cell');
                if(this.$parent.$parent.leftMines == this.$parent.$parent.mines){
                    alert('Вы победили! УРА!');
                }
            }
        },
        markCell() {
            if ((!this.cell.open && this.$parent.$parent.flaggedCells > 0) || this.cell.marked) {
                this.cell.marked = !this.cell.marked;
            }
        },
    },
};
</script>
  
<style scoped>
.cell {
    width: 30px;
    height: 30px;
    border: 1px solid #000;
    display: flex;
    justify-content: center;
    align-items: center;
    cursor: pointer;
}

.open {
    background-color: #ddd;
}

.show {
    background-color: red;
}

.number {
    color: blue;
}

.marked {
    background-color: green;
}
</style>