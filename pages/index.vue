<template>
   <div class="project-board container mx-auto ">
    <div class="column " v-for="(column, index) in columns" :key="index"  
     @drop="handleDrop(index)" @dragover.prevent>
      <p> <span :class="{ 'bg1': column.name === 'Not started' ,'bg2':column.name === 'In progress','bg3':column.name==='Completed'}">{{ column.name }}</span> 
      <span class="s">{{ column.cards.length }}</span>
      <span class="dot">...</span><span class="plus"> <button @click="addNewColumn">+</button></span></p>
      <div class="card" v-for="card in column.cards" :key="card.id" draggable="true" @dragstart="handleDragStart(card, index)" @click="viewTaskDetails(card)">

        <div v-if="!card.editing" @click="startEditing(card)">{{ card.title }}</div>

        <input v-else v-model="card.title" @keyup.enter="finishEditing(card)" @blur="finishEditing(card)">

        <button class="delete-btn" @click="deleteCard(card.id, index)">Delete</button>
      </div>
      <button @click="addNewCard(index)" class="text-gray-100">+ New</button>
    </div>
   
  </div>
</template>

<script>

export default {
  data() {
    return {
      columns: [
        {
          name: "Not started",
          cards:[
            { id: 4, title: "Card 4", description: "Description 4", status: "Not started", editing: false },
            { id: 1, title: "Card 1", description: "Description 1", status: "Not started", editing: false },
            { id: 5, title: "Card 5", description: "Description 5", status: "Not started", editing: false }
          ]
        },
        {
          name: "In progress",
          cards: [
            { id: 2, title: "Card 2", description: "Description 2", status: "In progress", editing: false }
          ]
        },
        {
          name: "Completed",
          cards: [
            { id: 3, title: "Card 3", description: "Description 3", status: "Completed", editing: false }
          ]
        }
      ],
      selectedTask: null
    };
  },
  methods: {
    handleDragStart(card, columnIndex) {
      event.dataTransfer.setData("text/plain", JSON.stringify({ id: card.id, columnIndex }));
    },
    handleDrop(targetColumnIndex) {
      event.preventDefault();
      const { id, columnIndex } = JSON.parse(event.dataTransfer.getData("text/plain"));
      if (columnIndex !== targetColumnIndex) {
        const card = this.columns[columnIndex].cards.find(t => t.id === id);
        this.columns[columnIndex].cards = this.columns[columnIndex].cards.filter(t => t.id !== id);
        card.editing = false; 
        this.columns[targetColumnIndex].cards.push(card);
      }
    },
    addNewCard(columnIndex) {
      const cards = this.columns[columnIndex].cards;
      const newCardId = cards.length ? cards[cards.length - 1].id + 1 : 1;
      const newCard = {
        id: newCardId,
        title: `Card ${newCardId}`,
        description: `Description ${newCardId}`,
        status: this.columns[columnIndex].name,
        editing: false 
      };
      this.columns[columnIndex].cards.push(newCard);
    },
    deleteCard(cardId, columnIndex) {
      this.columns[columnIndex].cards = this.columns[columnIndex].cards.filter(card => card.id !== cardId);
    },
    startEditing(card) {
      card.editing = true;
    },
    finishEditing(card) {
      card.editing = false;
    },
    addNewColumn() {
      const Name1 = prompt("Enter New Status");
      if (!Name1) return;
      const newColumn = {
        name: Name1,
        cards: []
      };
      this.columns.push(newColumn);
    },
    viewTaskDetails(card) {
      this.selectedTask = card;
    },
    closeTaskDetails() {
      this.selectedTask = null;
    }
  }
};
</script>

<style scoped>
.project-board {
  display: flex;
  gap: 20px;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 14px;
}
.column {
  flex: 1;
  padding: 20px;
  border-radius: 8px;
  max-width: 300px;
}

.card {
  position: relative;
  background-color: #ffffff;
  border: 1px solid #ddd;
  padding: 10px;
  margin-bottom: 10px;
  border-radius: 4px;
  cursor: grab;
}
 button {
  border: none;
  background: transparent;
  color: darkgrey;
  
} 
.delete-btn {
  position: absolute;
  top: 5px;
  right: 5px;
  background: transparent;
  border: none;
  color:#ddd;
  cursor: pointer;
  font-weight: normal;
}

.card:hover {
  background-color: #f9f9f9;
}
.s {
  margin-left: 20px;
}
.plus{
 padding-left:10px;
  font-weight:bold;
}
.s{
  margin-left: 18px;
}
.t{
  background-color: lightcoral;
}
.u{
background-color: lightyellow;
}
.v{
background-color: lightgreen;
}
.dot{
  padding-left: 130px;
}
.bg1 {
  background-color: lightpink;
}
.bg2 {
  background-color: lightyellow;
}
.bg3 {
  background-color: lightgreen;
}
</style>
