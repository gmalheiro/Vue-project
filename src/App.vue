<script setup>
import { ref, onMounted, computed } from 'vue'

const query = ref('')
const my_anime = ref([])
const search_results = ref([])

const my_anime_asc = computed(() => {
	return my_anime.value.sort((a, b) => {
		return a.title.localeCompare(b.title)
	})
})

const searchAnime = () => {
	const url = `https://api.jikan.moe/v4/anime?q=${query.value}`
	fetch(url)
		.then(res => res.json())
		.then(data => {
			search_results.value = data.data
		})
}

const handleInput = (e) => {
	if (!e.target.value) {
		search_results.value = []
	}
}

const addAnime = (anime) => {
	search_results.value = []
	query.value = ''

	my_anime.value.push({
		id: anime.mal_id,
		title: anime.title,
		image: anime.images.jpg.image_url,
		total_episodes: anime.episodes,
		watched_episodes: 0,
	})

	localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

const increaseWatch = (anime) => {
	anime.watched_episodes++
	localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

const decreaseWatch = (anime) => {
	anime.watched_episodes--
	localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

onMounted(() => {
	my_anime.value = JSON.parse(localStorage.getItem('my_anime')) || []
})
</script>

<template>
	<main>
		<h1>Anime Tracker</h1>

		<form @submit.prevent="searchAnime">
			<input 
			type="text" 
			placeholder="Search for an anime..." 
			v-model = "query" 
			@input="handleInput"
			/>

			<button type="submit" >Search</button>
		</form>

		<div class="results" v-if="search_results.length > 0">
			<div class="result" v-for="anime in search_results" >
				<img :src="anime.images.jpg.image_url" />
				 <div class="details">
					<h3>{{ anime.title }}</h3>
				 </div>
			</div>
		</div>
	</main>
</template>

<style>

</style>