<template>
  <div class="todo-container">
    <TodoCardLoader v-if="isTodoSearching" class="loader" />

    <span class="todo__add-text">{{ $t('add-task') }}</span>
    <div class="todo__header">
      <button
        class="btn__create-task"
        :class="lightenClass"
        @click.prevent="setIsCreating"
      >
        <PlusIcon />
        <span>{{ $t('create') }}</span>
      </button>
      <FilterOptions />
    </div>

    <div class="card-container">
      <div v-if="isTodoCreating" class="card-item">
        <AddTodoCard />
      </div>

      <!-- List of todos -->
      <template v-if="isTodoAvailable">
        <div v-for="todo in todos" :key="todo.id" class="card-item">
          <TodoItem class="todo-item" :todo="todo" />
        </div>
      </template>
    </div>

    <TodoNotFound v-if="!isTodoFound" />

    <!-- Load more/less todos -->
    <TodoFooter />
  </div>
</template>
<script>
import { mapGetters } from 'vuex'
import FilterOptions from '@/components/todo/FilterOptions.vue'
import AddTodoCard from '@/components/todo/AddTodoCard.vue'
import TodoItem from '@/components/todo/TodoItem.vue'
import TodoNotFound from '@/components/todo/utils/TodoNotFound.vue'
import TodoFooter from '@/components/todo/utils/TodoFooter.vue'
import TodoCardLoader from '@/components/todo/utils/TodoCardLoader.vue'
import PlusIcon from '@/icons/PlusIcon.vue'

export default {
  name: 'TodoContainer',
  components: {
    FilterOptions,
    AddTodoCard,
    TodoItem,
    TodoNotFound,
    TodoFooter,
    TodoCardLoader,
    PlusIcon,
  },
  data() {
    return {}
  },

  computed: {
    ...mapGetters('todos', [
      'isTodoCreating',
      'todos',
      'isTodoSearching',
      'isButtonDisabled',
      'isTodoAdding',
    ]),
    isTodoAvailable() {
      return this.todos.length > 0
    },
    isTodoFound() {
      return this.isTodoAvailable || this.isTodoCreating
    },
    lightenClass() {
      return {
        lighten: this.isTodoAdding,
      }
    },
  },

  methods: {
    setIsCreating() {
      if (this.isButtonDisabled) return
      this.$store.dispatch('todos/setIsCreating')
    },
  },
}
</script>
<style scoped lang="scss">
@import '@/assets/css/variables';
@import '@/assets/css/mixins';

$grid-breakpoints: (#{$sm}: 2, #{$lg}: 3);

.todo-container {
  @include padding(20px, $padding-breakpoints);
  @include flex(column, nowrap, space-between);
  gap: 10px;
}
.todo__header {
  @include flex;
  width: 100%;

  @media (max-width: $sm) {
    @include flex(column, nowrap, space-between);
    & > * {
      width: 100%;
    }
  }
}
.card-container {
  @include grid(1, 20px, $grid-breakpoints);
  margin: 25px 0;
}

$card-padding: 15px;
.card-item {
  border: $border-1;
  border-radius: 5px;
  background-color: $bg-white;
  padding: $card-padding;
  min-height: 150px;
}

.todo-item {
  height: 100%;
}

.todo-footer {
  @include flex(row, nowrap, center, center, 10px);
}

.btn__create-task {
  @include flex(row, nowrap, center, center, 5px);
  border: none;
  outline: none;
  background-color: #7a8dfd;
  color: $bg-white;
  width: 120px;
  height: $button-height;
  padding: 5px 10px;
  border-radius: 5px;
  font-size: 16px;
  font-weight: 500;
  cursor: pointer;

  @media (max-width: $sm) {
    width: 100%;
  }
}

.todo__add-text {
  margin: 25px 0;
  font-size: 30px;
  font-weight: 700;
  line-height: 35.16px;
  color: #32394b;
  overflow-wrap: break-word;
}

.lighten {
  opacity: 0.5;
}

.loader {
  height: calc(100% - #{$header-height});
}
</style>
