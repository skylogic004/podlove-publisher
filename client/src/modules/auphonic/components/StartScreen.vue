<template>
  <div>
    <div class="m-6 text-center max-w-5xl">
      <AuphonicLogo className="mx-auto h-16 w-16 text-gray-400" />

      <div class="w-full flex justify-center" v-if="isInitializing">
        <div class="animate-pulse mt-4 flex space-x-4">
          <RefreshIcon class="animate-spin h-5 w-5 mr-3" />
          {{ __('Loading...', 'podlove-podcasting-plugin-for-wordpress') }}
        </div>
      </div>

      <div :class="{ 'text-left': true, 'opacity-0': isInitializing }">
        <p class="mt-1 text-sm text-gray-500">
          {{
            __(
              'Manage your audio post production with Auphonic. Get started by selecting an existing production or create a new one from an Auphonic preset.', 'podlove-podcasting-plugin-for-wordpress'
            )
          }}
        </p>
        <div
          class="sm:divide-x sm:divide-gray-200 mt-6 py-6 gap-8 sm:gap-0 grid grid-cols-1 sm:grid-cols-2"
        >
          <div class="flow-root sm:px-6">
            <div
              class="relative -m-2 p-2 flex items-center justify-around space-x-4 rounded-xl hover:bg-gray-50 focus-within:ring-2 focus-within:ring-indigo-500"
            >
              <div>
                <h3 class="text-sm font-medium text-gray-900">
                  {{ __('Create New Production from Preset', 'podlove-podcasting-plugin-for-wordpress') }}
                </h3>
              </div>
            </div>
            <div
              class="mt-2 sm:mt-8 flex justify-center align-middle content-center items-center gap-3"
            >
              <div class="w-full max-w-md">
                <SelectPreset />
              </div>
            </div>
            <div
              class="mt-10 flex flex-col justify-center align-middle content-center items-center gap-3"
            >
              <podlove-button
                :disabled="buttonState == 'idle'"
                :variant="buttonState == 'idle' ? 'primary-disabled' : 'primary'"
                @click="handleCreateProduction"
                ><plus-sm-icon class="-ml-0.5 mr-2 h-4 w-4" aria-hidden="true" />
                {{
                  buttonState == 'multi'
                    ? __('Create Multitrack Production', 'podlove-podcasting-plugin-for-wordpress')
                    : __('Create Production', 'podlove-podcasting-plugin-for-wordpress')
                }}</podlove-button
              >
            </div>
          </div>
          <div class="flow-root sm:px-6">
            <div
              class="relative -m-2 p-2 flex items-center justify-around space-x-4 rounded-xl hover:bg-gray-50 focus-within:ring-2 focus-within:ring-indigo-500"
            >
              <div>
                <h3 class="text-sm font-medium text-gray-900">
                  {{ __('Select Existing Production', 'podlove-podcasting-plugin-for-wordpress') }}
                </h3>
              </div>
            </div>
            <div
              class="mt-2 sm:mt-8 flex justify-center align-middle content-center items-center gap-3"
            >
              <div class="w-full max-w-md">
                <SelectProduction />
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import Module from '@components/module/Module.vue'
import PodloveButton from '@components/button/Button.vue'
import { PlusIcon as PlusSmIcon, ArrowPathIcon as RefreshIcon } from '@heroicons/vue/24/outline'
import { selectors } from '@store'

import { injectStore, mapState } from 'redux-vuex'
import * as auphonic from '@store/auphonic.store'
import SelectProduction from '../components/SelectProduction.vue'
import SelectPreset from '../components/SelectPreset.vue'
import AuphonicLogo from '../components/Logo.vue'

export default defineComponent({
  components: {
    Module,
    PodloveButton,
    PlusSmIcon,
    RefreshIcon,
    SelectProduction,
    SelectPreset,
    AuphonicLogo,
  },
  data() {
    return {}
  },
  setup() {
    return {
      state: mapState({
        presetUUID: selectors.auphonic.preset,
        presets: selectors.auphonic.presets,
        isInitializing: selectors.auphonic.isInitializing,
      }),
      dispatch: injectStore().dispatch,
    }
  },

  methods: {
    handleCreateProduction() {
      switch (this.buttonState) {
        case 'single':
          this.createProduction()
          break

        case 'multi':
          this.createMultitrackProduction()
          break

        case 'idle':
        // do nothing
        default:
          break
      }
    },
    createProduction() {
      this.dispatch(auphonic.createProduction())
    },
    createMultitrackProduction() {
      this.dispatch(auphonic.createMultitrackProduction())
    },
  },

  computed: {
    preset(): auphonic.Preset {
      return this.state.presets.find((p: auphonic.Preset) => p.uuid === this.state.presetUUID)
    },
    isInitializing(): boolean {
      return this.state.isInitializing
    },
    buttonState(): 'idle' | 'single' | 'multi' {
      if (!this.preset) {
        return 'idle'
      }

      return this.preset.is_multitrack ? 'multi' : 'single'
    },
  },
})
</script>
