<template>
   <v-text-field
      ref="timeSelector"
      v-bind="$attrs"
      v-on="$attrs"
      v-model="time"
      :aria-label="$attrs.label || 'Time'"
      :aria-required="$attrs.required || false"
      :append-inner-icon="($attrs.appendInnerIcon as any) || 'calendar_today'"
      @click:prepend-inner="icoClick('click:prepend-inner', $event)"
      @click:prepend-outer="icoClick('click:prepend-outer', $event)"
      @click:append-inner="icoClick('click:append-inner', $event)"
      @click:append-outer="icoClick('click:append-outer', $event)"
   >
      <template v-for="(_, slotName) in $slots" :key="slotName" v-slot:[slotName]>
         <slot :name="slotName"></slot>
      </template>
   </v-text-field>
</template>

<script lang="ts">
import { computed, defineComponent, ref, onMounted, onBeforeUnmount, PropType, Ref, VNode } from 'vue'
import { TempusDominus, Namespace as TdNs, Options as TdOp } from '@eonasdan/tempus-dominus'

interface MyComponentSlots {
   default?: () => VNode[];
   [key: string]: (() => VNode[]) | undefined;
}

export default defineComponent({
   name: 'VuetifyTempusDominus',
   props: {
      options: {
         type: Object as PropType<TdOp>,
         default: () => ({})
      }
   },
   setup(props: any, { attrs, slots, emit }: any) {
      let picker: TempusDominus | null = null

      const
         time: Ref<string> = ref(''),

         timeSelector: Ref<null | HTMLInputElement> = ref(null),

         subscription: ReturnType<TempusDominus['subscribe']> = [],

         icoClick = (eventName: string, event: Event): void => {
            if (attrs[eventName]) attrs[eventName](event);
            (timeSelector.value as unknown as HTMLInputElement).click()
         }

      onMounted(() => {
         const options: TdOp = Object.assign({
            debug: true,  //For some reason required to work
            useCurrent: false,
            container: (timeSelector as any).value.closest(".v-input"),
            display: {
               icons: {
                  time: 'material-icons access_time',
                  date: 'material-icons calendar_today',
                  up: 'material-icons arrow_upward',
                  down: 'material-icons arrow_downward',
                  previous: 'material-icons chevron_left',
                  next: 'material-icons chevron_right',
                  today: 'material-icons today',
                  clear: 'material-icons clear',
                  close: 'material-icons close'
               },
               theme: 'light'
            }
         }, props.options)

         picker = new TempusDominus(timeSelector.value as unknown as HTMLInputElement, options)
         emit('picker:init', picker)

         Object.values(TdNs.events).forEach((event) => {
            picker && subscription.push(picker.subscribe(event, (e) => {
               const eventType = e.type.replace('.td', '')
               if (eventType === 'change') time.value = e.date.format(picker!.dates.formatInput(picker!.viewDate))
                  .replace(/([ap])9/gi, (_match: any, p1: string) => p1 === 'a' ? 'am' : p1 === 'A' ? 'AM' : p1 === 'p' ? 'pm' : 'PM')  //Hack for weirness with am/pm
               emit(`picker:${eventType}`, e.date || null)
            }) as { unsubscribe: () => void })
         })

         document.addEventListener('click', (e) => picker && !options.container!.contains(e.target as Node) && picker.hide())
      })

      onBeforeUnmount(() => {
         subscription.forEach((sub) => sub?.unsubscribe())
         subscription.length = 0
         picker = null
      })

      return {
         time,
         timeSelector,
         icoClick
      }
   }
})
</script>

<style lang="scss">
@import '@eonasdan/tempus-dominus/src/scss/tempus-dominus';
</style>