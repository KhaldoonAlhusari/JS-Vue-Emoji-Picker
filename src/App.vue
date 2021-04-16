<template>
  <div id="app">
    <div class="search-bar">
      <input
        type="text"
        name="search"
        placeholder="Search..."
        @keyup.enter="search"
        v-model="query"
      />
      <button v-on:click="search()">Submit</button>
      <button v-on:click="reset()">All Emojis</button>
    </div>

    <h1>Click on an Emoji!</h1>

    <div
      class="emoji-container"
      v-for="(char, i) in emojiCharacters"
      v-bind:key="i"
    >
      <div
        class="emoji"
        v-on:click="copyToClipboard(char, i)"
        v-bind:title="title(i)"
      >
        {{ char }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      apiKey: "6634995d82d170176492e2d28116355bc9629abc",
      baseUrl: "https://emoji-api.com/emojis",
      query: "",
      emojis: {},
      emojiCharacters: [],
    };
  },
  methods: {
    async fetchAllEmojis() {
      const emojiRes = await fetch(`${this.baseUrl}?access_key=${this.apiKey}`);
      this.emojis = await emojiRes.json();
      this.getEmojiCharacters();
    },
    getEmojiCharacters() {
      if (this.emojis[0] != "undefined") {
        for (let emoji in this.emojis) {
          if (
            !this.emojis[emoji].unicodeName.includes("E0.6") &&
            !this.emojis[emoji].unicodeName.includes("E0.7")
          ) {
            this.emojiCharacters.push(this.emojis[emoji].character);
          }
        }
      }
    },
    copyToClipboard(char, i) {
      const el = document.createElement("textarea");
      el.value = char;
      document.body.appendChild(el);
      el.select();
      document.execCommand("copy");
      document.body.removeChild(el);
      const chosenEmojiContainer = document.querySelector(
        `.emoji-container:nth-of-type(${i + 2})`
      );
      const popUp = document.createElement("div");
      popUp.classList.add("popUp");
      popUp.innerText = `You copied ${char} to your clipboard!`;
      chosenEmojiContainer.appendChild(popUp);
      setTimeout(() => {
        setTimeout(() => {
          chosenEmojiContainer.removeChild(popUp);
        }, 100);
        popUp.style.opacity = 0;
      }, 2500);
    },
    search() {
      const allEmojisOnPage = document.querySelectorAll(".emoji-container");
      for (let i = 0; i < allEmojisOnPage.length; i++) {
        if (
          !allEmojisOnPage[i].firstChild.title.includes(
            this.query.toLowerCase()
          )
        ) {
          allEmojisOnPage[i].style.display = "none";
        } else {
          allEmojisOnPage[i].style.display = "inline-block";
        }
      }
    },
    reset() {
      this.query = "";
      this.search();
    },
    title(i) {
      if (
        !this.emojis[i].unicodeName.includes("E0.6") &&
        !this.emojis[i].unicodeName.includes("E0.7")
      ) {
        return this.emojis[i].unicodeName;
      }
    },
  },
  mounted() {
    this.fetchAllEmojis();
  },
};
</script>

<style>
body {
  background-color: #f2f2f2;
  margin: 0;
  padding: 0;
}

#app {
  text-align: center;
  overflow: hidden;
  font-family: "Nunito", sans-serif;
}

.emoji-container {
  font-size: 40px;
  margin: 30px;
  padding: 10px;
  width: 50px;
  display: inline-block;
  position: relative;
  text-align: center;
}

.emoji {
  cursor: pointer;
}

.popUp {
  position: absolute;
  left: 50%;
  top: 100%;
  transform: translate(-50%, 0);
  padding: 20px;
  width: 200px;
  z-index: 10;
  background: white;
  border: 2px solid rgba(0, 0, 0, 0.465);
  border-radius: 10px;
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.198);
  font-size: 20px;
  transition: 400ms;
  display: inline-block;
}

.search-bar {
  background-color: #42b883;
  padding: 10px;
}

.search-bar input {
  width: 50%;
  max-width: 400px;
  font-size: 20px;
  outline: none;
  border: none;
  padding: 8px 10px;
  transition: 200ms;
}

.search-bar input:focus {
  box-shadow: 0 0 8px #35595e;
  border: 1px solid #35595e;
}

.search-bar button {
  font-size: 20px;
  outline: none;
  border: 1px solid #35595e;
  padding: 6px 16px;
  background-color: #35495e;
  color: white;
  cursor: pointer;
  transition: 200ms;
  font-family: "Nunito", sans-serif;
  font-weight: 300;
}

.search-bar button:hover {
  background-color: #f2f2f2;
  color: black;
}

.search-bar * {
  margin: 5px;
  border-radius: 5px;
}

h1 {
  margin-bottom: 0;
  font-size: 2.5rem;
  font-weight: 400;
}

@media only screen and (max-width: 600px) {
  .emoji-container {
    width: 35px;
    font-size: 30px;
  }

  .search-bar input {
    width: 30%;
  }

  .search-bar button {
    padding: 6px 6px;
  }

}

@media only screen and (max-width: 350px) {
  .emoji-container {
    width: 20px;
    font-size: 30px;
    margin: 20px 30px;
  }
}

</style>
