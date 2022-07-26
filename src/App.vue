<script>
import { defineComponent, ref, onMounted, computed, watch } from 'vue'

export default defineComponent({
  name: 'App',
  setup() {
    const todos = ref<[]>([]);
    const name = ref('');
    const inputContent = ref('');
    const inputCategory = ref(null);

    const todosAsc = computed(() => todos.value.sort((a, b) => {
      return b.createdAt - a.createdAt
    }));

    const addToDo = () => {
      if(inputContent.value.trim() === '' || inputCategory.value === null) {
        return
      }

      todos.value.push({
        content: inputContent,
        category: inputCategory.value,
        done: false,
        editable: false,
        createdAt: new Date().getTime()
      })
    }

    const removeTodo = (todo) => {
      todos.value = todos.value.filter((t) => t !== todo)
    }

    watch(todos, (newVal) => {
      localStorage.setItem('todos', JSON.stringify(newVal))
    })

    watch(name, (newVal) => {
      localStorage.setItem('name', newVal)
    });

    onMounted(() => {
      name.value = localStorage.getItem('name') || ''
      todos.value = JSON.parse(localStorage.getItem('todos')) || []
    });

    return { todos, name, inputContent, inputCategory, todosAsc, addToDo, removeTodo }
  }
})
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up? 
        <input 
          type="text"
          placeholder="Enter your name"
          class="inputfield"
          v-model="name">
      </h2>
    </section>

    <section class="create-todo">
      <h3 class="title">CREATE A TODO</h3>

      <form @submit.prevent="addToDo" class="todoform">
        <div class="inputbox">
          <label for="addtodo" class="inputlabel">Whats on your todo list?</label>
          <input 
            v-model="inputContent"
            type="text"
            id="addtodo"
            placeholder="Add a todo"
            class="inputfield">
        </div>

        <div class="options">
          <header class="options-header">
            <h4 class="option-title">Pick a Category</h4>
          </header>

          <div class="options-body">
            <div class="option">
              <input 
                type="radio" 
                name="category" 
                id="catetory-1"
                v-model="inputCategory"
                value="business">
              <label for="catetory-1" class="option-label business">Business</label>
            </div>

            <div class="option">
              <input 
                type="radio" 
                name="category" 
                id="catetory-2" 
                v-model="inputCategory"
                value="personal">
              <label for="catetory-2" class="option-label personal">Personal</label>
            </div>
            
          </div>
        </div>
        
        <button type="submit" class="btn-submit">Add ToDo</button>
      </form>
    </section>

    <section class="todolist">
      <header class="todolist-header">
        <h3 class="todolist-title">TODO LIST</h3>
      </header>

			<div class="list" id="todo-list">
				<div v-for="todo in todosAsc" :key="todo" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
					</label>

					<div class="todo-content">
						<input type="text" v-model="todo.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Delete</button>
					</div>
				</div>
			</div>
		</section>
  </main>
</template>