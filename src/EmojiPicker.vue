<template>
  <div class="picker-container" v-bind:class="{ active: isActive }">
    <i class="material-icons iiicon" @click="toggleClass" >tag_faces</i>
    <ul>
      <li v-for="(src, index) in emojiImgSrc">
        <img v-on:click="insertSmiley(index)" alt="" :src="`img/${src}.png`"/>
      </li>
    </ul>
  </div>
</template>

<script>

export default {
  name:'emoji-picker',
  props:['emojiImgSrc', 'insertSmiley'],
  data: () => {
    return{
      isActive: false
    }
  },
  methods: {
    toggleClass: function(){
        this.isActive = !this.isActive;
        console.log(';)')
    }
  }

}

//query = emoji[index]
</script>
<style lang="sass">

.iiicon
    cursor: pointer

.picker-container
    padding: .5rem
    display: flex
    flex-direction: row-reverse
    width: 80%
    color: #d9d9d9;


.picker-container
    ul
      display: none

.picker-container.active
    ul
      display: flex
      background-color: #ffffff
      max-width: 278px
      height: 375px
      border: 0
      border-radius: 6px
      box-shadow: 0 0 0 1px rgba(0, 0, 0, .1), 0 1px 10px rgba(0, 0, 0, .35)
      position: absolute
      top: -365px
      padding: .2rem
      flex-wrap: wrap

      li
        flex: 1
        margin: .5rem

</style>
