<template>
  <i class="info" @click="showDialog">!</i>
  <FeedbackDialog ref="feedbackDialog" @submitFeedback="handleFeedbackSubmit"/>

  <el-dialog v-model="successDialogVisible" width="400px" class="success">
    <div>
      <div class="title">
        <el-icon><CircleCheck /></el-icon>
        <h1>提交成功</h1>
      </div>

      <div>感謝您的反饋，我們將繼續努力，提供更優質的服務！</div>
    </div>
  </el-dialog>
</template>

<script setup>
import FeedbackDialog from './components/FeedbackDialog.vue'
import { ref } from 'vue';
import { CircleCheck } from '@element-plus/icons-vue'

const feedbackDialog = ref(null);
const showDialog = () => {
  feedbackDialog.value?.acceptParams();
};

const successDialogVisible = ref(false);
const handleFeedbackSubmit = (data) => {
 successDialogVisible.value = true;
  // 設定5秒後自動關閉彈窗
  setTimeout(() => {
    successDialogVisible.value = false;
  }, 5000);
};
</script>

<style lang="scss" scoped>
.info {
  position: absolute;
  bottom: 30px;
  right: 30px;
  border-radius: 50%;
  background: blue;
  width: 50px;
  height: 50px;
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 40px;
  font-weight: 500;
}
.success {
  text-align: left;
  .title {
    display: flex;
    align-items: center;
    justify-content: flex-start;
    color: green;
    h1 {
      font-size: 20px;
    }
  }
}
</style>
