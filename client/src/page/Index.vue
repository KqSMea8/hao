<template>
  <div class="index">
    <!-- Logo -->
    <div class="logo">
      <span class="blue">C</span>
      <span class="red">C</span>
      <span class="yellow">D</span>  
    </div>

    <!-- 搜索栏 -->
    <el-input class="search" placeholder="请输入搜索内容" spellcheck="false" v-model="input" @keyup.enter.native="onSearch">
      <el-select v-model="engine" slot="prepend" placeholder="搜索引擎" @change="onSearch">
        <el-option label="全网" value="all"></el-option>
        <el-option label="Google" value="google"></el-option>
        <el-option label="百度" value="baidu"></el-option>
        <el-option label="必应" value="bing"></el-option>
        <el-option label="搜狗" value="sougou"></el-option>
        <el-option label="知乎" value="zhihu"></el-option>
        <el-option label="KM" value="km"></el-option>
        <el-option label="GitHub" value="github"></el-option>
      </el-select>
      
      <el-button slot="append" icon="el-icon-search" @click="onSearch"></el-button>
    </el-input>

    <!-- 常用网址 -->
    <el-tabs class="website" v-model="currentTabName" @tab-click="onTabClick">
      <el-tab-pane v-for="(item, index) in data" :key="index" :label="item.label">
        <el-row v-for="row in Math.ceil(item.data.length / cols)" :key="row">
          <el-col v-for="col in cols" :key="col" v-if="(col - 1) + (row - 1) * cols < item.data.length" :span="24 / cols">
            <a target="_blank" :href="item.data[(col - 1) + (row - 1) * cols].href"><i class="el-icon-document"></i>{{ item.data[(col - 1) + (row - 1) * cols].name }}</a>
          </el-col>
        </el-row>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
export default {
  name: 'Index',
  data: function () {
    return {
      engine: '',
      input: '',
      currentTabName: '',
      cols: 3,
      data: []
    }
  },
  created: function () {
    var $this = this;

    this.$http.post('/api/getIndexData').then(function (response) { 
      $this.data = response.body;

      if (window.localStorage.getItem('currentTabName')) {
        $this.currentTabName = window.localStorage.getItem('currentTabName') > $this.data.length - 1 ? '0' : window.localStorage.getItem('currentTabName');
      } else {
        $this.currentTabName = '0';
      }
      window.localStorage.setItem('currentTabName', $this.currentTabName);
    });

    this.engine = window.localStorage.getItem('engine');
  },
  methods: {
    onSearch: function () {
      if (this.input) {
        if (this.engine === 'all') {
          window.open(`./#/browser?q=${this.input}`);
        } else if (this.engine === 'google') {
          window.open(`https://www.google.com/search?q=${this.input}`);
        } else if (this.engine === 'baidu') {
          window.open(`https://www.baidu.com/s?wd=${this.input}`);  
        } else if (this.engine === 'bing') {
          window.open(`https://cn.bing.com/search?q=${this.input}`);  
        } else if (this.engine === 'sougou') {
          window.open(`https://www.sogou.com/web?query=${this.input}`);  
        } else if (this.engine === 'zhihu') {
          window.open(`https://www.zhihu.com/search?q=${this.input}`);
        } else if (this.engine === 'km') {
          window.open(`https://km.oa.com/search?q=${this.input}`);
        } else if (this.engine === 'github') {
          window.open(`https://www.github.com/search?q=${this.input}`);
        }
      } 
      
      window.localStorage.setItem('engine', this.engine);
    },
    onTabClick: function () {
      window.localStorage.setItem('currentTabName', this.currentTabName);
    }
  }
};
</script>

<style>
.index .logo {
  margin-top: 50px;
  text-align: center;
}

.index .logo span {
  font-family: Anurati;
  font-size: 80px;
}

.index .blue {
  color: #4285f4;
}

.index .red {
  color: #EA4335;
}

.index .yellow {
  color: #FBBC05;
}

.index .search {
  margin-top: 50px;
}

.index .search .el-input-group__prepend {
  background-color: #fff;
  width: 70px;
}

.index .website {
  margin-top: 50px;
}

.index .website .el-col {
  padding: 10px;
}

.index .website .el-col:hover {
  background: #F5F5F5;
}

.index .website i {
  margin-right: 10px;
}

.index .website a,
.index .website a:link,
.index .website a:visited,
.index .website a:hover,
.index .website a:active {
  display: block;   
  white-space: nowrap;
  text-decoration: none;
  overflow: hidden;   
  text-overflow: ellipsis;
  word-break: keep-all; 
  color: #000;
}
</style>
