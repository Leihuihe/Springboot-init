<template>
  <div class="index-page">
    <a-input-search
      v-model:value="searchParams.text"
      placeholder="input search text"
      enter-button="Search"
      size="large"
      @search="onSearch"
    />
    <!--    {{ JSON.stringify(postList) }}-->
    <MyDivider />
    <a-tabs v-model:activeKey="activeKey" @change="onTabChange">
      <a-tab-pane key="1" tab="Tab 1">
        <PostList :post-list="postList" />
      </a-tab-pane>
      <a-tab-pane key="2" tab="Tab 2">
        <PictureList :picture-list="pictureList" />
      </a-tab-pane>
      <a-tab-pane key="3" tab="Tab 3">Content of Tab Pane 3</a-tab-pane>
    </a-tabs>
  </div>
</template>

<script setup lang="ts">
import { ref, watchEffect } from "vue";
import PostList from "@/components/PostList.vue";
import MyDivider from "@/components/MyDivider.vue";
import { useRoute, useRouter } from "vue-router";
import myAxios from "@/plugins/myAxios";
import PictureList from "@/components/PictureList.vue";

// .post("post/get/vo", {
//   id: "1737644750309183490",
// })
// myAxios.get("/post/get/vo?id=" + "1737644750309183490").then((res) => {
//   console.log(res);
// });

const postList = ref([]);
const pictureList = ref([]);

const loadData = (params: any) => {
  //封装 text -> searchText
  const searchQuery = {
    ...params,
    searchText: params.text,
  };

  myAxios.post("/search/all", searchQuery).then((res: any) => {
    postList.value = res.postList;
    pictureList.value = res.pictureList;
  });
  // const postQuery = {
  //   ...params,
  //   searchText: params.text,
  // };
  // const pictureQuery = {
  //   ...params,
  //   searchText: params.text,
  // };
  //
  // myAxios.post("/post/list/page/vo", postQuery).then((res: any) => {
  //   postList.value = res.records;
  // });
  // myAxios.post("/picture/list/page/vo", pictureQuery).then((res: any) => {
  //   pictureList.value = res.records;
  // });
};

// const searchText = ref("");
//const activeKey = ref("1");
const router = useRouter();
const route = useRoute();
const activeKey = route.params.category;

const initSearchParams = {
  text: "",
  pageSize: 10,
  pageNum: 1,
};

const searchParams = ref(initSearchParams);
//First load
loadData(initSearchParams);

// alert(route.params.category);

watchEffect(() => {
  searchParams.value = {
    ...initSearchParams,
    text: route.query.text,
  } as any;
});
const onSearch = (value: string) => {
  //alert(value);
  router.push({
    // query: {
    //   text: value,
    // },
    query: searchParams.value,
  });
  loadData(searchParams.value);
};

const onTabChange = (key: string) => {
  router.push({
    // params: {
    //   category: key,
    // },
    path: `${key}`,
    // query: {
    //   text: searchText,
    // },
    query: searchParams.value,
  });
};
</script>
