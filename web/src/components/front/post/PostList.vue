<template>
  <n-spin class="postlist" :show="PostSpinShow" style="min-height: 300px">
    <n-tabs
      :bar-width="28"
      type="line"
      class="custom-tabs"
      animated
      @update:value="changeCategory"
    >
      <n-tab-pane
        v-for="citem in cdata.cids"
        :key="citem"
        :name="String(citem.id)"
        :tab="citem.name"
      >
        <div class="PostSpin" v-if="PostData[citem.id] != false">
          <PostVue
            v-for="(item, index) in PostData[citem.id]"
            :class="index % 2 === 0 ? 'left' : 'right'"
            :key="item"
            :item="item"
          />
        </div>
        <n-empty v-else description="没有东西。。。" />
      </n-tab-pane>
    </n-tabs>
    <template #description>加载中~~~~</template>
    <n-pagination
      v-model:page="page"
      :page-count="pageCount"
      @update:page="upPage"
    />
  </n-spin>
</template>

<script setup>
import { ref } from "vue";
import PostVue from "./Post.vue";
import api from "@/api";
const PostSpinShow = ref(true);
const props = defineProps(["cdata"]);
const page = ref(1);
const pageCount = ref(1);
const cid = ref("-1");
//各分类下的文章
const PostData = ref({
  "-1": [{ id: -1 }, { id: -2 }, { id: -3 }],
});

//请求主页文章列表
// c是分类请求,m是菜单请求
function getPosts(id, ty = "c") {
  const params = { pagesize: 6, pagenum: page.value };
  if (ty == "m" || id == "-1") {
    params.mid = props.cdata.id;
  } else if (ty == "c") {
    params.cid = id;
  }
  api.article.getList(params).then((res) => {
    PostData.value[id] = res.data.map((item) => {
      item.cname = props.cdata.cids.find((citem) => {
        return citem.id == item.cid;
      });
      return item;
    });
    PostSpinShow.value = false;
    pageCount.value = Math.ceil(res.total / params.pagesize);
  });
}

getPosts("-1", "m");

function upPage(p) {
  if (cid.value == "-1") {
    getPosts("-1", "m");
  } else {
    getPosts(cid.value, "c");
  }
}

function changeCategory(val) {
  cid.value = val;
  page.value = 1;
  pageCount.value = 1;
  PostSpinShow.value = true;
  if (PostData.value[val] == undefined) {
    getPosts(val, "c");
  } else {
    PostSpinShow.value = false;
  }
}
</script>

<style scoped lang="scss">
.PostSpin {
  display: flex;
  flex-direction: column;
  min-width: 200px;
  align-items: center;
}

.postlist {
  :deep(.n-pagination) {
    width: 80%;
    margin: 0 auto;
    justify-content: center;
  }
}
</style>
