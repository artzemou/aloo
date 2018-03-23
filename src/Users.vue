<template>
	<ul id="users">
		<li  v-for="(user, index) in users">
			<ul class="user" @click="clicked(index)">
					<li class="avatar"></li>
					<li class="name">{{ user.email }}</li>
			</ul>
		</li>
	</ul>
</template>
<script>
export default {
	mounted() {

  },
  name: 'Users',
	data () {
    return {
			users: [
        {
					name:'Lorem',
					email:'lorem@ipsum.com'
				},
				{
					name:'Ipsum',
					email:'ipsum@dolores.com'
				},
				{
					name:'emile',
					email:'emile@ipsum.com'
				},
      ]
    }
	 },
	 methods: {
			clicked(user) {
	      this.$emit('clicked', user);
	    }

	 }
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="sass">
ul, li
	padding:  0
	margin: 0
	list-style: none

#users
	width: 25%
	border-right: 1px solid #d9d9d9

.user
	padding: 1rem
	border-bottom: 1px solid #d9d9d9
	text-align: center
	display: flex
	margin: auto

	.avatar
		width: 50px
		height: 50px
		border-radius: 50%
		background: #0084ff

	.name
		display: inline-flex
		margin: auto

.user:hover
	background: #F3F3F3

</style>
