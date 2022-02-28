<template>
    <default-layout>
        <h1>Account page!</h1>

        <div v-if="isLoading">Loading account information...</div>
        <PriceSelection v-else-if="!subscription" />
        <SubscribedAccount v-else :subscription="subscription"/>
    </default-layout>
</template>

<script>
import DefaultLayout from "@/layouts/DefaultLayout";
import PriceSelection from "@/components/PriceSelection";
import SubscribedAccount from "@/components/SubscribedAccount";
import { firebaseAuth } from "@/firebase";
import {
  getFirestore,
  collection,
  query,
  where,
  getDocs,
} from "firebase/firestore";

export default {
  components: {
    DefaultLayout,
    PriceSelection,
    SubscribedAccount
  },

  data() {
    return {
      isLoading: false,
      subscription: null,
    };
  },

  mounted() {
    this.fetchSubscription();
  },

  methods: {
    async fetchSubscription() {
      this.isLoading = true;
      const db = getFirestore();
      const subsRef = collection(
        db,
        "customers", firebaseAuth.currentUser.uid, "subscriptions"
      );
      const subsQuery = query(
        subsRef,
        where("status", "in", ["trialing", "active", "past_due", "unpaid"])
      );

      this.subscription = await getDocs(subsQuery)
        .then((sub) => 
            sub.docs.length > 0 ? sub.docs[0].data() : null);

      this.isLoading = false;
    },
  },
};
</script>
