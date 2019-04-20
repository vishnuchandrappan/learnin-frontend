<template>
  <div>
    <h4>Mentors</h4>

    <b-input-group prepend="Search">
      <b-form-input v-model="searchKeyword"></b-form-input>
    </b-input-group>
    <br>

    <b-card-group deck>
        <b-card
          v-for="mentor in searchResults"
          :title="mentor.name"
          :img-src="mentor.image"
          :img-alt="mentor.name"
          img-top
          tag="article"
          style="max-width: 15rem;"
          class="mb-2"
        >
          <b-card-text>
            {{ mentor.description }}
          </b-card-text>
        </b-card>
    </b-card-group>

    <div class="text-center" v-if="loading"><h5>Loading...</h5></div>

  </div>
</template>

<script>
import axios from 'axios'
import md5 from 'blueimp-md5'

const { extractSheets } = require("spreadsheet-to-json");

function parseSheet (id) {
    var sheet = 1,
      query = '',
      useIntegers =  true,
      showRows = true,
      showColumns = false,
      url = 'https://spreadsheets.google.com/feeds/list/' + id + '/' + sheet + '/public/values?alt=json';

    return axios.get(url)
      .then(function (response) {
        var data = response.data
        var responseObj = {};
        var rows = [];
        var columns = {};
        for(var i = 0; i < data.feed.entry.length; i++) {
          var entry = data.feed.entry[i];
          var keys = Object.keys(entry);
          var newRow = {};
          var queried = false;
          for(var j = 0; j < keys.length; j++) {
            var gsxCheck = keys[j].indexOf('gsx$');
            if(gsxCheck > -1) {
              var key = keys[j];
              var name = key.substring(4);
              var content = entry[key];
              var value = content.$t;
              if(value.toLowerCase().indexOf(query.toLowerCase()) > -1) {
                queried = true;
              }
              if(useIntegers === true && !isNaN(value)) {
                value = Number(value);
              }
              newRow[name] = value;
              if(queried === true) {
                if(!columns.hasOwnProperty(name)) {
                  columns[name] = [];
                  columns[name].push(value);
                } else {
                  columns[name].push(value);
                }
              }
            }
          }
          if(queried === true) {
            rows.push(newRow);
          }
        }
        if(showColumns === true) {
          responseObj['columns'] = columns;
        }
        if(showRows === true) {
          responseObj['rows'] = rows;
        }

        return responseObj
      })
      .catch(function (error) {
        console.log(error)
      })
}

export default {
  name: 'MentorsList',
  props: {
    name
  },
  mounted () {
    this.loading = true
    parseSheet('1BVYucZH2rjou2BGhTz62Wib5BBSrMnetcDLSAkLp9mE').then((response) => {
        this.mentors = response.rows.map((item) => {
            item.image = 'https://www.gravatar.com/avatar/' + md5(item.email) + '?s=250&d=identicon'
            return item
        })

        this.loading = false
    })
  },
  data () {
    return {
        mentors: [],
        searchKeyword: "",
        loading: false
      }
  },
  computed: {
    searchResults () {
        return this.mentors.filter((mentor) => {
            if (this.searchKeyword.trim() === "") {
                return true
            } else {
                return mentor.name.toLowerCase().indexOf(this.searchKeyword.trim().toLowerCase()) !== -1
            }
        })
    }
  }
}
</script>

<style scoped>
</style>
