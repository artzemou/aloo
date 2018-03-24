<template>
	<ul id="users">
		<li  v-for="(user, index) in users">
			<ul class="user" @click="clicked(index, user.email)">
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
			clicked(user, email) {
	      this.$emit('clicked', user, email);
	    }

	 }
}
</script>

<style lang="sass">
ul, li
	padding:  0
	margin: 0
	list-style: none

#users
	width: 25%
	border-right: 1px solid #d9d9d9

	@media screen and (max-width: 720px)
		width: 74px

.user
	padding: .5rem
	border-bottom: 1px solid #d9d9d9
	text-align: center
	display: flex
	margin: auto
	@media screen and (max-width: 720px)
		padding: 0
		height: 64px
		display: flex

	.avatar
		min-width: 50px
		height: 50px
		border-radius: 50%
		background: #d9d9d9
		@media screen and (max-width: 720px)
			margin: auto

	.name
		flex: 1 1 0%
		min-width: 0
		overflow: hidden
		text-overflow: ellipsis
		white-space: nowrap
		margin: auto
		padding-left: .5rem
		text-align: left

		@media screen and (max-width: 720px)
			display: none

.user:hover
	background: #F5F5F5

</style>
