<template>
    <div>
      <label for="episode-license-name" class="block text-sm font-medium text-gray-700">{{ __('License name', 'podlove-podcasting-plugin-for-wordpress') }}</label>
      <div class="mt-1">
        <input
          name="episode-license-name"
          type="text"
          :value="getLicenseName"
          @input="updateLicenseName"
          class="
            shadow-sm
            focus:ring-indigo-500 focus:border-indigo-500
            block
            w-full
            sm:text-sm
            border-gray-300
            rounded-md
          "
        />
      </div>
    </div>
  </template>

<script lang="ts">
import { PropType, defineComponent } from 'vue'
import { mapState, injectStore } from 'redux-vuex';

import { selectors } from '@store';

import Module from '@components/module/Module.vue'
import * as episode from '@store/episode.store'
import * as podcast from '@store/podcast.store'

import { PodloveLicenseScope } from '../../../types/license.types';

export default defineComponent({
  components: {
    Module,
  },
  props: {
    scope: {
      type: String as PropType<PodloveLicenseScope>,
      default: PodloveLicenseScope.Episode
    }
  },
  setup() {
    return {
      state: mapState({
        episodeLicenseName: selectors.episode.license_name,
        podcastLicenseName: selectors.podcast.license_name,
      }),
      dispatch: injectStore().dispatch,
    }
  },
  created() {
    this.dispatch(episode.init())
  },
  computed: {
    getLicenseName() : string {
      return this.scope == PodloveLicenseScope.Episode ? this.state.episodeLicenseName : this.state.podcastLicenseName
    }
  },
  methods: {
    updateLicenseName(event: Event) {
      if (this.scope === PodloveLicenseScope.Episode) {
        this.dispatch(
          episode.update({prop: 'license_name', value: (event.target as HTMLInputElement).value})
        )
      }
      if (this.scope === PodloveLicenseScope.Podcast) {
        this.dispatch(
          podcast.update({prop: 'license_name', value: (event.target as HTMLInputElement).value})
        )
      }
    }
  }
})
</script>



<style>
</style>
