<template>
  <div class="edit-page">
    <div v-if="editLink">
      <b-button
        tag="a"
        target="_blank"
        rel="noopener noreferrer"
        class="button"
        :href="editLink"
        icon-left="github-circle"
      >
        {{ editLinkText }}
      </b-button>
    </div>
    <br />
    <div v-if="lastUpdated" class="is-size-7">
      <span class="prefix">{{ lastUpdatedText }}:</span>
      <br />
      <span class="time">{{ lastUpdated }}</span>
    </div>
  </div>
</template>

<script>
import isNil from "lodash/isNil";
import {
  endingSlashRE,
  outboundRE,
} from "@vuepress/theme-default/util/index.js";

export default {
  name: "PageEdit",

  computed: {
    lastUpdated() {
      return this.$page.lastUpdated;
    },

    lastUpdatedText() {
      if (typeof this.$themeLocaleConfig.lastUpdated === "string") {
        return this.$themeLocaleConfig.lastUpdated;
      }
      if (typeof this.$site.themeConfig.lastUpdated === "string") {
        return this.$site.themeConfig.lastUpdated;
      }
      return "Last Updated";
    },

    editLink() {
      const showEditLink = isNil(this.$page.frontmatter.editLink)
        ? this.$site.themeConfig.editLinks
        : this.$page.frontmatter.editLink;

      const {
        repo,
        docsDir = "",
        docsBranch = "master",
        docsRepo = repo,
      } = this.$site.themeConfig;

      if (showEditLink && docsRepo && this.$page.relativePath) {
        return this.createEditLink(
          repo,
          docsRepo,
          docsDir,
          docsBranch,
          this.$page.relativePath
        );
      }
      return null;
    },

    editLinkText() {
      return (
        this.$themeLocaleConfig.editLinkText ||
        this.$site.themeConfig.editLinkText ||
        `Edit this page`
      );
    },
  },

  methods: {
    createEditLink(repo, docsRepo, docsDir, docsBranch, path) {
      const bitbucket = /bitbucket.org/;
      if (bitbucket.test(docsRepo)) {
        const base = docsRepo;
        return (
          base.replace(endingSlashRE, "") +
          `/src` +
          `/${docsBranch}/` +
          (docsDir ? docsDir.replace(endingSlashRE, "") + "/" : "") +
          path +
          `?mode=edit&spa=0&at=${docsBranch}&fileviewer=file-view-default`
        );
      }

      const gitlab = /gitlab.com/;
      if (gitlab.test(docsRepo)) {
        const base = docsRepo;
        return (
          base.replace(endingSlashRE, "") +
          `/-/edit` +
          `/${docsBranch}/` +
          (docsDir ? docsDir.replace(endingSlashRE, "") + "/" : "") +
          path
        );
      }

      const base = outboundRE.test(docsRepo)
        ? docsRepo
        : `https://github.com/${docsRepo}`;
      return (
        base.replace(endingSlashRE, "") +
        "/edit" +
        `/${docsBranch}/` +
        (docsDir ? docsDir.replace(endingSlashRE, "") + "/" : "") +
        path
      );
    },
  },
};
</script>

<style lang="scss"></style>
