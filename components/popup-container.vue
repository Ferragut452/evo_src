<template>
  <div class="wrapp">
    <vendor-filter :albums="photos"></vendor-filter>
    <div class="wrapp__bg"></div>
  </div>
</template>

<script>
import VendorFilter from "@/components/vendor-filter";

export default {
  components: { VendorFilter },

  data() {
    return {
      photos: [],
    };
  },

  computed: {},

  created() {
    fetch("https://jsonplaceholder.typicode.com/photos")
      .then((r) => {
        return r.json();
      })
      .then((r) => {
        let arr = r.slice(0, 32).reduce((acc, e) => {
          let group = e.title[0];
          if (!acc[group]) acc[group] = { group, children: [e] };
          else acc[group].children.push(e);
          return acc;
        }, {});

        let photos = [];
        for (var photo in arr) {
          photos.push(arr[photo]);
        }

        this.photos = photos.sort((a, b) => {
          return a.group.localeCompare(b.group);
        });
      });
  },

  methods: {},
};
</script>

<style lang="scss">
.wrapp {
  position: relative;
  height: 100vh;
  display: flex;

  &__bg {
    position: fixed;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    z-index: -1;
    background: rgba(27, 32, 79, 0.2);
  }
}
</style>
