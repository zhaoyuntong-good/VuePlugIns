<template>
  <div class="home">
    <div class="total">{{total}}</div>
    <el-table
      :data="tableData"
      style="width: 100%">
      <el-table-column
        label="日期"
        width="180">
        <template slot-scope="scope">
          <i class="el-icon-time"></i>
          <span style="margin-left: 10px">{{ scope.row.date }}</span>
        </template>
      </el-table-column>
      <el-table-column
        label="姓名"
        prop="name"
        width="180">
      </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-input v-model="scope.row.address" @input="inputChange(scope.row.address, scope.$index)" @blur="setNum(scope.row.address, scope.$index)" placeholder="请输入内容"></el-input>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  name: 'Home',
  components: {
  },
  data(){
  	return {
      total: 0,
      tableData: [{
        date: '2016-05-02',
        name: '王小虎',
        address: 0
      }, {
        date: '2016-05-04',
        name: '王小虎',
        address: 0
      }, {
        date: '2016-05-01',
        name: '王小虎',
        address: 0
      }, {
        date: '2016-05-03',
        name: '王小虎',
        address: 0
      }]
  	}
  },
  watch: {
    
  },
  created(){
  },
  methods: {
    inputChange (val, index) {
      if (val === '') {
        this.$nextTick(() => {
          this.$set(this.tableData[index], 'address', '0');
        })
      }
      if (val[0] == '0') {
        if (val[1] && val[1] == '0') {
          this.$nextTick(() => {
            this.$set(this.tableData[index], 'address', '0');
          })
        }
      }
      if (/[^\d\.]/.test(val)) {
        this.$nextTick(() => {
          this.$set(this.tableData[index], 'address', '0');
        })
      }
      // 去掉除了数字和.以外的其他字符
      let newVal = val.replace(/[^\d\.]/g, '');
      //保证.只出现一次，而不能出现两次以上
      newVal = newVal.replace(".","$#$").replace(/\./g,"").replace("$#$",".");
      // 去掉前面多余的0
      newVal = newVal.replace(/^0+\./g,'0.');
      newVal.match(/^0+[1-9]+/) ? newVal = newVal.replace(/^0+/g,'') : newVal;
      // 限制最多输入小数点后两位
      if (newVal.split('.')[1] && newVal.split('.')[1].length > 2) {
        newVal = newVal.split('.')[0] + '.' + newVal.split('.')[1].substr(0,2);
      }
      this.$set(this.tableData[index], 'address', newVal);
      this.$nextTick(() => {
        let num = 0;
        for (let i = 0; i < this.tableData.length; i ++) {
          num += parseFloat(this.tableData[i].address)
        }
        let str = num + '';
        str.match(/^\d+(?:\.\d{0,2})?/) ? this.total = str.match(/^\d+(?:\.\d{0,2})?/)[0] : 0;
      })
      
    },
    setNum (val, index) {
      let newVal = val + '';
      if (newVal.split('.')[1] === '') {
        this.$nextTick(() => {
          this.$set(this.tableData[index], 'address', newVal.split('.')[0]);
        })
      }
    }
  }
}
</script>

<style scoped>
  .total {
    width: 100%;
    height: 40px;
    font-size: 20px;
    text-align: center;
    line-height: 40px;
  }
</style>