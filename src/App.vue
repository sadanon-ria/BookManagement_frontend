<template>
  <div>
    <!-- ************** Loader / Error ***************** -->
    <!-- ไม่น่าต้องมีเพราะเอาไปใช้ใน component book-list แล้ว -->
    <!-- <appLoader v-if="loading" /> -->
    <!-- <div v-if="error" class="fixed top-4 right-4 z-50">
      <appError :message="errorMessage" />
    </div> -->
    <!-- ********************************************* -->

    <book-list @open-add="showModal = true" @open-update="handleOpenUpdate"></book-list>
    <!-- add book ใช้  showModal -->
    <div v-if="showModal" class="fixed inset-0 bg-black/50 flex justify-center items-center z-50">
      <div class="p-6 rounded-lg w-full max-w-md">
        <bookFormVue @close="showModal = false "/>
      </div>
    </div>

    <!-- update book ใช้  -->
    <div v-if="showUpdate" class="fixed inset-0 bg-black/50 flex justify-center items-center z-50">
      <div class="p-6 rounded-lg w-full max-w-md">
        <updateBookVue :book="selectedBook" @closeUpdate="showUpdate = false "/>
      </div>
    </div>   
  </div>
  
  
</template>

<script>
import bookFormVue from './components/bookForm.vue'
import bookList from './components/bookList.vue'
import updateBookVue from './components/updateBook.vue'

export default {
  name: 'App',
  components: {
    bookList,
    bookFormVue,
    updateBookVue
  },
}
</script>
<script setup>
import { ref } from 'vue'
const showModal = ref(false)
const showUpdate = ref(false)
const selectedBook = ref(null) 

const handleOpenUpdate = (book) => {

  selectedBook.value = book
  showUpdate.value = true
}

</script>


<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  margin-top: 60px;
  padding: 50px;
}
</style>
