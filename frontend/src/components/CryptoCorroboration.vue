<template>
  <div>
    Origin of the crypto funds
    <q-select
      :options="originCryptoFundChoice1"
      v-model="originCryptoFundData1"
      outlined
    />
    Origin of the crypto funds
    <q-select
      :options="originCryptoFundChoice2"
      v-model="originCryptoFundData2"
      outlined
    />
    First supporting document (if you have any other document to share with us)
    <q-uploader
      outlined
      :label="formStatus.individual.utilityBill"
    />
    Second supporting document (if you have any other document to share with us)
    <q-uploader
      outlined
      :label="formStatus.individual.utilityBill"
    />
    Third supporting document (if you have any other document to share with us)
    <q-uploader
      outlined
      :label="formStatus.individual.utilityBill"
    />

    <q-stepper-navigation>
      <q-btn
        @click="patchCryptoCorroboration(done6)"
        color="primary"
        label="Continue"
      />
      <q-btn
        flat
        @click="step = 5"
        color="primary"
        label="Back"
        class="q-ml-sm"
      />
    </q-stepper-navigation>
  </div>
</template>
<script>
import axios from 'axios'

export default {
  data: () => ({
    formStatus: {
      individual: {
        IdCardFront: '',
        IdCardBack: '',
        firstname: '',
        lastname: '',
        dateOfBirth: '',
        residentialAddress: '',
        zipCode: '',
        city: '',
        country: '',
        nationality: '',
        professionalActivity: '',
        utilityBill: ''
      }
    },
    originCryptoFundData1: '',
    originCryptoFundData2: '',
    originCryptoFundChoice1: [
      'Exchange', 'Otc', 'Mined', 'Other'
    ],
    originCryptoFundChoice2: [
      `I'm a trader`, `I'm an early adopter`, `I'm an ico investor`, `I'm a miner`, `Goods and services`
    ]
  }),
  created () {
    this.subscriptionData()
  },
  methods: {
    patchCryptoCorroboration (value) {
      axios(`https://api-staging.altcoinomy.com/api/v1/subscriptions/${this.$route.params.id}`, {
        method: 'patch',
        data: {
          'subscription_id': `${this.$route.params.id}`,
          'groups': {
            'crypto_tracing': {
              'fields': {
                'origin_of_funds': {
                  'value': '7898'
                }
                // 'exchange_source': 'Binance',
                // 'supporting_document1': '',
                // 'supporting_document2': '',
                // 'supporting_document3': ''
              }
            }
          }
        },
        headers: {
          'Authorization': `Bearer ${this.$store.state.token}`
        }
      }).then(res => {
        console.log(res.data)
        this.value = true
        this.step = 6
      })
    },
    subscriptionData () {
      console.log(this.$route.params.id)
      if (this.$route.params.id !== undefined) {
        axios(`https://api-staging.altcoinomy.com/api/v1/subscriptions/${this.$route.params.id}/fill-status`, {
          method: 'get',
          headers: {
            'Authorization': `Bearer ${this.$store.state.token}`
          }
        }).then(res => {
          console.log(res.data.groups)
          this.formStatus.subscribeAs = res.data.groups.basics.fields.subscribed_as.status
        })
      }
    }
  }
}
</script>
