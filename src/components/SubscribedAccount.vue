<template>
  <div class="row">
    <div class="col">
      <h2>Subscribed Account</h2>
      
      <div class="alert alert-warning" v-if="subscription.cancel_at_period_end">
        This subscription will cancel at the end of the period.
      </div>
      <p>
        Current period start:
        {{
          new Date(
            subscription.current_period_start.seconds * 1000
          ).toLocaleString()
        }}
      </p>

      <p>
        Current period end:
        {{
          new Date(
            subscription.current_period_end.seconds * 1000
          ).toLocaleString()
        }}
      </p>

      <button class="btn btn-primary" 
              :disabled="isLoading"
              @click="openCustomerPortal">
        {{ isLoading ? "Loading..." : "Open my billing portal" }}
      </button>
    </div>
  </div>
</template>

<script>
import { getFunctions, httpsCallable } from "firebase/functions";

export default {
  props: ["subscription"],

  data() {
    return {
      isLoading: false,
    }
  },

  methods: {
    async openCustomerPortal() {
      this.isLoading = true;

      const functions = getFunctions();
      const functionRef = httpsCallable(
        functions,
        "ext-firestore-stripe-payments-createPortalLink"
      );
      const { data } = await functionRef({
        returnUrl: window.location.origin,
      });

      window.location.assign(data.url);
    },
  },
}
</script>