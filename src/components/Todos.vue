<template>
    <div>
    <section class="hero is-primary is-medium">
      <div class="hero-body">
        <div class="container">
          <h1 class="title has-text-centered">
            {{ title }}
          </h1>
          <h2 class="subtitle has-text-centered">
            {{ subtitle }}
          </h2>
        </div>
      </div>
    </section>

    <section class="section">
      <div class="content" >
        <b-field label="Add a todo item" has-text-danger>
            <b-input placeholder="Select a name" v-model="newTodo" @keyup.native.enter="addTodo"></b-input>
        </b-field>
      </div>
    </section>

    <section class="section">
      <div class="content" >
            <b-table :data="todos" :bordered=true >
            <template slot-scope="props">
                <b-table-column field="id" label="ID" width="40">{{props.row.id}}</b-table-column>
                <b-table-column v-if="!props.row.editing" @dblclick.native="editTodo(props.row)" field="name" label="Name" width="200" :class="{completed : props.row.completed}" >{{props.row.name}}</b-table-column>

                <b-table-column v-else field="name" label="Name" @keyup.native.enter="doneEdit(props.row)">
                    <b-input v-focus v-model="props.row.name" width="200" @keyup.native.esc="cancelEdit(props.row)" ></b-input>
                </b-table-column>

                <b-table-column field="description" label="Description" width="300" :class="{completed : props.row.completed}">{{props.row.description}}</b-table-column>
                <b-table-column field="completed" label="Completed" width="40" >

                <b-checkbox v-model="props.row.completed">
                    {{props.row.completed}}
                </b-checkbox>

                </b-table-column>
                <b-table-column label="Actions" custom-key="actions" width="40">
                    <button class="button" @click="editTodo(props.row.id)">
                        <b-icon pack="fa" icon="edit"></b-icon>
                    </button>
                    <button class="button" @click="removeTodo(props.row.id)">
                        <b-icon pack="fa" icon="eraser"></b-icon>
                    </button>
                </b-table-column>
        </template>
        </b-table>


      </div>

    </section>
    </div>
</template>


<script>
    import axios from 'axios';
    axios.defaults.baseURL = "http://localhost:8000/api"
    const focus = {
        inserted(el) {
          el.focus()
        },
    }

    export default {
        name: 'todos',
        directives: { focus },
        data() {
            return {
                title: "Todo application",
                subtitle: "Simpliest interface for all your todos",
                beforeEditName: "",
                newTodo: '',
                idForTodo: 4,
                checkedRows: 0,
                todos: [
                    {
                        'id': 1,
                        'name': "Todo 1",
                        'description': "Todo 1 description",
                        'completed': false,
                        'editing': false
                    },
                    {
                        'id': 2,
                        'name': "Todo 2",
                        'description': "Todo 2 description",
                        'completed': false,
                        'editing': false
                    },
                    {
                        'id': 3,
                        'name': "Todo 3",
                        'description': "Todo 3 description",
                        'completed': false,
                        'editing': false
                    }
                ]
            }
        },
        created() {
            axios.get('/todos')
                .then(response => {
                    console.log(response)
                    this.todos = response.data
                })
                .catch(error => {
                    console.log(error)
                })
            console.log(this.items);
          },
        methods: {
            addTodo() {
                if (this.newTodo.trim().length==0) {
                    return
                }
                this.todos.push({
                    id: this.idForTodo,
                    name: this.newTodo,
                    description: this.newTodo,
                    completed: false
                })
                this.newTodo = ''
                this.idForTodo++
            },
            editTodo(obj) {
                this.beforeEditName = obj.name
                obj.editing = true
            },
            doneEdit(obj) {
                if (obj.name.trim()=='') {
                    obj.name = this.beforeEditName
                }

            },
            cancelEdit(obj) {
                obj.name = this.beforeEditName
                obj.editing = false
            },
            removeTodo(index) {
                this.todos = this.todos.filter(function( obj ) {
                    return obj.id !== index;
                });
            }
        }
    }
</script>

<style>
    .completed {
        text-decoration: line-through;
        color: red;
    }
</style>
