<template>
  <file-upload
    :size="4096 * 2048"
    accept="image/*, application/pdf, audio/mpeg, video/mp4, audio/ogg, text/csv"
    @input-file="onFileUpload"
  >
    <span class="attachment-button">
      <i v-if="!isUploading.image" class="ion-android-attach" />
      <spinner v-if="isUploading" size="small" />
    </span>
  </file-upload>
</template>

<script>
import FileUpload from 'vue-upload-component';
import Spinner from 'shared/components/Spinner.vue';
import { checkFileSizeLimit } from 'shared/helpers/FileHelper';
import { MAXIMUM_FILE_UPLOAD_SIZE } from 'shared/constants/messages';
import { BUS_EVENTS } from 'shared/constants/busEvents';

export default {
  components: { FileUpload, Spinner },
  props: {
    onAttach: {
      type: Function,
      default: () => {},
    },
  },
  data() {
    return { isUploading: false };
  },
  methods: {
    getFileType(fileType) {
      return fileType.includes('image') ? 'image' : 'file';
    },
    async onFileUpload(file) {
      if (!file) {
        return;
      }
      this.isUploading = true;
      try {
        if (checkFileSizeLimit(file, MAXIMUM_FILE_UPLOAD_SIZE)) {
          const thumbUrl = window.URL.createObjectURL(file.file);
          await this.onAttach({
            fileType: this.getFileType(file.type),
            file: file.file,
            thumbUrl,
          });
        } else {
          window.bus.$emit(BUS_EVENTS.ATTACHMENT_SIZE_CHECK_ERROR);
        }
      } catch (error) {
        // Error
      }
      this.isUploading = false;
    },
  },
};
</script>
<style scoped lang="scss">
@import '~widget/assets/scss/variables.scss';

.attachment-button {
  background: transparent;
  border: 0;
  cursor: pointer;
  position: relative;
  width: 20px;

  i {
    font-size: $font-size-large;
    color: $color-gray;
  }
}
</style>
<style lang="scss">
.file-uploads .attachment-button + label {
  cursor: pointer;
}
</style>
