<template>
  <Loader v-if="loading" class="fixed inset-0 bg-white/70 flex items-center justify-center z-50" />
  <Error class="fixed top-4 right-4 z-50" v-if="error" :message="error" />
  

  <div class="relative p-4 w-full max-w-md max-h-full">
        <!-- Modal content -->
        <div class="relative bg-white rounded-lg shadow-sm dark:bg-gray-700">
          <!-- Modal header -->

          <div class="flex items-center justify-between p-4 md:p-5 border-b rounded-t dark:border-gray-600 border-gray-200">
            <h3 class="text-lg font-semibold text-gray-900 dark:text-white">
              Update Book
            </h3>
            <button
              @click="$emit('closeUpdate')"
              type="button"
              class="text-gray-400 bg-transparent hover:bg-gray-200 hover:text-gray-900 rounded-lg text-sm w-8 h-8 ms-auto inline-flex justify-center items-center dark:hover:bg-gray-600 dark:hover:text-white"
            >
              <svg class="w-3 h-3" aria-hidden="true" fill="none" viewBox="0 0 14 14">
                <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="m1 1 6 6m0 0 6 6M7 7l6-6M7 7l-6 6" />
              </svg>
              <span class="sr-only">Close modal</span>
            </button>
          </div>

          <!-- Modal body -->
          <form @submit.prevent="submitForm" class="p-4 md:p-5">
            <div class="grid gap-4 mb-4 grid-cols-2">
              <div class="col-span-2">
                <label for="title" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">title</label>
                <input v-model="form.title" type="string" id="title"
                  class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-primary-600 focus:border-primary-600 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:text-white"
                  placeholder="title" required />
              </div>

              <div class="col-span-2">
                <label for="author" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">author</label>
                <input v-model="form.author" type="text" id="author"
                  class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-primary-600 focus:border-primary-600 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:text-white"
                  placeholder="author" required />
              </div>

              <div class="col-span-2">
                <label for="published_year" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">published_year</label>
                <input v-model="form.published_year" type="number" id="published_year"
                  class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-primary-600 focus:border-primary-600 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:text-white"
                  placeholder="published_year" required />
              </div>

              <div class="col-span-2">
                <label for="genre" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">genre</label>
                <input v-model="form.genre" type="text" id="genre"
                  class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-primary-600 focus:border-primary-600 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:text-white"
                  placeholder="genre" required />
              </div>
            </div> 

            <button type="submit" 
              class="text-white inline-flex items-center bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800">
              <svg class="me-1 -ms-1 w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                <path fill-rule="evenodd"
                  d="M10 5a1 1 0 011 1v3h3a1 1 0 110 2h-3v3a1 1 0 11-2 0v-3H6a1 1 0 110-2h3V6a1 1 0 011-1z"
                  clip-rule="evenodd"></path>
              </svg>
              Update
            </button>
            <appSuccess class="fixed top-4 right-4 z-50" v-if="success" :message="success"/>
          </form>
    </div>
  </div>
</template>

<script>
import { ref, watch } from 'vue'
import Loader from './Loader.vue'
import Error from './Error.vue'
import appSuccess from './success.vue'


export default {
  name: 'updateBook',
  components: { Loader, Error, appSuccess },
  props: {
    book: Object, 
  },
  mounted() {
    console.log('book ที่ updateBook', this.book)
  },
  emits: ['closeUpdate'],
  setup(props, { emit }) {
    const form = ref({
      title: '',
      author: '',
      published_year: '',
      genre: '',
    })

    const loading = ref(false)
    const error = ref(null)
    const success = ref(null)
    

    watch(() => props.book, (newBook) => {
      if (newBook) {
        form.value = {
          title: newBook.title,
          author: newBook.author,
          published_year: newBook.published_year,
          genre: newBook.genre,
        }
      }
    }, { immediate: true })

    const submitForm = async () => {
      loading.value = true
      error.value = null
      success.value = null

      try {
        const res = await fetch(`http://127.0.0.1:8000/book/${props.book.id}`, {
          method: 'PATCH',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            ...form.value,
            updated_at: new Date().toISOString()
          })
        })
        if (!res.ok) throw new Error('ไม่สามารถอัปเดตข้อมูลได้')
        success.value = 'ทำรายการสำเร็จ'
        emit('closeUpdate') // ปิด modal เมื่ออัปเดตสำเร็จ
      } catch (err) {
        error.value = err.message
      } finally {
        loading.value = false
      }
    }

    return {
      form,
      loading,
      error,
      success,
      submitForm
    }
  }
}
</script>


<style>
</style>
