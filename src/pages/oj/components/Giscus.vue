<template>
  <div id="gist"></div>
</template>

<script>
  /* eslint-disable no-useless-escape */
  import postscribe from 'postscribe'
  export default {
    name: 'Giscus',
    mounted: function () {
      postscribe('#gist', `<script src="https://utteranc.es/client.js"
        repo="nqduy91299/OnlineJudgeDiscuss"
        issue-term="url"
        label="OnlineJudgeDiscuss"
        theme="github-light"
        crossorigin="anonymous"
        async>
      <\/script>`)
    }
  }
</script>

<style scoped lang="less">
</style>
