<script setup lang="ts">
import { useRouter } from 'vue-router';
import { useUserStore } from '@/stores/User';
import * as AuthService from '@/_services/AuthService';
import { ref } from 'vue'
import { useCartStore } from '@/stores/cart'

const mobileMenuOpen = ref(false);
const cartStore = useCartStore()
const isCartOpen = ref(false)

function toggleMobileMenu() {
  mobileMenuOpen.value = !mobileMenuOpen.value;
}

// logoutUser et toggleCart doivent venir de ton store ou méthodes


function toggleCart() {
  isCartOpen.value = !isCartOpen.value
}

const router = useRouter();
const User = useUserStore();

async function logoutUser() {
  await AuthService.logout();
  router.push('/login');
}
</script>

<template>
  <div class="app-wrapper">
    <!-- HEADER -->
    <header>
      <div class="container-header">
        <!-- Top bloc -->
        <div class="top-bloc-header">
          <a href="/" class="logo-header">
            <img alt="Revolve Realm logo" src="@/assets/images/RRlogobrown.png" />
          </a>

          <!-- Navigation desktop/auth -->
          <nav class="nav-auth desktop-only">
            <div class="container-account" v-if="User.isLogged">
              <div class="account">
                <button class="deconnexion-button" @click="logoutUser">LOGOUT</button>
                <span>{{ User.user?.email }}</span>
              </div>
              <div class="cart-icon" @click="toggleCart">
                <img src="@/assets/images/cart-icon.svg" alt="Panier" />
                <span v-if="cartStore.totalItems > 0" class="cart-badge">{{ cartStore.totalItems }}</span>
              </div>
            </div>
            <RouterLink class="connexion-header" to="/login" v-else>LOGIN</RouterLink>
          </nav>

          <!-- Burger menu icon (mobile) -->
          <div class="cart-icon" @click="toggleCart">
            <img src="@/assets/images/cart-icon.svg" alt="Panier" />
            <span v-if="cartStore.totalItems > 0" class="cart-badge">{{ cartStore.totalItems }}</span>
          </div>

          <div class="burger-menu mobile-only" @click="toggleMobileMenu">
            <span :class="{ open: mobileMenuOpen }"></span>
            <span :class="{ open: mobileMenuOpen }"></span>
            <span :class="{ open: mobileMenuOpen }"></span>
          </div>
        </div>

        <!-- Bottom bloc desktop -->
        <div class="bottom-bloc-header desktop-only">
          <div class="wrapper">
            <nav>
              <RouterLink to="/">HOME</RouterLink>
              <RouterLink to="/shop">SHOP</RouterLink>
              <RouterLink to="/contact">CONTACT</RouterLink>
            </nav>
          </div>
        </div>

        <!-- Mobile menu -->
        <transition name="slide-fade">
          <div class="mobile-menu" v-if="mobileMenuOpen">
            <nav>
              <div class="container-links">
                <RouterLink to="/" @click="mobileMenuOpen = false">HOME</RouterLink>
                <RouterLink to="/shop" @click="mobileMenuOpen = false">SHOP</RouterLink>
                <RouterLink to="/contact" @click="mobileMenuOpen = false">CONTACT</RouterLink>
              </div>
              <div class="auth-links">
                <div class="account" v-if="User.isLogged">
                  <span>{{ User.user?.email }}</span>
                  <button class="deconnexion-button" @click="logoutUser">LOGOUT</button>
                </div>
                <RouterLink v-else @click="mobileMenuOpen = false" to="/login">LOGIN</RouterLink>
              </div>
            </nav>
          </div>
        </transition>
      </div>
    </header>

    <!-- MAIN CONTENT -->
    <main class="main-content">
      <RouterView />
    </main>

    <!-- PANIER SLIDE-IN -->
    <div class="cart-panel" :class="{ open: isCartOpen }">
      <div class="container-titre-croix">
        <h3 id="h3-cart">My Cart</h3>
        <button class="cart-close-btn" @click="toggleCart">✕</button>
      </div>

      <!-- Panier vide -->
      <div id="panier-vide" v-if="cartStore.items.length === 0">Your cart is empty.</div>

      <!-- Liste des produits -->
      <div v-else class="cart-items">
        <div v-for="item in cartStore.items" :key="`${item.productId}-${item.optionId ?? 'noopt'}`" class="cart-item">
          <div class="container-image-produit-panier">
            <img :src="item.picture ?? '/images/products/fallback.jpg'" :alt="item.name" />
          </div>
          <div class="cart-item-info">
            <p>{{ item.name }}</p>
            <!-- Affichage de la taille -->
            <p v-if="item.size">Size : {{ item.size }}</p>

            <p>
              {{ item.price.toFixed(2) }}€ x
              <input type="number" min="1" v-model.number="item.quantity"
                @change="cartStore.updateQuantity(item.productId, item.optionId ?? null, item.quantity)" />
              <button class="btn-supprimer" @click="cartStore.removeItem(item.productId, item.optionId ?? null)">
                Delete
              </button>
            </p>
          </div>
        </div>
      </div>

      <!-- Total -->
      <div class="cart-total">
        <span>Total : {{ cartStore.totalPrice.toFixed(2) }}€</span>

        <!-- Vider le panier -->
        <div v-if="cartStore.items.length > 0" class="cart-actions">
          <button class="btn-clear-cart" @click="cartStore.clearCart()">Clear cart</button>
        </div>
      </div>
    </div>

    <!-- FOOTER -->
    <footer>
      <div class="container-footer">
        <div class="social-media">
          <span class="titre-social">OUR SOCIAL MEDIA</span>
          <div class="container-reseaux">
            <a href="https://www.instagram.com/revolverealm/" class="container-logo-instagram" target="_blank">
              <img src="@/assets/images/instagram-logo.svg" alt="Logo insta">
            </a>
            <a href="https://www.tiktok.com/@revolverealm" class="container-logo-tiktok" target="_blank">
              <img src="@/assets/images/logo-tiktok.svg" alt="Logo TikTok">
            </a>
          </div>
        </div>
        <a href="/" class="container-logo-footer">
          <img src="@/assets/images/RRlogobrown.png" alt="Logo Revolve Realm">
        </a>
        <div class="container-subscribe">
          <h3 class="titre-subscribe">SUBSCRIBE</h3>
          <span class="text-style">
            Keep up to date with the latest releases, <br>early access passwords & exclusive deals
          </span>
          <input type="text" class="input" placeholder="Email" />
          <button type="submit" class="button">SUBSCRIBE</button>
        </div>
      </div>
      <div class="bottom-container-footer">
        <span class="politique"><a href="/privacy-policy"> PRIVACY POLICY</a> | <a href="/legal-notice">LEGAL NOTICE</a>
          | <a href="/refund-policy">REFUND POLICY</a></span>
        <span class="revolve-realm">REVOLVE REALM™ | 2025</span>
      </div>
    </footer>
  </div>
</template>


<style scoped>
footer {
  display: flex;
  width: 100%;
  height: auto;
  position: relative;
  flex-direction: column;
  justify-content: center;
  padding: 1vw;
  background: var(--color-beige);
  align-items: center;
}

.revolve-realm {
  font-family: nexa-bold;
}

.politique a {
  color: var(--color-brown) !important;
  font-family: 'nexa-bold';
}

.container-image-produit-panier {
  width: fit-content;
  height: auto;
  position: relative;
  display: flex;
}

#panier-vide {
  margin: 1vw;
  font-family: nexa-light;
  color: var(--color-beige);
}

.cart-item-info {
  display: flex;
  flex-direction: column;
  justify-content: center;
  width: 60%;
  gap: 1vw;
  height: auto;
  position: relative;
}

.cart-item-info p {
  margin: 0vw;
  display: flex;
  gap: 1vw;
  font-family: sans-serif;
  align-items: center;
}

.container-titre-croix {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.cart-total {
  margin: 1vw;
  font-size: var(--font-size-texte);
  font-family: sans-serif;
  color: var(--color-beige);
  display: flex;
  justify-content: space-evenly;
  align-items: center;
}

.cart-total span {
  font-weight: bold;
}


.container-image-produit-panier img {
  width: 6vw;
  height: 6vw;
  border: solid 3px var(--color-creme);
  position: relative;
  border-radius: 0.7vw;
  object-fit: cover;
}

#h3-cart {
  margin: 1vw;
  font-family: 'nexa-book';
  font-size: 2vw;
}

.container-footer {
  display: flex;
  justify-content: space-around;
  position: relative;
  width: 100%;
  align-items: center;
  flex-direction: row;
}

.container-logo-footer {
  width: 8%;
  display: flex;
  height: auto;
  position: relative;
}

.app-wrapper {
  display: flex;
  flex-direction: column;
  width: 100%;
  min-height: 100vh;
}

.main-content {
  flex: 1;
  display: flex;
  margin-top: unset;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.container-logo-footer img {
  width: 100%;
  object-fit: cover;
  height: 5.5vw;
  object-position: center;
}

.container-logo-instagram img:hover,
.container-logo-tiktok img:hover {
  transform: scale(1.2);
  filter: drop-shadow(0px 2px grey);
}

.social-media {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 15%;
  height: auto;
}

.titre-social {
  color: var(--color-brown);
}

.titre-subscribe {
  margin-bottom: 0vw;
}

.container-subscribe {
  color: var(--color-brown);
  display: flex;
  flex-direction: column;
  text-align: center;
  justify-content: center;
  gap: 0.5vw;
  align-items: center;
}

.container-logo-instagram {
  width: 54px;
  height: 50px;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
  transition: all 0.3s ease-in-out;
  position: relative;
}

.container-logo-tiktok {
  width: 40px;
  height: 50px;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
  transition: all 0.3s ease-in-out;
  position: relative;
}

.container-logo-instagram img {
  width: 100%;
  height: auto;
  object-fit: cover;
  transition: transform 0.3s ease-in-out;
}

.container-logo-tiktok img {
  width: 100%;
  height: auto;
  object-fit: cover;
  transition: transform 0.3s ease-in-out;
}

.container-reseaux {
  display: flex;
  width: 100%;
  justify-content: center;
  align-items: center;
}

.bottom-container-footer {
  width: 100%;
  color: var(--color-brown);
  height: auto;
  display: flex;
  margin-top: 2.5vw;
  padding: 0vw 2vw;
  justify-content: space-between;
}

header {
  max-height: 100vh;
  width: 100%;
  position: fixed;
  z-index: 999;
  padding-top: 1vw;
  padding-bottom: 1vw;
  background-color: #F2EADF;
  justify-content: center;
  align-items: center;
  display: flex;
  box-shadow: 0px 0px 8px black;
}

.deconnexion-button {
  width: fit-content;
  text-decoration: none;
  background: none;
  color: var(--color-brown);
  transition: 0.4s;
  font-family: nexa-bold;
  padding: 0 0.5vw;
  border: none;
  border-left: 1px solid var(--color-brown);
  border-right: 1px solid var(--color-brown);
}

.deconnexion-button:hover {
  background-color: #D9CCC1;
}

.container-account {
  position: absolute;
  top: 0vw;
  right: 1vw;
  gap: 1vw;
  display: flex;
  flex-direction: row-reverse;
  justify-content: flex-start;
  align-items: center;
}

.connexion-header {
  position: absolute;
  top: 0vw;
  right: 1vw;
}

.top-bloc-header {
  display: flex;
  width: 100%;
  height: auto;
  justify-content: center;
  align-items: center;
  position: relative;
}

.bottom-bloc-header {
  display: flex;
  position: relative;
  justify-content: center;
  align-items: center;
}

.container-header {
  width: 100%;
  height: auto;
  display: flex;
  position: relative;
  gap: 2vw;
  flex-direction: column;
}

.logo-header {
  display: block;
  width: 8%;
  height: auto;
  position: relative;
}

.logo-header img {
  width: 100%;
  height: 5.5vw;
  object-fit: cover;
  object-position: center;
}

nav {
  width: fit-content;
  font-size: 0.8vw;
  height: auto;
  display: block;
  text-align: center;
}

nav a.router-link-exact-active:hover {
  background-color: transparent;
}

nav a {
  display: inline-block;
  padding: 0 1rem;
  color: #403933;
  font-family: nexa-bold;
  border-left: 1px solid var(--color-brown);
}

.bottom-bloc-header .wrapper nav a:first-child {
  border-left: unset;
}

.bottom-bloc-header .wrapper nav a:hover {
  background-color: #D9CCC1;
}

.account a {
  padding: 0 0.5vw;
  border-left: unset;
}

.account a:hover {
  background-color: #D9CCC1;
}

.account span {
  padding: 0 0.5vw;
  font-family: nexa-bold;
  color: var(--color-brown);
  font-style: italic;
  text-transform: uppercase;
}

nav a:first-of-type {
  color: #403933;
}

.nav-auth .connexion-header:hover {
  background-color: #D9CCC1;
}






/* Icône panier */
.cart-icon {
  position: relative;
  cursor: pointer;
  width: 4%;
  display: flex;
  height: auto;
}

.cart-icon img {
  width: 100%;
  height: auto;
  object-fit: cover;
  position: relative;
}

.cart-badge {
  position: absolute;
  top: 0px;
  right: -5px;
  background: red;
  color: white;
  font-family: system-ui;
  font-size: 0.6rem;
  font-weight: bold;
  border-radius: 60%;
  padding: 0px 4px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}







/* Panneau panier */
.cart-panel {
  position: fixed;
  top: 0;
  right: -30vw;
  width: 30vw;
  height: 100%;
  background-color: var(--color-brown);
  box-shadow: -2px 0 5px rgba(0, 0, 0, 0.3);
  transition: right 0.3s ease;
  z-index: 1000;
  display: flex;
  flex-direction: column;
}

.cart-panel.open {
  right: 0;
}

.cart-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem;
  border-bottom: 1px solid #ccc;
}

.close-cart {
  background: none;
  border: none;
  font-size: 1.2rem;
  cursor: pointer;
}

.cart-items {
  padding: 1rem;
  overflow-y: auto;
}

.cart-item {
  padding: 1.5rem 0;
  display: flex;
  gap: 3vw;
  border-bottom: 1px solid var(--color-beige);
  align-items: center;
  justify-content: flex-start;
}

.cart-close-btn {
  position: relative;
  right: 15px;
  color: var(--color-beige);
  border: none;
  background: transparent;
  font-size: 24px;
  cursor: pointer;
  line-height: 1;
}




/**************** CSS MODIFIER / SUPPRIMER / VIDER LE PANIER ******************/

/* Input quantité */
.cart-item-info input[type="number"] {
  width: 50px;
  display: flex;
  padding: 0px 3px;
  font-family: sans-serif;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 15px;
}

/* Bouton supprimer un produit */
.btn-supprimer {
  display: flex;
  padding: 4px 8px;
  background-color: #ff4d4d;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 12px;
  transition: background-color 0.2s ease;
}

.btn-supprimer:hover {
  background-color: #e60000;
}

/* Bouton vider le panier */
.btn-clear-cart {
  display: flex;
  padding: 10px 15px;
  font-family: sans-serif;
  width: fit-content;
  background-color: var(--color-beige);
  color: var(--color-brown);
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-weight: bold;
  transition: background-color 0.2s ease;
}

.btn-clear-cart:hover {
  background-color: #ff4d4d;
  color: white;
}

/* Ajustement de la section info pour que tout soit aligné */
.cart-item-info {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}















/* Desktop / mobile helpers */
.desktop-only {
  display: flex;
}

.mobile-only {
  display: none;
}








@media (max-width: 768px) {

  /* Top header */
  .top-bloc-header {
    display: flex;
    justify-content: flex-end;
    align-items: center;
    padding: 1rem;
  }

  /* Burger menu */
  .burger-menu {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    width: 30px;
    height: 20px;
    cursor: pointer;
  }

  .burger-menu span {
    display: block;
    height: 3px;
    background-color: var(--color-brown);
    border-radius: 2px;
    transition: all 0.3s;
  }

  .burger-menu span.open:nth-child(1) {
    transform: rotate(45deg) translate(7px, 7px);
  }

  .burger-menu span.open:nth-child(2) {
    opacity: 0;
  }

  .burger-menu span.open:nth-child(3) {
    transform: rotate(-45deg) translate(5px, -5px);
  }

  .container-links {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 4vw;
  }

  nav {
    width: fit-content;
    font-size: 5vw;
    height: auto;
    gap: 35vw;
    display: flex;
    text-align: center;
    flex-direction: column;
    justify-content: center;
  }

  /* Mobile menu */
  .mobile-menu {
    position: absolute;
    top: 17vw;
    height: 90vh;
    left: 0;
    right: 0;
    background-color: var(--color-brown);
    padding: 1rem;
    display: flex;
    flex-direction: column;
    gap: 1rem;
    z-index: 1000;
    justify-content: center;
    align-items: center;
  }

  nav a {
    display: inline-block;
    padding: 0 1rem;
    color: var(--color-beige) !important;
    font-family: nexa-bold;
    border-left: unset;
  }

  .account {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4vw;
    justify-content: center;
  }

  .deconnexion-button {
    width: fit-content;
    text-decoration: none;
    background: var(--color-beige);
    color: var(--color-brown);
    transition: .4s;
    font-family: nexa-bold;
    padding: 1vw 5vw;
    border: none;
    font-size: 3.5vw;
    border-left: unset;
    border-right: unset;
  }

  .account span {
    padding: 0 .5vw;
    font-family: nexa-bold;
    color: var(--color-beige);
    font-style: italic;
    text-transform: uppercase;
  }

  .slide-fade-enter-active,
  .slide-fade-leave-active {
    transition: all 0.3s ease;
  }

  .slide-fade-enter-from,
  .slide-fade-leave-to {
    opacity: 0;
    transform: translateY(-10px);

  }

  header {
    max-height: 100vh;
    width: 100%;
    position: fixed;
    z-index: 999;
    padding-top: 5vw;
    padding-bottom: 5vw;
    background-color: #f2eadf;
    justify-content: center;
    align-items: center;
    display: flex;
    box-shadow: 0 0 8px #000;
  }

  .logo-header {
    display: block;
    width: 25%;
    height: auto;
    position: absolute;
    left: 38%;
  }

  .logo-header img {
    width: 100%;
    height: 17.5vw;
    object-fit: cover;
    object-position: center;
  }


  .desktop-only {
    display: none;
  }

  .mobile-only {
    display: flex;
  }









  .container-logo-footer {
    width: 33%;
    display: flex;
    height: auto;
    position: relative;
  }

  .container-logo-footer img {
    width: 100%;
    object-fit: cover;
    height: 33vw;
    object-position: center;
  }

  .social-media {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 33%;
    height: auto;
  }

  .titre-social {
    color: var(--color-brown);
    font-size: 4vw;
    text-align: center;
  }

  .titre-subscribe {
    margin-bottom: 0vw;
    font-size: 4vw;
  }

  .text-style {
    font-size: 2vw;
  }

  .button {
    background-color: #f2eadf;
    color: #403933;
    padding: .5vw 1vw;
    border: none;
    font-size: 3vw;
    cursor: pointer;
  }

  .input {
    font-size: 2.5vw;
    width: 80%;
    height: auto;
  }

  .container-subscribe {
    color: var(--color-brown);
    display: flex;
    flex-direction: column;
    width: 33%;
    text-align: center;
    justify-content: center;
    gap: .5vw;
    align-items: center;
  }

  .bottom-container-footer {
    width: 100%;
    color: var(--color-brown);
    height: auto;
    display: flex;
    margin-top: unset;
    padding: 0vw 2vw;
    justify-content: center;
    flex-direction: column;
    align-items: center;
  }

  .politique {
    font-size: 2.5vw;
  }

  .revolve-realm {
    font-family: nexa-bold;
    font-size: 2.5vw;
  }




















}
</style>