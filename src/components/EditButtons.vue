<template>
    <button
      v-if="showAddBtn"
      class="edit-btn edit-btn-add"
      @click="$emit('addClick')"
    >
      {{ textAdd }}
    </button>
    <button
      v-if="showRemoveBtn"
      class="edit-btn edit-btn-remove"
      @click="$emit('removeClick')"
    >
      {{ textRemove }}
    </button>
  </template>
  
  <script>
  export default {
    props: {
      'showRemoveBtn': {
        default: true,
        type: Boolean
      },
      'showAddBtn': {
        default: true,
        type: Boolean
      },
      textAdd: {
        default: "+"
      },
      textRemove: {
        default: "-"
      }
    },
  }
  </script>

<style scoped>

.edit-off .edit-btn {
  display: none;
}

.edit-btn {
  min-width: 20px;
  height: 20px;
  border-radius: 4px;
  border: none;
  background-color: transparent;
  margin-left: 8px;
}

.edit-btn-add {
  background-color: #3FEEE6;  
  color: #000;
}

.edit-btn-add:hover {
  background-color: rgb(68, 219, 212);
}

.edit-btn-remove {
  background-color: #F97068;
  color: #fff;
}

.edit-btn-remove:hover {
  background-color: #e0635c;
}

</style>