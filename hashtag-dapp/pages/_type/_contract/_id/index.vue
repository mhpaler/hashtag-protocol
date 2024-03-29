<template>
  <div>
    <section class="main" v-if="isLoaded">
      <div class="container">
        <h1 class="title is-1">{{ nftInfo.nftName }}</h1>
        <h2 class="subtitle">
          ERC-721 Digital asset
          <span class="is-pulled-right is-size-6 has-text-weight-bold">
            <nuxt-link :to="{ name: 'nfts' }">Browse tagged assets</nuxt-link>&nbsp;
            <b-icon icon="arrow-up" type="is-dark" size="is-small"></b-icon>
          </span>
        </h2>
        <div class="tile is-ancestor">
          <div class="tile is-horizontal">
            <div class="tile is-parent is-6 is-12-mobile">
              <div class="card">
                <div class="card-image">
                  <figure class="image">
                    <video
                      v-if="nftInfo.nftImage.includes('mp4')"
                      autoplay=""
                      controlslist="nodownload"
                      loop=""
                      playsinline=""
                      poster=""
                      preload="metadata"
                      class=""
                    >
                      <source :src="nftInfo.nftImage" @error="setPendingImage" type="video/mp4" />
                    </video>
                    <img v-else :src="nftInfo.nftImage" @error="setPendingImage" :alt="nftInfo.nftName" />
                  </figure>
                </div>
                <div class="card-content">
                  <h2 class="title is-4">Asset information</h2>
                  <div class="b-table">
                    <div class="table-wrapper">
                      <table class="table">
                        <tbody>
                          <tr draggable="false" class="">
                            <td class="has-text-weight-bold">Name</td>
                            <td>
                              {{ nftInfo.nftName }}
                            </td>
                          </tr>
                          <tr draggable="false" class="">
                            <td class="has-text-weight-bold">Chain</td>
                            <td>
                              {{ nftInfo.chainName }}
                            </td>
                          </tr>
                          <tr draggable="false" class="">
                            <td class="has-text-weight-bold">Asset Id</td>
                            <td style="word-break: break-word">
                              <NftId
                                :name="nftInfo.nftName"
                                :chain="nftInfo.chain"
                                :contract="nftInfo.nftContract"
                                :value="nftInfo.nftId"
                              ></NftId>
                              <a
                                target="_blank"
                                :href="`https://opensea.io/assets/${nftInfo.nftContract}/${nftInfo.nftId}`"
                              >
                                View on OpenSea
                              </a>
                            </td>
                          </tr>
                        </tbody>
                      </table>
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <div class="tile is-parent is-6 is-12-mobile">
              <article class="tile is-child box">
                <p class="title is-5">Tag this asset</p>
                <form>
                  <div class="field">
                    <div class="control">
                      <b-taginput
                        v-model="hashtag"
                        :data="hashtagInputTags"
                        autocomplete
                        :allow-new="true"
                        maxtags="1"
                        :has-counter="false"
                        field="name"
                        ref="tagginginput"
                        icon="pound"
                        placeholder="Seach for hashtag"
                        @typing="getFilteredTags"
                        :before-adding="validateTag"
                      >
                        <template slot-scope="props">
                          <b-taglist attached>
                            <b-tag type="is-primary" size="is-medium">{{ props.option.displayHashtag }}</b-tag>
                            <b-tag type="is-dark" size="is-medium">{{ props.option.tagCount }}</b-tag>
                          </b-taglist>
                        </template>
                        <template slot="empty"> New hashtag! Press enter to continue... </template>
                        <template slot="selected" slot-scope="props">
                          <div v-bind:class="{ box: isTaggable }">
                            <b-tag
                              v-for="(tag, index) in props.tags"
                              :key="index"
                              :tabstop="false"
                              ellipsis
                              attached
                              type="is-primary"
                              size="is-medium"
                              closable
                              close-type="is-dark"
                              @close="$refs.tagginginput.removeTag(index, $event)"
                            >
                              <div v-if="tag.displayHashtag">
                                {{ tag.displayHashtag }}
                              </div>
                              <div v-else>#{{ tag }}</div>
                            </b-tag>
                            <div class="field">
                              <div class="control">
                                <b-button
                                  type="is-primary"
                                  class="is-outlined"
                                  @click="tagNft()"
                                  :disabled="!isTaggable"
                                  v-bind:class="{
                                    'is-hidden': !isTaggable,
                                  }"
                                  >Tag asset
                                </b-button>
                              </div>
                            </div>
                          </div>
                        </template>
                      </b-taginput>
                    </div>
                  </div>
                </form>
                <hr />
                <h2 class="title is-5">Recent tags</h2>
                <div class="b-table">
                  <div class="table-wrapper has-mobile-cards">
                    <table tabindex="0" class="table is-hoverable">
                      <thead>
                        <tr>
                          <th class="">
                            <div class="th-wrap">Hashtag</div>
                          </th>
                          <th class="">
                            <div class="th-wrap">Tagged</div>
                          </th>
                          <th class="">
                            <div class="th-wrap">Tagger</div>
                          </th>
                          <th class="">
                            <div class="th-wrap">Publisher</div>
                          </th>
                        </tr>
                      </thead>
                      <tbody>
                        <tr draggable="false" v-for="tag in tagsByDigitalAsset" v-bind:key="tag.id">
                          <td data-label="Hashtag" class="">
                            <span class="has-text-weight-bold">
                              <hashtag :value="tag.hashtagDisplayHashtag"></hashtag>
                            </span>
                          </td>
                          <td data-label="Tagged" class="">
                            <timestamp-from :value="tag.timestamp"></timestamp-from>
                          </td>
                          <td data-label="Owner" class="">
                            <eth-account :value="tag.tagger" route="tagger-address"></eth-account>
                          </td>
                          <td data-label="Publisher" class="">
                            <eth-account :value="tag.publisher" route="publisher-address"></eth-account>
                          </td>
                        </tr>
                      </tbody>
                    </table>
                  </div>
                </div>
              </article>
            </div>
          </div>
        </div>
      </div>
    </section>
    <section v-else>Loading...</section>
  </div>
</template>

<script>
import EthAccount from "~/components/EthAccount";
import Hashtag from "~/components/Hashtag";
import TimestampFrom from "~/components/TimestampFrom";
import NftId from "~/components/NftId";
import { TAGS_BY_DIGITAL_ASSET, FIRST_THOUSAND_HASHTAGS } from "~/apollo/queries";

import HashtagValidationService from "~/services/HashtagValidationService";
import axios from "axios";

export default {
  name: "NftDetail",
  components: {
    EthAccount,
    Hashtag,
    TimestampFrom,
    NftId,
  },
  data() {
    return {
      isLoaded: false,
      tagsByHashtag: null,
      hashtagsByName: null,
      hashtag: [],
      hashtags: null,
      hashtagInputTags: [],
      nftInfo: null,
    };
  },
  mounted() {
    this.pullNFTFromAPI();
  },
  asyncData({ params }) {
    return {
      name: params.name,
      type: params.type,
      contract: params.contract,
      id: params.id,
    };
  },
  computed: {
    isTaggable() {
      return this.hashtag.length;
    },
  },
  apollo: {
    tagsByDigitalAsset: {
      query: TAGS_BY_DIGITAL_ASSET,
      variables() {
        return {
          nftContract: this.contract,
          nftId: this.id,
        };
      },
      pollInterval: 1000, // ms
    },
    hashtags: {
      query: FIRST_THOUSAND_HASHTAGS,
      pollInterval: 1000, // ms
    },
  },
  methods: {
    async tagNft() {
      const hashtag = this.hashtag[0];
      let hashtagValue = hashtag && hashtag.name ? hashtag.name : hashtag;

      if (hashtagValue.charAt(0) !== "#") {
        hashtagValue = `#${hashtagValue}`;
      }

      await this.$store.dispatch("wallet/tag", {
        hashtag: hashtagValue,
        nftContract: this.nftInfo.nftContract,
        nftId: this.nftInfo.nftId,
        nftChain: this.nftInfo.chain,
      });

      // Reset the tagging input
      this.hashtag = [];
    },
    // Bulma taginput widget.
    getFilteredTags: function (text) {
      const hashtags = this.hashtags || [];
      this.hashtagInputTags = hashtags.filter((tag) => `${tag.name.toLowerCase()}`.indexOf(text.toLowerCase()) === 1);
    },
    pullNFTFromAPI: async function () {
      let nftData = await this.$apollo.queries.tagsByDigitalAsset.refetch();
      nftData = nftData.data.tagsByDigitalAsset[0];
      let chain = "";
      if (nftData.nftChainId === "1") {
        chain = "ethereum";
      } else if (nftData.nftChainId === "137") {
        chain = "polygon";
      }
      const headers = {
        Authorization: this.$config.nftPortAPIKey,
      };
      axios
        .get("https://api.nftport.xyz/v0/nfts/" + nftData.nftContract + "/" + nftData.nftId, {
          params: {
            chain: chain,
            page_number: 1,
            page_size: 50,
          },
          headers: headers,
        })
        .then((response) => {
          let nftHold = {};
          nftHold["nftName"] = response.data.nft.metadata.name;
          let res = response.data.nft.cached_file_url.split("//");
          if (res[0] == "ipfs:") {
            nftHold["nftImage"] = "https://ipfs.io/" + res[1];
          } else {
            nftHold["nftImage"] = response.data.nft.cached_file_url;
          }
          nftHold["nftId"] = response.data.nft.token_id;
          nftHold["nftDescription"] = response.data.nft.metadata.description;
          nftHold["chain"] = nftData.nftChainId;
          nftHold["chainName"] = response.config.params.chain;
          nftHold["nftContract"] = nftData.nftContract;
          //nftHold["nftContractName"] = response.data.nft.contract;
          //nftHold["nftContractSymbol"] = response.data.nft.contract.symbol;
          //nftHold["nftContractType"] = response.data.nft.contract.type;
          this.nftInfo = nftHold;
          this.isLoaded = true;
        });
    },
    validateTag(hashtag) {
      return this.hashtagValidationService.validateTag(hashtag);
    },
  },
  created() {
    this.hashtagValidationService = new HashtagValidationService(this.$buefy.toast);
  },
};
</script>

<style lang="scss"></style>
