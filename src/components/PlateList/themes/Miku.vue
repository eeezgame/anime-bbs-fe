<template>
  <div class="w-full">
    <div class="w-full lg:w-8/12" v-if="loginState === 1">
      <div class="w-full bg-miku-700 px-3 py-2 space-x-2">
        <span
          v-for="item in plateOptions"
          :key="item.id"
          class="p-1 cursor-pointer  hover:bg-gray-200 hover:text-red-500"
          :class="[
            item.id === queryParam.plateId
              ? 'bg-white text-black'
              : 'text-white'
          ]"
          @click="
            handleQueryParamChange('plateId', item.id);
            getAllUserPost();
          "
          >{{ item.name }}</span
        >
      </div>
      <div class="w-full bg-gray-200 pl-1 py-2 ">
        <div class="flex mx-auto text-gray-600 space-x-1">
          <select
            :value="queryParam.categoryId"
            @input="handleQueryParamChange('categoryId', $event.target.value)"
            class="w-24 border-2 border-gray-300 rounded-sm focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50"
          >
            <option disabled value="">帖子分类</option>
            <option
              v-for="item in postCategoryOptions"
              :key="item.id"
              :value="item.id"
              >{{ item.name }}</option
            >
          </select>
          <input
            :value="queryParam.title"
            @input="handleQueryParamChange('title', $event.target.value)"
            class="w-64 border-2 border-gray-300 bg-white h-8 pl-3 rounded-sm text-sm focus:outline-none"
            type="search"
            name="search"
            placeholder="查询帖子标题"
          />
          <button @click="getAllUserPost">
            <svg
              class="text-gray-600 h-4 w-4 fill-current"
              xmlns="http://www.w3.org/2000/svg"
              xmlns:xlink="http://www.w3.org/1999/xlink"
              version="1.1"
              id="Capa_1"
              x="0px"
              y="0px"
              viewBox="0 0 56.966 56.966"
              style="enable-background:new 0 0 56.966 56.966;"
              xml:space="preserve"
              width="512px"
              height="512px"
            >
              <path
                d="M55.146,51.887L41.588,37.786c3.486-4.144,5.396-9.358,5.396-14.786c0-12.682-10.318-23-23-23s-23,10.318-23,23  s10.318,23,23,23c4.761,0,9.298-1.436,13.177-4.162l13.661,14.208c0.571,0.593,1.339,0.92,2.162,0.92  c0.779,0,1.518-0.297,2.079-0.837C56.255,54.982,56.293,53.08,55.146,51.887z M23.984,6c9.374,0,17,7.626,17,17s-7.626,17-17,17  s-17-7.626-17-17S14.61,6,23.984,6z"
              />
            </svg>
          </button>
        </div>
      </div>
      <div class="mb-6 divide-y">
        <div
          v-for="item in plates"
          :key="item.id"
          class="max-w-4xl px-3 py-2 bg-miku-1000 border-miku-1100 rounded-sm shadow-md"
        >
          <table cellpadding="0" cellspacing="0" border="0" width="100%">
            <tbody>
              <tr>
                <td width="48" valign="top" align="center">
                  <router-link
                    :to="{ name: 'Space', params: { id: item.userId } }"
                    class="text-gray-800 hover:text-red-500 text-sm"
                  >
                    <img
                      :src="$imgURL + item.user.avatar"
                      class="rounded-sm"
                      border="0"
                      align="default"
                      :alt="item.user.name"
                  /></router-link>
                </td>
                <td width="10"></td>
                <td width="auto" valign="middle">
                  <span class="item_title">
                    <router-link
                      :to="{ name: 'Post', params: { id: item.id } }"
                      class="text-gray-800 hover:text-red-500 text-sm"
                    >
                      {{
                        item.adopted === 1
                          ? `[🙏待采纳][💰${item.adoptedPoints}]`
                          : item.adopted === 3
                          ? `[✅已采纳][💰${item.adoptedPoints}]`
                          : ""
                      }}
                      {{ item.title }}</router-link
                    ></span
                  >
                  <div class="sep5"></div>
                  <span class="topic_info"
                    ><div class="votes"></div>
                    <strong>
                      <router-link
                        :to="{ name: 'Space', params: { id: item.userId } }"
                        class="text-gray-800 hover:text-red-500 text-sm"
                      >
                        {{ item.user.name }}</router-link
                      ></strong
                    >
                    <!-- <span title="2021-01-31 15:43:58 +08:00">5 分钟前</span>
                  <strong><a href="/member/cmllwxxl">cmllwxxl</a></strong> -->
                  </span>
                </td>
                <td width="70" align="right" valign="middle">
                  <!-- <a href="/t/749886#reply79" class="count_livid">233</a> -->
                </td>
              </tr>
            </tbody>
          </table>
        </div>
        <div
          v-if="plates.length === 0"
          class="h-64 max-w-4xl px-3 py-2 bg-miku-1000 border-miku-1100 rounded-sm shadow-md relative"
        >
          <p
            class="w-full text-center absolute transform translate-x-1/2 translate-y-1/2 right-1/2 top-1/2"
          >
            暂无数据
          </p>
        </div>
      </div>
      <slot name="pagination"> </slot>
      <div class="mt-8">
        <t-pagination
          v-if="listQuery"
          v-show="total > 0"
          :value="listQuery.page"
          :total-items="total"
          :per-page="listQuery.limit"
          @change="
            handlePageChance($event);
            getAllUserPost();
          "
        />
      </div>
    </div>
    <div
      v-if="loginState === 0"
      class="w-full lg:w-8/12 h-64 max-w-4xl px-3 py-2 bg-miku-1000 border-miku-1100 rounded-sm shadow-md relative"
    >
      <p
        class="w-full text-center absolute transform translate-x-1/2 translate-y-1/2 right-1/2 top-1/2"
      >
        请先登录
      </p>
    </div>
    <div
      v-if="loginState === 2"
      class="w-full lg:w-8/12 h-64 max-w-4xl px-3 py-2 bg-miku-1000 border-miku-1100 rounded-sm shadow-md relative"
    >
      <p
        class="w-full text-center absolute transform translate-x-1/2 translate-y-1/2 right-1/2 top-1/2"
      >
        违规账号
      </p>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    plateOptions: {
      type: Array,
      default: () => []
    },
    queryParam: {
      type: Object,
      default: () => {
        return {
          title: "",
          categoryId: "",
          plateId: ""
        };
      }
    },
    postCategoryOptions: {
      type: Array,
      default: () => []
    },
    plates: {
      type: Array,
      default: () => []
    },
    total: {
      type: Number,
      default: 0
    },
    listQuery: {
      type: Object,
      default: () => {
        return {
          page: 1,
          limit: 5
        };
      }
    },
    getAllUserPost: {
      type: Function,
      default: () => {}
    },
    loginState: {
      type: Number,
      default: 0
    }
  },
  methods: {
    handleQueryParamChange(field, value) {
      let obj = {};
      obj[field] = value;
      this.$emit("update:query-param", Object.assign({}, this.queryParam, obj));
    },
    handlePageChance(page) {
      this.$emit(
        "update:list-query",
        Object.assign({}, this.listQuery, { page })
      );
    }
  }
};
</script>

<style></style>
