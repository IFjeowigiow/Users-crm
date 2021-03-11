<template>
  <div class="wrapper-content wrapper-content--fixed">
    <section>
      <div class="container">

        <!-- table -->
        <table>

          <!-- head -->
          <thead>
          <tr>
            <th @click="sort('name')">Name &#8595;</th>
            <th @click="sort('age')">Age &#8595;</th>
            <th @click="sort('gender')">Gender &#8595;</th>
          </tr>
          </thead>

          <!-- body -->
          <tbody>
          <tr v-for="user in usersSort" :key="user.id">
            <td>
              <img :src='user.img'  :alt="user.name">
              <span>{{ user.name }}</span>
            </td>
            <td> {{ user.age }}</td>
            <td> {{ user.gender }} </td>
          </tr>
          </tbody>

        </table>
      </div>
    </section>
    <section>
      <div class="container">
        <div class="button-list">
          <button class="btn btnPrimary" @click="prevPage">&#8592;</button>
          <button class="btn btnPrimary" @click="nextPage">&#8594;</button>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data () {
    return {
      users: [],
      currentSort: 'name',
      currentSortDir: 'asc',
      page: {
        current: 1,
        length: 3,
    }
    }
  },
  created () {
      axios
      .get('https://jsonplaceholder.typicode.com/users')
        .then(response => {
          // console.log(response.data)
          this.users = response.data;
          this.users.forEach(item => {
            item.age = Math.round(Math.random() * 30 + 16);
            item.gender = Math.random() > 0.5 ? 'Male' : 'Female';
            item.img = `https://via.placeholder.com/150/${Math.random().toString(16).substr(2,6)}`;
          })
        })
        .catch(error => {
          console.log(error)
        });
    // this.users = [
    //   { id: 1, name: 'Jack', age: 22, gender: 'male' },
    //   { id: 2, name: 'Alex', age: 24, gender: 'male' }
    // ]
  },
  computed: {
    usersSort () {
      return this.users.sort((a, b) => {
        let mod = 1
        if (this.currentSortDir === 'desc') mod = -1;
        if (a[this.currentSort] < b[this.currentSort]) return -1 * mod;
        if (a[this.currentSort] > b[this.currentSort]) return 1 * mod;
        return 0
        })
      .slice(this.page.length * (this.page.current - 1), this.page.length * this.page.current);
    }
  },
  methods: {
    sort (e) {
      if (e === this.currentSort) {
        this.currentSortDir = this.currentSortDir === 'asc' ? 'desc' : 'asc';
      } else {
        this.currentSort = e;
      }
      this.page.current = 1;
    },
    nextPage () {
      if ((this.page.current * this.page.length) < this.users.length) this.page.current += 1;
    },
    prevPage () {
      if (this.page.current > 1) this.page.current -= 1;
    },
  }

}
</script>


<style lang="scss" scoped>
img {
  width: 60px;
  height: auto;
  border-radius: 50%;
  margin-right: 16px;
}
.button-list {
  width: 100%;
  text-align: center;
  .btn {
    border-radius: 60px;
    margin: 0 20px;
  }
}
</style>
