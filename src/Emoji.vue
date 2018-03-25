<template>
    <img alt="" :src="`img/${src[emoji]}.png`"/>
</template>

<script>

export default {
  name:'emoji',
  props:['emoji','src'],

}
</script>
