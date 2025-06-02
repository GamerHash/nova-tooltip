<template>
  <div
    class="flex flex-col md:flex-row -mx-6 px-6 py-2 md:py-0 space-y-2 md:space-y-0"
    :dusk="field.attribute"
  >
    <div class="md:w-1/4 md:py-3">
      <slot>
        <h4 class="font-bold md:font-normal">
          <span class="flex items-center">
            {{ label }}

            <span v-if="tooltip !== ''" class="ml-1 inline-flex">
              <span v-tooltip="tooltip">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 cursor-pointer text-gray-400 dark:text-gray-500" viewBox="0 0 20 20" fill="currentColor">
                  <path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-8-3a1 1 0 00-.867.5 1 1 0 11-1.731-1A3 3 0 0113 8a3.001 3.001 0 01-2 2.83V11a1 1 0 11-2 0v-1a1 1 0 011-1 1 1 0 100-2zm0 8a1 1 0 100-2 1 1 0 000 2z" clip-rule="evenodd" />
                </svg>
              </span>
            </span>
          </span>
        </h4>
      </slot>
    </div>
    <div class="md:w-3/4 md:py-3 break-all lg:break-words">
      <slot name="value">
        <CopyButton
          v-if="fieldValue && field.copyable && !shouldDisplayAsHtml"
          @click.prevent.stop="copy"
          v-tooltip="__('Copy to clipboard')"
        >
          <span ref="theFieldValue">
            {{ fieldValue }}
          </span>
        </CopyButton>

        <p
          v-else-if="fieldValue && !field.copyable && !shouldDisplayAsHtml"
          class="text-90 flex items-center"
        >
          {{ fieldValue }}
        </p>
        <div
          v-else-if="fieldValue && !field.copyable && shouldDisplayAsHtml"
          v-html="fieldValue"
        />
        <p v-else>&mdash;</p>
      </slot>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    index: {
      type: Number,
      required: true,
    },

    field: {
      type: Object,
      required: true,
    },

    fieldName: {
      type: String,
      default: '',
    },
  },

  data() {
    return {
      clipboard: null,
    }
  },

  methods: {
    copy() {
      // Implementacja własnego copyValueToClipboard
      const el = document.createElement('textarea')
      el.value = this.field.value
      document.body.appendChild(el)
      el.select()
      document.execCommand('copy')
      document.body.removeChild(el)

      // Powiadomienie o skopiowaniu
      if (this.$toasted) {
        this.$toasted.show(this.__('Copied!'), { type: 'success' })
      }
    },
  },

  computed: {
    label() {
      return this.fieldName || this.field.name
    },

    /**
     * Return the tooltip that should be used for the field.
     */
    tooltip() {
      return this.field.tooltip || ''
    },

    // Implementacja właściwości z FieldValue
    fieldValue() {
      if (!this.field) return ''

      if (this.field.displayCallback) {
        return this.field.displayCallback(this.field.value)
      }

      return this.field.value !== null ? this.field.value : ''
    },

    shouldDisplayAsHtml() {
      return this.field.asHtml
    },
  },
}
</script>
