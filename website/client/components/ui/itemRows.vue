<template lang="pug">
  .item-rows
    div.items(v-resize="500", @resized="currentWidth = $event.width")
      template(v-for="item in itemsToShow(showAll)")
        slot(
          name="item",
          :item="item",
        )

    div(v-if="items.length === 0")
      p(v-once) {{ noItemsLabel }}

    .btn-flat.btn-show-more(
      @click="showAll = !showAll",
      v-if="items.length > itemsPerRow()"
    ) {{ showAll ? $t('showLess') : $t('showMore') }}

    div.fill-height(v-else)

</template>

<style lang="scss" scoped>
  .fill-height {
    height: 38px; // button + margin + padding
  }
</style>

<script>
  import ResizeDirective from 'client/directives/resize.directive';

  import _take from 'lodash/take';
  import _drop from 'lodash/drop';

  export default {
    directives: {
      resize: ResizeDirective,
    },
    data () {
      return {
        currentWidth: 0,
        currentPage: 0,

        showAll: false,
      };
    },
    methods: {
      itemsToShow (showAll) {
        let itemsPerRow = this.itemsPerRow();
        let rowsToShow = showAll ? Math.ceil(this.items.length / itemsPerRow) : 1;
        let result = [];

        for (let i = 0; i < rowsToShow; i++) {
          let skipped = _drop(this.items, i * itemsPerRow);
          let row = _take(skipped, itemsPerRow);
          result = result.concat(row);
        }

        return result;
      },
      itemsPerRow () {
        return Math.floor(this.currentWidth / (this.itemWidth + this.itemMargin));
      },
    },
    props: {
      items: {
        type: Array,
      },
      itemWidth: {
        type: Number,
      },
      itemMargin: {
        type: Number,
      },
      noItemsLabel: {
        type: String,
      },
    },
  };
</script>
