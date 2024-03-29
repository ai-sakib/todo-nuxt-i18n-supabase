<template>
  <div class="todo-item-container">
    <TodoCardLoader v-if="todo.isLoading" icon-width="64" icon-height="64" />
    <div v-if="!isTodoEditing" class="todo-item">
      <div class="todo-item__header">
        <span
          class="todo-title"
          :class="isTodoCompleted ? 'text-line-through' : ''"
          >{{ todo.title }}</span
        >
        <span class="time"
          >{{ $t('created-at') }}:
          {{ format(parseISO(todo.createdAt), 'dd-MMM-yyyy') }}
        </span>
      </div>

      <div class="todo-item__footer">
        <TodoActions :todo="todo" />
        <div>
          <button v-if="isTodoCompleted" class="btn__completed-in">
            {{ $t('completed-in') }}:
            {{ getCompletedInTime }}
          </button>
        </div>
      </div>
    </div>
    <div v-else class="card__add">
      <AddTodoCard :todo="todo" :is-todo-editing="true" />
    </div>
  </div>
</template>
<script>
import { mapGetters } from 'vuex'
import { format, formatDistance, parseISO } from 'date-fns'
import { bn, es, fr, it, de } from 'date-fns/locale'
import { BN, ES, FR, IT, DE } from '@/utils/constants'

import TodoActions from '@/components/todo/utils/TodoActions.vue'
import AddTodoCard from '@/components/todo/AddTodoCard.vue'
import TodoCardLoader from '@/components/todo/utils/TodoCardLoader.vue'

export default {
  components: {
    TodoActions,
    AddTodoCard,
    TodoCardLoader,
  },
  props: {
    todo: {
      type: Object,
      required: true,
    },
  },

  setup() {
    return {
      format,
      formatDistance,
      parseISO,
      bn,
      es,
      fr,
      it,
      de,
    }
  },
  data() {
    return {}
  },

  computed: {
    ...mapGetters('todos', ['editingTodo', 'loadingId']),
    isTodoCompleted() {
      return !!this.todo.completedAt
    },
    isTodoEditing() {
      return this.editingTodo ? this.editingTodo.id === this.todo.id : false
    },
    getLocale() {
      let locale
      switch (this.$i18n.locale) {
        case BN.code:
          locale = { locale: bn }
          break
        case ES.code:
          locale = { locale: es }
          break
        case FR.code:
          locale = { locale: fr }
          break
        case IT.code:
          locale = { locale: it }
          break
        case DE.code:
          locale = { locale: de }
          break
        default:
          locale = {}
          break
      }

      return locale
    },
    getCompletedInTime() {
      return formatDistance(
        parseISO(this.todo.createdAt),
        parseISO(this.todo.completedAt),
        this.getLocale
      )
    },
  },
}
</script>
<style scoped lang="scss">
@import '@/assets/css/variables';
@import '@/assets/css/mixins';
.time {
  font-size: 14px;
  font-weight: 700;
  line-height: 16.41px;
  color: #bbbdd0;
}

.todo-item-container {
  position: relative;
  height: 100%;
  width: 100%;
}

.todo-item {
  height: 100%;
  @include flex(column, nowrap, space-between, stretch, 16px);
}

.todo-item__header {
  @include flex(column);
}

.todo-item__footer {
  @include flex(row, nowrap, space-between, center);
}

.todo-title {
  color: #32394b;
  font-size: 24px;
  font-weight: 700;
  line-height: 28.13px;
  overflow-wrap: break-word;
}
.text-line-through {
  color: #0bc375;
  text-decoration: line-through;
}

.btn__completed-in {
  cursor: pointer;
  border: none;
  border-radius: 5px;
  min-height: 24px;
  background-color: #7a8dfd;
  color: #ffffff;
  font-size: 12px;
  font-weight: 700;
  line-height: 14.06px;
}

.card__add {
  height: 100%;
}
</style>
