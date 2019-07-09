<template>
  <div>
    <div class="header">
      <h4 class="header-text">
        検査結果変換ツール
      </h4>
    </div>
    <div class="form-div container-fluid text-center">
      <b-form-textarea
        id="textarea"
        v-model="text"
        placeholder="検査項目を貼り付けてください..."
        rows="8"
        max-rows="8"
      />
    </div>
    <div class="container-fluid text-center result-div">
      <b-jumbotron lead="変換結果：">
        <p>
          {{ fixedText }}
        </p>
      </b-jumbotron>
      <b-button variant="success" @click="resetText">
        リセット
      </b-button>
    </div>
    <div class="container-fluid text-center help-button">
      <b-button
        size="sm"
        variant="outline-info"
        class="m-1"
        @click="showDismissibleAlert = true"
      >
        ヘルプ
      </b-button>
      <b-alert v-model="showDismissibleAlert" variant="info" dismissible>
        <img src="~/assets/help.png" />
      </b-alert>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      text: '',
      showDismissibleAlert: false
    }
  },
  computed: {
    fixedText() {
      if (!this.text) {
        return ''
      } else {
        const textArray = this.text.split(/\r\n|\r|\n/)
        let result = ''
        textArray.forEach((text) => {
          if (text !== '') {
            if (text.indexOf('＾') !== -1) {
              text = text.replace('＾', '^')
            }

            const item = text.match(/^\S*/gi)
            result += item + ' '

            const number = text.match(/(?<=\s)(<|>|)(\d.*\d)/gi)
            if (number === null) {
              result += 'Nan'
            } else {
              result += number
            }

            const unit = text.match(/((?<=\()(\S*))[^ )]/gi)
            if (unit === '%') {
              result += unit + ', '
            } else if (unit === null) {
              result += ', '
            } else {
              result += ' ' + unit + ', '
            }
          }
        })
        return result.slice(0, -2)
      }
    }
  },
  methods: {
    resetText() {
      this.text = ''
    },
    showAlert() {
      this.dismissCountDown = this.dismissSecs
    }
  }
}
</script>

<style>
.header {
  background-color: #13203c;
  color: #deebf7;
}
.header-text {
  padding: 10px;
}
.help-button {
  max-width: 1000px;
}
.form-div {
  max-width: 1000px;
  margin-top: 25px;
}
.result-div {
  max-width: 1000px;
  margin-top: 25px;
}
img {
  max-width: 100%;
  height: auto;
}
</style>
