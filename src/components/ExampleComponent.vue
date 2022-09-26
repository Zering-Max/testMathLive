<!-- <template>
  <div>
    <p>{{ title }}</p>
    <ul>
      <li v-for="todo in todos" :key="todo.id" @click="increment">
        {{ todo.id }} - {{ todo.content }}
      </li>
    </ul>
    <p>Count: {{ todoCount }} / {{ meta.totalCount }}</p>
    <p>Active: {{ active ? 'yes' : 'no' }}</p>
    <p>Clicks on todos: {{ clickCount }}</p>
  </div>
</template>

<script lang="ts">
import {
  defineComponent,
  PropType,
  computed,
  ref,
  toRef,
  Ref,
} from 'vue';
import { Todo, Meta } from './models';

function useClickCount() {
  const clickCount = ref(0);
  function increment() {
    clickCount.value += 1
    return clickCount.value;
  }

  return { clickCount, increment };
}

function useDisplayTodo(todos: Ref<Todo[]>) {
  const todoCount = computed(() => todos.value.length);
  return { todoCount };
}

export default defineComponent({
  name: 'ExampleComponent',
  props: {
    title: {
      type: String,
      required: true
    },
    todos: {
      type: Array as PropType<Todo[]>,
      default: () => []
    },
    meta: {
      type: Object as PropType<Meta>,
      required: true
    },
    active: {
      type: Boolean
    }
  },
  setup(props) {
    return { ...useClickCount(), ...useDisplayTodo(toRef(props, 'todos')) };
  },
});
</script> -->

<template>
<div class="editor" v-if="editor">
  <div class="editor__header">
    <template v-for="(item, index) in items">
      <div class="divider" v-if="item.type === 'divider'" :key="`divider${index}`" />
      <button v-else :key="index" v-bind="item"
      class="menu-item"
      :class="{ 'is-active': isActive ? isActive(): null }"
      @click="item.action"
      :title="item.title"
      >
        <q-icon :name="item.icon" size="20px" />
      <!-- <svg class="remix">
        <use :xlink:href="`${remixiconUrl}#ri-${icon}`" />
      </svg> -->
      </button>
    </template>
  </div>
  <editor-content class="editor__content" :editor="editor" />

</div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { Editor, EditorContent, VueNodeViewRenderer } from '@tiptap/vue-3';
import StarterKit from '@tiptap/starter-kit';
import { Node } from '@tiptap/core';
import { CommandProps, RawCommands } from '@tiptap/core/src/types';
import { MathfieldElement } from 'mathlive';
import MathComponent from './MathComponent.vue';

export default defineComponent({
  components: {
    EditorContent,
  },

  setup() {
    const formula = new MathfieldElement();
    formula.value = 'x^2';
    formula.setOptions({
      virtualKeyboardMode: 'manual',
    });
    const MathFormula = Node.create({
      name: 'formulaComponent',
      group: 'block',
      addCommands(): Partial<RawCommands> {
        let addFormula:Partial<RawCommands> = () => MathfieldElement;
        addFormula = (attributes:Record<string, undefined>) => (attrObj:CommandProps) => {
          console.log('ok');
          const { selection } = attrObj.state;
          console.log(selection);
          const position = selection.$anchor ? selection.$anchor.pos : selection.$to.pos;
          console.log(position);
          const node = this.type.create(attributes);
          console.log(node);
          const transaction = attrObj.state.tr.insert(position, node);
          console.log(transaction);
          if (attrObj.dispatch !== undefined) {
            console.log('ok 2');
            attrObj.dispatch(transaction);
          }
        };
        return {
          addFormula,
        };
      },
      addNodeView() {
        return VueNodeViewRenderer(MathComponent);
      },
    });
    const editor = new Editor({
      content: '<p>I’m running Tiptap with Vue.js. 🎉</p>',
      extensions: [
        StarterKit,
        MathFormula,
      ],
    });

    const items = [
      {
        icon: 'format_bold',
        title: 'Bold',
        action: () => editor.chain().focus().toggleBold().run(),
        isActive: () => editor.isActive('bold'),
      },
      {
        icon: 'format_italic',
        title: 'Italic',
        action: () => editor.chain().focus().toggleItalic().run(),
        isActive: () => editor.isActive('italic'),
      },
      {
        icon: 'functions',
        title: 'Add Math formulas',
        action: () => editor.chain().focus().addFormula(),
      },
    ];

    return { editor, items };
  },
});
</script>

<style lang="scss">
.divider {
  width: 2px;
  height: 1.25rem;
  background-color: rgba(#000, 0.1);
  margin-left: 0.5rem;
  margin-right: 0.75rem;
}

.editor {
  display: flex;
  flex-direction: column;
  max-height: 26rem;
  color: #0D0D0D;
  background-color: #FFF;
  border: 3px solid #0D0D0D;
  border-radius: 0.75rem;

  &__header {
    display: flex;
    align-items: center;
    flex: 0 0 auto;
    flex-wrap: wrap;
    padding: 0.25rem;
    border-bottom: 3px solid #0D0D0D;
  }

  &__content {
    padding: 1.25rem 1rem;
    flex: 1 1 auto;
    overflow-x: hidden;
    overflow-y: auto;
    -webkit-overflow-scrolling: touch;
  }
}

.menu-item {
  width: 1.75rem;
  height: 1.75rem;
  color: #0D0D0D;
  border: none;
  background-color: transparent;
  border-radius: 0.4rem;
  padding: 0.25rem;
  margin-right: 0.25rem;

  svg {
    width: 100%;
    height: 100%;
    fill: currentColor;
  }

  &.is-active,
  &:hover {
    color: #FFF;
    background-color: #0D0D0D;
  }
}
</style>
