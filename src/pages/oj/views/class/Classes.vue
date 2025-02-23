<template>
  <Panel shadow :padding="10">
    <template slot="title">
      <Breadcrumb>
        <div @click="backToClasses()" style="display: inline;">
          <BreadcrumbItem to="/class">{{$t('m.Classes')}}</BreadcrumbItem>
        </div>
        <BreadcrumbItem 
          v-if="selectedClassroom && selectedClassroom['id']">
          {{selectedClassroom.name}}
        </BreadcrumbItem>
      </Breadcrumb>
      <Button v-if="isAdminRole && !selectedClassroom" @click="openCreateModal()">
        + {{$t('m.New')}} {{$t('m.Classroom')}}
      </Button>
    </template>
    <div class="card-wrapper" 
      v-if="!(selectedClassroom && selectedClassroom['id'])">
      <ClassroomCard 
        v-for="classroom of classrooms" 
        :key="classroom.id" 
        :classroom="classroom" 
        @onClick="handleSelectedClassroom($event)"
        @onEdit="handleEditClassroom($event)"
        @onDelete="onDeleteClassroom($event)">
      </ClassroomCard>
    </div>
    <ClassPanel 
      v-else 
      @activeTabChange="handleActiveTabChange($event)" 
      :activeClassroom="selectedClassroom">
    </ClassPanel>
    <ClassroomModal 
      :visibleModal="classroomVisibleModal"
      :editedClassroomId="editedClassroomId"
      @visibleModalChange="handleVisibleModalChange($event)">
    </ClassroomModal>
  </Panel>
</template>

<script>
  import api from '@oj/api'
  import ClassPanel from './ClassPanel.vue'
  import ClassroomModal from './ClassroomModal.vue'
  import ClassroomCard from './ClassroomCard.vue'
  import { mapGetters } from 'vuex'
  export default {
    name: 'Classes',
    components: {
      ClassPanel,
      ClassroomModal,
      ClassroomCard
    },
    data () {
      return {
        classrooms: [],
        selectedClassroom: null,
        activeTab: '0',
        classroomVisibleModal: false,
        editedClassroomId: null
      }
    },
    mounted () {
      this.setupClassrooms()
      this.setupSelectedClassroomFromRouter()
    },
    methods: {
      setupSelectedClassroomFromRouter () {
        if (this.$router.history.current.name !== 'class-detail') {
          this.selectedClassroom = null
          return
        }
        this.setupClassroom(this.$router.history.current.params['id'])
      },
      handleSelectedClassroom (classId) {
        this.$router.push(`/class/${classId}/detail`)
        this.setupClassroom(classId)
      },
      handleActiveTabChange (event) {
        this.activeTab = event
      },
      backToClasses () {
        this.selectedClassroom = null
      },
      setupClassrooms () {
        api.getClassrooms().then(resp => {
          this.classrooms = resp.data.data
        })
      },
      setupClassroom (classId) {
        api.getClassroom(classId).then(resp => {
          this.selectedClassroom = resp.data.data
        })
      },
      handleCreateClass (event) {
        this.classroomVisibleModal = false
      },
      handleVisibleModalChange ({status, shouldUpdate}) {
        this.classroomVisibleModal = status
        if (!status) {
          this.editedClassroomId = null
        }
        if (shouldUpdate) {
          this.setupClassrooms()
        }
      },
      openCreateModal () {
        this.handleVisibleModalChange({status: true, shouldUpdate: false})
      },
      handleEditClassroom (event) {
        this.editedClassroomId = event
        this.handleVisibleModalChange({status: true, shouldUpdate: false})
      },
      onDeleteClassroom (id) {
        this.$Modal.confirm({
          content: this.$i18n.t('m.Delete_classroom_confirm'),
          onOk: () => {
            // still error here, not fix yet
            api.deleteClassroom(id).then(resp => {
              if (!resp.error) {
                this.$success(this.$i18n.t('m.Delete_successfully'))
                this.setupClassrooms()
              } else {
                this.$error(this.$i18n.t('m.Something_went_wrong'))
              }
            }).catch(err => {
              this.$error(this.$i18n.t('m.Something_went_wrong') + ' ', err)
            })
          }
        })
      }
    },
    computed: {
      ...mapGetters(['isAuthenticated', 'isAdminRole'])
    },
    watch: {
      '$route' (newValue, oldValue) {
        if (newValue.name !== oldValue.name) {
          this.setupSelectedClassroomFromRouter()
        }
      }
    }
  }
</script>

<style scoped lang="less">
  .card-wrapper {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin: 20px;
  }
</style>
