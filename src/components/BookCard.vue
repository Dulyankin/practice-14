<template>
  <div :class="['book-card', { completed: book.completed }]">
    <div class="book-info">
      <h3>{{ book.title }}</h3>
      <p class="author">{{ book.author }}</p>
      <span class="genre">{{ book.genre }}</span>
    </div>
    <div class="book-actions">
      <div v-if="book.completed" class="rating">
        <span 
          v-for="star in 5" 
          :key="star" 
          @click="$emit('rate', star)"
        >
          {{ star <= book.rating ? '★' : '☆' }}
        </span>
      </div>
      
      <button 
        @click="$emit('toggle')" 
        :class="['btn', book.completed ? 'btn-secondary' : 'btn-primary']"
      >
        {{ book.completed ? 'Прочитано' : 'Отметить' }}
      </button>
      
      <button @click="$emit('delete')" class="btn btn-danger">X</button>
    </div>
  </div>
</template>

<script setup>
defineProps(['book']); // Получаем данные о книге от родителя [cite: 256]
defineEmits(['toggle', 'delete', 'rate']); // Сообщаем родителю о действиях [cite: 257]
</script>

<style scoped>
/* Стили из методички [cite: 259 - 342] */
.book-card { background: white; border-radius: 8px; padding: 16px; margin-bottom: 12px; display: flex; justify-content: space-between; align-items: center; transition: 0.3s; }
.book-card.completed { background: #f0f7f0; opacity: 0.8; }
.author { color: #666; font-size: 0.9em; }
.genre { background: #e0e0e0; padding: 2px 8px; border-radius: 4px; font-size: 0.8em; }
.rating span { color: gold; cursor: pointer; font-size: 20px; }
.btn { padding: 8px 12px; border: none; border-radius: 4px; cursor: pointer; }
.btn-primary { background: #4CAF50; color: white; }
.btn-secondary { background: #2196F3; color: white; }
.btn-danger { background: #f44336; color: white; }
</style>