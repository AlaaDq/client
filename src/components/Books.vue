<template>
  <div class="container">
      <input id="form-title-input"
             type="text"
             v-model="addBookForm.title"
             placeholder="Enter title">
        <input id="form-author-input"
               type="text"
               v-model="addBookForm.author"
               placeholder="Enter author">
        <div class="form-check">
            <input class="form-check-input" v-model="addBookForm.read" type="checkbox" value="true" id="form-checks">
            <label class="form-check-label" for="defaultCheck1">
            Default checkbox
            </label>
        </div>
    <div class="row">
      <div class="col-sm-10">
        <h1>Books</h1>
        <hr><br><br>
        <alert  v-if="showMessage" v-on:hide="clearAlert">
            {{this.message}}
        </alert>
        <button type="button" @click="onSubmit" class="btn btn-success btn-sm">Add Book</button>
        <br><br>
        <table class="table table-hover">
          <thead>
            <tr>
              <th scope="col">Title</th>
              <th scope="col">Author</th>
              <th scope="col">Read?</th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(book) in books" :key="book.id">
              <td v-if="editForm.id!=book.id">{{ book.title }}</td>
              <td v-if="editForm.id==book.id && edit"><input type="text" v-model="editForm.title"></td>
              <td v-if="editForm.id!=book.id">{{ book.author }}</td>
              <td v-if="editForm.id==book.id && edit"><input type="text" v-model="editForm.author"></td>
              <td v-if="editForm.id!=book.id">
                <span v-if="book.read">Yes</span>
                <span v-else>No</span>
              </td>
              <td v-if="editForm.id==book.id && edit"><input class="form-check-input" v-model="editForm.read" type="checkbox" value="true" id="form-checks"></td>
              <td>
                <div class="btn-group" role="group">
                  <button type="button" v-if="editForm.id!=book.id" @click="editBook(book)" class="btn btn-warning btn-sm">edit</button>
                  <button type="button" v-if="editForm.id==book.id && edit" @click="onSubmitUpdate" class="btn btn-warning btn-sm">Update</button>
                  <button type="button" @click="onDeleteBook(book)" class="btn btn-danger btn-sm">Delete</button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import Alert from './Alert.vue';

export default {
  data() {
    return {
      books: [],
      addBookForm: {
        title: '',
        author: '',
        read: [],
      },
      message: '',
      showMessage: false,
      edit: false,
      editForm: {
        id: '',
        title: '',
        author: '',
        read: false,
      },
    };
  },
  components: {
    alert: Alert,
  },
  methods: {
    clearAlert() {
      this.showMessage = false;
    },
    getBooks() {
      const path = 'http://localhost:5000/books';
      axios.get(path)
        .then((res) => {
          this.books = res.data.books;
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.error(error);
        });
    },
    addBook(payload) {
      const path = 'http://localhost:5000/books';
      axios.post(path, payload)
        .then(() => {
          this.getBooks();
          this.message = 'Book added!';
          this.showMessage = true;
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.log(error);
          this.getBooks();
        });
    },
    initForm() {
      this.addBookForm.title = '';
      this.addBookForm.author = '';
      this.addBookForm.read = [];
    },
    onSubmit() {
      let read = false;
      if (this.addBookForm.read[0]) read = true;
      const payload = {
        title: this.addBookForm.title,
        author: this.addBookForm.author,
        read, // property shorthand
      };
      this.addBook(payload);
      this.initForm();
    },
    editBook(book) {
      this.editForm = book;
      this.edit = true;
    },
    onSubmitUpdate() {
      let read = false;
      if (this.editForm.read) read = true;
      const payload = {
        title: this.editForm.title,
        author: this.editForm.author,
        read,
      };
      this.updateBook(payload, this.editForm.id);
      this.editForm.id = '';
      this.editForm.title = '';
      this.editForm.author = '';
      this.editForm.read = false;
      this.edit = false;
    },
    updateBook(payload, bookID) {
      const path = `http://localhost:5000/books/${bookID}`;
      axios.put(path, payload)
        .then(() => {
          this.getBooks();
          this.message = 'Book updated!';
          this.showMessage = true;
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.error(error);
          this.getBooks();
        });
    },
    removeBook(bookID) {
      const path = `http://localhost:5000/books/${bookID}`;
      axios.delete(path)
        .then(() => {
          this.getBooks();
          this.message = 'Book removed!';
          this.showMessage = true;
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.error(error);
          this.getBooks();
        });
    },
    onDeleteBook(book) {
      this.removeBook(book.id);
    },

  },
  created() {
    this.getBooks();
  },
};
</script>
