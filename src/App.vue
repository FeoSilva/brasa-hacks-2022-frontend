<template>
  <div id="app">
    <img alt="Collaboat logo" src="./assets/logo.svg" width="320" height="64" class="logo">
    <div class="upload-tool">
      <div>
        <file-upload
          ref="upload"
          v-model="files"

          :post-action="API_URL"
          @input-file="inputFile"
          @input-filter="inputFilter"
        >
        Enviar imagem
        </file-upload>
      </div>
      <button v-show="!$refs.upload || !$refs.upload.active" @click.prevent="$refs.upload.active = true" type="button" :disabled="!files.length">
        Começar upload
      </button>
    </div>
  </div>
</template>
<script>
import VueUploadComponent from 'vue-upload-component'
export default {
  data: function () {
    return {
      files: [],
      uploadToken: null,
      userId: null,
    }
  },
  components: {
    FileUpload: VueUploadComponent
  },
  created() {
    let uri = window.location.href.split('?');
    if(uri.length == 2) {
      let vars = uri[1].split('&');
      let getVars = {};
      let tmp = '';
      vars.forEach(function(v) {
        tmp = v.split('=');
        if(tmp.length == 2)
          getVars[tmp[0]] = tmp[1];
      });
      this.uploadToken = getVars.uploadToken
      this.userId = getVars.userId
    }
  },
  computed: {
    API_URL() {
      return `http://api.collaboat.network/v1/upload?uploadToken=${this.uploadToken}&userId=${this.userId}`
    }
  },
  methods: {
    inputFile: function (newFile, oldFile) {
      if (newFile && oldFile && !newFile.active && oldFile.active) {
        // Get response data
        console.log('response', newFile.response)
        if (newFile.xhr) {
          //  Get the response status code
          console.log('status', newFile.xhr.status)
        }
      }
    },
    inputFilter: function (newFile, oldFile, prevent) {
      if (newFile && !oldFile) {
        // Filter non-image file
        if (!/\.(jpeg|jpe|jpg|gif|png|webp)$/i.test(newFile.name)) {
          return prevent()
        }
      }

      // Create a blob field
      newFile.blob = ''
      let URL = window.URL || window.webkitURL
      if (URL && URL.createObjectURL) {
        newFile.blob = URL.createObjectURL(newFile.file)
      }
    }
  }
}
</script>
<style>
body {
  margin: 0px;
  width: 100%;
  height: 100%;
  position: relative;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-color: #EF8354;
  margin: 0px;
  height: 100vh;
  width: 100%;
  position: absolute;
}
.logo {
  margin-top: 12px;
}
.file-uploads {
  margin-top: 48px;
  width: 100%;
  background: #007bff;
  height: 100px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 8px dashed #fafafa;
  padding: 20px;
  margin-bottom: 32px;
  cursor: pointer;
  font-size: 32px;
}
</style>