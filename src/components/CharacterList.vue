<template>
  <div class="text-center mx-auto">
    <div id="characterContainer" class="row text-center mx-auto py-5 g-3">
      <div v-for="character in characters" :key="character.id" class="character-card col">
        <div class="card shadow-lg">
          <img class="bd-placeholder-img card-img-top" :src="character.image" :alt="character.name">
          <div class="card-body">
            <h5 class="card-text">{{ character.name }}</h5>
            <p class="card-text">Species: {{ character.species }}</p>
            <p class="card-text">Status: {{ character.status }}</p>
          </div>
        </div>
      </div>
    </div>
    <button v-if="showMore" @click="fetchCharacter" class="showMore btn btn-outline-secondary btn-block mt-4">Show More</button>
  </div>
</template>

<script>
export default {
  name: 'CharacterList',
  data() {
    return {
      characters: [],
      characterId: 1, // Start with the first character
      showMore: true
    };
  },
  created() {
    this.fetchCharacter();
  },
  methods: {
    fetchCharacter() {
      fetch(`https://rickandmortyapi.com/api/character/${this.characterId}`)
        .then(response => response.json())
        .then(data => {
          this.characters.push(data); // Add the single character to the list
          this.characterId++; // Increment to get the next character on the next call
          if (this.characterId > 826) { // Adjust 826 to the actual number of characters in the API
            this.showMore = false;
          }
        })
        .catch(error => console.error('Error fetching character:', error));
    }
  }
};
</script>
