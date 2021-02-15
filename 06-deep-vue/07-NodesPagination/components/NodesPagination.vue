<script>
export default {
  name: 'NodesPagination',

  props: {
    page: Number,
    perPage: Number,
  },

  render(h) {
    const content = this.$slots.default || [];

    const start = (this.$props.page - 1) * this.$props.perPage;
    const end = start + this.$props.perPage;

    return h('div', content.slice(start, end));
  },
};
</script>

<style scoped></style>
