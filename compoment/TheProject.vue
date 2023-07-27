<script setup>
const demo = [
  {
    name: "基于vitepress的博客搭建",
    description:
      "使用vitepress基于vue3和vite搭建一个简单的个人博客页面并且同步到Git Pages",
    tags: ["Vue3", "Vite", "Vitepress", "博客"],
    link: "https://gitee.com/zhangshuai6/zhangshuai6",
    pre: " http://zhangshuai6.gitee.io",
    href: "/articles/other/vitepress/01.vitepress的搭建",
    img: "/vitepress.jpg",
  },
  {
    name: "用JS写一个群友力扣排行榜",
    description:
      "使用Vue和node实现群友排行榜,配置nginx跨域调用leetcode官网api获取数据",
    tags: ["Node.js", "Vue3", "niginx"],
    pre: "http://47.97.158.11:8692/leetocde/#/home",
    link: "https://github.com/aiai0603/leetcode_rank",
    href: "",
    img: "/rank.jpg",
  },
  {
    name: "用Electron实现简单md编辑器",
    description: "使用Electron和Vue3实现一个纯前端的markdown格式编辑器",
    tags: ["Electron", "Vue3", "Node.js"],
    pre: "",
    link: "https://github.com/aiai0603/YSU-MD",
    href: "",
    img: "/md-editor.jpg",
  },
  {
    name: "基于React的会议室管理平台",
    description: "使用React和Springboot和ESP32实现的物联网会议室屏幕管理系统",
    tags: ["Raect", "物联网", "Springboot"],
    pre: "http://47.97.158.11:8087/card/#/login",
    link: "https://github.com/aiai0603/bysg",
    href: "",
    img: "/react.jpg",
  },
  {
    name: "基于Node和Jquery和游戏平台",
    description: "使用Node.js和Jquery的编写小游戏平台,包含5款游戏代码",
    tags: ["Node.js", "Jquery"],
    pre: "http://47.97.158.11:8087/nodejs/html/",
    link: "https://github.com/aiai0603/nodejs_work",
    href: "",
    img: "/game.jpg",
  },
  {
    name: "原生Web的小清新风格博客页面",
    description: "仅使用html5+css+js的纯前端小清新风格博客",
    tags: ["js", "html", "css"],
    pre: "",
    link: "https://github.com/aiai0603/zs_work/tree/master/class/web/31801150-%E5%BC%A0%E5%B8%85-%E8%AE%A1%E7%AE%97%E6%9C%BA1803%E5%A4%A7%E4%BD%9C%E4%B8%9A/%E5%A4%A7%E4%BD%9C%E4%B8%9A",
    href: "",
    img: "/web.png",
  },
  {
    name: "Seven-bit-ui组件库",
    description: "基于 Vue3+TS+Less 实现的轻量级组件库",
    tags: ["Vue3", "Vite3", "Less", "Vitepress"],
    pre: "https://aiai0603.github.io/",
    link: "https://github.com/aiai0603/seven-bit-ui",
    href: "",
    img: "/ui.png",
  },
  {
    name: "基于微信小程序的复诊配药平台",
    description: "基于 微信小程序+Springboot+Vue3 实现的复诊配药平台",
    tags: ["Vue3", "微信小程序", "Springboot"],
    pre: "",
    link: "https://github.com/aiai0603/doctor",
    href: "",
    img: "/uniapp.jpg",
  },
  {
    name: "基于Uni-app的奔驰购车APP",
    description: "基于 Uni-app+Springboot 实现的复诊配药平台",
    tags: ["Vue2", "Uni-app", "Springboot"],
    pre: "",
    link: "https://github.com/aiai0603/benzDemo",
    href: "",
    img: "/benz.jpg",
  },
  {
    name: "基于Cesium的秦皇岛模拟飞行",
    description:
      "基于 Cesium 和 Vue3 的导入秦皇岛建筑白膜数据搭建的模式实景飞行项目",
    tags: ["Vue3", "Cesium"],
    pre: "http://47.97.158.11:8692/cesium/",
    link: "",
    href: "",
    img: "/cesium.png",
  },
  {
    name: "基于.NET的奔驰品牌官网",
    description:
      "基于 .NET 搭建的部分奔驰品牌官网，链接sqlserver数据库，并且使用 .NET Core 和 Vue3 搭建网页元素管理平台",
    tags: ["Vue3", ".NET", ".NET Core"],
    pre: "http://192.144.239.68:88/index/index.aspx",
    link: "",
    href: "",
    img: "/asp.png",
  },
];

const online = [
  {
    name: "openEulur官网搭建项目组",
    description:
      "使用vitepress基于vue3和vite搭建openEulur项目官方网站，支持多语言多主题切换",
    tags: ["Vue3", "Vite", "Vitepress", "官网"],
    link: "https://gitee.com/zhangshuai6/openEuler-portal",
    pre: "https://www.openeuler.org/zh/",
    href: "",
    img: "/euler.png",
  },
  {
    name: "openEulur文档抓虫官方DEMO",
    description: "使用Jquery编写的文档有奖捉虫的演示DEMO",
    tags: ["Jquery", "html", "Javascript"],
    link: "https://gitee.com/zhangshuai6/docfork",
    pre: "",
    href: "",
    img: "/bugs.png",
  },
];
</script>

<template>
  <div class="container">
    <div class="title">项目分享</div>
    <div class="desc">
      分享个人编写的开源前端项目和实用工具,包含项目在线地址，开源地址和教程，开源项目可以任意取用(禁止用于学术不端等途径)，欢迎大家为作者寻找项目问题和提出PR，任何问题请联系作者
      <a href="mailto:1016868503@qq.com">邮箱</a>
    </div>
    <div class="list">
      <div class="card" v-for="item in demo" :key="item.name">
        <div class="img-pic">
          <div class="img-shadow">
            <div class="card-desc">{{ item.description }}</div>
          </div>
          <img :src="item.img" :alt="item.name" />
        </div>
        <div class="card-word">
          <div class="card-name">{{ item.name }}</div>

          <div class="card-tag">
            <div v-for="tag in item.tags" :key="tag" class="card-tag-item">
              {{ tag }}
            </div>
          </div>
          <div class="card-footer">
            <img src="/text.png" alt="" class="card-footer-img" />

            <a :href="item.href" v-if="item.href" class="card-footer-word has"
              >文档</a
            >
            <div v-else class="card-footer-word">暂无</div>

            <img src="/github.png" alt="" class="card-footer-img" />

            <a :href="item.link" v-if="item.link" class="card-footer-word has"
              >Git</a
            >
            <div v-else class="card-footer-word">暂无</div>

            <img src="/online.png" alt="" class="card-footer-img" />

            <a :href="item.pre" v-if="item.pre" class="card-footer-word has"
              >预览</a
            >
            <div v-else class="card-footer-word">暂无</div>
          </div>
        </div>
      </div>
    </div>

    <div class="title divide">一起开源</div>
    <div class="desc">
      分享作者参与过的开源项目，欢迎和作者一起参与开源项目实践，充实你的简历，参与各类开发竞赛，字学计划，开源之夏，开源社区，以及日常项目外包等，任何问题请联系作者
      <a href="mailto:1016868503@qq.com">邮箱</a>
    </div>
    <div class="list">
      <div class="card" v-for="item in online" :key="item.name">
        <div class="img-pic">
          <div class="img-shadow">
            <div class="card-desc">{{ item.description }}</div>
          </div>
          <img :src="item.img" :alt="item.name" />
        </div>
        <div class="card-word">
          <div class="card-name">{{ item.name }}</div>

          <div class="card-tag">
            <div v-for="tag in item.tags" :key="tag" class="card-tag-item">
              {{ tag }}
            </div>
          </div>
          <div class="card-footer">
            <img src="/text.png" alt="" class="card-footer-img" />

            <a :href="item.href" v-if="item.href" class="card-footer-word has"
              >文档</a
            >
            <div v-else class="card-footer-word">暂无</div>

            <img src="/github.png" alt="" class="card-footer-img" />

            <a :href="item.link" v-if="item.link" class="card-footer-word has"
              >Git</a
            >
            <div v-else class="card-footer-word">暂无</div>

            <img src="/online.png" alt="" class="card-footer-img" />

            <a :href="item.pre" v-if="item.pre" class="card-footer-word has"
              >预览</a
            >
            <div v-else class="card-footer-word">暂无</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="less" scoped>
.divide {
  margin-top: 64px;
}
.desc {
  font-size: 18px;
  line-height: 28px;
  font-weight: 300;
  margin-bottom: 48px;
  a {
    color: var(--vp-c-brand);
  }
}
.title {
  display: flex;
  flex-flow: row;
  justify-content: center;
  line-height: 60px;
  width: 100%;
  font-size: 48px;
  font-weight: 300;
  margin-bottom: 24px;
}
.img-pic {
  position: relative;

  &:hover .img-shadow {
    opacity: 1;
    transition: all 0.5s;
  }
}
.img-shadow {
  position: absolute;
  opacity: 0;
  width: 100%;
  height: 100%;
  transition: all 0.5s;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  flex-flow: row;
  justify-content: center;
  align-items: center;
  padding: 10%;
}
.container {
  max-width: 1440px;
  margin: 24px auto;
  padding: 48px;

  @media (max-width: 1280px) {
    padding: 36px;
  }

  @media (max-width: 1080px) {
    padding: 24px;
  }

  @media (max-width: 768px) {
    padding: 16px;
  }
}
.list {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: 48px;

  @media (max-width: 1280px) {
    grid-gap: 36px;
  }

  @media (max-width: 1080px) {
    grid-template-columns: repeat(2, 1fr);
  }

  @media (max-width: 768px) {
    grid-template-columns: repeat(1, 1fr);
  }
}

.has:hover {
  color: var(--vp-c-brand);
}
.card {
  display: flex;
  flex-flow: column;
  align-items: flex-start;
  background-color: var(--vp-c-bg-soft);
  transition: all 0.3s;
  box-shadow: 0px 0px 12px rgba(0, 0, 0, 0.12);

  &:hover {
    box-shadow: 0px 0px 18px rgba(0, 0, 0, 0.12);
    transition: all 0.3s;
  }

  &-footer {
    display: flex;
    flex-flow: row;
    justify-content: flex-start;
    align-items: center;
    margin-top: 24px;
    &-img {
      width: 20px;
      margin-right: 12px;
    }

    &-word {
      margin-right: 16px;
    }
  }
  &-word {
    padding: 24px 36px 36px;
    display: flex;
    flex-flow: column;
  }
  &-name {
    font-size: 24px;
    font-weight: 600;
    overflow: hidden;
    text-overflow: ellipsis; /* 超出部分省略号 */
    word-break: break-all; /* break-all(允许在单词内换行。) */
    display: -webkit-box; /* 对象作为伸缩盒子模型显示 */
    -webkit-box-orient: vertical; /* 设置或检索伸缩盒对象的子元素的排列方式 */
    -webkit-line-clamp: 1; /* 显示的行数 */
  }
  &-desc {
    margin-top: 16px;
    font-size: 18px;
    color: white;
    text-align: center;
    overflow: hidden;
    text-overflow: ellipsis; /* 超出部分省略号 */
    word-break: break-all; /* break-all(允许在单词内换行。) */
    display: -webkit-box; /* 对象作为伸缩盒子模型显示 */
    -webkit-box-orient: vertical; /* 设置或检索伸缩盒对象的子元素的排列方式 */
    -webkit-line-clamp: 3; /* 显示的行数 */
  }

  &-tag {
    display: flex;
    flex-flow: row;
    justify-content: flex-start;
    align-items: center;
    flex-wrap: wrap;
    &-item {
      cursor: pointer;
      margin-top: 16px;
      padding: 2px 12px;
      background: var(--vp-c-brand);
      margin-right: 12px;
      font-size: 14px;
      color: white;
      border-radius: 12px;
    }
  }
}
</style>