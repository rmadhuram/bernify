<template>
  <div
    class="loader"
    @change="change"
    @dragover="dragover"
    @drop="drop"
  >
    <p>
      Drop image here or
      <label class="browse">browse...
        <input
          id="file"
          class="sr-only"
          type="file"
          accept="image/*"
        >
      </label> for image.
    </p>
  </div>
</template>

<script>
const URL = window.URL || window.webkitURL;

export default {
  name: 'Loader',

  props: {
    data: {
      type: Object,
      default: () => ({}),
    },
  },

  methods: {
    read(files) {
      return new Promise((resolve, reject) => {
        if (!files || files.length === 0) {
          resolve();
          return;
        }

        const file = files[0];

        if (/^image\/\w+$/.test(file.type)) {
          if (URL) {
            resolve({
              loaded: true,
              name: file.name,
              type: file.type,
              url: URL.createObjectURL(file),
            });
          } else {
            reject(new Error('Your browser is not supported.'));
          }
        } else {
          reject(new Error('Please choose an image file.'));
        }
      });
    },

    change({ target }) {
      this.read(target.files).then((data) => {
        target.value = '';
        this.update(data);
      }).catch((e) => {
        target.value = '';
        this.alert(e);
      });
    },

    dragover(e) {
      e.preventDefault();
    },

    drop(e) {
      e.preventDefault();
      this.read(e.dataTransfer.files)
        .then((data) => {
          this.update(data);
        })
        .catch(this.alert);
    },

    alert(e) {
      window.alert(e && e.message ? e.message : e);
    },

    update(data) {
      Object.assign(this.data, data);
    },
  },
};
</script>

<style scoped>
.loader {
  display: table;
  height: 100%;
  overflow: hidden;
  width: 100%;

  & > p {
    color: #fff;
    position: fixed;
    z-index: 100;
    top: -5px;
    left: 45%;
  }
}

.browse {
  color: #0074d9;
  cursor: pointer;
  margin-left: .25rem;

  &:hover {
    color: #08f;
    text-decoration: underline;
  }
}
</style>
