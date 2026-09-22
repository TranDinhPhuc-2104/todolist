<template>
  <div class="form-card-wrapper">
    <div class="glass-form-card">
      <div class="form-header">
        <div class="form-header-icon">
          {{ methodSave === 'update' ? '✏️' : '✨' }}
        </div>
        <div>
          <h2 class="form-title">
            {{ methodSave === 'update' ? 'Chỉnh sửa công việc' : 'Tạo công việc mới' }}
          </h2>
          <p class="form-subtitle">
            {{ methodSave === 'update' ? 'Cập nhật lại tiêu đề hoặc nội dung chi tiết của bạn' : 'Thêm nhiệm vụ cần hoàn thành vào danh sách của bạn' }}
          </p>
        </div>
      </div>

      <b-form @submit.prevent="saveTask">
        <b-form-group
          label="Title *"
          label-for="subject"
          class="form-label-custom"
        >
          <b-form-input
            id="subject"
            v-model="formAddTask.subject"
            type="text"
            placeholder="Ví dụ: Hoàn thành báo cáo dự án..."
            required
            autocomplete="off"
            class="input-custom"
          ></b-form-input>
        </b-form-group>

        <b-form-group
          label="Mô tả chi tiết"
          label-for="description"
          class="form-label-custom"
        >
          <b-form-textarea
            id="description"
            v-model="formAddTask.description"
            rows="4"
            placeholder="Ví dụ: Gửi báo cáo cho trưởng nhóm trước 17:00 chiều nay..."
            autocomplete="off"
            class="input-custom textarea-custom"
          ></b-form-textarea>
        </b-form-group>

        <!-- Checkbox Tick Đã Hoàn Thành -->
        <div class="complete-toggle-row" @click="formAddTask.completed = !formAddTask.completed">
          <div class="custom-check-box" :class="{ checked: formAddTask.completed }">
            <span v-if="formAddTask.completed">✓</span>
          </div>
          <span class="complete-toggle-label">Đánh dấu công việc đã hoàn thành</span>
        </div>

        <div class="form-actions">
          <b-button type="button" to="/" class="btn-cancel">
            Hủy bỏ
          </b-button>
          <b-button type="submit" class="btn-submit">
            <span>{{ methodSave === 'update' ? 'Cập nhật' : 'Lưu công việc' }}</span>
          </b-button>
        </div>
      </b-form>
    </div>
  </div>
</template>

<script>
import ToastMixin from "@/mixins/toastMixin.js";

export default {
  name: "formAddTask",

  mixins: [ToastMixin],

  data() {
    return {
      formAddTask: {
        subject: "",
        description: "",
        completed: false
      },
      methodSave: "new"
    }
  },

  created() {
    if(this.$route.params.index === 0 || this.$route.params.index !== undefined){
      this.methodSave = "update";
      let tasks = JSON.parse(localStorage.getItem("tasks"));
      if (tasks && tasks[this.$route.params.index]) {
        this.formAddTask = {
          subject: tasks[this.$route.params.index].subject || "",
          description: tasks[this.$route.params.index].description || "",
          completed: Boolean(tasks[this.$route.params.index].completed)
        };
      }
    }
  },

  methods: {
    saveTask() {
      if (!this.formAddTask.subject || !this.formAddTask.subject.trim()) {
        this.showToast("danger", "Thông báo", "Vui lòng nhập tiêu đề công việc!");
        return;
      }

      const taskData = {
        subject: this.formAddTask.subject.trim(),
        description: this.formAddTask.description.trim(),
        completed: Boolean(this.formAddTask.completed)
      };

      if(this.methodSave === "update"){
        let tasks = JSON.parse(localStorage.getItem("tasks")) || [];
        tasks[this.$route.params.index] = taskData;
        localStorage.setItem("tasks", JSON.stringify(tasks));
        this.showToast("success", "Thành công!", "Công việc đã được cập nhật thành công.");
        this.$router.push({ name: "list" });
        return;
      }

      let tasks = (localStorage.getItem("tasks")) ? JSON.parse(localStorage.getItem("tasks")) : [];
      tasks.push(taskData);
      localStorage.setItem("tasks", JSON.stringify(tasks));
      this.showToast("success", "Thành công!", "Đã thêm công việc mới vào danh sách!");
      this.$router.push({ name: "list" });
    }
  }
}
</script>

<style scoped>
.form-card-wrapper {
  width: 100%;
  max-width: 620px;
  margin: 0 auto;
}

.glass-form-card {
  background: var(--bg-card);
  backdrop-filter: var(--backdrop-blur);
  -webkit-backdrop-filter: var(--backdrop-blur);
  border: 1px solid var(--border-glass);
  border-radius: 24px;
  padding: 2.5rem 2rem;
  box-shadow: var(--glass-shadow);
  animation: fadeIn 0.4s ease-out;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(12px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.form-header {
  display: flex;
  align-items: center;
  gap: 1.25rem;
  margin-bottom: 2rem;
  padding-bottom: 1.25rem;
  border-bottom: 1px solid var(--border-glass);
}

.form-header-icon {
  font-size: 2rem;
  background: rgba(99, 102, 241, 0.15);
  border: 1px solid rgba(99, 102, 241, 0.3);
  border-radius: 16px;
  width: 54px;
  height: 54px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.form-title {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--text-primary);
  margin-bottom: 0.25rem;
  letter-spacing: -0.3px;
}

.form-subtitle {
  font-size: 0.9rem;
  color: var(--text-muted);
  margin-bottom: 0;
}

:deep(.form-label-custom label) {
  color: var(--text-secondary) !important;
  font-weight: 600;
  font-size: 0.95rem;
  margin-bottom: 0.5rem;
}

.input-custom {
  background: var(--bg-input) !important;
  border: 1px solid var(--border-glass) !important;
  border-radius: 12px !important;
  color: var(--text-primary) !important;
  font-size: 0.95rem;
  padding: 0.75rem 1rem !important;
  transition: all 0.2s ease;
  box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.3);
}

.input-custom::placeholder {
  color: var(--text-muted) !important;
  opacity: 0.6;
}

.input-custom:focus {
  background: rgba(15, 23, 42, 0.85) !important;
  border-color: var(--border-focus) !important;
  box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.25) !important;
  outline: none;
}

.textarea-custom {
  resize: vertical;
  min-height: 110px;
}

/* Checkbox Toggle Row */
.complete-toggle-row {
  display: flex;
  align-items: center;
  gap: 0.85rem;
  padding: 0.85rem 1.15rem;
  background: rgba(255, 255, 255, 0.03);
  border: 1px solid var(--border-glass);
  border-radius: 14px;
  cursor: pointer;
  margin-top: 1.25rem;
  transition: all 0.2s ease;
  user-select: none;
}

.complete-toggle-row:hover {
  background: rgba(255, 255, 255, 0.06);
  border-color: var(--border-glass-hover);
}

.custom-check-box {
  width: 24px;
  height: 24px;
  border-radius: 7px;
  border: 2px solid rgba(255, 255, 255, 0.3);
  display: flex;
  align-items: center;
  justify-content: center;
  color: #ffffff;
  font-size: 0.85rem;
  font-weight: 800;
  transition: all 0.2s ease;
}

.custom-check-box.checked {
  background: linear-gradient(135deg, #10b981 0%, #059669 100%);
  border-color: #10b981;
  box-shadow: 0 0 10px rgba(16, 185, 129, 0.45);
}

.complete-toggle-label {
  color: var(--text-secondary);
  font-size: 0.95rem;
  font-weight: 500;
}

.form-actions {
  display: flex;
  justify-content: flex-end;
  gap: 1rem;
  margin-top: 2rem;
  padding-top: 1.25rem;
  border-top: 1px solid var(--border-glass);
}

.btn-cancel {
  background: rgba(255, 255, 255, 0.05) !important;
  border: 1px solid var(--border-glass) !important;
  color: var(--text-secondary) !important;
  font-weight: 600;
  padding: 0.65rem 1.4rem;
  border-radius: 12px;
  transition: all 0.2s ease;
  text-decoration: none;
}

.btn-cancel:hover {
  background: rgba(255, 255, 255, 0.1) !important;
  color: var(--text-primary) !important;
  border-color: var(--border-glass-hover) !important;
}

.btn-submit {
  background: var(--accent-gradient) !important;
  border: none !important;
  color: #ffffff !important;
  font-weight: 600;
  padding: 0.65rem 1.75rem;
  border-radius: 12px;
  box-shadow: 0 4px 15px rgba(99, 102, 241, 0.35);
  transition: all 0.25s ease;
}

.btn-submit:hover {
  box-shadow: 0 6px 20px rgba(99, 102, 241, 0.55);
  transform: translateY(-1px);
}

@media (max-width: 576px) {
  .glass-form-card {
    padding: 1.75rem 1.25rem;
  }
  .form-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 0.75rem;
  }
  .form-actions {
    flex-direction: column-reverse;
  }
  .btn-cancel, .btn-submit {
    width: 100%;
    text-align: center;
  }
}
</style>

