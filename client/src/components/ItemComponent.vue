<template>
  <div
    :id="containerId"
    class="swiper-container"
    v-bind:key="item._id">
    <div class="swiper-wrapper">
      <v-list-item
        v-if="!item.checked"
        class="swiper-slide swiper-slide-left"></v-list-item>
      <v-list-item
        v-bind:class="{ checked: item.checked }"
        class="swiper-slide"
        @click="onClick"
        @dblclick="onDoubleClick">
        <v-list-item-avatar>
          <v-icon v-if="!item.checked">mdi-checkbox-blank-circle-outline</v-icon>
          <v-icon v-if="item.checked">mdi-checkbox-marked-circle-outline</v-icon>
        </v-list-item-avatar>

        <v-list-item-content>
          <v-list-item-title>{{ item.name }}</v-list-item-title>
          <v-list-item-subtitle v-if="item.qty">{{ item.qty }}</v-list-item-subtitle>
          <v-list-item-subtitle v-else class="empty-qty">{{ $t('item.empty-qty-label') }}</v-list-item-subtitle>
          <v-list-item-subtitle>{{ item.details }}</v-list-item-subtitle>
        </v-list-item-content>

        <v-list-item-action class="d-none d-sm-flex">
          <v-btn @click="onEditClick"
                 icon
                 :aria-label="$t('edit')">
            <v-icon color="primary">mdi-pencil</v-icon>
          </v-btn>
        </v-list-item-action>
      </v-list-item>
      <v-list-item
        v-if="item.checked"
        class="swiper-slide swiper-slide-right"></v-list-item>
    </div>
  </div>
</template>

<script>
import 'swiper/css'
import { Swiper } from 'swiper/core'

export default {
  name: 'ItemComponent',
  components: {
  },
  props: [
    'item'
  ],
  data: function() {
    return {
      'containerId': `item-${this._uid}`,
      'swiper': null
    }
  },
  watch: {
    'item.checked': function() {
      this.initSwiper()
    }
  },
  mounted() {
    const self = this
    this.$nextTick(function() {
      self.initSwiper()
    })
  },
  methods: {
    initSwiper() {
      const self = this
      if (this.swiper) {
        this.swiper.destroy(true)
      }

      const el = '#' + this.containerId
      this.swiper = new Swiper(el, {
        initialSlide: this.item.checked ? 0 : 1,
        resistanceRatio: 0,
        speed: 150
      })

      this.swiper.on('transitionEnd', function() {
      if (!self.item.checked && this.activeIndex === 0) {
        self.$emit('swipeOutRight', self.item, self)
      }
      if (self.item.checked && this.activeIndex === 1) {
        self.$emit('swipeOutLeft', self.item, self)
      }
    })
    },
    onClick() {
      this.$emit('click', this.item)
    },
    onDoubleClick() {
      this.$emit('doubleClick', this.item)
    },
    onEditClick() {
      this.$emit('editClick', this.item)
    }
  },
}
</script>

<style lang="scss" scoped>
.v-list-item {
  user-select: none;

  /*
   * Just to make "text-overflow: ellipsis" work correctly with small devices and long texts.
   * See https://chenhuijing.com/blog/flexbox-and-padding/
   */
  &::after {
    content: '';
    padding: 0 16px; /* = .v-list-item margin-left (and still the right margin will be 8px less than the left's */
  }

  &.checked {
    opacity: .65;
  }

  .v-list-item__subtitle.empty-qty {
    display: none;
  }

  .v-list-item__action {
    margin-right: 3rem;
  }
}
.swiper-container {
  overflow-x: hidden;
}
.swiper-slide {
  /* This one made me lose a huge amount of time... */
  flex-shrink: 0 !important;
}
.swiper-slide-left,
.swiper-slide-right {
  padding: 0;
}

</style>
