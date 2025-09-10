<template>
  <div class="formulaire-contact">
    <form @submit.prevent="sendMessage" class="top-container">
      <div class="container-bloc-titre-connexion">
        <h1 class="form-title">CONTACT</h1>
        <span class="texte-style">Contact us by filling out the form if you have any questions — we’ll get back to you
          as quickly as possible.</span>
      </div>
      <div class="container-champs">
        <input v-model="form.name" type="text" placeholder="Your name" required class="border p-2 w-full" />
        <input v-model="form.email" type="email" placeholder="Your e-mail" required class="border p-2 w-full" />
        <input v-model="form.subject" type="text" placeholder="Subject" class="border p-2 w-full" />
        <textarea v-model="form.message" placeholder="Your message" required class="border p-2 w-full"></textarea>
        <div class="form-group">
          <button type="submit" class="button">Send</button>
        </div>
      </div>
      <p v-if="successMessage" class="text-envoie-style">{{ successMessage }}</p>
      <p v-if="errorMessage" class="text-envoie-style">{{ errorMessage }}</p>
    </form>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import Axios from '@/_services/CallerService'
import { useHead } from '@vueuse/head'

useHead({
  title: 'Contact | Revolve Realm',
  meta: [
    { name: 'description', content: "Get in touch with Revolve Realm. For questions, collaborations, or support, reach out to our team — we’re here to help you." },
    { property: 'og:title', content: 'Contact | Revolve Realm' }
  ],
  link: [
    { rel: 'canonical', href: 'https://projet-front.revolverealm.com/contact' }
  ]
})

// Formulaire
const form = ref({
  name: '',
  email: '',
  subject: '',
  message: ''
})

// Messages d'état
const successMessage = ref('')
const errorMessage = ref('')

const sendMessage = async () => {
  successMessage.value = ''
  errorMessage.value = ''

  try {
    // POST vers Laravel API publique
    const res = await Axios.post('https://projet.revolverealm.com/api/messages', form.value, {
      headers: { 'Content-Type': 'application/json' }
    })

    // Message de succès et reset du formulaire
    successMessage.value = res.data.message
    form.value = { name: '', email: '', subject: '', message: '' }

  } catch (err: any) {
    // Gestion des erreurs
    if (err.response) {
      errorMessage.value = err.response.data.message || 'Erreur lors de l\'envoi du message.'
    } else {
      errorMessage.value = 'Impossible de contacter le serveur.'
    }
    console.error(err)
  }
}
</script>

<style scoped>
.top-container {
  width: 60%;
  box-shadow: 6px 10px 9px gray;
  background: #403933;
  padding: 2vw;
  gap: 2vw;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

h1 {
  color: rgb(242, 234, 223);
  margin-bottom: 0vw;
  font-size: 2rem;
}

.top-container input {
  border-radius: 0vw;
  border: unset;
  padding: .5vw;
  font-family: nexa-regular;
  width: 19vw;
}

.container-champs {
  display: flex;
  flex-direction: column;
  position: relative;
  gap: .8vw;
  justify-content: center;
  align-items: center;
}

.top-container textarea {
  border-radius: 0vw;
  border: unset;
  padding: .5vw;
  font-family: nexa-regular;
  width: 19vw;
}

.button {
  background-color: #f2eadf;
  color: #403933;
  padding: .5vw 1vw;
  border: none;
  cursor: pointer;
}

.form-group {
  border-radius: 0vw;
  border: unset;
  font-family: nexa-regular;
  width: 13vw;
  margin-top: 1.5vw;
}

.formulaire-contact {
  display: flex;
  flex-direction: column;
  width: 100%;
  height: auto;
  flex: 1;
  padding: 7vw;
  position: relative;
  justify-content: center;
  align-items: center;
}

@media screen and (max-width: 767px) {

  .formulaire-contact {
    display: flex;
    flex-direction: column;
    width: 100%;
    height: auto;
    flex: 1;
    padding: 7vw;
    position: relative;
    justify-content: center;
    align-items: center;
  }

  .top-container {
    width: 100%;
    box-shadow: 6px 10px 9px gray;
    background: #403933;
    padding: 5vw;
    gap: 5vw;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }

  .texte-style {
    font-size: 3.5vw;
  }

  .container-champs {
    display: flex;
    flex-direction: column;
    position: relative;
    gap: 2vw;
    justify-content: center;
    align-items: center;
  }

  .top-container input {
    border-radius: 0vw;
    border: unset;
    padding: .5vw;
    font-family: nexa-regular;
    font-size: 3vw;
    width: 50vw;
  }

  .top-container textarea {
    border-radius: 0vw;
    border: unset;
    padding: .5vw;
    font-family: nexa-regular;
    width: 50vw;
    font-size: 3vw;
  }

  .button {
    background-color: #f2eadf;
    color: #403933;
    padding: 1.5vw 6vw;
    border: none;
    font-size: 3vw;
    cursor: pointer;
  }

  .form-group {
    border-radius: 0vw;
    border: unset;
    font-family: nexa-regular;
    width: fit-content;
    margin-top: 5vw;
  }

  .text-envoie-style {
    font-size: 3vw;
  }







}
</style>
