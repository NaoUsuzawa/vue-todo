<template>
  <app-wrapper>
    <app-navi />
    <app-register v-if="todoFilter !== 'completedTodos'" />
    <app-error-message
      v-if="errorMessage"
      :error-message="errorMessage"
    />
    <template v-slot:todos>
      <app-list v-if="todos.length"
      :todos="todos"
      @delete-todo="deleteTodo"/>
    </template>
  </app-wrapper>
</template>

<script>
import Wrapper from 'TodoVuexDir/components/Wrapper/index.vue';
import { ErrorMessage, EmptyMessage } from 'TodoVuexDir/components/Message';
import Register from 'TodoVuexDir/components/Register/index.vue';
import List from 'TodoVuexDir/components/List/index.vue';
import Navi from 'TodoVuexDir/components/Navi/index.vue';
import { mapActions } from 'vuex';

export default {
  components: {
    appWrapper: Wrapper,
    appErrorMessage: ErrorMessage,
    appEmptyMessage: EmptyMessage,
    appList: List,
    appRegister: Register,
    appNavi: Navi,
  },
  data() {
    return {
      emptyMessage: '',
    };
  },
  computed: {
    todoFilter() {
      return this.$store.state.todoFilter;
    },
    todos() {
      if (this.todoFilter === 'allTodos') {
        return this.$store.state.todos;
      }
      return this.$store.getters[this.todoFilter];
    },
    errorMessage() {
      return this.$store.state.errorMessage;
    },
  },
  methods: {
    ...mapActions([
      'deleteTodo',
    ]),
  },
  watch: {
    todos(todos) {
      if (!todos.length) {
        this.$store.dispatch('setEmptyMessage', this.todoFilter);
      }
    },
    $route(to) {
      this.$store.dispatch('setTodoFilter', to.name);
    },
  },
  created() {
    this.$store.dispatch('getTodos');
    this.$store.dispatch('setTodoFilter', this.$route.name);
  },
};
</script>
