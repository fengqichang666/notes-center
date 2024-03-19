## 无需样式简单导出



```json
 "xlsx": "^0.18.5"
```

```js
//1、使用json_to_sheet需要转化数据为json格式 但是默认key值会作为表头显示出来  需要设置 workSheet['!rows'] = [{ hidden: true }]
//2、使用aoa_to_sheet需要转化数据为二维数组 
// 其它的还有table_to_sheet等等
const exportToExcel = () => {
	// 创建一个工作簿
  	const workbook = XLSX.utils.book_new();.
     const header = {
        OutletCode: '客户编号',
        OutletName: '客户名称',
       	EffectFrom: '活动生效时间',
       	EffectTo: '活动截止时间',
     }
    // 数据
    const data: any = [{'OutletCode':'xxx','OutletName':'xxx',...}];
    const widthList = [
      { wpx: 180 },
      { wpx: 180 },
      { wpx: 100 },
      { wpx: 80 },
      { wpx: 80 },
    ];
    data.unshift(header);
    // 创建一个工作表
   const workSheet = XLSX.utils.json_to_sheet(data);
  // 将工作表添加到工作簿
   XLSX.utils.book_append_sheet(workbook, workSheet, 'sheet名字');
   workSheet['!cols'] = widthList; //设置单元格宽度
   workSheet['!rows'] = [{ hidden: true }];  // 可以不显示第一行英文
   	// 如果需要合并   s:start开始位置 e:end结束位置  r:row行号 从 0 计起 c:col列号 从 0 计起
    // 示例
    // workSheet['!merges'] =  [
      // 纵向合并，范围是第1列的行1到行2
      { s: { r: 0, c: 0 }, e: { r: 1, c: 0 } },
      // 纵向合并，范围是第2列的行1到行2
      { s: { r: 0, c: 1 }, e: { r: 1, c: 1 } },
      // 横向合并，范围是第1行的列3到列5
      { s: { r: 0, c: 2 }, e: { r: 0, c: 4 } },
      // 横向合并，范围是第1行的列6到列11
      { s: { r: 0, c: 5 }, e: { r: 0, c: 10 } },
      // 横向合并，范围是第1行的列12到列17
      { s: { r: 0, c: 11 }, e: { r: 0, c: 16 } },
      // 横向合并，范围是第1行的列18到列23
      { s: { r: 0, c: 17 }, e: { r: 0, c: 22 } },
      // 横向合并，范围是第1行的列24到列29
      { s: { r: 0, c: 23 }, e: { r: 0, c: 28 } },
      // 横向合并，范围是第1行的列30到列35
      { s: { r: 0, c: 29 }, e: { r: 0, c: 34 } }
    ];
     XLSX.writeFile(workbook, 'xxxx.xlsx');
}
```



## 需要样式

```json
 "xlsx": "^0.18.5",	"file-saver": "^2.0.5",  "xlsx-style-vite": "^0.0.2"
```

```js
// 使用aoa_to_sheet
const exportToExcel = () => {
// 创建一个工作簿
    const workbook = XLSX.utils.book_new();
    const header = [
      '客户编号',
      '客户名称',
      '活动生效时间',
      '活动截止时间',
    ];
const data: any = [[x,x,x,x],[x,x,x,x]];
 const widthList = [
      { wpx: 180 },
      { wpx: 180 },
      { wpx: 100 },
      { wpx: 80 },
      { wpx: 80 },
    ];
    data.unshift(header);
    const workSheet = XLSX.utils.aoa_to_sheet(data);
    // 将工作表添加到工作簿
    XLSX.utils.book_append_sheet(workbook, workSheet, '分店明细列表');
    xlsxAddStyle(workSheet, widthList);
    // 导出Excel, 注意这里用到的是XLSXS对象
    let wbout = XLSXS.write(workbook, {
      bookType: 'xlsx',
      bookSST: false,
      type: 'binary',
    });
 	FileSaver.saveAs(
      new Blob([s2ab(wbout)], {
        type: 'application/octet-stream',
      }),
      'xxx.xlsx', // 保存的文件名
    );
}

 const xlsxAddStyle = (sheet, widthList) => {
    const mergeArr = []; // 合并的单元格
    const rowH: any = []; // 表格每列高度
    // 单元格外侧有框线
    // const borderAll = {
    //   top: { style: 'thin' },
    //   bottom: { style: 'thin' },
    //   left: { style: 'thin' },
    //   right: { style: 'thin' },
    // };
    // 单元格外侧无框线
    // const noBorder = {
    //   top: { style: '' },
    //   bottom: { style: '' },
    //   left: { style: '' },
    //   right: { style: '' },
    // };
    for (const key in sheet) {
      if (Object.hasOwnProperty.call(sheet, key)) {
        const element = sheet[key];
        if (typeof element === 'object') {
          const index = Number(letter(key)) - 1;
          rowH[index] = { hpx: 20 };
          element.s = {
            alignment: {
              horizontal: 'center', // 所有单元格水平居中
              vertical: 'center', // 所有单元格垂直居中
            },
            font: {
              name: '宋体',
              sz: 12,
              italic: false,
              underline: false,
              color: { auto: 1 },
            },
            // border: borderAll,
            // fill: {
            //   fgColor: { rgb: 'FFFFFFFF' },
            // },
          };
          // 指标值表格的样式
          // if (key.indexOf('C') > -1) {
          //   element.s.alignment.horizontal = 'right';
          // }
          // 标题的样式
          if (index === 0) {
            element.s.font.bold = true;
            element.s.border = {
              top: { style: 'thin', color: { rgb: '000000' } },
              left: { style: 'thin', color: { rgb: '000000' } },
              bottom: { style: 'thin', color: { rgb: '000000' } },
              right: { style: 'thin', color: { rgb: '000000' } },
            };
            element.s.fill = {
              patternType: 'solid',
              fgColor: { theme: 3, tint: 0.3999755851924192, rgb: 'DDD9C4' },
              bgColor: { theme: 7, tint: 0.3999755851924192, rgb: '8064A2' },
            };
          }
          // 处理合并单元格数组 s开始 e结束 r行 c列
          // if (element.v === '所属设备') {
          //   mergeArr.push({
          //     s: { r: 0, c: 0 },
          //     e: { r: 2, c: 1 },
          //   });
          // }
        }
      }
    }
    // 单元格的列宽
    sheet['!cols'] = widthList;
    // 单元格的行宽
    sheet['!rows'] = rowH;
    // 合并单元格
    sheet['!merges'] = mergeArr;
    return sheet;
  };

  // 去除字母方法，当列的数量超过25的时候会变成AA所以需要用正则匹配去掉所有的字母，剩下数字
  const letter = (str) => {
    let result;
    const reg = /[a-zA-Z]+/;
    while ((result = str.match(reg))) {
      //判断str.match(reg)是否没有字母了
      str = str.replace(result[0], ''); //替换掉字母  result[0] 是 str.match(reg)匹配到的字母
    }
    return str;
  };
  const s2ab = (s) => {
    var buf = new ArrayBuffer(s.length);
    var view = new Uint8Array(buf);
    for (var i = 0; i != s.length; ++i) view[i] = s.charCodeAt(i) & 0xff;
    return buf;
  };
```





