<template>
  <div class="owner-container">
    <img src="../../assets/ic-git.svg" alt="Github logo" class="gh-logo">
    <a class="owner-name" :href="`https://github.com/${getOwner}`" v-if="!getOwnerGenerated">
      {{
      reduce(15, getOwner)
      }}
    </a>
    <span class="owner-name" v-else>{{ reduce(15, getOwner) }}</span>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'

export default {
  name: 'owner',
  data: () => ({
    url: ''
  }),
  computed: mapGetters(['getOwner', 'getOwnerGenerated']),
  mounted () {},
  methods: {
    reduce (size, str) {
      return str.length > size ? `${str.substr(0, size)}...` : str
    }
  }
}
</script>

<style lang="scss" scoped>
.owner-container {
  display: flex;
  justify-content: flex-start;
  align-items: center;

  .gh-logo {
    margin-right: 8px;
    width: 17px;
    height: 17px;
  }

  a {
    height: 21px;
    color: #565872;
    font-family: Graphik;
    font-size: 14px;
    font-weight: 500;
    line-height: 21px;
    text-decoration: none;

    &:hover {
      text-decoration: underline;
    }
  }
}
</style>
