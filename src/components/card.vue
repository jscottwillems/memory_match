<template>
  <div
    class="card-container"
    :class="value.selected || value.matched ? 'card-selected' : ''"
    @click="!value.matched ? $emit('selected', value) : ''"
  >
    <div class="card-container-inner">
      <div
        class="card card-front"
        :style="{
          backgroundImage: 'url(' + require(`@/assets/img/card-back.jpg`) + ')',
        }"
      ></div>
      <div
        class="card card-back"
        :style="{
          backgroundImage:
            'url(' +
            require(`@/assets/img/${getBackImage(value.value)}.jpg`) +
            ')',
        }"
      >
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    value: {
      type: Object,
      default: () => {},
    },
  },
  methods: {
    getBackImage(value) {
      switch (value) {
        case 1:
          return `ace`;
        case 2:
          return `two`;
        case 3:
          return `three`;
        case 4:
          return `four`;
        case 5:
          return `five`;
        case 6:
          return `six`;
        default:
          return `ace`;
      }
    },
  },
};
</script>

<style lang="scss">
.card-container {
  display: inline-block;
  width: 115px;
  height: 167.5px;
  background-color: transparent;
  margin-bottom: 0.9rem;
  cursor: pointer;
  perspective: 1000px;
  transition: all 0.1s linear;

  &:hover:not(.card-selected) {
    transform: translateY(2px);
  }
}

.card-container-inner {
  position: relative;
  width: 100%;
  height: 100%;
  text-align: center;
  transition: transform 0.8s;
  transform-style: preserve-3d;
  border-radius: 3px;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);

  &:hover {
    box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.2),
      0 1.5px 5px 0 rgba(0, 0, 0, 0.19);
  }
}

.card-selected .card-container-inner {
  transform: rotateY(180deg);

  &:hover {
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
  }
}

.card-front,
.card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  -webkit-backface-visibility: hidden;
  backface-visibility: hidden;
  border-radius: 3px;
}

.card-front,
.card-back {
  background-position: center;
  background-repeat: no-repeat;
  background-size: contain;
}

.card-back {
  background-color: white;
  color: black;
  transform: rotateY(180deg);
}

@media only screen and (max-width: 560px) {
  .card-container {
    height: 112px;
    width: 80px;
  }
}
</style>