<template>
  <node-view-wrapper>
    <!-- {{ formula }} -->
    <!-- <vue-mathfield virtualKeyboardMode="manual">
      x^2
    </vue-mathfield> -->
    <!-- <div>
      Coucou
    </div> -->
    <!-- <mathlive-mathfield>
      x^2
    </mathlive-mathfield>
    <div>
      {{formula}}
    </div> -->
      {{formula}}
  </node-view-wrapper>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { NodeViewWrapper, nodeViewProps } from '@tiptap/vue-3';
import { MathfieldElement } from 'mathlive';
import 'mathlive/dist/mathlive-fonts.css';
import 'mathlive/dist/mathlive';

export default defineComponent({
  components: {
    NodeViewWrapper,
  },
  props: nodeViewProps,
  setup() {
    const formula = new MathfieldElement();
    formula.value = 'x^2';
    formula.setOptions({
      virtualKeyboardMode: 'manual',
    });
    console.log(formula);
    return {
      formula,
    };
  },
});
</script>
