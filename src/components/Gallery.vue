<template>
  <div ref="myButton" class="gallery">
    <div class="gallery-strip">
      <a href="#" :class="['oval', 'prev', { disabled: !isPrevPossible }]" @click.prevent="goPrev">
        <span class="progress-button">&lsaquo;</span>
      </a>
      <div class="filler" />
      <div class="card-line">
        <span
          v-for="(item, index) in items"
          :key="'card_' + index"
          :style="{ display: displayState(index) }"
          :class="['key-image-span', { active: isActive(index) }]"
        >
          <card :data="item" :width="cardWidth" :height="cardHeight" :show-card-details="showCardDetails" />
        </span>
      </div>
      <div class="filler" />
      <a href="#" :class="['oval', 'next', { disabled: !isNextPossible }]" @click.prevent="goNext">
        <span class="progress-button">&rsaquo;</span>
      </a>
    </div>
    <div v-if="showIndicatorBar" class="bottom-spacer" />
    <index-indicator v-if="showIndicatorBar" :count="itemCount" :current="currentIndex" @clicked="indicatorClicked" />
  </div>
</template>

<script>
import IndexIndicator from './IndexIndicator'
import Card from './Card'

function convertRemToPixels(rem) {
  if (typeof window !== 'undefined') {
    return rem * parseFloat(window.getComputedStyle(document.documentElement).fontSize)
  }
  return rem * 16
}

export default {
  name: 'Gallery',
  components: { IndexIndicator, Card },
  props: {
    items: {
      type: Array,
      default: () => {
        return []
      },
    },
    maxWidth: {
      type: Number,
      required: true,
    },
    cardWidth: {
      type: Number,
      default: 13.8,
    },
    showIndicatorBar: {
      type: Boolean,
      default: true,
    },
    highlightActive: {
      type: Boolean,
      default: true,
    },
    showCardDetails: {
      type: Boolean,
      default: true,
    },
    metaData: {
      type: Object,
      default: () => {
        return {
          datasetVersion: -1,
          datasetId: -1,
        }
      },
    },
    description: {
      type: String,
      default: '',
    },
  },
  data() {
    return {
      count: 0,
      currentIndex: 0,
      controlHeight: 2,
      controlWidth: 2,
    }
  },
  computed: {
    itemCount() {
      return this.items.length
    },
    isPrevPossible() {
      return this.currentIndex > 0
    },
    isNextPossible() {
      return this.currentIndex < this.itemCount - 1
    },
    cardHeight() {
      return 0.78 * this.cardWidth
    },
    cardLineWidth() {
      const cardSpacing = 0.25
      return this.itemCount * (this.cardWidth + cardSpacing) - cardSpacing
    },
    numberOfItemsVisible() {
      // The maximum width we are allowed minus two buttons for next and previous
      // divided by the width of a card.
      // const n = this.itemCount - 1
      const cardSpacingPx = convertRemToPixels(0.5)
      const buttonPx = convertRemToPixels(2)
      const cardWidthPx = convertRemToPixels(this.cardWidth)
      const cardItems = (this.maxWidth - 2 * buttonPx - 2 * cardSpacingPx) / (1.1 * cardWidthPx)
      return Math.floor(cardItems)
    },
  },
  methods: {
    isActive(index) {
      return this.currentIndex === index && this.highlightActive
    },
    goNext() {
      this.currentIndex += 1
    },
    goPrev() {
      this.currentIndex -= 1
    },
    displayState(index) {
      const oddImagesVisible = this.numberOfItemsVisible % 2 === 1
      let halfVisible = this.numberOfItemsVisible / 2
      if (oddImagesVisible) {
        halfVisible = (this.numberOfItemsVisible - 1) / 2
      }
      let rawIndicies = [this.currentIndex]
      for (let i = 1; i <= halfVisible; i++) {
        rawIndicies.push(this.currentIndex + i)
        rawIndicies.push(this.currentIndex - i)
      }

      if (!oddImagesVisible) {
        rawIndicies.pop()
      }
      let indecies = []
      for (let v of rawIndicies) {
        if (v < 0) {
          indecies.push(v + this.numberOfItemsVisible)
        } else if (v >= this.itemCount) {
          indecies.push(v - this.numberOfItemsVisible)
        } else {
          indecies.push(v)
        }
      }

      return indecies.includes(index) ? undefined : 'none'
    },
    indicatorClicked(index) {
      if (this.currentIndex !== index) {
        this.currentIndex = index
      }
    },
  },
}
</script>

<style scoped>
.oval {
  width: 2rem;
  height: 2rem;
  line-height: 2rem;
  box-shadow: 0 0.125rem 0.25rem 0 rgba(0, 0, 0, 0.25);
  border: solid 1px var(--pale-grey);
  background-color: #ffffff;
  border-radius: 1rem;
  display: flex;
  justify-content: center;
  user-select: none;
}

.gallery-strip,
.card-line {
  display: flex;
  flex-wrap: nowrap;
  justify-content: space-around;
  align-items: center;
}

.card-line {
  flex-grow: 2;
}

.progress-button {
  font-size: 1.5rem;
  font-weight: bold;
}

.bottom-spacer {
  min-height: 4rem;
}

.filler {
  flex-grow: 1;
}

.key-image-span.active {
  transform: scale(1.1);
}

a.prev:not(.underline),
a.next:not(.underline) {
  text-decoration: none;
}

.disabled {
  opacity: 0.5;
  pointer-events: none;
}
</style>
