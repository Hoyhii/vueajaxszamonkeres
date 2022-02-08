<template>
  <div class="container">
    <div class="border border-dark">
      <h1>Statues</h1>
    </div>

    <table class="table table-hover table-info">
      <thead>
        <tr>
          <th>Személy</th>
          <th>Magasság</th>
          <th>Ár</th>
          <th>Műveletek</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="statue in statues" v-bind:key="statue.id">
          <td>{{ statue.person }}</td>
          <td>{{ statue.height }}</td>
          <td>{{ statue.price }}</td>
          <td>
            <button @click="loadEditData(statue.id)" class="btn btn-primary">
              Szerkesztés
            </button>
            <button @click="deleteStatue(statue.id)" class="btn btn-danger">
              Törlés
            </button>
          </td>
        </tr>
        <tr>
          <td>
            <input type="text" v-model="statue.person" class="form-control" />
          </td>
          <td>
            <input type="number" v-model="statue.height" class="form-control" />
          </td>
          <td>
            <input type="number" v-model="statue.price" class="form-control" />
          </td>
          <td>
            <button v-if="ujAdat" @click="newStatue" class="btn btn-primary">
              Létrehozás
            </button>
            <button
              v-if="!ujAdat"
              @click="saveEditData"
              class="btn btn-primary"
            >
              Mentés
            </button>
            <button v-if="!ujAdat" @click="clearForm" class="btn btn-light">
              Mégse
            </button>
            <button v-if="ujAdat" @click="clearForm" class="btn btn-light">
              Tisztítás
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: "Statues",
  data() {
    return {
      ujAdat: true,
      statue: {
        id: null,
        person: "",
        height: "",
        price: "",
      },
      statues: [],
    };
  },
  methods: {
    async loadData() {
      let Response = await fetch("http://127.0.0.1:8000/api/statues");
      let data = await Response.json();
      this.statues = data;
    },
    async newStatue() {
      await fetch("http://127.0.0.1:8000/api/statues", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Accept: "application/json",
        },
        body: JSON.stringify(this.statue),
      });
      await this.loadData();
      this.clearForm();
    },
    async deleteStatue(id) {
      let Response = await fetch(`http://127.0.0.1:8000/api/statues/${id}`, {
        method: "DELETE",
      });
      console.log(Response);
      await this.loadData();
    },
    async loadEditData(id) {
      let Response = await fetch(`http://127.0.0.1:8000/api/statues/${id}`);
      let data = await Response.json();
      this.statue = { ...data };
      this.ujAdat = false;
    },
    async saveEditData() {
      await fetch(`http://127.0.0.1:8000/api/statues/${this.statue.id}`, {
        method: "PATCH",
        headers: {
          "Content-Type": "application/json",
          Accept: "application/json",
        },
        body: JSON.stringify(this.statue),
      });
      this.loadData();
      this.clearForm();
      this.ujAdat = true;
    },
    clearForm() {
      this.statue = {
        id: null,
        person: "",
        height: "",
        price: "",
      };
      this.ujAdat = true;
    },
  },
  mounted() {
    this.loadData();
  },
};
</script>