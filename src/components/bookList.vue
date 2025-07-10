<template>
  <Loader v-if="loading" class="fixed inset-0 bg-white/70 flex items-center justify-center z-50" />
  <Error class="fixed top-4 right-4 z-50" v-if="error" :message="error" />
  <appSuccess class="fixed top-4 right-4 z-50" v-if="success" :message="success" />

  <div v-if="!loading && !error">
    <div class="overflow-x-auto">
      <!-- ส่วนหัวตาราง บรรทัด 12 ส่งค่าตำแหน่งไป -->
      <div class="flex flex-col md:flex-row justify-between items-center mb-6">
        <h2 class="text-3xl font-bold mb-4 md:mb-0">📚 รายการหนังสือ</h2>
        <button type="button" 
         @click="$emit('open-add')"
        class="text-white bg-green-700 hover:bg-green-800 focus:outline-none focus:ring-4 focus:ring-green-300 font-medium rounded-full text-sm px-5 py-2.5 text-center me-2 mb-2 dark:bg-green-600 dark:hover:bg-green-700 dark:focus:ring-green-800">
          เพิ่ม
        </button>
      </div>
      <!-- หัวข้อตาราง -->
      <table class="min-w-full divide-y-2 divide-gray-200">
        <thead class="ltr:text-left rtl:text-right">
          <tr class="*:font-medium *:text-gray-900">
            <th class="px-3 py-2 whitespace-nowrap">title</th>
            <th class="px-3 py-2 whitespace-nowrap">author</th>
            <th class="px-3 py-2 whitespace-nowrap">published_year</th>
            <th class="px-3 py-2 whitespace-nowrap">genre</th>
            <th class="px-3 py-2 whitespace-nowrap">Created time</th>
            <th class="px-3 py-2 whitespace-nowrap">Update time</th>
          </tr>
        </thead>
    
        <tbody class="divide-y divide-gray-200">
          <tr v-for="book in books" :key="book.id" class="*:text-gray-900 *:first:font-medium">
            <td class="px-3 py-2 whitespace-nowrap">{{ book.title }}</td>
            <td class="px-3 py-2 whitespace-nowrap">{{ book.author }}</td>
            <td class="px-3 py-2 whitespace-nowrap">{{ book.published_year }}</td>
            <td class="px-3 py-2 whitespace-nowrap">{{ book.genre }}</td>
            <td class="px-3 py-2 whitespace-nowrap">{{ dayjs(book.created_at).format('DD-MM-YYYY / HH:mm') }}</td>
            <td class="px-3 py-2 whitespace-nowrap">{{ dayjs(book.updated_at).format('DD-MM-YYYY / HH:mm') }}</td>

            <td>
              <span class="inline-flex divide-x divide-gray-300 overflow-hidden rounded border border-gray-300 bg-white shadow-sm">
                <button 
                  type="button"
                  @click="$emit('open-update', book)"
                  class="px-3 py-1.5 text-sm font-medium text-white bg-blue-500 hover:bg-blue-600 rounded-md shadow-sm transition-colors focus:ring-2 focus:ring-blue-300 focus:outline-none"
                  >
                  Edit
                </button>
                <button @click="deleteBook(book.id)"
                  type="button"
                  class="px-3 py-1.5 text-sm font-medium text-white bg-red-500 hover:bg-red-600 rounded-md shadow-sm transition-colors focus:ring-2 focus:ring-red-300 focus:outline-none">
                  Delete
                </button>
              </span>
            </td>

          </tr>

        </tbody>
      </table>
      
    </div>
  </div>
  

  <!-- <div>-------------</div>
  <div>file bookList</div>
  <div>-------------</div>
  <div>
    <Loader v-if="loading" />
    <Error v-if="error" :message="error" />
    <div v-if="!loading && !error">
        
      <h2>📚 รายการหนังสือ</h2>
      <div class="grid grid-cols-1 gap-4 lg:grid-cols-2 lg:gap-8 ">
        <div class="h-32 rounded bg-gray-300"></div>
        <div class="h-32 rounded bg-gray-300"></div>
      </div>
      <div>

      </div>
      <ul>
        <li v-for="book in books" :key="book.id">
          {{ book.title }} - {{ book.author }} ({{ book.published_year }})
          <button @click="deleteBook(book.id)">ลบ</button>
        </li>
      </ul>

      <div>
        --------
      </div>
      
      <div class="text-2xl text-blue-500 font-bold">Hello Tailwind!</div>

      <div class="p-6 max-w-sm mx-auto bg-white rounded-xl shadow-md space-y-4">
        <h1 class="text-xl font-bold text-gray-900">Welcome to Book Manager</h1>
        <button class="px-4 py-2 bg-indigo-500 text-white rounded hover:bg-indigo-600">
          Add Book
        </button>
      </div>
      
    </div>
  </div> -->
</template>

<script>

export default {
  components: { appSuccess },
  name: 'bookList',
}
</script>

<script setup>
import dayjs from 'dayjs'

import { ref, onMounted, } from 'vue'
import Loader from './Loader.vue'
import Error from './Error.vue'
import appSuccess from './success.vue'

const books = ref([])
const loading = ref(false)
const error = ref(null)
const success = ref(null)


const fetchBooks = async () => {
  loading.value = true
  error.value = null
  try {
    const res = await fetch('http://127.0.0.1:8000/books?offset=0&limit=10')
    if (!res.ok) throw new Error('ดึงข้อมูลไม่สำเร็จ')
    books.value = await res.json()
  } catch (err) {
    error.value = err.message
  } finally {
    loading.value = false
  }
}


const deleteBook = async (book_id) => {
  loading.value = true
  try {
    await fetch(`http://127.0.0.1:8000/book/${book_id}`, {
      method: 'DELETE'
    })
    success.value = 'ทำรายการสำเร็จ'
    await fetchBooks()
  } catch (err) {
    error.value = err.message
  } finally {
    loading.value = false
  }
}
onMounted(() => fetchBooks())
// onMounted(() => loading.value = true)
</script>

<style scoped>

</style>
