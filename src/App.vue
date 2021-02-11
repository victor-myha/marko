<template>
  <div style="padding-left: 50px; padding-right: 50px; padding-top: 50px;">
    <b-form @submit="onSubmit" v-if="show">
      <b-form-group id="input-group-1" label-for="input-2">
        <b-form-input
            id="input-2"
            v-model="form.login"
            placeholder="Enter Github Repository Login"
            required
        ></b-form-input>
      </b-form-group>

      <b-button type="submit" variant="primary">Show repo details</b-button>
    </b-form>
    <div v-if="name !=='Not found' && name !==''">
      <b-card class="mt-3" :header="name + ' with ' + repos.length + ' repositories'">
      </b-card>
    </div>
    <div v-else>
      <b-card class="mt-3" :header=" 'Not found' ? name === 'Not found' : ''"></b-card>
    </div>
    <ul class="list-group list-group-flush" v-for="repo in repos.sort(function(a,b){
    return a.name.localeCompare(b.name);
})" :key="repo">

      <li class="list-group-item"><a target="_blank" :href="repo.url">{{ repo.name }}</a></li>
    </ul>
  </div>
</template>

<script>

import $ from 'jquery';

export default {
  data() {
    return {

      form: {
        login: '',
      },
      name: '',
      repos: [],
      server_address: 'https://graphqlgithubinfo.herokuapp.com',
      show: true
    }
  },
  methods: {
    onSubmit(event) {
      alert(1)
      event.preventDefault()
      alert(2)
      this.getName()
      alert(3)
      if (this.name !== "Not found")
        this.getRepos()
    },
    getName() {
      var self = this

      $.ajax({

        type: "GET",
        contentType: "application/json",
        async: false,
        url: self.server_address + '/users/' + self.form.login,
        success: function (data) {
          if (data.status === 200)
            self.name = data.user.name
          else {
            self.repos = []
            self.name = 'Not found'
          }
        },
        error: function (e) {
          self.name = 'Not found'
          self.repos = []
          console.log(e)
        }
      });

    },
    getRepos() {
      var self = this

      $.ajax({

        type: "GET",
        contentType: "application/json",
        async: false,
        url: self.server_address + "/users/" + self.form.login + "/repos/info",
        success: function (data) {
          self.repos = data["repo_info"]["data"]["repositoryOwner"]["repositories"]["nodes"]
        },
        error: function (e) {
          self.repos = []
          console.log(e)
        }
      });
    },

  }
}
</script>
