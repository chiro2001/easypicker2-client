<template>
  <div class="footer">
    <ul>
      <li v-for="(item, index) in navs" :key="index">
        <a target="_blank" rel="noopener" v-bind:href="item.link">{{ item.title }}</a>
      </li>
      </ul>
    <ul>
      <li>
        <router-link to="/author">联系作者</router-link>
        <!-- <a style="position: relative;left: 20px;"><Praise>👍🏻</Praise></a> -->
      </li>
    </ul>
    <ul v-if="navs2.length">
      <li v-for="(item, index) in navs2" :key="index">
        <a target="_blank" rel="noopener" v-bind:href="item.link">{{ item.title }}</a>
      </li>
    </ul>
    <p>v{{version}}
      © 2019 - {{ cunnrentYear }} by
      <a style="color: aliceblue;" target="_blank" rel="noopener" href="https://sugarat.top">粥里有勺糖</a>
      , 特化修改 by 
      <a style="color: aliceblue;" target="_blank" rel="noopener" href="https://github.com/chiro2001">Chiro</a>
    </p>
  </div>
</template>

<script lang="ts" setup>
import { reactive } from 'vue'
import { version } from '../../../package.json'
import Praise from '../Praise/index.vue'

const navs = reactive([
  {
    title: "照片收集反馈",
    link: "https://www.wenjuan.com/s/UZBZJvyooC/"
  },
  {
    title: '应用介绍',
    link: 'https://docs.ep.sugarat.top/',
  },
  {
    title: '(原项目)问卷反馈',
    link: 'https://www.wenjuan.com/s/UZBZJvA040/',
  },
])

const navs2 = reactive([

])

const cunnrentYear = new Date().getFullYear()
</script>

<style lang="scss" scoped>
.footer {
  ul {
    margin: 10px auto;
    display: flex;
    justify-content: center;
    li {
      width: 80px;
      list-style: none;
      text-align: center;
      a {
        text-align: center;
        color: #fff;
        opacity: 0.8;
        font-size: 1rem;
        line-height: 1rem;
        &:hover {
          opacity: 1;
          text-shadow: 0 0 2px #ddd;
        }
      }
    }
  }

  p {
    margin-top: 40px;
    padding-bottom: 20px;
    color: #ddd;
    a {
      color: #ddd;
      margin-left: 10px;
    }
  }
  p {
    text-align: center;
  }
}
</style>
