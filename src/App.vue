<template>
  <section id ="app">
    <div id="header">
      <div class="_1tqi">Messenger</div>
      <div class="_673w" >{{email}}</div>
    </div>
    <div id="wrap">
      <Users @clicked="clicked"/>
      <Chat :user="user"/>

    </div>
  </section>
</template>

<style lang="sass">
@import url('https://fonts.googleapis.com/css?family=Roboto')

$color: #FF9800

\:root
  --mdc-theme-primary: $color

*
  box-sizing: border-box

body
    margin: 0
    font-size: 15px
    font-family: Helvetica Neue, Segoe UI, Helvetica, Arial, sans-serif
    line-height: 1.28
    overflow: hidden
    text-rendering: optimizeLegibility

#header
    display: flex
    text-align: center
    border-bottom: 1px solid #d9d9d9

._1tqi
    width: 25%
    padding: 1rem
    border-right: 1px solid #d9d9d9

    @media screen and (max-width: 720px)
      width: 74px

._673w
    flex: 1
    margin: auto

#wrap
    display: flex
    height: calc(100vh - 53px)



</style>

<script>
import Users from './Users.vue'
import Chat from './Chat.vue'


export default {
    name: 'app',
    components: {
  		Users,
      Chat
    },
    updated(){
      //console.log(this.user);
    },
    data: function(){
        return {
            user: 0,
            email: 'lorem@ipsum.com'

        }
    },
    watch: {

    },
    methods: {
      clicked(user, email) {
        this.user = user
        this.email = email
      },
      onInput(event) {
          //event.data contains the value of the textarea
      }
    }
}
</script>
