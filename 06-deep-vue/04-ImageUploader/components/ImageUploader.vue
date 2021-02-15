<template>
  <div class="image-uploader" @click="deleteImage">
    <label
      :class="[
        'image-uploader__preview',
        { 'image-uploader__preview-loading': this.loading },
      ]"
      :style="bgImage"
    >
      <span>{{ imageLabel }}</span>
      <input
        ref="inputFile"
        type="file"
        accept="image/*"
        class="form-control-file"
        @change="imageChanged"
        :disabled="loading"
      />
    </label>
  </div>
</template>

<script>
import { ImageService } from '../image-service';

export default {
  name: 'ImageUploader',

  model: {
    prop: 'imageId',
    event: 'change',
  },

  props: {
    imageId: {
      type: Number,
      default: null,
    },
  },

  data() {
    return {
      loading: false,
    };
  },

  computed: {
    imageLabel() {
      if (this.imageId === null) {
        if (!this.loading) {
          return 'Загрузить изображение';
        }
        return 'Загрузка...';
      }
      return 'Удалить изображение';
    },
    bgImage() {
      if (this.imageId) {
        return {
          '--bg-image': `url('${ImageService.getImageURL(this.imageId)}')`,
        };
      }
      return {};
    },
  },

  methods: {
    imageChanged(event) {
      this.loading = true;
      ImageService.uploadImage(event.target.files[0]).then((value) => {
        this.loading = false;
        this.$emit('change', value.id);
      });
    },
    deleteImage(event) {
      if (this.imageId) {
        this.$emit('change', null);
        this.$refs.inputFile.value = null;
        event.preventDefault();
      }
    },
  },
};
</script>

<style scoped>
.image-uploader .form-control-file {
  opacity: 0;
  height: 0;
}

.image-uploader .image-uploader__preview {
  --bg-image: var(--default-cover);
  background-size: cover;
  background-position: center;
  background-image: linear-gradient(
      0deg,
      rgba(0, 0, 0, 0.4),
      rgba(0, 0, 0, 0.4)
    ),
    var(--bg-image);
  border: 2px solid var(--blue-light);
  border-radius: 8px;
  transition: 0.2s border-color;
  cursor: pointer;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  max-width: 512px;
  height: 228px;
}

.image-uploader .image-uploader__preview > span {
  color: var(--white);
  font-family: 'Nunito', sans-serif;
  font-weight: 600;
  font-size: 20px;
  line-height: 28px;
}

.image-uploader .image-uploader__preview:hover {
  border-color: var(--blue);
}

.image-uploader .image-uploader__preview.image-uploader__preview-loading {
  cursor: no-drop;
}
</style>
