<template>
  <div>
    <div class="w-8/12 mx-auto mt-8" v-if="userInfo">
      <div v-if="isMySpace">
        <h3 class="bg-miku-1100 py-1 px-2">个人信息</h3>
        <div class="bg-gray-50 py-2 px-6">
          <div class="group w-16 h-16 mx-auto cursor-pointer  relative">
            <img
              class="w-16 h-16 rounded-full"
              :src="$imgURL + this.userInfo.avatar"
              alt=""
            />
            <div
              class="group-hover:opacity-100 opacity-0 transition-opacity duration-200 w-16 h-16 bg-black bg-opacity-60 text-center text-gray-100 absolute top-0 left-0  rounded-full "
              style="line-height:4rem;"
              @click="handleChangeAvatar"
            >
              更换头像
            </div>
            <input
              ref="avatarUpload"
              class="hidden"
              type="file"
              name="file"
              accept="image/*"
              id="upload"
              @change="updateAvatar"
            />
            <!-- class="hidden" -->
          </div>
          <p class="text-center">
            {{ userInfo.name }}
          </p>
          <div class="mx-auto flex" style="width: 300px;">
            <span class="flex-1 text-center cursor-pointer hover:text-red-500"
              >关注:{{ attentions.length }}</span
            >
            <span class="flex-1 text-center cursor-pointer hover:text-red-500"
              >粉丝:{{ fans.length }}</span
            >
            <span class="flex-1 text-center">积分:{{ userInfo.points }}</span>
          </div>
          <div class="w-full mx-auto my-8 space-y-8">
            <div class="flex" style="height: 18px;">
              <div class="w-5/12 pl-8 flex space-x-2">
                <span class="safe-icon"></span><span>邮箱</span>
              </div>
              <div class="w-5/12">{{ userInfo.email }}</div>
              <a
                class="w-2/12 text-blue-500 hover:text-blue-300 cursor-pointer"
                @click="handleShowChangeEmailDialog"
                >更换邮箱</a
              >
            </div>
            <div class="flex" style="height: 18px;">
              <div class="w-5/12 pl-8 flex space-x-2">
                <span class="safe-icon"></span><span>联系方式</span>
              </div>
              <div class="w-5/12">{{ userInfo.phone }}</div>
              <a
                class="w-2/12 text-blue-500 hover:text-blue-300 cursor-pointer"
                @click="handleShowChangePhoneDialog"
                >更换手机</a
              >
            </div>
            <div class="flex" style="height: 18px;">
              <div class="w-5/12 pl-8 flex space-x-2">
                <span class="safe-icon"></span><span>设置密码</span>
              </div>
              <div class="w-5/12">已设置</div>
              <a
                class="w-2/12 text-blue-500 hover:text-blue-300 cursor-pointer"
                @click="handleShowChangePasswordDialog"
              >
                修改密码
              </a>
            </div>
          </div>
        </div>
      </div>
      <div v-if="!isMySpace">
        <h3 class="bg-miku-1100 py-1 px-2">TA的信息</h3>
        <div class="bg-gray-50 py-2 px-6">
          <div class="group w-16 h-16 mx-auto cursor-pointer  relative">
            <img
              class="w-16 h-16 rounded-full"
              :src="$imgURL + this.taInfo.avatar"
              alt=""
            />
          </div>
          <p class="text-center">
            {{ taInfo.name }}
          </p>
        </div>
      </div>
      <div>
        <h3 class="bg-miku-1100 py-1 px-2">
          {{ isMySpace ? "我" : "TA" }}的帖子<span
            class="hover:text-red-500 cursor-pointer float-right"
            >[更多]</span
          >
        </h3>
        <div class=" bg-gray-50">
          <t-table
            :headers="['标题', '板块', '分类', '时间']"
            :data="userPosts"
          >
            <template v-slot:row="props">
              <tr :class="[props.trClass]">
                <td :class="props.tdClass">
                  <router-link
                    :to="{ name: 'Post', params: { id: props.row.id } }"
                    class="text-gray-800 hover:text-red-500 text-sm"
                  >
                    {{ props.row.title }}</router-link
                  >
                </td>
                <td :class="props.tdClass">{{ props.row.postPlate.name }}</td>
                <td :class="props.tdClass">
                  {{ props.row.postCategory.name }}
                </td>
                <td :class="props.tdClass">{{ props.row.createTime }}</td>
              </tr>
            </template>
            <template v-if="!userPosts.length" v-slot:tbody="props">
              <tbody :class="props.tbodyClass">
                <tr :class="[props.trClass, 'text-center']">
                  <td :class="props.tdClass" colspan="4">
                    暂无数据
                  </td>
                </tr>
              </tbody>
            </template>
          </t-table>
        </div>
      </div>
      <div v-if="isMySpace">
        <h3 class="bg-miku-1100 py-1 px-2">
          {{ isMySpace ? "我" : "TA" }}的收藏<span
            class="hover:text-red-500 cursor-pointer float-right"
            >[更多]</span
          >
        </h3>
        <div class=" bg-gray-50">
          <t-table
            :headers="['标题', '板块', '分类', '时间']"
            :data="postCollections"
          >
            <template v-slot:row="props">
              <tr :class="[props.trClass]">
                <td :class="props.tdClass">
                  <router-link
                    :to="{ name: 'Post', params: { id: props.row.id } }"
                    class="text-gray-800 hover:text-red-500 text-sm"
                  >
                    {{ props.row.title }}</router-link
                  >
                </td>
                <td :class="props.tdClass">{{ props.row.postPlate.name }}</td>
                <td :class="props.tdClass">
                  {{ props.row.postCategory.name }}
                </td>
                <td :class="props.tdClass">{{ props.row.createTime }}</td>
              </tr>
            </template>
            <template v-if="!postCollections.length" v-slot:tbody="props">
              <tbody :class="props.tbodyClass">
                <tr :class="[props.trClass, 'text-center']">
                  <td :class="props.tdClass" colspan="4">
                    暂无数据
                  </td>
                </tr>
              </tbody>
            </template>
          </t-table>
        </div>
      </div>
      <div v-if="isMySpace">
        <h3 class="bg-miku-1100 py-1 px-2">
          {{ isMySpace ? "我" : "TA" }}的关注<span
            class="hover:text-red-500 cursor-pointer float-right"
            >[更多]</span
          >
        </h3>
        <div class=" bg-gray-50">
          <t-table :headers="['头像', '名称', '操作']" :data="attentions">
            <template v-slot:row="props">
              <tr :class="[props.trClass]">
                <td :class="props.tdClass">
                  <router-link
                    :to="{ name: 'Post', params: { id: props.row.id } }"
                    class="text-gray-800 hover:text-red-500 text-sm"
                  >
                    <img
                      :src="$imgURL + props.row.avatar"
                      alt=""
                      class="w-4 h-4"
                  /></router-link>
                </td>
                <td :class="props.tdClass">{{ props.row.name }}</td>
                <td :class="props.tdClass">
                  <button
                    v-if="props.row._isFollowed"
                    class="bg-gray-300 text-gray-600 hover:text-red-500 px-2 rounded"
                    @click="cancelFollow(props.row)"
                  >
                    取消关注
                  </button>
                  <button
                    v-else
                    class="bg-white border border-miku-1000 text-gray-600 hover:text-miku-400 px-2 rounded"
                    @click="follow(props.row)"
                  >
                    关注
                  </button>
                </td>
              </tr>
            </template>
            <template v-if="!attentions.length" v-slot:tbody="props">
              <tbody :class="props.tbodyClass">
                <tr :class="[props.trClass, 'text-center']">
                  <td :class="props.tdClass" colspan="4">
                    暂无数据
                  </td>
                </tr>
              </tbody>
            </template>
          </t-table>
        </div>
      </div>
      <div v-if="isMySpace">
        <h3 class="bg-miku-1100 py-1 px-2">
          {{ isMySpace ? "我" : "TA" }}的粉丝<span
            class="hover:text-red-500 cursor-pointer float-right"
            >[更多]</span
          >
        </h3>
        <div class=" bg-gray-50">
          <t-table :headers="['头像', '名称']" :data="fans">
            <template v-slot:row="props">
              <tr :class="[props.trClass]">
                <td :class="props.tdClass">
                  <router-link
                    :to="{ name: 'Post', params: { id: props.row.id } }"
                    class="text-gray-800 hover:text-red-500 text-sm"
                  >
                    <img
                      :src="$imgURL + props.row.avatar"
                      alt=""
                      class="w-4 h-4"
                  /></router-link>
                </td>
                <td :class="props.tdClass">{{ props.row.name }}</td>
              </tr>
            </template>
            <template v-if="!fans.length" v-slot:tbody="props">
              <tbody :class="props.tbodyClass">
                <tr :class="[props.trClass, 'text-center']">
                  <td :class="props.tdClass" colspan="4">
                    暂无数据
                  </td>
                </tr>
              </tbody>
            </template>
          </t-table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { uploadImage } from "@/api/upload";
import { updateUser } from "@/api/user-manage";
export default {
  props: [
    "loginState",
    "userInfo",
    "taInfo",
    "postCollections",
    "userPosts",
    "fans",
    "attentions",
    "isMySpace",
    "getUserById",
    "getUserPost",
    "getUserCollect",
    "getFollow",
    "getFollowed",
    "follow",
    "cancelFollow",
    "handleShowChangeEmailDialog",
    "handleShowChangePhoneDialog",
    "handleShowChangePasswordDialog"
  ],
  methods: {
    handleChangeAvatar() {
      this.$refs.avatarUpload && this.$refs.avatarUpload.click();
    },
    updateAvatar() {
      this.$refs.avatarUpload.files && this.$refs.avatarUpload.files[0];
      let fd = new FormData();
      fd.append("file", this.$refs.avatarUpload.files[0]);
      uploadImage(fd).then(res => {
        updateUser(
          Object.assign({}, this.userInfo, {
            avatar: res.data,
            lastLoginTime: void 0
          })
        ).then(() => {
          this.userInfo.avatar = res.data;
        });
      });
    }
  }
};
</script>

<style></style>
