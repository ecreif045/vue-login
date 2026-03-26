<script setup>
import { ref, onMounted } from 'vue'
import Vue3Datatable from '@bhplugin/vue3-datatable'
import '@bhplugin/vue3-datatable/dist/style.css'

const photos = ref([])
const loading = ref(false)
const error = ref(null)
const selectedPhoto = ref(null)
const photoColumns = ref([
  {
    field: 'id',
    title: 'ID',
    width: '70px',
    type: 'number',
  },
  {
    field: 'albumId',
    title: 'Album',
    width: '80px',
    cellRenderer: (row) => `
      <span style="
        background:#f0f0ff;
        color:#6366f1;
        padding:3px 10px;
        border-radius:20px;
        font-size:11px;
        font-family:monospace;
        font-weight:600;
      ">
        #${row.albumId}
      </span>`,
  },
 {
  field: 'thumbnailUrl',
  title: 'Photo',
  width: '100px',
  cellRenderer: (row) => `
    <img
      src="https://picsum.photos/seed/${row.id}/60/60"
      alt="${row.title}"
      data-url="https://picsum.photos/seed/${row.id}/600/600"
      data-thumbnail="https://picsum.photos/seed/${row.id}/60/60"
      data-title="${row.title.replace(/"/g, '&quot;')}"
      data-id="${row.id}"
      data-albumid="${row.albumId}"
      class="thumb-img"
      width="60" height="60"
      style="border-radius:8px;cursor:pointer;object-fit:cover;display:block;"
    />`,
},
  {
    field: 'title',
    title: 'Title',
    cellRenderer: (row) => `
      <span style="font-size:13px;text-transform:capitalize;color:#374151">
        ${row.title}
      </span>`,
  },
  {
  field: 'action',
  title: 'Preview',
  width: '90px',
  cellRenderer: (row) => `
    <button
      class="preview-btn"
      data-url="https://picsum.photos/seed/${row.id}/600/600"
      data-thumbnail="https://picsum.photos/seed/${row.id}/60/60"
      data-title="${row.title.replace(/"/g, '&quot;')}"
      data-id="${row.id}"
      data-albumid="${row.albumId}"
      style="background:#6366f1;color:white;border:none;padding:5px 12px;border-radius:6px;cursor:pointer;font-size:12px;"
    >Preview</button>`,
},
])

function handleTableClick(e) {
  const img = e.target.closest('.thumb-img')
  const btn = e.target.closest('.preview-btn')
  const el = img || btn
  if (!el) return

  selectedPhoto.value = {
    id:        el.dataset.id,
    albumId:   el.dataset.albumid,
    title:     el.dataset.title,
    url:       el.dataset.url,
    thumbnail: el.dataset.thumbnail,
  }
}

function closeModal() {
  selectedPhoto.value = null
}

async function fetchPhotos() {
  loading.value = true
  error.value = null
  try {
    const res = await fetch('https://jsonplaceholder.typicode.com/photos')
    if (!res.ok) throw new Error(`HTTP error: ${res.status}`)
    photos.value = await res.json()
  } catch (err) {
    error.value = 'Failed to load photos: ' + err.message
  } finally {
    loading.value = false
  }
}

onMounted(() => fetchPhotos())
</script>

<template>
  <div class="gallery-page">

    <div class="page-header">
      <h1 class="page-title">Photo Gallery</h1>
      <p class="page-subtitle">
        {{ photos.length > 0 ? photos.length.toLocaleString() + ' photos' : 'Loading...' }}
        — click any thumbnail or Preview to see full size
      </p>
    </div>

    <div v-if="loading" class="loading-state">
      <div class="spinner"></div>
      <p>Fetching 5,000 photos...</p>
    </div>

    <div v-else-if="error" class="error-state">
      ⚠️ {{ error }}
    </div>

    <div v-else @click="handleTableClick">
      <vue3-datatable
        :rows="photos"
        :columns="photoColumns"
        :loading="loading"
        skin="bh-table-hover bh-table-bordered"
        noDataContent="No photos found"
      />
    </div>

    <div v-if="selectedPhoto" class="modal-overlay" @click.self="closeModal">
      <div class="modal">
        <button class="modal-close" @click="closeModal">✕</button>

        <div class="modal-meta">
          <span class="modal-album">Album #{{ selectedPhoto.albumId }}</span>
          <span class="modal-id">Photo #{{ selectedPhoto.id }}</span>
        </div>

        <p class="modal-title">{{ selectedPhoto.title }}</p>

        <div class="modal-img-wrap">
          <img
            :src="selectedPhoto.url"
            :alt="selectedPhoto.title"
            class="modal-img"
          />
        </div>
      </div>
    </div>

  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Syne:wght@400;700;800&family=JetBrains+Mono&family=Nunito:wght@400;500;600&display=swap');

.gallery-page {
  font-family: 'Nunito', sans-serif;
  padding: 30px 20px;
  max-width: 1200px;
  margin: 0 auto;
}

.page-header { margin-bottom: 28px; }
.page-title {
  font-family: 'Syne', sans-serif;
  font-size: 32px;
  font-weight: 800;
  color: #1a1a2e;
  margin-bottom: 4px;
}
.page-subtitle { color: #6b7280; font-size: 14px; }

.loading-state, .error-state {
  text-align: center;
  padding: 60px 20px;
  color: #6b7280;
}
.spinner {
  width: 40px; height: 40px;
  border: 3px solid #e5e7eb;
  border-top-color: #6366f1;
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
  margin: 0 auto 16px;
}
@keyframes spin { to { transform: rotate(360deg); } }

:deep(.bh-datatable table thead th) {
  background: #1a1a2e !important;
  color: #9ca3af !important;
  font-family: 'Syne', sans-serif;
  font-size: 11px;
  letter-spacing: 0.06em;
  text-transform: uppercase;
}
:deep(.bh-datatable) {
  font-family: 'Nunito', sans-serif;
  font-size: 14px;
}

:deep(.thumb-img) {
  border-radius: 8px;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
  display: block;
  object-fit: cover;
}
:deep(.thumb-img:hover) {
  transform: scale(1.1);
  box-shadow: 0 4px 16px rgba(0,0,0,0.2);
}

:deep(.preview-btn) {
  background: #6366f1;
  color: white;
  border: none;
  padding: 5px 12px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 12px;
  font-family: 'Nunito', sans-serif;
  transition: background 0.2s;
}
:deep(.preview-btn:hover) { background: #4f46e5; }

.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2000;
  padding: 20px;
}
.modal {
  background: white;
  border-radius: 16px;
  padding: 28px;
  max-width: 680px;
  width: 100%;
  position: relative;
}
.modal-close {
  position: absolute;
  top: 14px; right: 14px;
  background: #f3f4f6;
  border: none;
  width: 32px; height: 32px;
  border-radius: 50%;
  cursor: pointer;
  font-size: 14px;
}
.modal-meta {
  display: flex;
  gap: 8px;
  margin-bottom: 10px;
}
.modal-album, .modal-id {
  font-family: 'JetBrains Mono', monospace;
  font-size: 11px;
  padding: 3px 10px;
  border-radius: 20px;
  background: #f0f0ff;
  color: #6366f1;
}
.modal-title {
  font-family: 'Syne', sans-serif;
  font-size: 16px;
  font-weight: 700;
  color: #1a1a2e;
  text-transform: capitalize;
  margin-bottom: 18px;
  line-height: 1.4;
}
.modal-img-wrap {
  border-radius: 12px;
  overflow: hidden;
  background: #f3f4f6;
}
.modal-img {
  width: 100%;
  height: auto;
  display: block;
}
</style>