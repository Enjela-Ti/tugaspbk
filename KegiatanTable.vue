<template>
  <div id class="app">
    <!-- Header -->
    <header>
      <nav>
        <ul>
          <li><a href="#" @click="selectedTab = 'post'; fetchUserPosts()">Post</a></li>
          <li><a href="#" @click="selectedTab = 'todos'">Todos</a></li>
        </ul>
      </nav>
    </header>

    <div class="container">
      <!-- Konten untuk Post -->
      <div v-if="selectedTab === 'post'" class="post-container">
        <h1 class="post-title">Postingan Pengguna</h1>
        <!-- Select option untuk memilih pengguna -->
        <select v-model="selectedUser" @change="fetchUserPosts">
          <option v-for="user in users" :key="user.id" :value="user.id">{{ user.name }}</option>
        </select>

        <!-- Tampilkan postingan pengguna yang dipilih -->
        <div v-for="post in userPosts" :key="post.id">
          <h2>{{ post.title }}</h2>
          <p>{{ post.body }}</p>
        </div>
      </div>

      <!-- Konten untuk Todos -->
      <div v-if="selectedTab === 'todos'">
        <div>
          <div class="background">
            <img src="https://wallpaperwaifu.com/wp-content/uploads/2023/11/texas-exusiai-arknights-police-chase-thumb.jpg" />
          </div>

          <div class="filter-icon" @click="toggleFilter">
            <button class="filter-button">
              <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="30" height="30" viewBox="0 0 256 256" xml:space="preserve">
                <!-- SVG Path for filter icon -->
              </svg>
              <!-- Tambahkan logika untuk menampilkan ikon filter -->
              <svg v-if="filterActive" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="30" height="30" viewBox="0 0 256 256" xml:space="preserve" fill="black">
                <!-- SVG Path for active filter icon -->
              </svg>
            </button>
          </div>

          <div class="add-activity">
            <input type="text" v-model="newActivity" placeholder="Tambah kegiatan" @keyup.enter="tambahKegiatan">
            <button @click="tambahKegiatan">Tambah</button>
          </div>

          <div class="table-container">
            <table>
              <thead>
                <tr>
                  <th>Kegiatan</th>
                  <th>Status</th>
                  <th>Opsional</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(kegiatan, index) in kegiatanList" :key="index">
                  <td :class="{ 'completed': kegiatan.status === 'Selesai' }">
                    <span v-if="editedIndex !== index">{{ kegiatan.nama }}</span>
                    <input v-else v-model="editedActivity" @keyup.enter="saveKegiatan" @keyup.esc="cancelEdit" autofocus>
                  </td>
                  <td>
                    <select v-if="editedIndex !== index" v-model="kegiatan.status" @change="saveKegiatan">
                      <option value="Belum Selesai">Belum Selesai</option>
                      <option value="Selesai">Selesai</option>
                    </select>
                    <span v-else>{{ kegiatan.status }}</span>
                  </td>
                  <td>
                    <button @click="editKegiatan(index)" v-if="editedIndex !== index">Edit</button>
                    <button @click="saveKegiatan" v-else>Simpan</button>
                    <button @click="cancelEdit" v-if="editedIndex === index">Batal</button>
                    <button @click="hapus(index)">Hapus</button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedTab: 'post',
      kegiatanList: [],
      editedIndex: null,
      editedActivity: '',
      newActivity: '',
      filterActive: false,
      users: [],
      userPosts: [],
      selectedUser: null
    };
  },
  methods: {
    toggleFilter() {
      this.filterActive = !this.filterActive;
    },
    editKegiatan(index) {
      this.editedIndex = index;
      this.editedActivity = this.kegiatanList[index].nama;
    },
    saveKegiatan() {
      if (this.editedIndex !== null && this.editedActivity.trim() !== '') {
        this.kegiatanList[this.editedIndex].nama = this.editedActivity.trim();
        this.editedIndex = null;
        this.editedActivity = '';
      }
    },
    cancelEdit() {
      this.editedIndex = null;
      this.editedActivity = '';
    },
    hapus(index) {
      this.kegiatanList.splice(index, 1);
    },
    tambahKegiatan() {
      if (this.newActivity.trim() !== '') {
        this.kegiatanList.push({ nama: this.newActivity.trim(), status: 'Belum Selesai' });
        this.newActivity = '';
      }
    },
    toggleStatus(index) {
      this.kegiatanList[index].status = this.kegiatanList[index].status === 'Selesai' ? 'Belum Selesai' : 'Selesai';
    },
    async fetchUsers() {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/users');
        const data = await response.json();
        this.users = data;
      } catch (error) {
        console.error('Gagal mengambil data pengguna:', error);
      }
    },
    async fetchUserPosts() {
      if (!this.selectedUser) return;
      try {
        const response = await fetch(`https://jsonplaceholder.typicode.com/posts?userId=${this.selectedUser}`);
        const data = await response.json();
        this.userPosts = data;
      } catch (error) {
        console.error('Gagal mengambil postingan pengguna:', error);
      }
    }
  },
  created() {
    this.fetchUsers();
  }
};
</script>

<style scoped>
/* CSS */
/* Styling CSS Anda di sini */

.completed {
  text-decoration: line-through;
}
header {
  background: linear-gradient(to right, #ff6b6b, #556270); /* Menggunakan gradient warna */
  padding: 10px; /* Memberi ruang di sekitar konten header */
  box-shadow: 0 6px 10px rgba(0, 0, 0, 0.1); /* Menambahkan bayangan lembut */
  backdrop-filter: blur(10px); /* Efek blur latar belakang */
}

nav ul {
  list-style: none; /* Menghilangkan bullet point */
  padding: 2;
  margin: 0;
  display: flex;
}

nav ul li {
  margin-right: 20px; /* Ruang antara setiap item navigasi */
}

nav ul li a {
  color: white; /* Warna teks */
  text-decoration: none; /* Menghilangkan garis bawah */
  font-weight: bold; /* Membuat teks lebih tebal */
  transition: color 0.3s; /* Efek transisi untuk perubahan warna saat dihover */
}

nav ul li a:hover {
  color: #ff8c00; /* Mengubah warna teks saat dihover */
}

.background img {
  position: fixed;
  top: 0;
  left: 0;
  min-height: 90%;
  min-width: 1500px;
  width: 100%;
  height: 100%;
  overflow: hidden;
  z-index: -2;
  object-fit: cover;
  filter: brightness(0.8);
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.filter-icon {
  position: fixed;
  top: 50%;
  left: 90%;
  transform: translate(-50%, -50%);
  cursor: pointer;
  z-index: 9;
}

.add-activity {
  margin-top: 20px;
}

.add-activity input,
.add-activity button {
  margin-right: 10px;
  background-color: transparent;
  backdrop-filter: blur(10px);
}

.table-container {
  display: flex;
  justify-content: center;
  overflow-x: auto;
}

table {
  border-collapse: collapse;
  width: 80%;
}

th,
td {
  border: 1px solid black;
  padding: 8px;
  text-align: left;
  color: wheat;
  text-shadow: 0 0 10px black;
  background-color: transparent;
  backdrop-filter: blur(5px);
}

th {
  color: black;
  text-shadow: none;
  background-color: gray;
}

.completed {
  text-decoration: line-through;

}

/* Style untuk bagian Post */
/* Anime-Inspired CSS Styles */

/* Global Styles */
body {
  margin: 0;
  padding: 0;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: #f8f8f8;
}

/* Header Styles */
header {
  position: fixed;
  top: 0;
  width: 100%;
  background-color: #ffcc00;
  padding: 20px 0;
  text-align: center;
  box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.1);
  z-index: 1000;
}

header h1 {
  margin: 0;
  color: #333;
  font-size: 28px;
  text-transform: uppercase;
  letter-spacing: 2px;
}

/* Navigation Styles */
nav ul {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  justify-content: center;
}

nav ul li {
  margin: 0 15px;
}

nav ul li a {
  text-decoration: none;
  color: #333;
  font-weight: bold;
  font-size: 18px;
  transition: color 0.3s;
}

nav ul li a:hover {
  color: #ff8c00;
}

/* Main Content Styles */
.container {
  max-width: 1200px;
  margin: 100px auto;
  padding: 20px;
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 0px 5px 15px rgba(0, 0, 0, 0.1);
}

/* Post Styles */
.post-container {
  margin-bottom: 30px;
}

.post-title {
  color: #333;
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 10px;
}

.post-content {

  font-size: 16px;
  line-height: 1.6;
}

/* Todos Styles */
.todos-container {
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 0px 5px 15px rgba(0, 0, 0, 0.1);
  padding: 20px;
  margin-top: 30px;
}

.todos-title {
  color: #333;
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 20px;
}

.todo-item {
  margin-bottom: 10px;
}

.completed {
  text-decoration: line-through;
  color: #999;
}

/* Footer Styles */
footer {
  text-align: center;
  padding: 20px 0;

  color: #fff;
}

footer p {
  margin: 0;
}

</style>
