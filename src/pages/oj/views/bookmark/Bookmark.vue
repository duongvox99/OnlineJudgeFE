<template>
  <Row type="flex" :gutter="24">
    <Col :span=24>
      <Panel shadow>
        <div slot="title">{{$t('m.Bookmarks_List')}}</div>
        <div slot="extra">
          <Input v-model="textSearch"
            @input="filterByKeyword()"
            @on-enter="filterByKeyword()"
            :placeholder="$t('m.Keyword')"
            icon="ios-search-strong"/>
        </div>
        <Table style="width: 100%; font-size: 16px;"
          :columns="problemTableColumns"
          :data="problemList"
          :loading="loadings"
          disabled-hover>
        </Table>
      </Panel>
    </Col>
  </Row>
</template>

<script>
  import api from '@oj/api'
  export default {
    name: 'Bookmark',
    data () {
      return {
        textSearch: '',
        problemTableColumns: [
          {
            title: '#',
            key: '_id',
            width: 80,
            render: (h, params) => {
              return h('Button', {
                props: {
                  type: 'text',
                  size: 'large'
                },
                on: {
                  click: () => {
                    this.$router.push({name: 'problem-details', params: {problemID: params.row._id}})
                  }
                },
                style: {
                  padding: '2px 0'
                }
              }, params.row._id)
            }
          },
          {
            title: this.$i18n.t('m.Title'),
            width: 400,
            render: (h, params) => {
              return h('Button', {
                props: {
                  type: 'text',
                  size: 'large'
                },
                on: {
                  click: () => {
                    this.$router.push({name: 'problem-details', params: {problemID: params.row._id}})
                  }
                },
                style: {
                  padding: '2px 0',
                  overflowX: 'auto',
                  textAlign: 'left',
                  width: '100%'
                }
              }, params.row.title)
            }
          },
          {
            title: this.$i18n.t('m.Level'),
            width: 100,
            render: (h, params) => {
              let t = params.row.difficulty
              let color = 'blue'
              if (t === 'Low') color = 'green'
              else if (t === 'High') color = 'yellow'
              return h('Tag', {
                props: {
                  color: color
                }
              }, this.$i18n.t('m.' + params.row.difficulty))
            }
          },
          {
            title: this.$i18n.t('m.Option'),
            width: '10%',
            textAlign: 'center',
            render: (h, params) => {
              return h('div', [
                h('Button', {
                  props: {
                    shape: 'circle',
                    type: 'ghost',
                    icon: 'heart'
                  },
                  on: {
                    click: () => {
                      this.unBookmarkProblem(params.row.problem_id)
                    }
                  },
                  style: {
                    padding: '2px 0',
                    textAlign: 'center',
                    maxWidth: '40px',
                    color: 'red'
                  }
                })
              ])
            }
          }
        ],
        problemList: [],
        loadings: true,
        timeoutId: null
      }
    },
    mounted () {
      this.getBookmarks()
    },
    methods: {
      getBookmarks () {
        this.loadings = true
        api.getBookmarks(this.textSearch).then(resp => {
          this.loadings = false
          if (resp.data.data && resp.data.data.length) {
            this.problemList = resp.data.data
          }
        }, resp => {
          this.loadings = false
        })
      },
      unBookmarkProblem (problemId) {
        api.unBookmarkProblem({problem_ids: [problemId]}).then(resp => {
          if (!resp.error) {
            this.getBookmarks()
            this.$success(this.$i18n.t('m.Un_bookmark_successfully'))
          }
        })
      },
      filterByKeyword () {
        clearTimeout(this.timeoutId)
        this.timeoutId = setTimeout(() => {
          this.getBookmarks()
        }, 700)
      }
    }
  }
</script>
<style lang="less" scoped>
</style>
