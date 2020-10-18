<template>
  <div class="vendor-filter">
    <div class="vendor-filter__header">
      <div class="vendor-filter__title">
        Фильтр:
        <span @click="favoritesMode = false" class="vendor-filter__name"
          >По альбомам</span
        >
        <span @click="favoritesMode = true" class="vendor-filter__name"
          >Избранное</span
        >
      </div>
    </div>
    <div class="vendor-filter__scroll-wrap">
      <div class="vendor-filter__scroll">
        <ul v-if="!favoritesMode" class="vendor-filter__list">
          <li
            :key="album.id"
            v-for="album in albums"
            class="vendor-filter__item"
          >
            <div class="album">
              <h2 class="album__title">{{ album.group }}</h2>
              <ul>
                <li
                  @click="toggleFavorite(child.id)"
                  :key="child.id"
                  v-for="child in album.children"
                >
                  <div class="album__img">
                    <img :src="child.thumbnailUrl" alt="album image" />
                  </div>
                  <div class="album__name">
                    <span>{{ child.title }}</span>
                  </div>
                  <div v-if="checkIfFavorite(child.id)" class="favorites">
                    <span>&#10084;</span>
                  </div>
                </li>
              </ul>
            </div>
          </li>
        </ul>
        <div v-else class="favorites-list">
          <ul v-if="favoritesAlbums.length > 0">
            <li
              class="album"
              @click="deleteFavorite(album.id)"
              :key="album.id"
              v-for="album in favoritesAlbums"
            >
              <div class="album__img">
                <img :src="album.thumbnailUrl" alt="album image" />
              </div>
              <div class="album__name">
                <span>{{ album.title }}</span>
              </div>
              <div class="delete-icon">
                <span>&#10006;</span>
              </div>
            </li>
          </ul>
          <h2 v-else>You have no favorites albums</h2>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import PopupContainer from "@/components/popup-container";

let STORAGE_KEY = "Favs";
let favsStorage = {
  fetch: function () {
    let albums = JSON.parse(localStorage.getItem(STORAGE_KEY) || "[]");
    return albums;
  },
  save: function (albums) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(albums));
  },
};

export default {
  components: { PopupContainer },
  props: ["albums"],

  data() {
    return {
      favoritesMode: false,
      favorites: favsStorage.fetch(),
    };
  },

  computed: {
    favoritesAlbums() {
      return this.albums
        .map((el) => el.children)
        .reduce((a, b) => [...a, ...b], [])
        .filter((x) => this.favorites.includes(x.id));
    },
  },

  created() {},

  methods: {
    toggleFavorite(id) {
      var index = this.favorites.findIndex((x) => x == id);

      if (index === -1) {
        this.favorites.push(id);
      } else {
        this.favorites.splice(index, 1);
      }

      favsStorage.save(this.favorites);
    },
    checkIfFavorite(id) {
      return this.favorites.includes(id);
    },
    deleteFavorite(id) {
      this.favorites = this.favorites.filter((el) => {
        return el !== id;
      });
      favsStorage.save(this.favorites);
    },
  },
};
</script>

<style lang="scss">
.vendor-filter {
  font-weight: 700;
  font-family: Open Sans, Roboto, sans-serif;
  width: 1300px;
  height: 600px;
  margin: auto;
  padding: 3.6rem 4.8rem 6rem;
  font-size: 1.2rem;
  background: #fff;
  border-radius: 1.2rem;
  box-sizing: border-box;

  &__header {
    margin: 0 0 1.5rem;
  }

  &__title {
    font-weight: bold;
    font-size: 1.4rem;
    line-height: 1.9rem;
  }

  &__name {
    margin: 0 0.5rem;
    text-decoration: underline;
    cursor: pointer;
  }

  &__scroll-wrap {
    overflow: hidden;
  }

  &__scroll {
    width: 100%;
    height: 470px;
    overflow-y: auto;

    &::-webkit-scrollbar {
      display: none;
    }
  }

  &__list {
    height: 710px;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    flex-wrap: wrap;
  }
  &__item {
    max-width: 260px;
  }

  .album {
    &__title {
      text-transform: uppercase;
      margin-left: 20px;
      margin-bottom: 8px;
    }
    ul {
      li {
        position: relative;
        background-color: #f7f8f9;
        border-radius: 8px;
        display: flex;
        margin-bottom: 8px;
        align-items: center;
        transition: all 0.15s ease-in-out;
        cursor: pointer;
        &:hover {
          background-color: #e9e9e9;
        }
        .favorites {
          position: absolute;
          top: -14px;
          right: -11px;
          font-size: 24px;
          color: red;
        }
      }
    }
    &__img {
      img {
        height: 40px;
        width: 40px;
        margin: 2px;
        margin-right: 10px;
        border-radius: 8px;
      }
    }
  }

  .favorites-list {
    ul {
      display: flex;
      flex-wrap: wrap;
    }
    .album {
      width: 260px;
      position: relative;
      background-color: #f7f8f9;
      border-radius: 8px;
      display: flex;
      margin-bottom: 8px;
      align-items: center;
      transition: all 0.15s ease-in-out;
      cursor: pointer;
      margin-right: 10px;
      margin-top: 10px;
      &:hover {
        background-color: #e9e9e9;
        .delete-icon {
          display: block;
        }
      }
      .delete-icon {
        display: none;
        position: absolute;
        top: -9px;
        right: -5px;
        font-size: 15px;
        color: red;
      }
    }
  }
}
</style>
