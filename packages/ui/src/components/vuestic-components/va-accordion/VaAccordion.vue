<template>
  <div class="va-accordion">
    <slot />
  </div>
</template>

<script lang="ts">
import { Component, Mixins, Provide } from 'vue-property-decorator'

import { makeContextablePropsMixin } from '../../context-test/context-provide/ContextPlugin'
import { StatefulMixin } from '../../vuestic-mixins/StatefulMixin/StatefulMixin'

const PropsMixin = makeContextablePropsMixin({
  value: { type: Array, default: () => [] },
  multiply: { type: Boolean, default: false },
  inset: { type: Boolean, default: false },
  popout: { type: Boolean, default: false },
})

@Component({
  name: 'VaAccordion',
})
export default class VaAccordion extends Mixins(
  StatefulMixin,
  PropsMixin,
) {
  @Provide() accordion = {
    onChildChange: (child: any, state: any) => this.onChildChange(child, state),
  }

  onChildChange (child: any, state: any) {
    const emitValue: any = []
    this.$children.forEach(collapse => {
      if (collapse === child) {
        emitValue.push((collapse as any).valueProxy)
        return
      }
      if (!this.c_multiply) {
        (collapse as any).valueProxy = false
      }
      emitValue.push((collapse as any).valueProxy)
    })
    this.valueComputed = emitValue
  }

  mounted () {
    this.$children.forEach((collapse, index) => {
      (collapse as any).valueProxy = this.valueComputed[index]
    })
  }

  updated () {
    this.$children.forEach((collapse, index) => {
      (collapse as any).valueProxy = this.valueComputed[index]
    })
  }
}
</script>

<style lang="scss">
.va-accordion {
}
</style>
