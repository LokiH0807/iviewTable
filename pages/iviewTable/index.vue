<template>
  <div style="padding: 20px">
    <Table
      :columns="columns14"
      :data="configFlat"
      border
      :span-method="handleSpan"
    ></Table>
  </div>
</template>
<script>
export default {
  data() {
    return {
      table: [],
      columns14: [
        {
          title: '玩法',
          key: 'title',
          render: (h, params) => {
            return h('span',
              params.row.title
            )
          }
        },
        {
          title: '子玩法',
          key: 'subTitle',
          render: (h, params) => {
            return h('span',
              params.row.subTitle
            )
          }
        },
        {
          title: '投注項目',
          key: 'betItem',
          render: (h, params) => {
            const bet = params.row.betItem[0]
            if (typeof (bet) === 'string') return h('div', bet === '-' ? '-' : bet)
            return h('div', '')
          }
        },
        {
          title: '賠率',
          key: 'oddsKey'
        },
        {
          title: '投注項目',
          key: 'betItem2',
          render: (h, params) => {
            const bet = params.row.betItem[1]
            if (bet && typeof (bet) === 'string') return h('div', bet === '-' ? '-' : bet)

            return h('div', '')
          }
        },
        {
          title: '賠率',
          key: 'oddsKey2'
        },
        {
          title: '投注項目',
          key: 'betItem3',
          render: (h, params) => {
            const bet = params.row.betItem[2]
            if (bet && typeof (bet) === 'string') return h('div', bet === '-' ? '-' : bet)

            return h('div', '')
          }
        },
        {
          title: '賠率',
          key: 'oddsKey3'
        },
        {
          title: '投注項目',
          key: 'betItem4',
          render: (h, params) => {
            const bet = params.row.betItem[3]
            if (bet && typeof (bet) === 'string') return h('div', bet === '-' ? '-' : bet)

            return h('div', '')
          }
        },
        {
          title: '賠率',
          key: 'oddsKey4'
        }

      ],
      data5: [
       {
      title: 'ziDing',
      second: [
        'REPEATABLE:MATCH:ORDER:2',
        'REPEATABLE:MATCH:ORDER:3',
        'REPEATABLE:MATCH:ORDER:4'
      ],
      betItem: ['-', '-', '-']
    },
    {
      title: 'ziXian',
      second: ['ziXian2', 'ziXian3', 'ziXian4'],
      betItem: [
        ['REPEATABLE:2:MAㄇCH:1:0', 'REPEATABLE:2:MATCH:0:2'],
        ['REPEATABLE:3:MATCH:1:0', 'REPEATABLE:3:MATCH:1:1', 'REPEATABLE:3:MATCH:0:3'],
        ['REPEATABLE:4:MATCH:1:0', 'REPEATABLE:4:MATCH:1:1', 'REPEATABLE:4:MATCH:2:0', 'REPEATABLE:4:MATCH:1:2', 'REPEATABLE:4:MATCH:0:4']
      ]
    }
      ],
      spanArr: [],
      dateArr: [],
      pos1: 0,
      pos2: 0,
    };
  },

  computed: {
    configFlat () {
      const tempArr = []
      const spanArr = []
      
      if (this.data5) {
        this.data5.forEach((item, idx) => {

          item.second.forEach((subTitle, i) => {
            const title = item.title
            // 超過 4 行 處理
            if (item.betItem[i].length > 4) {
              // 合併子玩法處理
              const bet = this.sliceArray(item.betItem[i], 4) // 如果超過4，陣列切分
              // 針對切分後的陣列做處理，如果是陣列的第一個元素，計算長度將colspan 加進去
              bet.forEach((it, idx) => {
                if (idx === 0) {
                  const obj = {
                    title,
                    subTitle,
                    betItem: it,
                    colspan: bet.length
                  } 
                  tempArr.push(obj)
                } else {
                  // 如果是 第2後的元素，則加入 noshow 要隱藏要本合併的儲存格，不然會被往右擠
                  const obj = {
                    title,
                    subTitle,
                    betItem: it,
                    noshow: true
                  } 
                  tempArr.push(obj)
                }
              })

            } else {
              const obj = {
                title,
                subTitle,
                betItem: item.betItem[i]
              }
              tempArr.push(obj)
            }
          })
          // 合併 玩法 判斷合併的起始點=> findIndex 會回完第一個 ture 的值
          const span = tempArr.findIndex((it) => item.title === it.title)
          spanArr.push(span)
          // 合併 玩法 判斷合併的格數
          let num = 0
          for(let i = span; i < tempArr.length; i++) {
            if (tempArr[i].title === item.title) num ++
          }

          for(let i = span; i < span + num; i ++) {
            if (i === span) tempArr[i].rowspan = num
            else tempArr[i].norowshow = true
          }
        })

      }
      return tempArr
    },
  },

  created() {
    // this.getNewData(this.data5);
    this.getData()
  },

  methods: {
    handleSpan({ row, column, rowIndex, columnIndex }) {
      // 玩法列合併
      if (columnIndex === 0) {
        if(row.rowspan) return [row.rowspan, 1]
        if (row.norowshow) return [0, 0]
      }
      // 子玩法列合併
      if (columnIndex === 1) {
        if (row.colspan) return [row.colspan, 1]
        if (row.noshow) return [0, 0]
      }
      // 投注項目及賠率合併
      if (columnIndex === 4) {
        if (row.betItem.length === 1) return [1,6]
      }
      if (columnIndex === 6) {
        if (row.betItem.length === 2) return [1,4]
      }
      if (columnIndex === 8) {
        if (row.betItem.length === 3) return [1,2]
      }
    },
    // 陣列切分函式
    sliceArray(originData, length) {
      const number = Math.ceil(originData.length / length);
      const arr = [];
      for (let i = 0; i < number; i++) {
        arr.push(originData.slice(i * length, length * (i + 1)));
      }
      console.log(arr)
      return arr;
    },

    rowspan(spanArr, position, spanName) {
      // console.log(spanArr, position, spanName)
      let name;
      this.data5.forEach((item, index) => {
        if (item.name) name = item.name;
        if (!item.name) item.name = name;
        // console.log(index)
        if (index === 0) {
          spanArr.push(1);
          position = 0;
        } 
        else {
          if (
            this.data5[index][spanName] ===
            this.data5[index - 1][spanName]
          ) {
            spanArr[position] ++;
            spanArr.push(0);
          } else {
            spanArr.push(1);
            position = index;
          }
        }
      });
    },
    getData () {
      this.rowspan(this.spanArr, this.pos1, "name");
      this.rowspan(this.dateArr, this.pos2, "date");
    },
    getNewData(data) {
      // 將值補回去給空值的位置
      const d = data;
      let name = "";
      d.forEach((item) => {
        if (item.name) name = item.name;
        if (!item.name) item.name = name;
        // console.log(name)
      });
      // console.log(d)
      // 詢問後端是否可以將值補給我
      for (var i = 0; i < data.length; i++) {
        // 第一轮，此时新数组spanArr=[1]
        if (i === 0) {
          this.spanArr.push(1);
          this.pos = 0;
          continue;
        }
        // 判断当前元素与上一个元素的name是否相同
        if (data[i].name === data[i - 1].name) {
          this.spanArr[this.pos]++;
          this.spanArr.push(0);
          // 第二轮，假设走这里，则此时为新数组为[2,0]
          if (data[i].date === data[i - 1].date) {
            this.dateArr[this.spos]++;
            this.dateArr.push(1);
          }
        } else {
          this.spanArr.push(1);
          this.pos = i;
          // 第三轮，假设走这里，则此时为新数组为[2,0,1]
        }
      }

      // for (var j = 0; j < data.length; j++) {
      //   // 第一轮，此时新数组spanArr=[1]
      //   if (j === 0) {
      //     this.dateArr.push(1);
      //     this.spos = 0;
      //     continue;
      //   }
      //   // 判断当前元素与上一个元素的name是否相同
      //   if (data[j].date === data[j - 1].date) {
      //     this.dateArr[this.spos]++;
      //     this.dateArr.push(0);
      //   } else {
      //     this.dateArr.push(1);
      //     this.spos = j;
      //     // 第三轮，假设走这里，则此时为新数组为[2,0,1]
      //   }
      // }

      this.table = data;
      // console.log(this.dateArr);
      // console.log(this.spanArr);
      // console.log(this.table);
    },
  },
};
</script>


