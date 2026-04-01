<template>
  <main class="modal-card">
    <header class="modal-card-head">
      <span class="modal-card-title has-text-weight-semibold">Incoming Call</span>
    </header>

    <section class="modal-card-body">
      <o-loading v-model="isLoading" :is-full-page="false" />
    </section>

    <footer class="modal-card-foot">
      <button class="button is-success" @click="answerCall">Accept</button>
      <button class="button is-danger" @click="declineCall">Decline</button>
      <span v-if="error" class="is-flex-grow-1 has-text-right has-text-danger">{{ error }}</span>
    </footer>
  </main>
</template>

<script>
export default {
  name: 'RingingAlert',
  props: {
    from: {
      type: Object,
      default: {},
    },
    jingleId: {
      type: String,
      required: true,
    },
    hasCancelButton: {
      type: Boolean,
      default: true,
    },
  },
  emits: [
    'close',
  ],
  data () {
    return {
      isLoading: false,
      error: null,
    }
  },
  computed: {
  },
  created () {
    // detect if the call is ringing
    window.addEventListener('ringing', () => {
      console.log('ALERT! RINGING!')
    })
  },
  methods: {
    async answerCall () {
      console.log('CALL = ANSWERED')
      this.$xmpp.sendCallAccepted(this.from, this.jingleId)
      this.$emit('close')
    },
    async declineCall() {
      console.log('CALL = DECLINE')
      this.$xmpp.sendCallDeclined(this.from, this.jingleId)
      this.$emit('close')
    },
    async saveRoomConfiguration () {
      this.isLoading = true
      try {
        await this.$xmpp.setRoomConfig(this.roomJid, this.form)
        this.$parent.$emit('saved')
        this.$emit('close')
      } catch (error) {
        this.error = error.message ? error.message : 'Oups, an error occurs'
      }
      this.isLoading = false
    },
  },
}
</script>
