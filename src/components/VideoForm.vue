<template>
    <div>
        <v-form ref="form" v-model="valid" lazy-validation>
            <v-text-field
                v-model="url"
                :rules="YTurlRules"
                label="Enter YouTube video url here..."
                required>
            </v-text-field>
            <v-btn
                :disabled="!valid"
                @click="submit">
                Encrypt
            </v-btn>
        </v-form>
        <div class="text-xs-center mt-5">
            <v-progress-linear
                v-model="value"
                height="4"
                width="4"
                :active="show"
                :indeterminate="query"
                :query="true">
            </v-progress-linear>
        </div>
    </div>
</template>

<script>
import axios from 'axios'

export default {
    data: () => ({
        valid: true,
        url: '',
        YTurlRules: [
            v => !!v || 'Video url is required',
            v => (v && v.length > 10) || 'Url must be more than 10 characters',
            // v => /.+http.+/.test(v) || 'not valid url'
        ],
        value: 0,
        query: false,
        show: true,
        interval: 0
    }),

    methods: {
      submit () {
        if (this.$refs.form.validate()) {
          axios.post('/api/download', {
            url: this.url,
          })
        }
      },
      queryAndIndeterminate () {
        this.query = true
        this.show = true
        this.value = 0

        setTimeout(() => {
          this.query = false

          this.interval = setInterval(() => {
            if (this.value === 100) {
              clearInterval(this.interval)
              this.show = false
              return setTimeout(this.queryAndIndeterminate, 2000)
            }
            this.value += 25
          }, 1000)
        }, 2500)
      }
    },

    mounted () {
      this.queryAndIndeterminate()
    },

    beforeDestroy () {
      clearInterval(this.interval)
    },
  }
</script>

<style scoped lang="stylus">

</style>
