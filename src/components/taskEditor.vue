<template>
  <div class="tasks-page-wrapper">
    <!-- Header bar -->
    <div class="tasks-header">
      <div class="tasks-header-info">
        <h2 class="tasks-page-title">Dashboard công việc</h2>
        <div class="header-badges-group" v-if="tasks.length > 0">
          <span class="tasks-counter-badge">
            {{ completedCount }}/{{ tasks.length }} hoàn thành
          </span>
          <span class="progress-pill" v-if="tasks.length > 0">
            {{ Math.round((completedCount / tasks.length) * 100) }}%
          </span>
        </div>
      </div>
      <b-button to="/form" class="btn-create-header" v-if="tasks.length > 0">
        <span>➕ Thêm việc mới</span>
      </b-button>
    </div>

    <!-- Filter Tabs (nếu có tasks) -->
    <div class="filter-tabs-row" v-if="tasks.length > 0">
      <button 
        class="filter-tab-btn" 
        :class="{ active: currentFilter === 'all' }" 
        @click="currentFilter = 'all'"
      >
        Tất cả ({{ tasks.length }})
      </button>
      <button 
        class="filter-tab-btn" 
        :class="{ active: currentFilter === 'active' }" 
        @click="currentFilter = 'active'"
      >
        Đang làm ({{ tasks.length - completedCount }})
      </button>
      <button 
        class="filter-tab-btn" 
        :class="{ active: currentFilter === 'completed' }" 
        @click="currentFilter = 'completed'"
      >
        Đã hoàn thành ({{ completedCount }})
      </button>
    </div>

    <!-- Task List -->
    <div class="tasks-list" v-if="filteredTasks.length > 0">
      <div 
        v-for="task in filteredTasks" 
        :key="task.originalIndex" 
        class="task-item-card"
        :class="{ 'task-completed': task.completed }"
      >
        <!-- Tick Button -->
        <button 
          class="btn-check-task" 
          :class="{ 'is-completed': task.completed }" 
          @click="toggleTaskComplete(task.originalIndex)"
          :title="task.completed ? 'Đánh dấu chưa hoàn thành' : 'Đánh dấu đã hoàn thành'"
        >
          <span v-if="task.completed" class="check-icon">✓</span>
        </button>

        <div class="task-card-content" @click="toggleTaskComplete(task.originalIndex)">
          <div class="task-badges">
            <span class="task-badge-index">#{{ task.originalIndex + 1 }}</span>
            <span v-if="task.completed" class="badge-done">
              <span class="done-dot"></span> Đã hoàn thành
            </span>
          </div>
          <h3 class="task-title" :class="{ 'completed-text': task.completed }">
            {{ task.subject }}
          </h3>
          <p class="task-desc" :class="{ 'completed-desc': task.completed }" v-if="task.description">
            {{ task.description }}
          </p>
        </div>

        <div class="task-actions" @click.stop>
          <button class="btn-action btn-action-edit" @click="edit(task.originalIndex)" title="Chỉnh sửa">
            <span class="action-icon">✏️</span> Sửa
          </button>
          <button class="btn-action btn-action-delete" @click="remove(task, task.originalIndex)" title="Xóa">
            <span class="action-icon">🗑️</span> Xóa
          </button>
        </div>
      </div>
    </div>

    <!-- Filter Empty State (khi lọc không có việc) -->
    <div class="empty-filter-card" v-else-if="tasks.length > 0">
      <p class="empty-filter-text">Không có công việc nào trong mục này.</p>
    </div>

    <!-- Empty State Toàn Bộ -->
    <div class="empty-state-card" v-else>
      <div class="empty-icon-wrapper">
        <span class="empty-icon">📝</span>
      </div>
      <h3 class="empty-title">Chưa có công việc nào!</h3>
      <p class="empty-desc">
        Danh sách công việc của bạn hiện đang trống. Hãy bắt đầu lên kế hoạch và hoàn thành mục tiêu ngay hôm nay.
      </p>
      <b-button to="/form" class="btn-create-empty">
        <span>➕ Tạo công việc đầu tiên</span>
      </b-button>
    </div>

    <!-- Delete Confirmation Modal -->
    <b-modal 
      ref="modalRemove" 
      hide-footer 
      centered 
      title="Xác nhận xóa công việc"
      header-class="custom-modal-header"
      body-class="custom-modal-body"
      content-class="custom-modal-content"
    >
      <div class="modal-confirm-body text-center py-2">
        <div class="modal-warning-icon">⚠️</div>
        <p class="modal-confirm-text">Bạn có chắc chắn muốn xóa công việc này không?</p>
        <div class="modal-task-preview" v-if="taskSelected">
          <strong>"{{ taskSelected.subject }}"</strong>
        </div>
        <p class="modal-note">Hành động này không thể hoàn tác.</p>
      </div>
      
      <div class="modal-actions d-flex justify-content-end gap-2 mt-4">
        <button class="btn-modal-cancel" @click="hideModal">
          Hủy bỏ
        </button>
        <button class="btn-modal-delete" @click="confirmRemoveTask">
          Xác nhận xóa
        </button>
      </div> 
    </b-modal>
  </div>
</template>
    
<script>
export default {
  name: "TaskEditor",

  data() {
    return {
      tasks: [],
      taskSelected: {},
      currentFilter: 'all' // 'all', 'active', 'completed'
    }
  },

  computed: {
    completedCount() {
      return this.tasks.filter(t => Boolean(t.completed)).length;
    },

    filteredTasks() {
      const indexed = this.tasks.map((task, originalIndex) => ({
        ...task,
        originalIndex
      }));

      if (this.currentFilter === 'active') {
        return indexed.filter(t => !t.completed);
      }
      if (this.currentFilter === 'completed') {
        return indexed.filter(t => Boolean(t.completed));
      }
      return indexed;
    }
  },

  created() {
    let saved = localStorage.getItem("tasks");
    if (saved) {
      try {
        let parsed = JSON.parse(saved);
        this.tasks = parsed.map(t => ({
          ...t,
          completed: Boolean(t.completed)
        }));
      } catch (e) {
        this.tasks = [];
      }
    } else {
      this.tasks = [];
    }
  },

  methods: {
    toggleTaskComplete(index) {
      const task = this.tasks[index];
      if (task) {
        this.$set(task, 'completed', !task.completed);
        localStorage.setItem("tasks", JSON.stringify(this.tasks));
      }
    },

    edit(index) {
      this.$router.push({ name: "form", params: { index } });
    },

    remove(task, index) { 
      this.taskSelected = { ...task, index };
      this.$refs.modalRemove.show();
    },

    hideModal() {
      this.$refs.modalRemove.hide();
    },

    confirmRemoveTask() {
      this.tasks.splice(this.taskSelected.index, 1);
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
      this.hideModal();
    }
  }
}
</script>
    
<style scoped>
.tasks-page-wrapper {
  max-width: 860px;
  width: 100%;
  margin: 0 auto;
  padding: 2rem 1.25rem;
  min-height: calc(100vh - 75px);
}

.tasks-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
  padding-bottom: 1.25rem;
  border-bottom: 1px solid var(--border-glass);
}

.tasks-header-info {
  display: flex;
  align-items: center;
  gap: 1rem;
  flex-wrap: wrap;
}

.header-badges-group {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.tasks-page-title {
  font-size: 1.75rem;
  font-weight: 800;
  color: var(--text-primary);
  letter-spacing: -0.5px;
  margin: 0;
}

.tasks-counter-badge {
  background: rgba(99, 102, 241, 0.15);
  border: 1px solid rgba(99, 102, 241, 0.3);
  color: #a5b4fc;
  font-size: 0.85rem;
  font-weight: 600;
  padding: 0.3rem 0.8rem;
  border-radius: 9999px;
}

.progress-pill {
  background: rgba(16, 185, 129, 0.15);
  border: 1px solid rgba(16, 185, 129, 0.3);
  color: #34d399;
  font-size: 0.85rem;
  font-weight: 700;
  padding: 0.3rem 0.7rem;
  border-radius: 9999px;
}

/* Filter Tabs */
.filter-tabs-row {
  display: flex;
  gap: 0.6rem;
  margin-bottom: 1.5rem;
  flex-wrap: wrap;
}

.filter-tab-btn {
  background: rgba(255, 255, 255, 0.04);
  border: 1px solid var(--border-glass);
  color: var(--text-secondary);
  padding: 0.45rem 1rem;
  border-radius: 10px;
  font-size: 0.875rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
}

.filter-tab-btn:hover {
  background: rgba(255, 255, 255, 0.08);
  color: var(--text-primary);
}

.filter-tab-btn.active {
  background: rgba(99, 102, 241, 0.18);
  border-color: rgba(99, 102, 241, 0.4);
  color: #c7d2fe;
  font-weight: 600;
  box-shadow: 0 0 12px rgba(99, 102, 241, 0.25);
}

.btn-create-header {
  background: var(--accent-gradient) !important;
  border: none !important;
  color: #ffffff !important;
  font-weight: 600;
  font-size: 0.95rem;
  padding: 0.6rem 1.3rem;
  border-radius: 12px;
  box-shadow: 0 4px 15px rgba(99, 102, 241, 0.3);
  transition: all 0.25s ease;
}

.btn-create-header:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(99, 102, 241, 0.5);
}

/* Tasks list */
.tasks-list {
  display: flex;
  flex-direction: column;
  gap: 1.25rem;
}

.task-item-card {
  background: var(--bg-card);
  backdrop-filter: var(--backdrop-blur);
  -webkit-backdrop-filter: var(--backdrop-blur);
  border: 1px solid var(--border-glass);
  border-radius: 20px;
  padding: 1.4rem 1.75rem;
  box-shadow: var(--glass-shadow);
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 1.25rem;
  transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
}

.task-item-card:hover {
  transform: translateY(-2px);
  border-color: var(--border-glass-hover);
  background: var(--bg-card-hover);
  box-shadow: 0 12px 32px -8px rgba(0, 0, 0, 0.6);
}

.task-completed {
  background: rgba(15, 23, 42, 0.55);
  border-color: rgba(16, 185, 129, 0.22);
  opacity: 0.88;
}

.task-completed:hover {
  opacity: 1;
  border-color: rgba(16, 185, 129, 0.45);
}

/* Check button */
.btn-check-task {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  border: 2px solid rgba(255, 255, 255, 0.28);
  background: rgba(255, 255, 255, 0.04);
  color: #ffffff;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  flex-shrink: 0;
  margin-top: 0.25rem;
  transition: all 0.2s ease;
  padding: 0;
}

.btn-check-task:hover {
  border-color: #10b981;
  background: rgba(16, 185, 129, 0.12);
  transform: scale(1.1);
}

.btn-check-task.is-completed {
  background: linear-gradient(135deg, #10b981 0%, #059669 100%);
  border-color: #10b981;
  box-shadow: 0 0 12px rgba(16, 185, 129, 0.45);
}

.check-icon {
  font-size: 0.95rem;
  font-weight: 800;
  line-height: 1;
}

.task-card-content {
  flex: 1;
  cursor: pointer;
}

.task-badges {
  display: flex;
  align-items: center;
  gap: 0.6rem;
  margin-bottom: 0.35rem;
}

.task-badge-index {
  display: inline-block;
  font-size: 0.75rem;
  font-weight: 700;
  color: #818cf8;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.badge-done {
  display: inline-flex;
  align-items: center;
  gap: 0.35rem;
  background: rgba(16, 185, 129, 0.14);
  border: 1px solid rgba(16, 185, 129, 0.3);
  color: #34d399;
  font-size: 0.75rem;
  font-weight: 700;
  padding: 0.15rem 0.55rem;
  border-radius: 9999px;
}

.done-dot {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: #10b981;
  box-shadow: 0 0 6px #10b981;
}

.task-title {
  font-size: 1.25rem;
  font-weight: 700;
  color: var(--text-primary);
  margin-bottom: 0.45rem;
  line-height: 1.3;
  transition: all 0.2s ease;
}

.completed-text {
  text-decoration: line-through;
  color: var(--text-muted) !important;
}

.task-desc {
  font-size: 0.95rem;
  color: var(--text-secondary);
  line-height: 1.6;
  margin: 0;
  white-space: pre-line;
  transition: all 0.2s ease;
}

.completed-desc {
  text-decoration: line-through;
  color: #64748b !important;
}

.empty-filter-card {
  padding: 3rem 1.5rem;
  text-align: center;
  color: var(--text-muted);
  background: var(--bg-card);
  border-radius: 18px;
  border: 1px dashed var(--border-glass);
}

.empty-filter-text {
  margin: 0;
  font-size: 1rem;
}

.task-actions {
  display: flex;
  gap: 0.6rem;
  flex-shrink: 0;
}

.btn-action {
  display: inline-flex;
  align-items: center;
  gap: 0.4rem;
  border-radius: 10px;
  font-size: 0.875rem;
  font-weight: 600;
  padding: 0.45rem 0.9rem;
  border: 1px solid transparent;
  cursor: pointer;
  transition: all 0.2s ease;
}

.btn-action-edit {
  background: rgba(99, 102, 241, 0.12);
  border-color: rgba(99, 102, 241, 0.25);
  color: #c7d2fe;
}

.btn-action-edit:hover {
  background: rgba(99, 102, 241, 0.25);
  color: #ffffff;
  border-color: rgba(99, 102, 241, 0.5);
  transform: translateY(-1px);
}

.btn-action-delete {
  background: var(--danger-bg);
  border-color: var(--danger-border);
  color: #fca5a5;
}

.btn-action-delete:hover {
  background: var(--danger-color);
  color: #ffffff;
  border-color: var(--danger-hover);
  transform: translateY(-1px);
}

/* Empty State */
.empty-state-card {
  background: var(--bg-card);
  backdrop-filter: var(--backdrop-blur);
  -webkit-backdrop-filter: var(--backdrop-blur);
  border: 1px solid var(--border-glass);
  border-radius: 24px;
  padding: 4rem 2rem;
  text-align: center;
  box-shadow: var(--glass-shadow);
  display: flex;
  flex-direction: column;
  align-items: center;
  max-width: 560px;
  margin: 2rem auto 0;
}

.empty-icon-wrapper {
  width: 72px;
  height: 72px;
  border-radius: 24px;
  background: rgba(99, 102, 241, 0.12);
  border: 1px solid rgba(99, 102, 241, 0.3);
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 1.5rem;
}

.empty-icon {
  font-size: 2.2rem;
}

.empty-title {
  font-size: 1.4rem;
  font-weight: 700;
  color: var(--text-primary);
  margin-bottom: 0.75rem;
}

.empty-desc {
  font-size: 0.95rem;
  color: var(--text-muted);
  max-width: 420px;
  line-height: 1.6;
  margin-bottom: 2rem;
}

.btn-create-empty {
  background: var(--accent-gradient) !important;
  border: none !important;
  color: #ffffff !important;
  font-weight: 600;
  font-size: 1rem;
  padding: 0.75rem 1.75rem;
  border-radius: 14px;
  box-shadow: 0 4px 18px rgba(99, 102, 241, 0.4);
  transition: all 0.25s ease;
}

.btn-create-empty:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 24px rgba(99, 102, 241, 0.6);
}

/* Modal styling */
:deep(.custom-modal-content) {
  background: #111827 !important;
  border: 1px solid var(--border-glass-hover) !important;
  border-radius: 20px !important;
  color: var(--text-primary) !important;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.7) !important;
}

:deep(.custom-modal-header) {
  border-bottom: 1px solid var(--border-glass) !important;
  padding: 1.25rem 1.5rem !important;
}

:deep(.custom-modal-header .modal-title) {
  color: var(--text-primary) !important;
  font-weight: 700 !important;
  font-size: 1.15rem !important;
}

:deep(.custom-modal-header .close) {
  color: var(--text-muted) !important;
  opacity: 0.8 !important;
  text-shadow: none !important;
}

:deep(.custom-modal-header .close:hover) {
  color: var(--text-primary) !important;
}

:deep(.custom-modal-body) {
  padding: 1.5rem !important;
}

.modal-warning-icon {
  font-size: 2.5rem;
  margin-bottom: 0.75rem;
}

.modal-confirm-text {
  font-size: 1.05rem;
  color: var(--text-primary);
  font-weight: 600;
  margin-bottom: 0.5rem;
}

.modal-task-preview {
  background: rgba(255, 255, 255, 0.05);
  border-radius: 10px;
  padding: 0.75rem 1rem;
  margin: 0.75rem 0;
  color: #fca5a5;
  font-size: 0.95rem;
  word-break: break-word;
}

.modal-note {
  font-size: 0.85rem;
  color: var(--text-muted);
  margin-bottom: 0;
}

.btn-modal-cancel {
  background: rgba(255, 255, 255, 0.08);
  border: 1px solid var(--border-glass);
  color: var(--text-secondary);
  font-weight: 600;
  padding: 0.55rem 1.25rem;
  border-radius: 10px;
  cursor: pointer;
  transition: all 0.2s ease;
}

.btn-modal-cancel:hover {
  background: rgba(255, 255, 255, 0.15);
  color: var(--text-primary);
}

.btn-modal-delete {
  background: var(--danger-color);
  border: 1px solid var(--danger-hover);
  color: #ffffff;
  font-weight: 600;
  padding: 0.55rem 1.25rem;
  border-radius: 10px;
  cursor: pointer;
  transition: all 0.2s ease;
  box-shadow: 0 4px 12px rgba(239, 68, 68, 0.35);
}

.btn-modal-delete:hover {
  background: var(--danger-hover);
  transform: translateY(-1px);
}

@media (max-width: 576px) {
  .tasks-page-wrapper {
    padding: 1.5rem 1rem;
  }
  .task-item-card {
    flex-direction: column;
    padding: 1.25rem;
    gap: 1rem;
  }
  .task-actions {
    width: 100%;
    justify-content: flex-end;
  }
}
</style>
