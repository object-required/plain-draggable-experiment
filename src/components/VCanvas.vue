<template>
  <div
    class="v-canvas polkadot"
    ref="canvas"
    :style="{
      width: width + 'px',
      height: height + 'px',
    }"
  >
    <slot></slot>
  </div>
</template>

<script>
import PlainDraggable from "plain-draggable";

const CANVAS_PADDING = 100;

export default {
  props: {
    targetWidth: Number,
    targetHeight: Number,
  },

  data() {
    return {
      draggables: [],
      width: 0,
      height: 0,
    };
  },

  created() {
    this.width = this.targetWidth;
    this.height = this.targetHeight;
  },

  mounted() {
    this.draggables = [...this.$refs.canvas.querySelectorAll("*")].map(
      (el) =>
        new PlainDraggable(el, {
          onDragEnd: this.resize,
          autoScroll: this.$parent.$el,
        })
    );

    this.$nextTick(this.resize)
  },

  methods: {
    repositionElements() {
      for (const draggable of this.draggables) {
        draggable.position();
      }
    },

    translateElements(dx = 0, dy = 0, except = null) {
      for (const { element } of this.draggables) {
        if (element === except) continue;

        const m = /translate\((\d+)px,\s*(\d+)px\)/i.exec(
          element.style.transform
        );
        const x = parseInt(m ? m[1] : 0);
        const y = parseInt(m ? m[2] : 0);

        element.style.transform = `translate(${x + dx}px, ${y + dy}px)`;
      }
    },

    computeVirtualCoordinates() {
      const ws = this.$parent.$el;

      return this.draggables.map((draggable) => {
        const el = draggable.element.getBoundingClientRect();

        return {
          draggable,
          element: draggable.element,
          top: Math.round(el.top + ws.scrollTop - ws.offsetTop),
          left: Math.round(el.left + ws.scrollLeft - ws.offsetLeft),
          bottom: Math.round(el.bottom + ws.scrollTop - ws.offsetTop),
          right: Math.round(el.right + ws.scrollLeft - ws.offsetLeft),
        };
      });
    },

    findCorners(elements) {
      const corners = {
        top: null,
        left: null,
        bottom: null,
        right: null,
      };

      for (const element of elements) {
        corners.top = Math.min(corners.top ?? Infinity, element.top);
        corners.left = Math.min(corners.left ?? Infinity, element.left);
        corners.right = Math.max(corners.right ?? -Infinity, element.right);
        corners.bottom = Math.max(corners.bottom ?? -Infinity, element.bottom);
      }

      return corners;
    },

    resize() {
      const elements = this.computeVirtualCoordinates();
      const corners = this.findCorners(elements);

      // 01 Shift elements to the topleft corner

      const dx = CANVAS_PADDING - corners.left;
      const dy = CANVAS_PADDING - corners.top;

      this.translateElements(dx, dy);

      // 02 Adjust dimensions of the canvas

      this.width = Math.ceil(
        Math.max(this.targetWidth, corners.right - corners.left) +
          CANVAS_PADDING * 2
      );

      this.height = Math.ceil(
        Math.max(this.targetHeight, corners.bottom - corners.top) +
          CANVAS_PADDING * 2
      );

      // 03 Account for new canvas size

      this.$nextTick(() => {
        this.repositionElements();
      });
    },
  },
};
</script>
