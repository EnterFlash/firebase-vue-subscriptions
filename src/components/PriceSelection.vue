<template>
  <div class="row">
    <div class="col">
      
      <h2>Subscription Plans</h2>
      
      <hr />

      <div v-for="(product, index) in products" 
            :key="index + '-product'">
        <h3>{{ product.name }}</h3>

        <div
          v-for="(price, priceIndex) in product.prices"
          :key="priceIndex + '-price'">
          <div class="form-check">

            <input
              type="radio"
              class="form-check-input"
              v-model="selectedPrice"
              :value="price.id"
              :id="price.id" />
            
            <label :for="price.id" class="form-check-label">
              {{ price.interval }}
            </label>

          </div>
        </div>
      </div>

      <button
        class="btn btn-primary"
        @click="createSub"
        :disabled="!selectedPrice || isLoading"
      >
        {{ isLoading ? "Loading..." : "Create subscription" }}
      </button>
    </div>
  </div>
</template>

<script>
import {
  getFirestore,
  getDocs,
  where,
  query,
  collection,
  addDoc,
  onSnapshot,
} from "firebase/firestore";

import { firebaseAuth } from "@/firebase";

export default {
  data() {
    return {
      products: [],
      selectedPrice: null,
      isLoading: false,
    };
  },
  mounted() {
    this.fetchProducts();
  },
  methods: {
    async fetchProducts() {
      const db = getFirestore();

      const productsRef = collection(db, "products");
      const productsQuery = query(productsRef, where("active", "==", true));
      const productsQuerySnap = await getDocs(productsQuery);

      productsQuerySnap.forEach(async (doc) => {
        const pricesRef = collection(db, "products", doc.id, "prices");
        const pricesQuerySnap = await getDocs(pricesRef);

        this.products.push({
          id: doc.id,
          ...doc.data(),
          prices: pricesQuerySnap.docs.map((price) => {
            return {
              id: price.id,
              ...price.data(),
            };
          }),
        });
      });
    },
    async createSub() {
      this.isLoading = true;
      const db = getFirestore();
      const collectionRef = collection(
        db,
        "customers",
        firebaseAuth.currentUser.uid,
        "checkout_sessions"
      );

      const docRef = await addDoc(collectionRef, {
        price: this.selectedPrice,
        success_url: window.location.origin,
        cancel_url: window.location.origin,
      });

      onSnapshot(docRef, (snap) => {
        const { error, url } = snap.data();

        if (error) {
          console.error(`An error occured: ${error.message}`);
          this.isLoading = false;
        }

        if (url) {
          window.location.assign(url);
        }
      });
    },
  },
};
</script>