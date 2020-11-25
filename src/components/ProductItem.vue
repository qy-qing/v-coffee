<template>
  <div class="product-item">
    <div>
      <div>
        <img class="auto-img" :src="item.smallImg" alt="" />
      </div>
      <div class="pro-name one-text">{{item.name}}</div>
      <div class="pro-enname one-text">{{item.enname}}</div>
      <div class="clearfix">
        <div class="fl pro-price">￥{{item.price}}</div>
        <div class="fr delete" @click.stop="remove()" v-if="isRemove">
          <van-icon name="delete" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'ProductItem',
    props: {

      //商品信息
      item: {
        type: Object,
        default () {
          return {};
        }
      },

      //是否显示删除
      isRemove: {
        type: Boolean,
        default: false
      }
    },

    methods: {

      //移除
      remove() {
        this.$emit('remove');
      }

    }
  }
</script>

<style lang="less" scoped>
  .product-item {


    .pro-name {
      margin-top: 10px;
      font-size: 14px;
    }

    .one-text {
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }

    .pro-enname {
      margin-top: 5px;
      margin-bottom: 10px;
      font-size: 12px;
      color: #999;
    }

    .pro-price {
      font-size: 14px;
      color: #0caaba;
      font-weight: bold;
    }

    .delete {
      width: 15px;
      height: 15px;
      margin-top: 2px;
      color: #999;
    }
  }
</style>