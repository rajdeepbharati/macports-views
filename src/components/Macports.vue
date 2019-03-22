<template>
  <div id="demo">
    <h1>Macports Latest Commits</h1>
    <p>macports/macports-ports@{{branch}}</p>
    <ul>
      <div class="record" v-for="(record, index) in commits" :key="index">
        <a :href="record.html_url" target="_blank" class="commit">{{ record.sha.slice(0, 7) }}</a>
        -
        <span class="message">{{ record.commit.message | truncate }}</span>
        <br>by
        <span class="author">
          <a :href="record.author.html_url" target="_blank">{{ record.commit.author.name }}</a>
        </span>
        at
        <span class="date">{{ record.commit.author.date | formatDate }}</span>
      </div>
    </ul>
  </div>
</template>

<script>
var apiURL =
  "https://api.github.com/repos/macports/macports-ports/commits?per_page=10&sha=";

export default {
  name: "Macports",
  props: {
    msg: String
  },
  data() {
    return {
      branch: "master",
      commits: null
    };
  },
  created: function() {
    this.fetchData();
  },
  watch: {
    branch: "fetchData"
  },
  filters: {
    truncate: function(v) {
      var newline = v.indexOf("\n");
      return newline > 0 ? v.slice(0, newline) : v;
    },
    formatDate: function(v) {
      return v.replace(/T|Z/g, " ");
    }
  },
  methods: {
    fetchData: function() {
      var xhr = new XMLHttpRequest();
      var self = this;
      xhr.open("GET", apiURL + self.branch);
      xhr.onload = function() {
        self.commits = JSON.parse(xhr.responseText);
        // console.log(self.commits[0].html_url);
      };
      xhr.send();
    }
  }
};
</script>

<style scoped>
#demo {
  font-family: "Helvetica", Arial, sans-serif;
}

a {
  text-decoration: none;
  color: #f66;
}

.message {
  font-weight: 600;
}

.record {
  line-height: 1.5em;
  margin-bottom: 20px;
}

.author,
.date {
  font-weight: bold;
}
</style>
