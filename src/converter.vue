<template>
  <div>
    <h1>Hypergate Kerberos krb5.conf Converter</h1>
    <el-upload action="" :on-change="convert" :auto-upload="false" single :limit="1">
      <el-button size="small" type="primary">Select krb5.conf File</el-button>
      <div slot="tip" class="el-upload__tip">krb5.conf Kerberos Configuration File</div>
    </el-upload>
    <p>- or -</p>
    <el-input ref="text" type="textarea" :rows="10" v-model="rawKrb5"> </el-input>
    <br />
    <br />
    <el-button @click="convert()">Convert</el-button>

    <div v-if="convertedConfiguration.length > 0">
      <h2>Converted krb5.conf for ManagedConfig</h2>
      <el-input ref="text" type="textarea" readonly :rows="10" v-model="convertedConfiguration"> </el-input>
      <br />
      <br />
      <el-button @click="copy()" :disabled="convertedConfiguration.length === 0">Copy</el-button>
    </div>
  </div>
</template>

<script>
import { Button, Message, Input, Upload } from 'element-ui'

export default {
  components: { 'el-button': Button, 'el-input': Input, 'el-upload': Upload },
  methods: {
    copy() {
      this.$refs.text.select()
      document.execCommand('copy')

      Message({
        message: 'Copied to your clipboard.',
        type: 'success'
      })
    },
    convertKrb5(inputFile) {
      return window.btoa(inputFile)
    },
    convert(name, files) {
      // handle file changes
      if (!files || !files.length || files.length === 0) {
        this.convertedConfiguration = this.convertKrb5(this.rawKrb5)
        Message({
          message: 'krb5.conf file converted.',
          type: 'success'
        })
      } else {
        const certFile = files[0].raw
        const reader = new FileReader()

        reader.onload = e => {
          let certificateContent = ''
          let bytes = new Uint8Array(e.target.result)
          let length = bytes.byteLength

          for (var i = 0; i < length; i++) {
            certificateContent += String.fromCharCode(bytes[i])
          }

          this.convertedConfiguration = this.convertKrb5(certificateContent)

          Message({
            message: 'krb5.conf file converted.',
            type: 'success'
          })
        }

        reader.readAsArrayBuffer(certFile)
      }
    }
  },
  data() {
    return {
      files: [],
      isSaving: false,
      rawKrb5: null,
      convertedConfiguration: ''
    }
  }
}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css?family=Roboto+Mono');

* {
  font-family: 'Roboto Mono', monospace;
}
</style>
