<template>
  <!-- Välkomstmeddelande och sökfunktion -->
  <BContainer fluid class="containerSearch">
    <BRow class="my-1">
      <BCol cols="12">
        <label for="search" class="labelForSearch"
          >Välkommen till SpendLess!</label
        >
      </BCol>
      <BCol cols="12">
        <!-- Sökinput för att användaren ska kunna skriva in en sökterm -->
        <BFormInput
          v-model="search"
          placeholder="Vad letar du efter idag?"
          class="inputSearch"
        />
      </BCol>
      <BCol cols="12">
        <!-- Sökknapp för att trigga en sökning -->
        <BButton variant="success" class="inputButton" @click="searchProduct"
          >Sök</BButton
        >
      </BCol>
    </BRow>
  </BContainer>

  <!-- Kategorival för att filtrera produkter -->
  <BRow id="categoryitem">
    <BCol cols="4">
      <BFormSelect
        v-model="selected"
        :options="productCategory"
        placeholder="Välj kategori"
        class="categoryChooser"
      />
      <BFormSelectOption :value="null" disabled></BFormSelectOption>
    </BCol>
  </BRow>

  <!-- Produktvisning -->
  <div id="containerForObject">
    <div v-if="search.trim() !== ''" class="cardContainer">
      <!-- Loopar genom filtrerade produkter och visar dem som kort -->
      <div
        class="card"
        v-for="product in filteredProducts"
        :key="product.id"
        @click="chosenProduct(products)"
      >
        <router-link :to="`selectedproduct/${product.id}`">
          <img :src="product.img" class="card-img-top" alt="" />
          <div class="card-body">
            <h5 class="card-title">{{ product.namn }}</h5>
            <p class="card-text">{{ product.adress }}</p>
            <p>{{ product.pris }} :-</p>
            <button class="btn btn-success knapp">Kontakta säljare</button>
          </div>
        </router-link>
      </div>
    </div>
    <!-- Visar standardprodukter om ingen sökning gjorts -->
    <div v-if="search === ''">
      <ItemsObject />
    </div>
  </div>
</template>

<script setup>
  import { ref, computed } from 'vue'
  import ItemsObject from '../components/ItemsObject.vue'

  // Lista av produktkategorier
  const productCategory = [
    { value: 'null', text: 'Välj en kategori' },
    { value: 'a', text: 'Växter' },
    { value: 'b', text: 'Elektronik' },
    { value: 'c', text: 'Heminredning' },
    { value: 'd', text: 'Hobby' },
    { value: 'e', text: 'Sport' }
  ]

  // Reaktiva variabler för kategorival, söktext och produktlista
  const selected = ref(null)
  const search = ref('')
  const products = ref([])

  // Funktion för att hämta produkter från en JSON-fil
  const productFetch = async () => {
    try {
      const response = await fetch('/ItemsObjectData.json')
      if (!response.ok) throw new Error('Nätverksfel vid hämtning av JSON')
      products.value = await response.json()
      console.log('Hämtade produkter:', products.value)
    } catch (error) {
      console.error('Fel vid hämtning:', error)
    }
  }

  // Filtrerar produkter baserat på söktext
  const filteredProducts = computed(() => {
    return products.value.filter((product) => {
      return product.namn.toLowerCase().includes(search.value.toLowerCase())
    })
  })

  // Funktion som hanterar sökning (loggar endast sökningen eftersom filtrering redan sker automatiskt)
  function searchProduct() {
    console.log('Sökning:', search.value)
  }

  // Funktion som hanterar val av produkt
  function chosenProduct(product) {
    console.log('Vald produkt:', product)
  }

  // Hämtar produkter vid start
  productFetch()
</script>

<style scoped>
  /* CSS för desktop här */

  @media screen and (min-width: 768px) {
    * {
      box-sizing: border-box;
      /* margin: 20px; */
    }

    .containerSearch {
      background-image: url(/assets/coffee-3989866_1280.jpg);
      background-size: 100%;
      background-position: 100%;
      min-height: 20rem;
      display: flex;
      justify-content: center;
      width: 100vw;
      margin: 20px;
      display: flex;
      margin: 0;
    }

    .labelForSearch {
      color: white;
      font-size: 50px;
      font-weight: 900;
      margin-bottom: 0;
      /* font-family:'Times New Roman', Times, serif; */
    }

    .inputSearch {
      font-size: 20px;
      padding-inline: 20px;
      padding: 10px;
      font-family: 'Times New Roman', Times, serif;
      border-radius: 5px;
      width: 50rem;
    }

    .categoryChooser {
      min-width: 20vw;
      font-size: 20px;
    }

    .inputButton {
      margin-top: 5px;
      width: 100px;
      padding: 0;
      height: 50px;
      font-size: 20px;
    }

    .my-1 {
      width: auto;
      display: flex;
      justify-content: center;
      flex-direction: column;
      align-items: center;
    }

    #categoryitem {
      /* display: flex; */
      /* background-color: black; */
      width: 100vw;
      padding: 10px;
      font-size: 100px;
      /* margin-top: 10px; */
    }

    #containerForObject {
      width: 100vw;
      padding: 5px;
    }
    .cardContainer {
      display: flex;
      flex-wrap: wrap;
      /* align-items: center; */
      justify-content: center;
    }

    .card {
      margin: 30px;
      height: 450px;
      box-sizing: border-box;
      position: relative;
      width: 250px;
    }

    .knapp {
      position: absolute;
      font-size: 12px;
      width: auto;
      height: fit-content;
      bottom: 0;
      align-items: center;
      margin-bottom: 10px;
    }
    .imgObject {
      max-width: 250px;
      max-height: 270px;
    }
  }

  /* här börjar css för mobil */

  @media screen and (max-width: 768px) {
    * {
      box-sizing: border-box;
    }

    #containerForAccounts {
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      z-index: 1;
    }

    .containerSearch {
      background-image: url(/assets/coffee-3989866_1280.jpg);
      background-size: cover;
      background-repeat: no-repeat;
      background-position: 100%;
      min-height: 300px;
      /* width: 100vw; */
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      /* margin: 0; */
    }

    .labelForSearch {
      color: white;
      font-size: 25px;
      font-weight: 900;
      /* font-family:'Times New Roman', Times, serif; */
    }

    .inputSearch {
      font-size: 20px;
      /* padding-inline: 20px; */
      font-family: 'Times New Roman', Times, serif;
      border-radius: 5px;
    }

    .categoryChooser {
      min-width: 200px;
    }

    .inputButton {
      margin-top: 10px;
      width: 100px;
      padding: 10px;
      height: 50px;
    }

    .my-1 {
      display: flex;
      /* justify-content: flex-start; */
      flex-direction: column;
      /* align-items: flex-start; */
      width: 100vw;
    }

    #categoryitem {
      /* display: flex; */
      /* background-color: black; */
      width: 100vw;
      margin-top: 10px;
    }

    #containerForObject {
      width: 100vw;
      padding: 10px;
    }
    .cardContainer {
      display: flex;
      flex-wrap: wrap;
      /* align-items: center; */
      justify-content: center;
    }

    .card {
      margin: 10px;
      height: 350px;
      box-sizing: border-box;
      position: relative;
    }

    .knapp {
      position: absolute;
      font-size: 12px;
      width: auto;
      height: fit-content;
      bottom: 0;
      align-items: center;
      margin-bottom: 10px;
    }

    /* #containerForAccounts button {
  	/* width: 80px;
  	height: 30px;
  	margin-top: 10px; */
    /* cursor: pointer; */
    /* }  */

    /* input[type="submit"] {
  	cursor: pointer;
  	width: 80px;
  	height: 30px;
  	margin-top: 10px;
  	font-size: 10px;
  } */
  }
</style>
