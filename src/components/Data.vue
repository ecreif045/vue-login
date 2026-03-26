<script setup>
import { ref, onMounted, watch } from 'vue'

import Vue3Datatable from '@bhplugin/vue3-datatable'
import '@bhplugin/vue3-datatable/dist/style.css'

const activeTab = ref('posts')
const posts = ref([])
const users = ref([])
const comments = ref([])
const loading = ref(false)
const error = ref(null)

const selectedPost = ref(null)
const postComments = ref([])
const modalLoading = ref(false)

const postColumns = ref([
  {
    field: 'id',
    title: 'ID',
    width: '60px',
    type: 'number',
  },
  {
    field: 'title',
    title: 'Title',
    width: '260px',
    cellRenderer: (row) => `
      <span style="font-weight:600;text-transform:capitalize;line-height:1.4;display:block">
        ${row.title}
      </span>`,
  },
  {
    field: 'body',
    title: 'Preview',
    cellRenderer: (row) => `
      <span style="color:#6b7280;font-size:13px">
        ${row.body.slice(0, 80)}...
      </span>`,
  },
  {
    field: 'userId',
    title: 'User',
    width: '90px',
    cellRenderer: (row) => `
      <span style="background:#f0f0ff;color:#6366f1;padding:3px 10px;border-radius:20px;font-size:11px;font-family:monospace">
        User ${row.userId}
      </span>`,
  },
  {
    field: 'action',
    title: 'Comments',
    width: '110px',
    cellRenderer: (row) => `
      <button
        class="view-btn"
        data-id="${row.id}"
        data-title="${row.title.replace(/"/g, '&quot;')}"
        data-body="${row.body.replace(/"/g, '&quot;')}"
        data-userid="${row.userId}"
      >View →</button>`,
  },
])

const userColumns = ref([
  { field: 'id',           title: 'ID',       width: '60px',  type: 'number' },
  { field: 'name',         title: 'Name',     width: '160px'                 },
  { field: 'username',     title: 'Username', width: '130px'                 },
  { field: 'email',        title: 'Email',    width: '210px'                 },
  { field: 'phone',        title: 'Phone',    width: '170px'                 },
  { field: 'company.name', title: 'Company',  width: '160px'                 },
  { field: 'address.city', title: 'City',     width: '120px'                 },
])

const commentColumns = ref([
  {
    field: 'id',
    title: 'ID',
    width: '60px',
    type: 'number',
  },
  {
    field: 'postId',
    title: 'Post',
    width: '80px',
    cellRenderer: (row) => `
      <span style="background:#f0f0ff;color:#6366f1;padding:3px 10px;border-radius:20px;font-size:11px;font-family:monospace">
        #${row.postId}
      </span>`,
  },
  {
    field: 'email',
    title: 'From',
    width: '220px',
    cellRenderer: (row) => {
      const colors = ['#6366f1','#8b5cf6','#ec4899','#f59e0b','#10b981','#3b82f6']
      const color = colors[row.email.charCodeAt(0) % colors.length]
      return `
        <div style="display:flex;align-items:center;gap:8px">
          <div style="width:26px;height:26px;border-radius:50%;background:${color};color:white;display:flex;align-items:center;justify-content:center;font-size:11px;font-weight:700;flex-shrink:0">
            ${row.email.charAt(0).toUpperCase()}
          </div>
          <span style="font-size:13px">${row.email}</span>
        </div>`
    },
  },
  {
    field: 'name',
    title: 'Subject',
    width: '200px',
    cellRenderer: (row) => `
      <span style="font-size:13px;font-weight:600;text-transform:capitalize">
        ${row.name.slice(0, 45)}${row.name.length > 45 ? '...' : ''}
      </span>`,
  },
  {
    field: 'body',
    title: 'Comment',
    cellRenderer: (row) => `
      <span style="color:#6b7280;font-size:13px">
        ${row.body.slice(0, 80)}...
      </span>`,
  },
])

async function fetchPosts() {
  loading.value = true; error.value = null
  try {
    const res = await fetch('https://jsonplaceholder.typicode.com/posts')
    posts.value = await res.json()
  } catch (err) {
    error.value = 'Failed to load posts: ' + err.message
  } finally { loading.value = false }
}

async function fetchUsers() {
  loading.value = true; error.value = null
  try {
    const res = await fetch('https://jsonplaceholder.typicode.com/users')
    users.value = await res.json()
  } catch (err) {
    error.value = 'Failed to load users: ' + err.message
  } finally { loading.value = false }
}

async function fetchComments() {
  loading.value = true; error.value = null
  try {
    const res = await fetch('https://jsonplaceholder.typicode.com/comments')
    comments.value = await res.json()
  } catch (err) {
    error.value = 'Failed to load comments: ' + err.message
  } finally { loading.value = false }
}

function handleTableClick(e) {
  const btn = e.target.closest('.view-btn')
  if (!btn) return
  openPostDetail({
    id:     parseInt(btn.dataset.id),
    title:  btn.dataset.title,
    body:   btn.dataset.body,
    userId: btn.dataset.userid,
  })
}

async function openPostDetail(post) {
  selectedPost.value = post
  modalLoading.value = true
  postComments.value = []
  try {
    const res = await fetch(`https://jsonplaceholder.typicode.com/comments?postId=${post.id}`)
    postComments.value = await res.json()
  } catch { postComments.value = [] }
  finally { modalLoading.value = false }
}

function closeModal() {
  selectedPost.value = null
  postComments.value = []
}

watch(activeTab, (tab) => {
  if (tab === 'posts'    && posts.value.length    === 0) fetchPosts()
  if (tab === 'users'    && users.value.length    === 0) fetchUsers()
  if (tab === 'comments' && comments.value.length === 0) fetchComments()
})

onMounted(() => fetchPosts())
</script>

<template>
  <div class="explorer-page">

    <div class="page-header">
      <h1 class="page-title">Data Explorer</h1>
    </div>

    <div class="tab-bar">
      <button
        v-for="tab in ['posts', 'users', 'comments']"
        :key="tab"
        :class="['tab-btn', { active: activeTab === tab }]"
        @click="activeTab = tab"
      >

        {{ tab.charAt(0).toUpperCase() + tab.slice(1) }}
        <span class="count" v-if="
          (tab==='posts' && posts.length) ||
          (tab==='users' && users.length) ||
          (tab==='comments' && comments.length)
        ">
          {{ tab==='posts' ? posts.length : tab==='users' ? users.length : comments.length }}
        </span>
      </button>
    </div>

    <div v-if="loading" class="loading-state">
      <div class="spinner"></div>
      <p>Fetching data...</p>
    </div>
    <div v-else-if="error" class="error-state">⚠️ {{ error }}</div>

    <div v-else-if="activeTab === 'posts'" @click="handleTableClick">
      <vue3-datatable
        :rows="posts"
        :columns="postColumns"
        :loading="loading"
        skin="bh-table-hover bh-table-bordered"
        noDataContent="No posts found"
      />
    </div>

    <div v-else-if="activeTab === 'users'">
      <vue3-datatable
        :rows="users"
        :columns="userColumns"
        :loading="loading"
        skin="bh-table-hover bh-table-bordered"
        noDataContent="No users found"
      />
    </div>

    <div v-else-if="activeTab === 'comments'">
      <vue3-datatable
        :rows="comments"
        :columns="commentColumns"
        :loading="loading"
        skin="bh-table-hover bh-table-bordered"
        noDataContent="No comments found"
      />
    </div>

    <div v-if="selectedPost" class="modal-overlay" @click.self="closeModal">
      <div class="modal">
        <button class="modal-close" @click="closeModal">✕</button>
        <div class="modal-post-id">Post #{{ selectedPost.id }}</div>
        <h2 class="modal-title">{{ selectedPost.title }}</h2>
        <p class="modal-body">{{ selectedPost.body }}</p>
        <div class="modal-divider"></div>
        <h3 class="modal-section-title">
          💬 Comments
          <span v-if="!modalLoading">({{ postComments.length }})</span>
        </h3>
        <div v-if="modalLoading" class="mini-loader">
          <div class="spinner small"></div> Loading...
        </div>
        <div v-else class="modal-comments">
          <div v-for="c in postComments" :key="c.id" class="modal-comment">
            <div class="mc-email">{{ c.email }}</div>
            <div class="mc-name">{{ c.name }}</div>
            <p class="mc-body">{{ c.body }}</p>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Syne:wght@400;700;800&family=JetBrains+Mono&family=Nunito:wght@400;500;600&display=swap');

.explorer-page { font-family: 'Nunito', sans-serif; padding: 30px 20px; max-width: 1200px; margin: 0 auto; }

.page-header { margin-bottom: 28px; }
.page-title { font-family: 'Syne', sans-serif; font-size: 32px; font-weight: 800; color: #1a1a2e; margin-bottom: 4px; }

.tab-bar { display: flex; gap: 8px; margin-bottom: 20px; flex-wrap: wrap; }
.tab-btn { display: flex; align-items: center; gap: 8px; padding: 10px 20px; border-radius: 10px; border: 2px solid #e5e7eb; background: white; font-family: 'Syne', sans-serif; font-size: 14px; font-weight: 600; color: #6b7280; cursor: pointer; transition: all 0.2s; }
.tab-btn:hover { border-color: #6366f1; color: #6366f1; }
.tab-btn.active { background: #6366f1; border-color: #6366f1; color: white; }
.count { background: rgba(255,255,255,0.25); border-radius: 20px; padding: 1px 8px; font-size: 11px; font-family: 'JetBrains Mono', monospace; }
.tab-btn:not(.active) .count { background: #f3f4f6; color: #374151; }

.loading-state, .error-state { text-align: center; padding: 60px 20px; color: #6b7280; }
.spinner { width: 40px; height: 40px; border: 3px solid #e5e7eb; border-top-color: #6366f1; border-radius: 50%; animation: spin 0.8s linear infinite; margin: 0 auto 16px; }
.spinner.small { width: 18px; height: 18px; border-width: 2px; margin: 0; display: inline-block; vertical-align: middle; }
@keyframes spin { to { transform: rotate(360deg); } }

:deep(.bh-datatable table thead th) {
  background: #1a1a2e !important;
  color: #9ca3af !important;
  font-family: 'Syne', sans-serif;
  font-size: 11px;
  letter-spacing: 0.06em;
  text-transform: uppercase;
}
:deep(.bh-datatable) { font-family: 'Nunito', sans-serif; font-size: 14px; }

:deep(.view-btn) {
  background: #6366f1; color: white; border: none;
  padding: 5px 12px; border-radius: 6px; cursor: pointer;
  font-size: 12px; font-family: 'Nunito', sans-serif; transition: background 0.2s;
}
:deep(.view-btn:hover) { background: #4f46e5; }

.modal-overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.5); display: flex; align-items: center; justify-content: center; z-index: 2000; padding: 20px; }
.modal { background: white; border-radius: 16px; padding: 32px; max-width: 600px; width: 100%; max-height: 80vh; overflow-y: auto; position: relative; }
.modal-close { position: absolute; top: 16px; right: 16px; background: #f3f4f6; border: none; width: 32px; height: 32px; border-radius: 50%; cursor: pointer; font-size: 14px; }
.modal-post-id { font-family: 'JetBrains Mono', monospace; font-size: 12px; color: #9ca3af; margin-bottom: 8px; }
.modal-title { font-family: 'Syne', sans-serif; font-size: 20px; font-weight: 800; color: #1a1a2e; margin-bottom: 12px; text-transform: capitalize; line-height: 1.4; }
.modal-body { font-size: 14px; color: #6b7280; line-height: 1.8; }
.modal-divider { border: none; border-top: 2px solid #f3f4f6; margin: 24px 0; }
.modal-section-title { font-family: 'Syne', sans-serif; font-size: 16px; font-weight: 700; margin-bottom: 16px; color: #1a1a2e; }
.mini-loader { display: flex; align-items: center; gap: 10px; color: #9ca3af; font-size: 13px; padding: 10px 0; }
.modal-comments { display: flex; flex-direction: column; gap: 14px; }
.modal-comment { padding: 14px; background: #f9fafb; border-radius: 8px; }
.mc-email { font-family: 'JetBrains Mono', monospace; font-size: 11px; color: #6366f1; margin-bottom: 2px; }
.mc-name { font-size: 13px; font-weight: 600; color: #374151; margin-bottom: 6px; text-transform: capitalize; }
.mc-body { font-size: 13px; color: #6b7280; line-height: 1.6; }
</style>