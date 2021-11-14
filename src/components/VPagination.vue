<script>
import { defineComponent, computed } from 'vue';
import VPage from './atoms/VPage.vue';
import IconPageFirst from '../assets/icons/page-first.svg';
import IconPageLast from '../assets/icons/page-last.svg';
import IconChevronLeft from '../assets/icons/chevron-left.svg';
import IconChevronRight from '../assets/icons/chevron-right.svg';

export default defineComponent({
  name: 'VPagination',
  components: {
    IconPageFirst,
    IconChevronLeft,
    IconChevronRight,
    IconPageLast,
    VPage,
  },
  props: {
    pages: {
      type: Number,
      default: 0,
    },
    rangeSize: {
      type: Number,
      default: 1,
    },
    modelValue: {
      type: Number,
      default: 0,
    },
    activeColor: {
      type: String,
      default: '#DCEDFF',
    },
    hideFirstButton: {
      type: Boolean,
      default: false,
    },
    hideLastButton: {
      type: Boolean,
      default: false,
    },
  },
  emits: ['update:modelValue'],

  setup(props, { emit }) {
    // pagination
    const pagination = computed(() => {
      const res = [];
      const minPaginationElems = 5 + props.rangeSize * 2;

      let rangeStart =
        props.pages <= minPaginationElems
          ? 1
          : props.modelValue - props.rangeSize;
      let rangeEnd =
        props.pages <= minPaginationElems
          ? props.pages
          : props.modelValue + props.rangeSize;

      rangeEnd = rangeEnd > props.pages ? props.pages : rangeEnd;
      rangeStart = rangeStart < 1 ? 1 : rangeStart;

      if (props.pages > minPaginationElems) {
        const isStartBoundaryReached = rangeStart - 1 < 3;
        const isEndBoundaryReached = props.pages - rangeEnd < 3;

        if (isStartBoundaryReached) {
          rangeEnd = minPaginationElems - 2;
          for (let i = 1; i < rangeStart; i++) {
            res.push(i);
          }
        } else {
          res.push(1);
          res.push(null);
        }

        if (isEndBoundaryReached) {
          rangeStart = props.pages - (minPaginationElems - 3);
          for (let i = rangeStart; i <= props.pages; i++) {
            res.push(i);
          }
        } else {
          for (let i = rangeStart; i <= rangeEnd; i++) {
            res.push(i);
          }
          res.push(null);
          res.push(props.pages);
        }
      } else {
        for (let i = rangeStart; i <= rangeEnd; i++) {
          res.push(i);
        }
      }

      return res;
    });

    function updatePageHandler(params) {
      emit('update:modelValue', params);
    }

    // controls
    const isPrevControlsActive = computed(() => {
      return props.modelValue > 1;
    });
    const isNextControlsActive = computed(() => {
      return props.modelValue < props.pages;
    });

    function goToFirst() {
      if (isPrevControlsActive.value) {
        emit('update:modelValue', 1);
      }
    }
    function goToPrev() {
      if (isPrevControlsActive.value) {
        emit('update:modelValue', props.modelValue - 1);
      }
    }

    function goToLast() {
      if (isNextControlsActive.value) {
        emit('update:modelValue', props.pages);
      }
    }
    function goToNext() {
      if (isNextControlsActive.value) {
        emit('update:modelValue', props.modelValue + 1);
      }
    }

    return {
      pagination,
      updatePageHandler,
      isPrevControlsActive,
      isNextControlsActive,
      goToFirst,
      goToLast,
      goToPrev,
      goToNext,
    };
  },
});
</script>

<template>
  <ul class="Pagination">
    <li v-if="!hideFirstButton" class="PaginationControl">
      <icon-page-first
        class="Control"
        :class="{ 'Control-active': isPrevControlsActive }"
        @click="goToFirst"
      />
    </li>
    <li class="PaginationControl">
      <icon-chevron-left
        class="Control"
        :class="{ 'Control-active': isPrevControlsActive }"
        @click="goToPrev"
      />
    </li>
    <v-page
      v-for="page in pagination"
      :key="`pagination-page-${page}`"
      :page="page"
      :current="modelValue"
      :active-color="activeColor"
      @update="updatePageHandler"
    />
    <li class="PaginationControl">
      <icon-chevron-right
        class="Control"
        :class="{ 'Control-active': isNextControlsActive }"
        @click="goToNext"
      />
    </li>
    <li v-if="!hideLastButton" class="PaginationControl">
      <icon-page-last
        class="Control"
        :class="{ 'Control-active': isNextControlsActive }"
        @click="goToLast"
      />
    </li>
  </ul>
</template>



<style  lang="scss" scoped>
.Pagination {
  display: flex;
  flex-flow: row;
  flex-wrap: wrap;
  align-items: center;
  margin: 0;
  padding: 0;
  list-style-type: none;
}

.PaginationControl {
  display: flex;
  align-items: center;
}

.Control {
  position: relative;
  display: block;
  width: 18px;
  height: 18px;
  margin: 0 2px;
  fill: #bbbbbb;

  &-active {
    fill: #333333;
    cursor: pointer;
    transition: fill 0.2s ease-in-out;

    &:hover {
      fill: #000000;
      transition: fill 0.2s ease-in-out;
    }
  }
}
</style>
