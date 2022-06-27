<template>
  <div class="game">
    <div ref="game__wrapper" class="game__wrapper">
      <div
        :class="`game__click ${isClick && 'game__click--active'}`"
        :style="clickStyle"
      ></div>
      <ul class="game__result">
        <li
          v-for="item in resultList"
          :id="item.id"
          :ref="item.id"
          :key="item.id"
          :class="`game__resultItem game__resultItem--${item.area}`"
          @click="throttle(bet(item))"
        >
          {{ item.name }}
        </li>
      </ul>
      <ul class="game__options">
        <li
          :id="item.id"
          v-for="item in optionList"
          :key="item.id"
          class="game__optionItem"
        >
          {{ item.value }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "Game-Card",
  data() {
    return {
      isClick: false,
      clickStyle: {
        left: 0,
      },
      //BP 40, B 40, P 30, PP 30, T 40
      chipsList: [],
      resultList: [
        {
          id: "BP",
          name: "BP",
          value: 40,
          area: 33,
        },
        {
          id: "T",
          name: "T",
          value: 40,
          area: 33,
        },
        {
          id: "PP",
          name: "PP",
          value: 30,
          area: 33,
        },
        {
          id: "B",
          name: "B",
          value: 40,
          area: 50,
        },
        {
          id: "P",
          name: "P",
          value: 30,
          area: 50,
        },
      ],
      optionList: [
        {
          id: "10-1",
          value: 10,
        },
        {
          id: "10-2",
          value: 10,
        },
        {
          id: "20",
          value: 20,
        },
        {
          id: "30",
          value: 30,
        },
        {
          id: "40",
          value: 40,
        },
        {
          id: "50",
          value: 50,
        },
      ],
    };
  },
  mounted: function () {
    this.$el.addEventListener("click", this.mouseEffect);
    window.addEventListener("resize", this.resetChips);
  },
  beforeDestroy: function () {
    this.$el.removeEventListener("click", this.mouseEffect);
    window.removeEventListener("resize", this.resetChips);
  },
  methods: {
    resetChips: function () {
      const list = [...this.chipsList];
      if (list.length > 0) {
        list.forEach((item) => {
          let target = document.getElementById(item.start);
          let end = document.getElementById(item.end);
          const endLeft = end.offsetLeft + end.offsetWidth / 2 - 23.6;
          const endTop = end.offsetTop + end.offsetHeight / 2 - 23.6;
          target.style.left = endLeft + "px";
          target.style.top = endTop + "px";
        });
      }
    },
    mouseEffect: function () {
      const newBoolean = !this.isClick;
      const e = window.event;
      const mouseSet = {
        left: e.clientX - 35 + "px",
        top: e.clientY - 35 + "px",
      };
      this.clickStyle = mouseSet;
      if (newBoolean) {
        this.isClick = newBoolean;
        const timeoutId = setTimeout(() => {
          this.isClick = false;
          clearTimeout(timeoutId);
        }, 10);
      }
    },
    throttle: function (func, timeout = 250) {
      let last;
      let timer;
      return function () {
        const context = this;
        const args = arguments;
        const now = +new Date();
        if (last && now < last + timeout) {
          clearTimeout(timer);
          timer = setTimeout(function () {
            last = now;
            func.apply(context, args);
          }, timeout);
        } else {
          last = now;
          func.apply(context, args);
        }
      };
    },
    bet: function (item) {
      try {
        const target = this.$refs[item.id][0];
        const targetLeft = target.offsetLeft + target.offsetWidth / 2 - 23.6;
        const targetTop = target.offsetTop + target.offsetHeight / 2 - 23.6;
        const startPoint = document.getElementById(item.value);
        const startPointLeft =
          startPoint.offsetLeft + startPoint.offsetWidth / 2 - 23.6;
        const startPointTop =
          startPoint.offsetTop + startPoint.offsetHeight / 2 - 23.6;
        const wrapper = this.$refs["game__wrapper"];
        const newChips = document.createElement("div");
        newChips.className = "game__chips";
        newChips.style.left = startPointLeft + "px";
        newChips.style.top = startPointTop - 100 + "px";
        newChips.innerHTML = "chip";
        const chipsId = Date.now();
        newChips.id = chipsId;
        const newArr = [...this.chipsList];
        const chipsObj = {
          start: chipsId,
          end: item.id,
        };
        newArr.push(chipsObj);
        this.chipsList = newArr;
        wrapper.appendChild(newChips);
        const booleanLeft = newChips.offsetLeft < targetLeft;
        const intervalId = setInterval(function () {
          const ratio = Math.abs(
            (targetTop - newChips.offsetTop) /
              (targetLeft - newChips.offsetLeft)
          );
          const newLeft = booleanLeft
            ? newChips.offsetLeft + 1.5 < targetLeft
              ? newChips.offsetLeft + 1.5
              : targetLeft
            : newChips.offsetLeft - 1.5 > targetLeft
            ? newChips.offsetLeft - 1.5
            : targetLeft;
          const newTop =
            newChips.offsetTop - 1.5 * ratio > targetTop
              ? newChips.offsetTop - 1.5 * ratio
              : targetTop;

          newChips.style.left = newLeft + "px";
          newChips.style.top = newTop + "px";
          newLeft === targetLeft &&
            newTop === targetTop &&
            clearInterval(intervalId);
        }, 5);
      } catch (e) {
        console.log(e);
      }
    },
  },
};
</script>