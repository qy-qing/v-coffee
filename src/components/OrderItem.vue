<template>
  <div class="order-item">
     <div class="cell-item-box">
              <div class="pro-info-box">
                <div class="fl pro-img">
                  <img class="auto-img" :src="item.small_img || item.smallImg" alt="" />
                </div>
                <div class="fl pro-info">
                  <div class="pro-info-text">
                    <div class="clearfix names">
                      <div class="fl pro-name">{{item.name}}</div>
                      <div class="fl pro-rule">{{item.rule}}</div>
                    </div>
                    <div class="pro-enname">{{item.enname}}</div>
                  </div>
                  <div class="pro-count-box">
                    <div class="fl pro-price">￥{{item.price}}</div>
                    <div class="fr step-box">x{{item.count}}</div>
                  </div>
                </div>
              </div>
            </div>
  </div>
</template>

<script>
  export default {
    name: 'OrderItem',
    props: {
      item: {
        type: Object,
        default() {
          return {
            name: '名称',
            rule: '规格',
            enname: '英文名称',
            price: 0,
            count: 1
          };
        }
      }
    }
  }
</script>

<style lang="less" scoped>
  .order-item{
    margin-bottom: 10px;
    &:last-child{
      margin-bottom: 0;
    }

  .pro-info-box{
    height: 80px;
  }

  .pro-img{
    width: 80px;
    height: 80px;
    background-color: #ddd;
  }

  .pro-info{
    width: calc(~"100% - 90px");
    margin-left: 10px;
  }

  .names{
    line-height: 16px;
  }

  .pro-info-text{
    height: 50px;
  }

  .pro-count-box{
    height: 30px;
  }

  .pro-enname{
    font-size: 12px;
    color: #999;
    margin-top: 5px;
  }

  .pro-name{
    font-size: 14px;
    margin-right: 15px;
  }

  .pro-rule{
    font-size: 12px;
    color: #999;
  }

  .pro-price{
    font-size: 14px;
    color: #0caaba;
    font-weight: bold;
    line-height: 30px;
  }

  .step-box{
    line-height: 30px;
    font-size: 12px;
    margin-right: 30px;
  }
  }
</style>