
<template>
  <div id="chat">
    <main class="wrapper ai-window">
        <!-- Display offline message -->
        <div v-if="answers.length == 0 && online == false">
            <h1 class="title mdc-typography--headline">
                <div class="material-icons up">cloud_off</div>
                <br>
                <br>
                    Oh, non!

                    <p class="mdc-typography--body2">Il semble que vous n'êtes pas connecté à Internet, cette page Web nécessite une connexion Internet, pour traiter vos demandes</p>
            </h1>
        </div>

        <!-- Chat window -->
        <table v-for="a in answers" class="chat-window">

            <!-- Your messages -->
            <tr v-if="a.result.resolvedQuery == ':)'">
              <td class="emoji">
                  <img alt="" src="./1f642.png"/>
              </td>
            </tr>
            <tr v-else-if="a.result.resolvedQuery == '=)'">
              <td class="emoji">
                  <img alt="" src="./1f60a.png"/>
              </td>
            </tr>

            <tr v-else-if="a.result.resolvedQuery == ':('">
              <td class="emoji">
                  <img alt="" src="./1f61e.png"/>
              </td>
            </tr>
            <tr v-else>
                <td class="bubble">{{a.result.resolvedQuery}}</td>
            </tr>

            <!-- Dialogflow messages -->
            <tr>
                <td>

                    <!-- Bot message -->
                    <div v-if="a.result.fulfillment.speech" class="bubble bot">
                        {{a.result.fulfillment.speech}}
                    </div>
                </td>
            </tr>
        </table>
        <br>
        <p v-if="answers.length > 0" id="bottom"></p>
    </main>
    <!-- The input -->
    <div class="query">
        <div class="wrapper" v-if="micro == false">
            <i class="material-icons iicon" @click="microphone(true)">mic</i>
            <input aria-label="Écrivez un message..." autocomplete="off" v-model="query" class="queryform"  @keyup.enter="submit()" placeholder="Écrivez un message..." autofocus type="text">

            <i class="material-icons iicon t2s" @click="mute(true)" v-if="muted == false">volume_up</i>
            <i class="material-icons iicon t2s" @click="mute(false)" v-else>volume_off</i>


        </div>
        <div class="wrapper" v-else>
            <i class="material-icons iicon recording" @click="microphone(false)">mic</i><input class="queryform" :placeholder="speech" readonly>
        </div>
        <div>
          <i class="material-icons iicon t2s">tag_faces</i>
        </div>


    </div>
  </div>
</template>


<script>
import Emoji from './Emoji.vue'
import { ApiAiClient } from 'api-ai-javascript'
const client = new ApiAiClient({accessToken: 'f0e65dc0f2614efd8844fc233f19ba28'}) // <- replace it with yours

export default {
    name: 'chat',
    components:{
      Emoji
    },
    mounted(){
      this.loadDataStorage()
    },
    props:['user'],
    data: () => {
        return {
            answers: [],
            data:[],
            query: '',
            speech: 'Dites quelque chose...',
            micro: false,
            muted: true,
            online: navigator.onLine
        }
    },
    watch: {
        answers: function(val){
            setTimeout(() => {
                if(document.querySelector('#bottom') !== null){
                  document.querySelector('#bottom').scrollIntoView({
                      behavior: 'smooth'
                  })
                }
            }, 2) // if new answers arrive, wait for render and then smoothly scroll down to .copyright selector, used as anchor
        },
        user: function(newVal, oldVal) { // watch it
          this.answers = []
          let data = JSON.parse(localStorage.getItem("data"))
          if(data !== null){
            let self = this
            self.data = data
            if(data[this.user]){
              data[this.user].forEach(function(element) {
                  client.textRequest(element).then((response) => {
                      self.answers.push(response)
                      self.handle(response) // <- handle the response in handle() method

                  })
              });
            }
          }
        },
        query: function(newVal, oldVal){
          console.log(newVal)

        }
    },
    methods: {
        submit(){
          if(this.query !== ''){

            if (this.data[this.user] === undefined || this.data[this.user] === null) this.data[this.user] = []
            console.log(this.data[this.user])
            this.data[this.user].push(this.query)
            localStorage.setItem("data", JSON.stringify(this.data))

            client.textRequest(this.query).then((response) => {

                this.answers.push(response)
                this.handle(response) // <- handle the response in handle() method

                this.query = ''
                this.speech = 'Go ahead, im listening...' // <- reset query and speech
            })
          }

        },
        loadDataStorage(){
          let data = JSON.parse(localStorage.getItem("data"))
          if(data !== null){
            this.data = data
            let self = this
            data[this.user].forEach(function(element) {
                client.textRequest(element).then((response) => {
                    self.answers.push(response)
                    self.handle(response) // <- handle the response in handle() method

                })
            });
          }
        },
        handle(response){
            if(response.result.fulfillment.speech || response.result.fulfillment.messages[0].type == 'simple_response'){

                let speech = new SpeechSynthesisUtterance(response.result.fulfillment.speech || response.result.fulfillment.messages[0].textToSpeech)
                speech.voiceURI = 'native'
                speech.lang = 'en-GB' // <- Nice british accent

                if(this.muted == false) window.speechSynthesis.speak(speech) // <- Speech output if microphone is allowed

            }
        },
        autosubmit(suggestion){
            this.query = suggestion
            this.submit()
        },
        mute(mode){
            this.muted = mode
        },
        microphone(mode){
            this.micro = mode
            let self = this // <- correct scope

            if(mode == true){
                let recognition = new webkitSpeechRecognition() // <- chrome speech recognition

                recognition.interimResults = true
                recognition.lang = 'en-US'
			          recognition.start()

                recognition.onresult = function(event){
        			        for (var i = event.resultIndex; i < event.results.length; ++i){
        			    	    self.speech = event.results[i][0].transcript
        			        }
      			    }

      			    recognition.onend = function(){
      				    recognition.stop()
                          self.micro = false
                          self.autosubmit(self.speech)
      			    }
            }
        },
        someHandler : function(){
          console.log('?')
        }
    }
}
</script>

<style lang="sass">
@import url('https://fonts.googleapis.com/css?family=Roboto')

$color: #FF9800

#chat
  width: 75%
  overflow: auto
  margin-right: -15px
  padding-right: 15px

.wrapper
    margin-left: auto
    margin-right: auto

.wrapper.ai-window
    padding: 1rem

.up
    font-size: 32px
    background-color: white
    padding: 10px
    border-radius: 50%

.title
    vertical-align: middle
    text-align: center
    font-weight: 700
    color: rgba(0,0,0,0.7)
    margin-top: 30%


.query
    padding: 1rem
    border-top: 1px solid #d9d9d9
    z-index: 999
    position: fixed
    width: 80%
    bottom: 0
    background-color: #ffffff
    font-size: 14px
    height: 83px

.queryform
    border: 0
    width: 100% - 20%
    margin-left: 60px
    font-size: 16px
    outline: none
    color: rgba(0,0,0,0.8)
    font-weight: 500
    background-color: #ffffff

    @media screen and (max-width: 320px)
        width: 100% - 35%

.iicon
    margin-left: 20px
    position: absolute
    vertical-align: middle
    color: rgba(0,0,0,0.8)
    cursor: pointer

.recording
    color: #F44336

.iicon.t2s
    margin-left: 10pxsomeHandler
    margin-right: 20px

    @media screen and (max-width: 720px)
        right: 0

.chat-window
    width: 100%

.bubble
    max-width: 300px
    background-color: #0084ff
    padding: 16px
    border-radius: 13px
    color: #ffffff
    float: right

.bubble.bot
    background-color: #e6e6e6
    color: initial
    float: left
    margin-right: 10px
    margin-left: 10px
    opacity: 0
    animation: msg .25s linear
    animation-delay: .75s
    animation-fill-mode: forwards

.emoji
  float: right
  padding: 16px

td
    margin-top: 30px
    margin-bottom: 10px

.mdc-card
    background-color: white
    max-width: 300px
    margin-bottom: 5px
    animation: msg .45s ease-in-out

.slide
    margin: 5px
    max-width: 300px

.slider
    max-width: 300px
    margin-left: -5px

.mdc-fab
    background-color: white
    color: $color

.rightnav
    margin-left: -32px

    @media screen and (max-width: 720px)
            margin-left: -35px

    @media screen and (max-width: 320px)
            margin-left: -70px

.leftnav
    margin-right: -35px

    @media screen and (max-width: 720px)
        display: none

.openlink
    vertical-align: middle
    margin-top: -5px
    margin-left: 5px

.mdc-card__media-item
    height: auto
    width: 100%
    margin-top: -5px

.chips
    margin-left: -10px

.suggestion
    margin-top: 10px
    float: left
    margin-left: 10px
    padding: 10px
    border: 2px rgba(0,0,0,0.5) solid
    color: rgba(0,0,0,0.5)
    border-radius: 6px
    cursor: pointer
    animation: controls .25s linear

.suggestion:active
    border: 2px rgba(0,0,0,1) solid
    color: rgba(0,0,0,1)

.suggestion.link
    color: white
    background-color: $color
    border: 2px $color solid

.suggestion.link:active
    background-color: darken($color, 10%)
    border: 2px darken($color, 10%) solid

.mdc-list-item__start-detail
    border-radius: 50%

@keyframes msg
    0%
        opacity: 0
        transform: scale(0.8)
    100%
        opacity: 1
        transform: scale(1)

@keyframes controls
    0%
        transform: scaleY(0)
    100%
        transform: scaleY(1)

.copyright
    font-weight: 600
    color: rgba(0,0,0,0.8)

.copyright a
    text-decoration: none
    color: rgba(0,0,0,0.8)
    border-bottom: 2px solid transparent

.copyright a:hover
    color: $color
    border-bottom: 2px solid $color

</style>
