<template>
  <section>
    <el-row>
        <el-col :span="8">
          <div class="left-box-inner">
            <div class="bottom-box">
                <template v-for="(i, index) in menuArr">
                  <el-popover
                    placement="top"
                    width="150"
                    trigger="click"
                    :key="index"
                    :disabled="!i.id"
                  >
                    <ul>
                      <li @click="addSubMenu(i.id)">添加</li>
                      <template v-for="(item, inIndex) in i.subMenu">
                        <li :key="inIndex" @click="showSubMenu(inIndex)" :class="{'sub-active': inIndex === secondMenuIndex}">
                            {{item.name}}
                        </li>
                      </template>
                    </ul>
                    <span slot="reference" @click="tabMenu(index)" :class="{'tab-active': index === menuIndex}">{{i.name}}</span>
                  </el-popover>
                </template>
            </div>
          </div>
        </el-col>
        <el-col :span="16">
          <el-form ref="myform" :model="form" label-width="80px">
            <el-form-item label="菜单名字">
              <el-input v-model="form.name"></el-input>
            </el-form-item>
            <el-form-item label="顺序">
              <el-input v-model="form.dis" :disabled="form.level === 1"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="save">保存</el-button>
              <el-button type="primary" @click="remove">删除</el-button>
            </el-form-item>
          </el-form>
        </el-col>
    </el-row>
  </section>
</template>

<script>
export default {
  name: 'page1',
  data () {
    return {
      count: 0,
      menuIndex: 0,
      secondMenuIndex: 0,
      menuArr: [
        {
          id: null,
          name: '菜单1',
          dis: 1,
          subMenu: [],
          pId: 0,
          level: 1
        },
        {
          id: null,
          name: '菜单2',
          dis: 2,
          subMenu: [],
          pId: 0,
          level: 1
        },
        {
          id: null,
          name: '菜单3',
          dis: 3,
          subMenu: [],
          pId: 0,
          level: 1
        }
      ],
      form: { // 没有subMenu 字段
        id: '',
        name: '',
        dis: 1,
        pId: 0,
        level: 1
      }
    }
  },
  methods: {
    init () {
      // 调取接口，初始化form
      // 让form字段指向menuArr第一个元素
      this.form = JSON.parse(JSON.stringify(this.menuArr[0]))
    },
    tabMenu (index) {
      this.menuIndex = index
      this.form = JSON.parse(JSON.stringify(this.menuArr[index]))
    },
    addSubMenu (id) { // id 是parentid
      this.secondMenuIndex = -1
      this.form.id = null
      this.form.name = ''
      this.form.dis = 1 // 初始化为二级菜单数组长度
      this.form.pId = id // 赋值父菜单
      this.form.level = 2 // 添加2级菜单。所以值为2
      if (this.form.subMenu) {
        this.form.subMenu = []
      }
    },
    showSubMenu  (index) { // 二级菜单索引
      this.secondMenuIndex = index
      this.form = JSON.parse(JSON.stringify(this.menuArr[this.menuIndex].subMenu[index]))
    },
    save () {
      // 校验处
      let flag = false
      if (this.form.level === 2 && this.menuArr[this.menuIndex].subMenu.length > 0) {
        for (let value of this.menuArr[this.menuIndex].subMenu) {
          if (value.dis === this.form.dis) {
            flag = true
            break
          }
        }
      }
      if (flag) {
        if (this.secondMenuIndex < 0) {
          alert('该顺序已经存在')
          return
        } else { // 修改
          // 待定
        }
      }
      //
      //
      // 调取接口
      //
      //
      //
      //
      if (this.form.level === 1) { // 一级菜单
        if (!this.form.id) { // 创建菜单
          this.form.id = ++this.count // this.count 模拟调取接口返回的数据，可以删除
        }
        this.$set(this.menuArr, this.menuIndex, JSON.parse(JSON.stringify(this.form)))
      } else { // 二级菜单
        if (this.secondMenuIndex >= 0 || this.form.id) { // 二级菜单索引大于等于0则是修改，不存在则创建
          // 需要获取二级菜单索引
          this.$set(this.menuArr[this.menuIndex].subMenu, this.secondMenuIndex, JSON.parse(JSON.stringify(this.form)))
        } else {
          this.form.id = ++this.count // this.count 模拟调取接口返回的数据，可以删除
          //
          //
          //
          this.menuArr[this.menuIndex].subMenu.push(JSON.parse(JSON.stringify(this.form)))
          // this.secondMenuIndex = -1
          this.form.id = null
          this.form.name = ''
          this.form.dis = 1
          // this.form.level = 2
          // this.form.pId = 0
        }
      }
    },
    remove () {
      if (this.form.id) { // 菜单存在
        // id调接口
        // 以下皆是接口调用成功之后操作
        // 接口调用成功之后要操作menuArr,删除保存的菜单
        if (this.form.level === 1) { // 一级菜单
          this.menuArr[this.menuIndex].name = `菜单${this.menuIndex + 1}`
          this.menuArr[this.menuIndex].id = null
          this.menuArr[this.menuIndex].subMenu = []
          // 下面这些字段不变，还和原来删除之前一样
          // dis: 1,
          // pId: 0,
          // level: 1
        } else { // 二级菜单
          // 删除subMenu中该菜单，没有其他需要初始化的字段
          this.menuArr[this.menuIndex].subMenu.splice(this.secondMenuIndex, 1)
        }
        this.form.id = null
        this.form.name = ''
        this.form.dis = 1
        this.form.level = 1
        // 只需要初始化form表单中显示字段即可, 因为切换一级菜单或者选择添加二级菜单的时候会重新初始化form对象
      } else { // 菜单不存在
        alert('菜单不存在')
      }
    }
  },
  created () {
    this.init()
  }
}
</script>

<style scoped lang="scss">
  .left-box-inner {
    position: relative;
    height: 400px;
    width: 80%;
    background-color: #f2f2f2;
    .bottom-box {
      position: absolute;
      bottom: 0;
      width: 100%;
      span {
        display: inline-block;
        width: 33.33%;
        padding: 10px 0;
      }
    }
    .tab-active {
      background-color: #ddd;
    }
    .sub-active {
      color: red;
    }
  }
</style>
