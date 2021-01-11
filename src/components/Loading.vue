<template>
  <div>
    loading page

    <div>{{ this.uploadPercentage }}</div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      resources: [
        "http://p2.qhimg.com/t01ed1438874f940dc0.jpg",
        "http://p9.qhimg.com/t01b4ff03b72c7dc6c7.jpg",
        "http://p2.qhimg.com/t01dd90dfbec92074d0.jpg",
        "http://p7.qhimg.com/t01cfec6d87cde457c5.jpg",
        "http://p9.qhimg.com/t01943ced462da67833.jpg",
        "http://p0.qhimg.com/t01943ced462da67833.jpg",
        "http://p6.qhimg.com/t01aa15a7ba7ccb49a7.jpg",
        "http://p8.qhimg.com/t010f1e8badf1134376.jpg",
        "http://p8.qhimg.com/t01cf37ea915533a032.jpg",
        "http://p3.qhimg.com/t0193d8a3963e1803e9.jpg",
        "http://p3.qhimg.com/t01cd6a4d4b4bd4457b.jpg",
      ],
      display: [
        "http://p2.qhimg.com/t01ed1438874f940dc0.jpg",
        "http://p9.qhimg.com/t01b4ff03b72c7dc6c7.jpg",
        "http://p2.qhimg.com/t01dd90dfbec92074d0.jpg",
        "http://p7.qhimg.com/t01cfec6d87cde457c5.jpg",
        "http://p9.qhimg.com/t01943ced462da67833.jpg",
        "http://p0.qhimg.com/t01943ced462da67833.jpg",
        "http://p6.qhimg.com/t01aa15a7ba7ccb49a7.jpg",
        "http://p8.qhimg.com/t010f1e8badf1134376.jpg",
        "http://p8.qhimg.com/t01cf37ea915533a032.jpg",
        "http://p3.qhimg.com/t0193d8a3963e1803e9.jpg",
        "http://p3.qhimg.com/t01cd6a4d4b4bd4457b.jpg",
      ],
      dataList: [
        {
          totalSize: 0,
          percentage: 0,
        },
      ],
    };
  },
  async created() {
    let data = require("../assets/data.json");
    // this.loaded(data);
  },
  mounted() {
    this.resources.forEach((img, index) => {
      this.dataList.push({
        totalSize: 0,
        percentage: 0,
      });
    });

    this.resources.forEach((img, index) => {
      var xhr = new XMLHttpRequest();
      // console.log(xhr.getResponseHeader("Content-Type"));
      xhr.open("GET", img);
      xhr.onprogress = this.createProgressHandler(this.dataList[index]);
      xhr.send();
    });
  },
  computed: {
    uploadPercentage() {
      if (!this.dataList.length) return 0;
      const total = this.dataList
        .map((item) => item.totalSize)
        .reduce((acc, cur) => {
          return acc + cur;
        });
      console.log("total", total);
      const loaded = this.dataList
        .map((item) => item.totalSize * item.percentage)
        .reduce((acc, cur) => acc + cur);
      // console.log(loaded);

      return parseInt((loaded / total).toFixed(2));
    },
  },
  methods: {
    createProgressHandler(item) {
      return (e) => {
        item.totalSize = e.total;
        item.percentage = parseInt(String((e.loaded / e.total) * 100));
      };
    },

    loaded(data) {
      this.$emit("loaded", data);
    },
    request() {},
  },
};
</script>

<style>
</style>