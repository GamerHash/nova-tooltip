<template>
    <div>
        <component
            :is="component"
            v-bind="linkAttributes"
            class="w-full flex min-h-8 px-1 py-1 rounded text-left text-gray-500 dark:text-gray-500 focus:outline-none focus:ring focus:ring-primary-200 dark:focus:ring-gray-600 cursor-pointer hover:bg-gray-200 dark:hover:bg-gray-800"
            :data-active-link="item.active"
            :class="{
        'font-bold text-primary-500 dark:text-primary-500': item.active,
      }"
            @click="handleClick"
        >

            <span class="inline-block shrink-0 w-6 h-6"/>
            <span class="flex-1 flex items-center w-full px-3 text-sm">
        {{ item.name }}
      </span>

            <span class="inline-block h-6 shrink-0">
        <Badge v-if="item.badge" :extra-classes="item.badge.typeClass">
          {{ item.badge.value }}
        </Badge>
      </span>

      <span v-if="tooltip !== null" class="absolute right-2">
        <span v-tooltip="tooltip">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 cursor-pointer text-gray-400 dark:text-gray-500" viewBox="0 0 20 20" fill="currentColor">
            <path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-8-3a1 1 0 00-.867.5 1 1 0 11-1.731-1A3 3 0 0113 8a3.001 3.001 0 01-2 2.83V11a1 1 0 11-2 0v-1a1 1 0 011-1 1 1 0 100-2zm0 8a1 1 0 100-2 1 1 0 000 2z" clip-rule="evenodd" />
          </svg>
        </span>
      </span>

        </component>
    </div>
</template>

<script>
import identity from 'lodash/identity'
import isNull from 'lodash/isNull'
import omitBy from 'lodash/omitBy'
import pickBy from 'lodash/pickBy'
import {mapGetters, mapMutations} from 'vuex'

export default {
    props: {
        item: {
            type: Object,
            required: true,
        },
    },

    methods: {
        ...mapMutations(['toggleMainMenu']),

        handleClick() {
            if (this.mainMenuShown) {
                this.toggleMainMenu()
            }
        },
    },

    computed: {
        ...mapGetters(['mainMenuShown']),

        requestMethod() {
            return this.item.method || 'GET'
        },

        component() {
            if (this.requestMethod !== 'GET') {
                return 'FormButton'
            } else if (this.item.external !== true) {
                return 'Link'
            }

            return 'a'
        },

        tooltip() {
            if (this.item.data)
                return this.item.data.tooltip;
            return null;
        },

        linkAttributes() {
            let method = this.requestMethod

            return pickBy(
                omitBy(
                    {
                        href: this.item.path,
                        method: method !== 'GET' ? method : null,
                        headers: this.item.headers || null,
                        data: this.item.data || null,
                        rel: this.component === 'a' ? 'noreferrer noopener' : null,
                        target: this.item.target || null,
                    },
                    isNull
                ),
                identity
            )
        },
    },
}
</script>
