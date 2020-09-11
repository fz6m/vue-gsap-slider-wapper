<template>
  <div class="hidden">
    <div class="px-4" ref="parent">
      <div
        v-for="(item, index) in images"
        :class="'slide-' + (index + 1)"
        :key="index"
      >
        <div class="full" @click="distribution(index)">
          <div class="full-p">
            <img :src="item.src" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { gsap } from "gsap";
export default {
  data() {
    return {
      lock: false,
      current: 2,
    };
  },
  props: {
    images: {
      default: () => [
        {
          src:
            "https://res.cloudinary.com/nuxt/image/upload/w_1200,h_900,f_auto,q_auto/vue-telemetry/6122c039fb93963f821a703609bb0b0f.jpg",
        },
        {
          src:
            "https://res.cloudinary.com/nuxt/image/upload/w_1200,h_900,f_auto,q_auto/vue-telemetry/8dd10d8b8150cfbc93a4e67994582ed8.jpg",
        },
        {
          src:
            "https://res.cloudinary.com/nuxt/image/upload/w_1200,h_900,f_auto,q_auto/vue-telemetry/a5d4be7e33f3dac04b225c201587bae9.jpg",
        },
        {
          src:
            "https://res.cloudinary.com/nuxt/image/upload/w_1200,h_900,f_auto,q_auto/vue-telemetry/cdfd5015969b07bd85dfa02069b99081.jpg",
        },
        {
          src:
            "https://res.cloudinary.com/nuxt/image/upload/w_1200,h_900,f_auto,q_auto/vue-telemetry/00f460039994ada6b9faf388b143cb05.jpg",
        },
      ],
      type: Array,
    },
  },
  mounted() {
    gsap.from(this.$refs.parent.childNodes, 0.5, {
      autoAlpha: 0,
      scale: 0,
      stagger: {
        from: "center",
        axis: "x",
        amount: 0.3,
      },
      transformOrigin: "center center",
    });
    this.noDrag();
  },
  methods: {
    noDrag() {
      document.getElementsByTagName("img").forEach((item) => {
        item.onmousedown = (e) => {
          e.preventDefault();
        };
      });
    },
    distribution(index) {
      if (index == 2 && !this.lock) {
        this.$emit("click-img", this.current);
      } else {
        this.slider(index + 1);
      }
    },
    slider(index) {
      if (this.lock) return;
      this.lock = true;
      const reverse = index < 3 ? false : true;
      const full = document.getElementsByClassName("full");
      const animationTask = [];
      full.forEach((item, originI) => {
        let left = null;
        let right = null;
        if (reverse) {
          left = originI;
          right = originI == full.length - 1 ? 0 : originI + 1;
        } else {
          left = originI == 0 ? full.length - 1 : originI - 1;
          right = originI;
        }
        animationTask.push(
          this.animation(
            full[left].firstElementChild,
            full[right].firstElementChild,
            reverse
          )
        );
      });
      Promise.all(animationTask).then(() => {
        // 先改变 current
        if (reverse) {
          this.current = this.current + 1 == full.length ? 0 : this.current + 1;
        } else {
          this.current =
            this.current - 1 < 0 ? full.length - 1 : this.current - 1;
        }
        this.lock = false;
      });
    },
    animation(slideLeft, slideRight, reverse) {
      const _this = this
      return new Promise((resolve, reject) => {
        try {
          // 在视图中后面，在 node 中前面的节点
          const beforeNode = reverse ? slideLeft : slideRight;
          // 在视图中前面，在 node 中后面的节点
          const afterNode = reverse ? slideRight : slideLeft;
          const afterNodeCopy = afterNode.cloneNode(true);
          afterNodeCopy.style.transform = reverse
            ? "translateX(100%)"
            : "translateX(-100%)";
          beforeNode.parentNode.appendChild(afterNodeCopy);
          gsap.to(afterNodeCopy, 1, {
            xPercent: reverse ? -100 : 100,
            ease: "expo.inOut",
            onStart: function() {
              _this.noDrag();
              gsap.to(beforeNode, 1, {
                scale: 1.15,
                ease: "expo.inOut",
              });
            },
            onComplete: function() {
              beforeNode.parentNode.removeChild(beforeNode);
              afterNodeCopy.style.transform = "";
              resolve();
            },
          });
        } catch (e) {
          reject(e);
        }
      });
    },
  },
};
</script>

<style lang="scss">
// body {
//   margin: 0;
//   padding: 0;
// }
</style>

<style lang="scss" scoped>
*,
*::after,
*::after {
  box-sizing: border-box;
  border: 0 solid;
}
@mixin absolute() {
  position: absolute;
  top: 0;
}
@mixin flex() {
  display: flex;
  justify-content: center;
  align-items: center;
}
@mixin h-w-full() {
  height: 100%;
  width: 100%;
}
@mixin i-flex() {
  display: inline-flex;
  width: 50% !important;
}
.hidden {
  overflow: hidden;
  width: 100%;
}
.px-4 {
  position: relative;
  margin: 6rem 0;
  padding-left: 1rem;
  padding-right: 1rem;
  @include flex;
  .slide {
    // z-index
    &-1,
    &-5 {
      transform: scale(0.5);
      z-index: 8;
    }
    &-2,
    &-4 {
      transform: scale(0.75);
      z-index: 9;
    }
    &-3 {
      z-index: 10;
    }
    // position
    @media (min-width: 834px) {
      @for $i from 1 through 5 {
        &-#{$i} {
          @include i-flex;
        }
      }
    }
    &-1,
    &-2,
    &-4,
    &-5 {
      @include absolute;
    }
    &-3 {
      position: relative;
      width: 100vw;
    }
    // absolute
    &-1 {
      left: -15%;
    }
    &-2 {
      left: 0;
    }
    &-4 {
      right: 0;
    }
    &-5 {
      right: -15%;
    }
  }

  .full {
    @include h-w-full;
    box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1),
      0 10px 10px -5px rgba(0, 0, 0, 0.04);
    border-radius: 0.875rem;
    position: relative;
    padding-bottom: 75%;
    overflow: hidden;
    pointer-events: unset;
    &-p {
      left: 0;
      @include absolute;
      @include h-w-full;
      cursor: pointer;

      img {
        @include h-w-full;
      }
    }
  }
}
</style>
