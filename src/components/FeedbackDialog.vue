<template>
  <el-dialog v-model="isVisible" title="意見反饋" width="600px">
    <el-form ref="ruleFormRef" :rules="rules" :model="formData">
      <el-form-item label="類別" prop="type" class="el-item-vertical">
        <el-select v-model="formData.type" placeholder="請選擇類別">
          <el-option label="操作問題" value="0"></el-option>
          <el-option label="優化建議" value="1"></el-option>
          <el-option label="Bug反饋" value="2"></el-option>
          <el-option label="其他" value="3"></el-option>
        </el-select>
      </el-form-item>
      
      <el-form-item label="標題(限30字符內)" prop="title" class="el-item-vertical">
        <el-input v-model="formData.title" placeholder="請輸入標題" maxlength="30"></el-input>
      </el-form-item>
      
      <el-form-item label="描述(限300字符內)" prop="description" class="el-item-vertical">
        <el-input
          v-model="formData.description"
          type="textarea"
          placeholder="請描述您的意見/問題"
          maxlength="300"
        ></el-input>
      </el-form-item>
      
      <el-form-item label="參考圖片(僅支援PNG、JPG格式、每張5MB)" class="el-item-vertical">
        <el-upload
          :auto-upload="false"
          v-model:file-list="fileList"
          list-type="picture-card"
          :on-preview="handlePictureCardPreview"
          :on-remove="handleRemove"
          accept="image/png, image/jpeg"
          :on-change="beforeUpload"
        >
          <el-icon><Plus /></el-icon>
        </el-upload>
      </el-form-item>
      
      <span slot="footer" class="dialog-footer">
        <el-button @click="isVisible = false">取消</el-button>
        <el-button type="primary" @click="handleSubmit">提交</el-button>
      </span>
    </el-form>

    <el-dialog v-model="dialogVisible">
      <img :src="dialogImageUrl" class="preview-img" />
    </el-dialog>

  </el-dialog>
</template>

<script setup>
import { ref, reactive } from 'vue';
import { Plus } from '@element-plus/icons-vue'
import { ElMessage } from 'element-plus'

const isVisible = ref(false);
const formData = ref({
  category: '',
  title: '',
  description: '',
});

const fileList = ref([]);

const rules = reactive({
  type: [{ required: true, message: "請選擇類別", trigger: "change" }],
  title: [{ required: true, message: "請輸入標題", trigger: "change" }],
  description: [{ required: true, message: "請描述您的意見/問題", trigger: "change" }],
});

const acceptParams = async () => {
  isVisible.value = true;
};

const emit = defineEmits(['submitFeedback']);
const ruleFormRef = ref();
const successDialogVisible = ref(false);
const handleSubmit = () => {
  ruleFormRef.value.validate(async valid => {
    if (!valid) return;
    try {
      isVisible.value = false;

      emit('submitFeedback');
    } catch (error) {
      console.log(error);
    }
  });
};

const dialogImageUrl = ref('')
const dialogVisible = ref(false)

const handleRemove= (uploadFile, uploadFiles) => {
  ElMessage({
    message: '移除成功',
    type: 'success',
  })
}

const handlePictureCardPreview = (uploadFile) => {
  dialogImageUrl.value = uploadFile.url
  dialogVisible.value = true
}

const beforeUpload = (uploadFile, uploadFiles) => {
  const isJPGOrPNG = uploadFile.raw.type === 'image/jpeg' || uploadFile.raw.type === 'image/png';
  const isLt5M = uploadFile.size / 1024 / 1024 < 5;

  if (!isJPGOrPNG) {
    ElMessage({
      message: '僅支持上傳 JPG 或 PNG 格式圖片',
      type: 'error',
    });
    // 從 fileList 中移除不符合條件的文件
    fileList.value = fileList.value.filter(file => file.uid !== uploadFile.uid);
    return false;
  }

  if (!isLt5M) {
    ElMessage({
      message: '上傳圖片大小不能超過 5MB',
      type: 'error',
    });
    // 從 fileList 中移除不符合條件的文件
    fileList.value = fileList.value.filter(file => file.uid !== uploadFile.uid);
    return false;
  }
  
  return true;
};

defineExpose({
  acceptParams
});
</script>

<style lang="scss">
.el-item-vertical {
  flex-direction: column;
  .el-form-item__label {
    justify-content: flex-start;
    width: 100% !important;
  }
}
.preview-img {
  width: 100%;
}
</style>
