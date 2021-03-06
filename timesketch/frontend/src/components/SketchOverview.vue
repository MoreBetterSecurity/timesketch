<!--
Copyright 2019 Google Inc. All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->
<template>
  <div>

    <section class="section">
      <div class="container">
        <ts-navbar-secondary currentAppContext="sketch" currentPage="overview">
          <a class="button is-success is-rounded" style="margin-right:7px;" v-on:click="showUploadTimelineModal = !showUploadTimelineModal">
              <span class="icon is-small">
                <i class="fas fa-plus"></i>
              </span>
            <span>Timeline</span>
          </a>
          <a class="button is-link is-rounded" style="margin-right:10px;">
              <span class="icon is-small">
                <i class="fas fa-users"></i>
              </span>
            <span>Share</span>
          </a>
          <div class="dropdown is-hoverable is-right" v-bind:class="{'is-active': settingsDropdownActive}">
            <div class="dropdown-trigger">
              <a class="button" style="background:transparent;border:none;" aria-haspopup="true" aria-controls="dropdown-menu" v-on:click="settingsDropdownActive = !settingsDropdownActive">
                <span>More</span>
                <span class="icon is-small">
                <i class="fas fa-angle-down" aria-hidden="true"></i>
              </span>
              </a>
            </div>
            <div class="dropdown-menu" id="dropdown-menu" role="menu">
              <div class="dropdown-content">
                <a v-if="meta.permissions.delete" class="dropdown-item" v-on:click="showDeleteSketchModal = !showDeleteSketchModal">
                  <span class="icon is-small" style="margin-right:5px;"><i class="fas fa-trash"></i></span>
                  <span>Delete</span>
                </a>
              </div>
            </div>
          </div>
        </ts-navbar-secondary>
      </div>
    </section>

    <div class="modal" v-bind:class="{ 'is-active': showUploadTimelineModal }">
      <div class="modal-background"></div>
      <div class="modal-content">
        <div class="card">
          <header class="card-header">
            <p class="card-header-title">Upload new timeline</p>
          </header>
          <div class="card-content">
            <div class="content">
              <ts-upload-timeline-form @toggleModal="showUploadTimelineModal = !showUploadTimelineModal"></ts-upload-timeline-form>
            </div>
          </div>
        </div>
      </div>
      <button class="modal-close is-large" aria-label="close" v-on:click="showUploadTimelineModal = !showUploadTimelineModal"></button>
    </div>

    <div class="modal" v-bind:class="{ 'is-active': showDeleteSketchModal }">
      <div class="modal-background"></div>
      <div class="modal-content">
        <div class="card">
          <header class="card-header">
            <p class="card-header-title">Delete sketch</p>
          </header>
          <div class="card-content">
            <div class="content">
              <p>Are you sure you want to delete this sketch?</p>
              <div class="field is-grouped">
                <p class="control">
                  <button class="button is-danger" v-on:click="deleteSketch">
                    <span class="icon is-small" style="margin-right:5px;"><i class="fas fa-trash"></i></span>
                    <span>Delete</span>
                  </button>
                </p>
                <p class="control">
                  <button class="button" v-on:click="showDeleteSketchModal = !showDeleteSketchModal">
                    <span>I changed my mind, keep the sketch!</span>
                  </button>
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <button class="modal-close is-large" aria-label="close" v-on:click="showDeleteSketchModal = !showDeleteSketchModal"></button>
    </div>

    <!-- Title and description -->
    <section class="section">
      <div class="container">
        <div class="card" style="min-height: 200px;">
          <div class="card-content">
            <ts-sketch-summary :sketch="sketch"></ts-sketch-summary>
          </div>
        </div>
      </div>
    </section>

    <!-- Stats -->
    <section class="section">
      <div class="container">
        <div class="card" style="min-height: 100px;">
          <div class="card-content">
            <ts-sketch-metrics :timelines="sketch.active_timelines" :views="meta.views" :stories="sketch.stories" :count="count"></ts-sketch-metrics>
          </div>
        </div>
      </div>
    </section>

    <!-- Timeline and View lists-->
    <section class="section">
      <div class="container">
        <div class="columns">
          <div class="column" v-if="sketch.timelines && sketch.timelines.length ? sketch.timelines.length: false">
            <div class="card has-min-height">
              <header class="card-header">
                <p class="card-header-title">Timelines</p>
              </header>
              <div class="card-content" style="padding:5px;">
                <ts-timeline-list :timelines="sketch.timelines"></ts-timeline-list>
              </div>
            </div>
          </div>
          <div class="column" v-if="meta.views && meta.views.length ? meta.views.length: false">
            <div class="card has-min-height">
              <header class="card-header">
                <p class="card-header-title">Views</p>
              </header>
              <div class="card-content" style="padding:10px;">
                <ts-saved-view-list :views="meta.views"></ts-saved-view-list>
              </div>
            </div>
          </div>
          <div class="column" v-if="sketch.stories && sketch.stories.length ? sketch.stories.length: false">
            <div class="card has-min-height">
              <header class="card-header">
                <p class="card-header-title">Stories</p>
              </header>
              <div class="card-content" style="padding:5px;">
                <ts-sketch-story-list></ts-sketch-story-list>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

  </div>
</template>

<script>
import ApiClient from '../utils/RestApiClient'
import TsSketchSummary from './SketchOverviewSummary'
import TsSketchMetrics from './SketchOverviewMetrics'
import TsTimelineList from './SketchOverviewTimelineList'
import TsSavedViewList from './SketchOverviewViewList'
import TsSketchStoryList from './SketchStoryList'
import TsUploadTimelineForm from './SketchUploadTimelineForm'

export default {
  name: 'ts-sketch-overview',
  components: {
    TsSketchMetrics,
    TsSketchSummary,
    TsTimelineList,
    TsSavedViewList,
    TsUploadTimelineForm,
    TsSketchStoryList
  },
  data () {
    return {
      settingsDropdownActive: false,
      showUploadTimelineModal: false,
      showDeleteSketchModal: false
    }
  },
  computed: {
    sketch () {
      return this.$store.state.sketch
    },
    meta () {
      return this.$store.state.meta
    },
    count () {
      return this.$store.state.count
    }
  },
  methods: {
    deleteSketch: function () {
      ApiClient.deleteSketch(this.sketch.id).then((response) => {
        this.$router.push({ name: 'Home' })
      }).catch((e) => {
        console.error(e)
      })
    }
  }

}
</script>

<style lang="scss">
  .has-min-height {
    min-height: 300px;
  }
</style>
