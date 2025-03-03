<template>
  <h1 v-if="products">{{ products.namn }}</h1>
  <div class="card" v-if="products">
    <img
      :src="Array.isArray(products.img) ? products.img[0] : products.img"
      class="card-img-top"
      alt="produktkort"
      style="max-width: 350px"
    />
    <div class="card-body">
      <h4 class="card-title">{{ products.namn }}</h4>
      <p class="card-text">{{ products.beskrivning }}</p>
      <p>Varan finns i {{ products.adress }}</p>
      <p>Varans skick: {{ products.skick }}</p>
      <p>Pris: {{ products.pris }} :-</p>
      <div class="säljare-info">
        <BAvatar class="avatarPicture" />
        <p>Varan säljs av {{ products.säljare }}</p>
      </div>
      <button class="btn btn-success" @click="showModal = true">
        Kontakta säljare
      </button>
    </div>
  </div>

  <!-- Modal -->
  <div v-if="showModal" class="modal">
    <!-- Innehållet i modalen -->
    <div class="modal-content">
      <span class="close" @click="showModal = false">&times;</span>
      <h2>Skicka meddelande</h2>
      <label for="message">Meddelande till säljaren:</label>

      <!-- Textrutan -->
      <textarea
        v-model="messageText"
        id="message"
        placeholder="Skriv ditt meddelande..."
      />

      <!-- Knappar till modalen -->
      <div class="buttons">
        <button class="btn-cancel" @click="showModal = false">Avbryt</button>
        <button class="btn-send" @click="sendMessage">Skicka</button>
      </div>
    </div>
  </div>
</template>

<script setup>
  import { ref, onMounted } from 'vue'
  import { useRoute } from 'vue-router'
  import { createAccountStore } from '../store'

  const route = useRoute()
  const productId = route.params.id
  const products = ref(null)
  const showModal = ref(false) // Stängd från start
  const messageText = ref('')
  const store = createAccountStore()

  // Funktion för att skicka meddelande
  const sendMessage = () => {
    // Om textfältet är tomt
    if (!messageText.value) {
      alert('För att skicka måste du skriva ett meddelande!')
      return
    }
    const newMessage = {
      productId: productId,
      productName: products.value?.namn,
      seller: products.value?.säljare,
      message: messageText.value
    }
    store.sendMessage(newMessage)

    console.log(`Meddelande skickat: "${messageText.value}" till säljaren.`)

    // Rensar textrutan efter att meddelandet har skickats
    messageText.value = ''
    // Stänger modalen efter att meddelandet har skickats
    showModal.value = false
  }

  function fetchProductDetails() {
    fetch(`/ItemsObjectData.json`) // Se till att filen finns i "public"-mappen
      .then((response) => {
        if (!response.ok) {
          throw new Error('Nätverksfel vid hämtning av JSON')
        }
        return response.json()
      })
      .then((data) => {
        const product = data.find((p) => p.id === Number(productId))
        products.value = product // Uppdatera refens .value
        console.log(products.value) // Logga datan EFTER att den har laddats
      })
      .catch((error) => console.error('Fel vid hämtning:', error))
  }

  onMounted(fetchProductDetails)
</script>

<style scoped>
  @media screen and (min-width: 768px) {
    .card {
      margin-left: 25px;
      margin-right: 25px;
      display: flex;
      flex-direction: row;
    }

    h1 {
      margin-left: 25px;
    }

    .säljare-info {
      display: flex;
      align-items: center;
    }

    .avatarPicture {
      margin-right: 10px;
      margin-bottom: 15px;
    }
    .modal {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0, 0, 0, 0.5);
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .modal-content {
      background: white;
      padding: 20px;
      width: 400px;
      border-radius: 8px;
      box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.3);
      text-align: center;
      position: relative;
    }

    .close {
      position: absolute;
      top: 10px;
      right: 15px;
      font-size: 24px;
      cursor: pointer;
    }

    textarea {
      width: 100%;
      height: 80px;
      margin-top: 10px;
      padding: 10px;
      border: 1px solid #ccc;
      border-radius: 4px;
    }

    .buttons {
      display: flex;
      justify-content: space-between;
      margin-top: 15px;
    }

    .btn-cancel {
      background: #ccc;
      color: black;
      padding: 10px;
      border: none;
      cursor: pointer;
      border-radius: 4px;
    }

    .btn-cancel:hover {
      background: #bbb;
    }

    .btn-send {
      background: green;
      color: white;
      padding: 10px;
      border: none;
      cursor: pointer;
      border-radius: 4px;
    }

    .btn-send:hover {
      background: darkgreen;
    }
  }

  @media screen and (max-width: 768px) {
    .card {
      /* margin-left: 25px; */
      /* margin-right: 25px; */
      display: flex;
      flex-direction: column;
      justify-content: center;
      width: 100vw;
    }

    .card-img-top {
      max-height: 200px;
      width: 300px;
    }

    h1 {
      display: none;
    }

    .säljare-info {
      display: flex;
      align-items: center;
    }

    .avatarPicture {
      margin-right: 10px;
      margin-bottom: 15px;
    }
    .modal {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0, 0, 0, 0.5);
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .modal-content {
      background: white;
      padding: 20px;
      width: 400px;
      border-radius: 8px;
      box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.3);
      text-align: center;
      position: relative;
    }

    .close {
      position: absolute;
      top: 10px;
      right: 15px;
      font-size: 24px;
      cursor: pointer;
    }

    textarea {
      width: 100%;
      height: 80px;
      margin-top: 10px;
      padding: 10px;
      border: 1px solid #ccc;
      border-radius: 4px;
    }

    .buttons {
      display: flex;
      justify-content: space-between;
      margin-top: 15px;
    }

    .btn-cancel {
      background: #ccc;
      color: black;
      padding: 10px;
      border: none;
      cursor: pointer;
      border-radius: 4px;
    }

    .btn-cancel:hover {
      background: #bbb;
    }

    .btn-send {
      background: green;
      color: white;
      padding: 10px;
      border: none;
      cursor: pointer;
      border-radius: 4px;
    }

    .btn-send:hover {
      background: darkgreen;
    }
  }
</style>
