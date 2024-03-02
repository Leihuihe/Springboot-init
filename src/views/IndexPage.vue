<template>
  <div class="index-page">
    <a-input-search
      v-model:value="searchText"
      placeholder="input search text"
      enter-button="Search"
      size="large"
      @search="onSearch"
    />
    <!--    {{ JSON.stringify(postList) }}-->
    <MyDivider />
    <a-tabs v-model:activeKey="activeKey" @change="onTabChange">
      <a-tab-pane key="post" tab="Post">
        <PostList :post-list="postList" />
      </a-tab-pane>
      <a-tab-pane key="picture" tab="Pictures">
        <PictureList :picture-list="pictureList" />
      </a-tab-pane>
      <a-tab-pane key="user" tab="User">Content of Users</a-tab-pane>
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
import { message } from "ant-design-vue";

// .post("post/get/vo", {
//   id: "1737644750309183490",
// })
// myAxios.get("/post/get/vo?id=" + "1737644750309183490").then((res) => {
//   console.log(res);
// });

const postList = ref([]);
const pictureList = ref([]);

//const activeKey = ref("1");
const router = useRouter();
const route = useRoute();
const activeKey = route.params.category;
//const searchText = ref(route.params.text);
const searchText = ref(route.query.text);

const initSearchParams = {
  type: activeKey,
  text: "",
  pageSize: 10,
  pageNum: 1,
};

const searchParams = ref(initSearchParams);
// //First load
// loadData(initSearchParams);

// alert(route.params.category);

const loadAllData = (params: any) => {
  const query = {
    ...params,
    searchText: params.text,
  };
  myAxios.post("search/all", query).then((res: any) => {
    postList.value = res.postList();
    pictureList.value = res.pictureList();
  });
};

const loadData = (params: any) => {
  // const type = activeKey;   faster than loadData
  const { type } = params;
  // alert("type: " + type);
  // console.log();
  if (!type) {
    message.error("Type is null");
    return;
  }
  //封装 text -> searchText
  const query = {
    ...params,
    searchText: params.text,
  };

  myAxios.post("/search/all", query).then((res: any) => {
    if (type === "post") {
      postList.value = res.dataList;
    } else if (type === "picture") {
      pictureList.value = res.dataList;
    }
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

watchEffect(() => {
  searchParams.value = {
    ...initSearchParams,
    text: route.query.text,
    type: route.params.category,
  } as any;
  loadData(searchParams.value);
});
const onSearch = (value: string) => {
  //alert(value);
  router.push({
    // query: {
    //   text: value,
    // },
    query: {
      ...searchParams.value,
      text: value,
    },
  });
  //loadData(searchParams.value);
};

const onTabChange = (key: string) => {
  router.push({
    // params: {
    //   category: key,
    // },
    path: `/${key}`,
    // query: {
    //   text: searchText,
    // },
    query: searchParams.value,
  });
};
</script>
