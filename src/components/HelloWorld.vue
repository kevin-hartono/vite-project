<script setup>
import {onMounted, ref} from 'vue'

defineProps({
	msg: String,
})

const refA = ref("")
const refB = ref("")

onMounted(() => {
	setTimeout(() => {
		refA.value = "5 detik selesai"
	}, 5000)
	setTimeout(() => {
		refB.value = "3 detik selesai"
	}, 3000)
})
const count = ref(0)
</script>

<template>
	<h1>{{ msg }}</h1>
	<h1>COBA</h1>
	<div class="card">
		<button type="button" @click="count++">count is {{ count }}</button>
		<p>
			Edit
			<code>components/HelloWorld.vue</code> to test HMR
		</p>
	</div>

	<h1>{{ refA }}<span v-show="refA == ''">refA loading</span></h1>
	<h1>{{ refB }}<span v-show="refB == ''">refB loading</span></h1>

	<p>
		Check out
		<a href="https://vuejs.org/guide/quick-start.html#local" target="_blank"
		>create-vue</a
		>, the official Vue + Vite starter
	</p>
	<p>
		Install
		<a href="https://github.com/vuejs/language-tools" target="_blank">Volar</a>
		in your IDE for a better DX
	</p>
	<p class="read-the-docs">Click on the Vite and Vue logos to learn more</p>
</template>

<style scoped>
.read-the-docs {
	color: #888;
}
</style>
