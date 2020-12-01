<template>
  <div class="home">
    <div class="center">
      <ul class="center">
        <li v-for="(anime, index) in animes.slice(0, 8)" :key="index">
          <span
            :class="
              checkClass(
                anime[1].episodes[anime[1].episodes.length - 1].formatedTime
              )
            "
          >
            {{ anime[1].episodes[anime[1].episodes.length - 1].formatedTime }}
          </span>
          <img src="../assets/dummy.jpg" />

          <p>{{ anime[1].anime_title }}</p>
          <p>
            Episode No.
            {{ anime[1].episodes[anime[1].episodes.length - 1].episode }}
          </p>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import $ from "jquery";
// import JsonAnime from "../json/anime2.json";

// @ is an alias to /src
export default {
  data: function() {
    return {
      animes: {},
    };
  },
  methods: {
    Unixtimestamp: function(countDownDate) {
      // Get today's date and time
      var now = new Date().getTime();

      // Find the distance between now and the count down date
      var distance = countDownDate - now;

      if (distance < 0) {
        return "Aired!";
      }

      // Time calculations for days, hours, minutes and seconds
      var days = Math.floor(distance / (1000 * 60 * 60 * 24));
      var hours = Math.floor(
        (distance % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60)
      );
      var minutes = Math.floor((distance % (1000 * 60 * 60)) / (1000 * 60));
      var seconds = Math.floor((distance % (1000 * 60)) / 1000);
      return days + "d " + hours + "h " + minutes + "m " + seconds + "s ";
    },
    checkClass(formatedTime) {
      if (formatedTime != "Aired!") {
        return "bg-green-500";
      }
      return "bg-gray-400";
    },
  },
  created() {
    var animelist = {};
    $.ajax({
      url: "https://cors-anywhere.herokuapp.com/https://jsonkeeper.com/b/XJW9",
      type: "GET",
      dataType: "JSON",
      async: false,
      success: (data) => {
        animelist = data;
      },
    });

    this.animes = animelist;

    let aired = [];
    let on_air = [];
    let today = new Date().setHours(0, 0, 0, 0);
    let current_time = new Date().getTime();

    // loop for each anime
    for (const key in this.animes) {
      if (Object.prototype.hasOwnProperty.call(this.animes, key)) {
        // loop for each episode of this anime
        for (let i = 0; i < this.animes[key].episodes.length; i++) {
          // get episode data
          const episode = this.animes[key].episodes[i];

          // add new element called time formated
          this.animes[key].episodes[i].formatedTime = this.Unixtimestamp(
            episode.on_air * 1000
          );

          // get the date of the episode
          let episode_day = new Date(episode.on_air * 1000).setHours(
            0,
            0,
            0,
            0
          );

          if (today == episode_day) {
            if (episode.on_air * 1000 - current_time < 0) {
              aired.push([key, this.animes[key]]);
            } else {
              on_air.push([key, this.animes[key]]);
            }
          }
        }
      }
    }

    on_air.sort((a, b) => a[1].episodes[0].on_air - b[1].episodes[0].on_air);
    aired.sort((a, b) => b[1].episodes[0].on_air - a[1].episodes[0].on_air);

    this.animes = on_air.concat(aired);
  },
  computed: {},
  name: "Home",
};
</script>

<style lang="scss" scoped>
ul {
  list-style-type: none;
  display: flex;
  margin: 0;
  padding: 0;
  li {
    position: relative;
    width: 90px;
    padding: 5px;
    margin: 8px 0px;
    img {
      width: 100%;
    }
    span {
      color: #fffefe;
      background: #18a6fb73;
      padding: 0 4px;
      margin: 2px;
      float: right;
      font-size: 10px;
      border: 1px dashed;
      position: absolute;
    }
  }
}
.center {
  width: 80%;
  margin: auto;
}
.bg-gray-400 {
  background: #4b4b4b73;
}
.bg-green-500 {
  background: #18fb2373;
}
</style>
