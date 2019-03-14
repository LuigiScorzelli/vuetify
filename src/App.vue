<template>
  <v-app>
    <template>
      <v-toolbar dark color="primary">
        
          <v-menu offset-y>
            <template v-slot:activator="{ on }">
              <v-toolbar-side-icon
                v-on="on"
              >
              </v-toolbar-side-icon>
            </template>
            <v-list>
              <v-list-tile
                v-for="(item, index) in dropdawunItems"
                :key="index"
              >
                <v-list-tile-title>{{ item }}</v-list-tile-title>
              </v-list-tile>
            </v-list>
        </v-menu>
        
        <v-toolbar-title class="white--text">Todo list</v-toolbar-title>
        <v-spacer></v-spacer>
       
        <v-toolbar-items>
          <v-btn icon>
           <v-icon>search</v-icon>
          </v-btn>
          <!--<router-link :to="{path: '/test'}">-->
            <v-btn flat value="test">
              <router-link :to="{path: '/test'}"></router-link>
              item
              <router-view />
            </v-btn>       
          <!--</router-link>
          <router-view />-->
          <v-btn flat>item</v-btn>
        </v-toolbar-items>
      </v-toolbar>
      
    </template>
    <!--<router-view />-->
    <v-container>
      <v-toolbar flat color="white">
        <v-toolbar-title>Todo List</v-toolbar-title>
        <v-divider
          class="mx-2"
          inset
          vertical
        ></v-divider>
        <v-spacer></v-spacer>
        <v-text-field
          v-model="search"
          append-icon="search"
          label="search"
          single-line
          hide-details
        ></v-text-field>
        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ on }">
            <v-btn color="primary" dark class="mb-2" v-on="on">{{ formTitle }}</v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="headline">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container grid-list-md>
                <v-layout column>
                  <v-flex xs12 sm6 md4>
                    <v-text-field v-model="editedItem.title" label="Titolo"></v-text-field>
                  </v-flex>
                  <v-flex xs12 sm6 md4>
                    <v-text-field v-model="editedItem.content" label="Contenuto"></v-text-field>
                  </v-flex>
                  <v-flex xs12 sm6 md4>
                    <v-text-field v-model="editedItem.user" label="Utente"></v-text-field>
                  </v-flex>
                  <v-flex xs12 sm6 md4>
                    <v-date-picker v-model="editedItem.date" label="Data"></v-date-picker>
                  </v-flex>
                </v-layout>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" flat @click="close">Cancel</v-btn>
              <v-btn color="blue darken-1" flat @click="save">Save</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
      <v-data-table
        :headers="columns"
        :items="todos"
        :expand="expand"
        class="elevation-1"
        :search="search"
        v-model="selected"
      >
        <template v-slot:items="props">
          <tr @click="props.expanded = !props.expanded">
            <td>
              <v-checkbox
                v-model="props.selected"
                primary
                hide-details
              ></v-checkbox>
            </td>
            <td v-for="(header, index) in headers" :key="index">
              <p v-if="header.value === 'actions'">
                <v-icon color="blue darken-2" class="mr-2" @click="editItem(props.item)">
                  edit
                </v-icon>
                <v-icon color="red darken-2" @click="deleteItem(props.item)">
                  delete
                </v-icon>
              </p>  
              <p v-else>{{ props.item[header.value] }}</p>
            </td>
          </tr>
        </template>
        <template v-slot:expand="props">
          <v-card flat>
            <v-card-text>{{props.item.content}}</v-card-text>
          </v-card>
        </template>
      </v-data-table>
    </v-container>
  </v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld'

const STATE = {
  COMPLETED: "completed",
  NOT_COMPLETED: "not_completed"
}

export default {
  name: 'App',
  components: {
    HelloWorld
  },
  data () {
    return {
      search: "",
      selected: [],
      expand: false,
      dialog: false,
      formTitle: "Nuovo Todo",
      editedIndex: -1,
      editedItem: {
        title: "",
        content: "",
        date: "",
        user: "",
        state: STATE.NOT_COMPLETED
      },
      headers: [
        {
          text: "Id",
          align: 'left',
          value: "id"
        },
        {
          text: "Utente",
          align: 'left',
          value: "user"
        },
        {
          text: "Titolo",
          align: 'left',
          value: "title"
        },
        {
          text: "Data",
          value: "date",
          align: "left"
        },
        {
          text: "Stato",
          align: 'left',
          value: "state"
        },
        {
          text: "Azioni",
          align: 'left',
          value: "actions"
        }
      ],
      todos: [
        {
          id: 1,
          title: "fare colazione",
          content: "contenuto 1",
          date: "2019-03-14",
          user: "Luigi",
          state: STATE.NOT_COMPLETED
        },
        {
          id: 2,
          title: "fare la spesa",
          content: "contenuto 2",
          date: "2019-03-14",
          user: "Adriano",
          state: STATE.NOT_COMPLETED
        },
        {
          id: 3,
          title: "preparare il pranzo",
          content: "contenuto 3",
          date: "2019-03-14",
          user: "Piero",
          state: STATE.NOT_COMPLETED
        }
      ],
      dropdawunItems: ["item1", "item2", "item3"]
    }
  },
  computed: {
    lastId() {
      return this.todos.length;
    },
    columns() {
      let toRet = [];

      toRet.push({
        text: "",
        align: 'left',
        sortable: false
      });

      this.headers.map(header => toRet.push(header));

      return toRet;
    },
    s() {
      console.log(this.selected);
    }
  },
  methods: {
    closeDialog() {
      this.dialog = false;
      this.formTitle = "Nuovo Todo";
      this.editedIndex = -1;
    },
    save() {
      if(this.editedIndex === -1) {
        this.editedItem.id = this.lastId + 1;
        this.todos.push(this.editedItem);
      } else {
        this.todos.map(todo => {
          if(todo.id === this.editedIndex) {
            todo = Object.assign(todo, this.editedItem);
          }
        })
      }
      this.editedItem = {
        title: "",
        content: "",
        date: "",
        user: "",
        state: STATE.NOT_COMPLETED
      };
      this.closeDialog();
    },
    close() {
      this.closeDialog();
    },
    editItem(item) {
      this.dialog = true;
      this.formTitle = "Modifica Todo";
      this.editedIndex = item.id;
      this.editedItem = Object.assign({}, item);
    },
    deleteItem(item) {
      /*
      let index = -1;
      this.todos.forEach((todo,i) => {
        if(todo.id === item.id) {
          index = i
        }
      });
      this.todos.splice(index, 1);
      */
      this.todos = this.todos.filter(todo => todo.id !== item.id);
    }
  }
}
</script>
