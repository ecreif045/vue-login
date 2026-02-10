<script setup>
import { ref } from 'vue'

const tiers = ref([
  {
    tier: 'Tier 1',
    minDeposit: '$50',
    rewardPercentage: '5%',
    maxRewardCap: '$25',
    status: 'Active',
    statusClass: 'active'
  },
  {
    tier: 'Tier 2',
    minDeposit: '$250',
    rewardPercentage: '7.5%',
    maxRewardCap: '$75',
    status: 'Active',
    statusClass: 'active'
  },
  {
    tier: 'Tier 3',
    minDeposit: '$1,000',
    rewardPercentage: '10%',
    maxRewardCap: '$200',
    status: 'Active',
    statusClass: 'active'
  },
  {
    tier: 'Tier 4',
    minDeposit: '$5,000',
    rewardPercentage: '15%',
    maxRewardCap: '$1,900',
    status: 'Active',
    statusClass: 'active'
  }
])

const editingIndex = ref(null)
const editForm = ref({
  tier: '',
  minDeposit: '',
  rewardPercentage: '',
  maxRewardCap: '',
  status: '',
  statusClass: ''
})

function startEdit(index) {
  editingIndex.value = index
  editForm.value = { ...tiers.value[index] }
}

function saveEdit(index) {
  tiers.value[index] = { ...editForm.value }
  cancelEdit()
}

function cancelEdit() {
  editingIndex.value = null
  editForm.value = {
    tier: '',
    minDeposit: '',
    rewardPercentage: '',
    maxRewardCap: '',
    status: '',
    statusClass: ''
  }
}

function toggleStatus() {
  if (editForm.value.status === 'Active') {
    editForm.value.status = 'Inactive'
    editForm.value.statusClass = 'expired'
  } else {
    editForm.value.status = 'Active'
    editForm.value.statusClass = 'active'
  }
}
</script>

<template>
  <div class="page-content">
    <h1 class="page-title">Reward Tiers</h1>
    
    <div class="table-wrapper">
      <div class="table-container">
        <table class="responsive-table">
          <thead>
            <tr>
              <th>TIER</th>
              <th>MIN DEPOSIT AMOUNT</th>
              <th>REWARD PERCENTAGE</th>
              <th>MAX REWARD CAP</th>
              <th>STATUS</th>
              <th>ACTIONS</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(tier, index) in tiers" :key="index">
              <template v-if="editingIndex !== index">
                <td>{{ tier.tier }}</td>
                <td>{{ tier.minDeposit }}</td>
                <td>{{ tier.rewardPercentage }}</td>
                <td>{{ tier.maxRewardCap }}</td>
                <td>
                  <span :class="['status-badge', tier.statusClass]">
                    {{ tier.status }}
                  </span>
                </td>

                <td>
                  <button class="edit-btn" @click="startEdit(index)">Edit</button>
                </td>
              </template>

              <template v-else>
                <td>
                  <input v-model="editForm.tier" class="edit-input" />
                </td>
                <td>
                  <input v-model="editForm.minDeposit" class="edit-input" />
                </td>
                <td>
                  <input v-model="editForm.rewardPercentage" class="edit-input" />
                </td>
                <td>
                  <input v-model="editForm.maxRewardCap" class="edit-input" />
                </td>
                <td>
                  <button @click="toggleStatus" class="status-toggle-btn">
                    <span :class="['status-badge', editForm.statusClass]">
                      {{ editForm.status }}
                    </span>
                  </button>
                </td>
                <td>
                  <div class="action-buttons">
                    <button class="save-btn" @click="saveEdit(index)">Save</button>
                    <button class="cancel-btn" @click="cancelEdit">Cancel</button>
                  </div>
                </td>
              </template>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<style scoped>
.page-content {
  padding: 20px;
  max-width: 1200px;
  margin: 0 auto;
}

.page-title {
  color: #333;
  margin-bottom: 30px;
  font-size: 28px;
}

.table-wrapper {
  background: #1a2332;
  border-radius: 8px;
  padding: 20px;
  color: #fff;
}

.table-container {
  width: 100%;
  overflow-x: auto;
}

.responsive-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 14px;
}

.responsive-table thead {
  background: #1a2332;
  border-bottom: 1px solid #2a3647;
}

.responsive-table th {
  padding: 12px 15px;
  text-align: left;
  font-weight: 600;
  font-size: 11px;
  color: #8b95a8;
}

.responsive-table tbody tr {
  border-bottom: 1px solid #2a3647;
}

.responsive-table tbody tr:hover {
  background: #242e3d;
}

.responsive-table td {
  padding: 15px;
  color: #e5e7eb;
}

.status-badge {
  padding: 4px 12px;
  border-radius: 4px;
  font-size: 12px;
  font-weight: 500;
  display: inline-block;
}

.status-badge.active {
  background: #10b981;
  color: #fff;
}

.status-badge.expired {
  background: #6b7280;
  color: #fff;
}

.edit-btn {
  background: transparent;
  color: #3b82f6;
  border: none;
  cursor: pointer;
  font-weight: 500;
  padding: 5px 10px;
}

.edit-btn:hover {
  text-decoration: underline;
}

.edit-input {
  background: #242e3d;
  border: 1px solid #3b82f6;
  color: #e5e7eb;
  padding: 8px 10px;
  border-radius: 4px;
  width: 100%;
  font-size: 14px;
}

.edit-input:focus {
  outline: none;
  border-color: #60a5fa;
}

.status-toggle-btn {
  background: transparent;
  border: none;
  cursor: pointer;
  padding: 0;
}

.action-buttons {
  display: flex;
  gap: 8px;
}

.save-btn {
  background: #10b981;
  color: #fff;
  border: none;
  padding: 6px 12px;
  border-radius: 4px;
  cursor: pointer;
  font-weight: 500;
  font-size: 13px;
}

.save-btn:hover {
  background: #059669;
}

.cancel-btn {
  background: #ef4444;
  color: #fff;
  border: none;
  padding: 6px 12px;
  border-radius: 4px;
  cursor: pointer;
  font-weight: 500;
  font-size: 13px;
}

.cancel-btn:hover {
  background: #dc2626;
}

@media (max-width: 768px) {
  .page-content {
    padding: 15px;
  }

  .action-buttons {
    flex-direction: column;
    gap: 5px;
  }

  .save-btn,
  .cancel-btn {
    font-size: 12px;
    padding: 5px 10px;
  }
}

 .edit-input {
    font-size: 12px;
    padding: 6px 8px;
  }
</style>