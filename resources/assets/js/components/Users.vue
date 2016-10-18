<style>
  .loading {
    display: block;
    text-align: center;
    padding: 1rem;
    margin-bottom: 1rem;
    font-size: 1.5rem;
  }

  .finished {
    text-align: center;
    color: #999;
  }

  [v-cloak] {
    display: none;
  }
</style>

<template>
    <div class="container">
        <div class="row">
            <div class="col-md-8 col-md-offset-2">
                <div class="panel panel-default">
                    <div class="panel-heading">JSON Paginate Vue Component</div>

                    <div class="panel-body">
                        <ul class="list-group" v-cloak>

                          <li
                            v-for="user in users"
                            class="list-group-item">
                            {{ user.name }}
                          </li>
                        </ul>
                        <div v-show="loading" class="loading">
                          Loading...
                        </div>
                        <h5 v-show="finished" class="finished">No more users</h5>
                        <button :disabled="finished" @click="nextUsers()" type="button" class="btn btn-primary btn-block" name="button">Next</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    // Vue component
    export default {
        data() {
          return {
            loading: true,
            users: [],
            next_link: '',
            current_page: '',
            last_page: ''
          }
        },
        computed: {
          finished() {
            if(this.current_page === this.last_page) {
              return true
            } else {
              return false;
            }
          }
        },
        methods: {
          request(link) {
            this.loading = true;
            this.$http.get(link).then((response) => {
              if(this.users.length === 0) {
                console.log('No users list yet');
                this.users = response.data.data;
              } else {
                console.log('Users already exist, push these in');
                this.users.push.apply(this.users, response.data.data)
              }
              this.loading = false;
              this.current_page = response.data.current_page;
              this.last_page = response.data.last_page;
              this.next_link = response.data.next_page_url;
            }, (response) => {
              console.log(response);
            });
          },
          onScroll(e, position) {
            this.position = position;
          },
          nextUsers() {
            var link = this.next_link;
            this.request(link);
          }
        },
        mounted() {
          this.request('/users');
          console.log('Component ready.');
        }
    }
</script>
