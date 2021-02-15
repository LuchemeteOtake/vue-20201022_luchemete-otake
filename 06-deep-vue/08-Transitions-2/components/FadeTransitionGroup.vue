<script>
export default {
  name: 'FadeTransitionGroup',

  render(h) {
    const content = this.$slots.default || [];

    return h(
      'transition-group',
      {
        class: 'fade-list',
        props: {
          name: 'fade-list',
        },
        attrs: {
          ...this.$attrs,
        },
        on: {
          ...this.$listeners,
        },
      },
      content.map((vNode) => {
        const newClass = 'fade-list-item ' + (vNode.data.staticClass || '');
        vNode.data.staticClass = newClass.trim();
        return vNode;
      }),
    );
  },
};
</script>

<style scoped>
.fade-list {
  position: relative;
}

.fade-list >>> .fade-list-item {
  opacity: 1;
  transition: opacity 0.3s ease-out;
}

.fade-list >>> .fade-list-leave-active {
  position: absolute !important;
  left: 0;
  right: 0;
}

.fade-list >>> .fade-list-enter,
.fade-list >>> .fade-list-leave-to {
  opacity: 0;
}

.fade-list >>> .fade-list-move {
  transition: transform 0.3s;
}
</style>
