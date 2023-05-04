<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])

const boja = () => ({
 light: '#e4dddd',
  dark: '#1f1b1b'}
)
const ime = ref('')
const mod = ref('Light mod')
const input_content = ref('')
const input_category = ref(null)
const addTodo = () => {
  if (input_content.value.trim() === '' || input_content.value === null) return
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdat: new Date().getTime()
  })
  input_content.value = ''
}
const removeTodo = (todo) =>{
  todos.value = todos.value.filter((t)=> t !== todo)

}

const promjenaboje = () =>{
  console.log(boja().light)
  if(mod.value === 'Light mod'){
    document.body.style.backgroundColor = boja().light
    mod.value = 'Dark mod'
  }else{
    document.body.style.backgroundColor = boja().dark
    mod.value = 'Light mod'
  }
}

const todos_poredano = computed(() => todos.value.sort((a, b) => {
  return a.createdat - b.createdat
}))
watch(ime, (novavrijednost) => {
  localStorage.setItem('ime', novavrijednost)
})

watch(todos, novavrijednost => {
  localStorage.setItem('todos', JSON.stringify(novavrijednost))
}, { deep: true })
onMounted(() => {
  ime.value = localStorage.getItem('ime') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
  
})

</script>

<template>
  <main class="app">
    <div class="greeting">
      <h2 class="title">
        Pozdrav ,
        <input type="text" placeholder="Vaše ime" v-model="ime" />
      </h2>
    </div>
    <div class="hel">
     <button  @click="promjenaboje" class="button">{{mod}}</button>
    </div>
    <div class="create-todo">
      <h3>Napravi todo!</h3>

      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>Što je na vašoj listi?</h4>
        <input type="text" name="content" v-model="input_content" placeholder="Napiši todo">
        <h4>Odaberi kategoriji</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" id="category1" value="buisness" v-model="input_category">
            <span class="bubble buisness"></span>
            <div>Poslovni</div>
          </label>
          <label>
            <input type="radio" name="category" id="category2" value="personal" v-model="input_category">
            <span class="bubble personal"></span>
            <div>Privatni</div>
          </label>
        </div>
        <input type="submit" value="Dodaj todo">
      </form>
    </div>

   <div class="todo-list">
			<h3>TODO LIST</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${todo.category}`"></span>
					</label>

					<div class="todo-content">
						<input type="text" v-model="todo.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Delete</button>
					</div>
				</div>

			</div>
		</div>
  </main>
</template>
