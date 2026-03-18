<template>
  <div class="app">
    <header>
      <h1>Менеджер книг</h1>
      <p>Управляй своей библиотекой</p>
    </header>
    <main>
      <AddBookForm @add-book="addBook" />
      
      <BookFilters 
        v-model:searchQuery="searchQuery"
        v-model:filter="currentFilter"
        :books="books"
      />

      <div v-if="filteredBooks.length === 0" class="empty-state">
        <p>Книги не найдены :(</p>
        <p>Добавьте первую книгу или измените параметры поиска</p>
      </div>

      <div v-else class="books-list">
        <BookCard 
          v-for="book in filteredBooks" 
          :key="book.id" 
          :book="book"
          @toggle="toggleBook(book.id)"
          @delete="deleteBook(book.id)"
          @rate="rateBook(book.id, $event)"
          @favorite="toggleFavorite(book.id)"
        />
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'
import AddBookForm from './components/AddBookForm.vue'
import BookFilters from './components/BookFilters.vue'
import BookCard from './components/BookCard.vue'

const books = ref(JSON.parse(localStorage.getItem('books') || '[]'))
const currentFilter = ref('all')
const searchQuery = ref('')

// Сохранение изменений
watch(books, (newBooks) => {
  localStorage.setItem('books', JSON.stringify(newBooks))
}, { deep: true })

const addBook = (bookData) => {
  books.value.push({
    id: Date.now(),
    ...bookData,
    completed: false,
    rating: 0,
    isFavorite: false // Поле для избранного
  })
}

const toggleBook = (id) => {
  const book = books.value.find(b => b.id === id)
  if (book) {
    book.completed = !book.completed
    if (!book.completed) book.rating = 0
  }
}

// Функция для Избранного
const toggleFavorite = (id) => {
  const book = books.value.find(b => b.id === id)
  if (book) book.isFavorite = !book.isFavorite
}

const rateBook = (id, rating) => {
  const book = books.value.find(b => b.id === id)
  if (book && book.completed) book.rating = rating
}

const deleteBook = (id) => {
  if (confirm('Удалить книгу?')) {
    books.value = books.value.filter(b => b.id !== id)
  }
}

// Улучшенная фильтрация с учетом Избранного
const filteredBooks = computed(() => {
  return books.value
    .filter(book => {
      if (currentFilter.value === 'unread') return !book.completed
      if (currentFilter.value === 'read') return book.completed
      if (currentFilter.value === 'favorites') return book.isFavorite
      return true
    })
    .filter(book => {
      if (!searchQuery.value) return true
      const query = searchQuery.value.toLowerCase()
      return book.title.toLowerCase().includes(query) || 
             book.author.toLowerCase().includes(query)
    })
})
</script>

<style>
* { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: #f0f2f5; line-height: 1.6; }
.app { max-width: 800px; margin: 0 auto; padding: 20px; }
header { text-align: center; margin-bottom: 30px; padding: 20px; background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white; border-radius: 10px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
main { background: white; padding: 30px; border-radius: 10px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
.empty-state { text-align: center; padding: 40px; color: #999; font-size: 1.2em; }
.books-list { margin-top: 20px; }
</style>