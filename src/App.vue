<script setup>
import { onMounted, reactive, ref, watch, provide } from 'vue';
import Header from './components/Header.vue';
import CardList from './components/CardList.vue';
import axios from 'axios';
import Drawer from './components/Drawer.vue';

const items = ref([])
const drawerOpen = ref(false)

const closeDrawer = () => {
  drawerOpen.value = false
}

const openDrawer = () => {
  drawerOpen.value = true
}



const filters = reactive({
  sortBy : 'title',
  searchQuery : ''
})





const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

const onChangeSearchInput = (event) => {
  filters.searchQuery = event.target.value
}

const fetchFavorites = async () => {
  try{
    const { data: favorites } = await axios.get(`https://9ac066f574a736c2.mokky.dev/favorites`)

    items.value = items.value.map((item) => {
        const favorite = favorites.find((favorite) => favorite.parentId === item.id)

        if(!favorite){
          return item
        }
        return{
          ...item,
          isFavorite: true,
          favoriteId: favorite.id
        }
    })
  }
  catch(err){
    console.log(err)
  }
}

const addToFavorite = async (item) => {
  try{
    if(!item.isFavorite){
      const obj = {
      parentId : item.id
    }

      item.isFavorite = true

      const {data} = await axios.post(`https://9ac066f574a736c2.mokky.dev/favorites`, obj)

      item.favoriteId = data.id
    }
    else{
      item.isFavorite = false
      await axios.delete(`https://9ac066f574a736c2.mokky.dev/favorites/${item.favoriteId}`)

      item.favoriteId = null
    }
  }
  catch(err){
    console.log(err)
  }
}


const fetchItems = async () => {
  try{
    const params = {
      sortBy: filters.sortBy
    }

    if(filters.searchQuery){
      params.title= `*${filters.searchQuery}*`
    }

    const {data}  = await axios.get(`https://9ac066f574a736c2.mokky.dev/items`,{
      params
    })
    items.value = data.map((obj) => ({
      ...obj,
      isFavorite: false,
      favoriteId : null,
      isAdded: false
     }))
  }
  catch(err){
    console.log(err)
  }
}
onMounted( async () => {
  await fetchItems()
  await fetchFavorites()
})

watch(filters, fetchItems)

provide('cartActions', {
  closeDrawer,
  openDrawer
})

</script>

<template>
  <Drawer v-if="drawerOpen" />



  <div class="w-4/5 m-auto bg-white  rounded-xl shadow-xl mt-14">
    <Header @openDrawer="openDrawer" />

    <div class="p-10">
      <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>
        <div class="flex gap-4">
          <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
            <option value="title">По названию</option>
            <option value="price">По цене (дешевые)</option>
            <option value="-price">По цене (дорогие)</option>
          </select>

          <div class="relative">
            <img src="/search.svg"
            class="absolute left-4 top-2.5" alt="search">
            <input
            @input="onChangeSearchInput"
            type="text"
            class="border
              rounded-md
              py-2 pl-11 pr-4
              outline-none
              focus:border-gray-400"
                placeholder="Поиск...">
          </div>
        </div>
      </div>


      <div class="mt-10">
        <CardList :items="items" @addToFavorite="addToFavorite" />
      </div>


    </div>

  </div>
</template>

<style scoped></style>
