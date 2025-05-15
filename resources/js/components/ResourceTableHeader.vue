<template>
  <thead class="bg-gray-50 dark:bg-gray-800">
    <tr>
      <!-- Select Checkbox -->
      <th
        class="td-fit uppercase text-xxs text-gray-500 tracking-wide pl-5 pr-2 py-2"
        :class="{
          'border-r border-gray-200 dark:border-gray-600':
            shouldShowColumnBorders,
        }"
        v-if="shouldShowCheckboxes"
      >
        <span class="sr-only">{{ __('Selected Resources') }}</span>
      </th>

      <!-- Field Names -->
      <th
        v-for="(field, index) in fields"
        :key="field.uniqueKey"
        :class="{
          [`text-${field.textAlign}`]: true,
          'border-r border-gray-200 dark:border-gray-600':
            shouldShowColumnBorders,
          'px-6': index == 0 && !shouldShowCheckboxes,
          'px-2': index != 0 || shouldShowCheckboxes,
          'whitespace-nowrap': !field.wrapping,
        }"
        class="uppercase text-gray-500 text-xxs tracking-wide py-2"
      >
        <span v-if="field.tooltip" class="mr-1">
          <span v-tooltip="field.tooltip">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 cursor-pointer text-gray-400 dark:text-gray-500 inline" viewBox="0 0 20 20" fill="currentColor">
              <path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-8-3a1 1 0 00-.867.5 1 1 0 11-1.731-1A3 3 0 0113 8a3.001 3.001 0 01-2 2.83V11a1 1 0 11-2 0v-1a1 1 0 011-1 1 1 0 100-2zm0 8a1 1 0 100-2 1 1 0 000 2z" clip-rule="evenodd" />
            </svg>
          </span>
        </span>

        <SortableIcon
          @sort="requestOrderByChange(field)"
          @reset="resetOrderBy(field)"
          :resource-name="resourceName"
          :uri-key="field.sortableUriKey"
          v-if="sortable && field.sortable"
        >
          {{ field.indexName }}
        </SortableIcon>

        <span v-else>{{ field.indexName }}</span>

      </th>

      <!-- View, Edit, and Delete -->
      <th class="uppercase text-xxs tracking-wide px-2 py-2">
        <span class="sr-only">{{ __('Controls') }}</span>
      </th>
    </tr>
  </thead>
</template>

<script>
export default {
  name: 'ResourceTableHeader',

  emits: ['order', 'reset-order-by'],

  props: {
    resourceName: String,
    shouldShowColumnBorders: Boolean,
    shouldShowCheckboxes: Boolean,
    fields: {
      type: [Object, Array],
    },
    sortable: Boolean,
  },
  methods: {
    /**
     * Broadcast that the ordering should be updated.
     */
    requestOrderByChange(field) {
      this.$emit('order', field)
    },

    /**
     * Broadcast that the ordering should be reset.
     */
    resetOrderBy(field) {
      this.$emit('reset-order-by', field)
    },
  },
}
</script>
