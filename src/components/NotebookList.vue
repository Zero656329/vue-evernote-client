<template>
  <div class="detail" id="notebook-list">
    <header><a href="#" class="btn" @click.stop.prevent="onCreate"><i class="iconfont icon-plus"></i>新建笔记本</a></header>
    <main>
      <div class="layout">
        <h3>笔记本列表{{notebooks.length}}</h3>
        <div class="book-List">
          <router-link v-for="notebook in notebooks" to="/note/1" class="notebook" :key="notebook.id">
            <div>
              <span class="iconfont icon-notebook"></span>{{notebook.title }}
              <span>{{notebook.userId}}</span>
              <span class="action" @click.stop.prevent="onEdit(notebook)">编辑</span>
              <span class="action" @click.stop.prevent="onDelete(notebook)">删除</span>
              <span class="date">{{notebook.friendlyCreatedAt}}</span>
            </div>

          </router-link>

        </div>
      </div>
    </main>
  </div>
</template>

<script>
  import Auth from "@/apis/auth"

  import Notebooks from "@/apis/notebooks"

  export default {
    data() {
      return {
        notebooks: []

      }
    },
    created() {
      Auth.getInfo().then(res => {
        if (!res.isLogin) {
          this.$router.push({path: '/login'})
        }
      })
      Notebooks.getAll().then(res => {

        this.notebooks = res.data

      })
    },
    methods: {
      onCreate() {
        let title = window.prompt('创建笔记本')
        if (title.trim() === '') {
          alert('笔记本名不能为空')
        }
        Notebooks.addNoteBook({title}).then(res => {
          console.log(res)
          alert(res.msg)
          this.notebooks.unshift(res.data)
        })
      },
      onDelete(notebook) {
        let isconfirm = window.confirm('你确定要删除吗？')
        if (isconfirm) {
          Notebooks.deleteNotebook(notebook.id).then(res=>{

            alert(res.msg)
            this.notebooks.splice(this.notebooks.indexOf(notebook),1)

          })
        }
      },

      onEdit(notebook) {
        let title = window.prompt('修改标题', notebook.title)
        Notebooks.updateNotebook(notebook.id, {title}).then(res => {
          alert(res.msg)
          notebook.title = title
        })
      }
    }
  }
</script>

<style lang="less" scoped>
  @import url(../assets/css/notebook-list.less);

</style>
