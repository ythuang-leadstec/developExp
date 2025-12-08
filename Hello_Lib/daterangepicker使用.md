[TOC]

# daterangepicker的使用

## 引入
```html
<script src="https://cdn.jsdelivr.net/jquery/latest/jquery.min.js"></script>
<script src="https://cdn.jsdelivr.net/momentjs/latest/moment.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/daterangepicker/daterangepicker.min.js"></script>
<link rel="stylesheet" type="text/css" href="https://cdn.jsdelivr.net/npm/daterangepicker/daterangepicker.css"/>

```

## 初始化
```html
<input id="inp" type="text">
<script>
$('#inp').daterangepicker();
</script>

```

## 基本的汉化配置
```js
$('#inp').daterangepicker({
    locale: {
        "format": 'YYYY-MM-DD',
        "separator": " - ",
        "applyLabel": "确定",
        "cancelLabel": "取消",
        "resetLabel": "重置",
        "fromLabel": "起始时间",
        "toLabel": "结束时间'",
        "customRangeLabel": "自定义",
        "weekLabel": "W",
        "daysOfWeek": ["日", "一", "二", "三", "四", "五", "六"],
        "monthNames": ["一月", "二月", "三月", "四月", "五月", "六月", "七月", "八月", "九月", "十月", "十一月", "十二月"],
        "firstDay": 1
    },
    ranges: {
        '今日': [moment(), moment()],
        '昨日': [moment().subtract(1, 'days'), moment().subtract(1, 'days')],
        '最近7日': [moment().subtract(6, 'days'), moment()],
        '最近30日': [moment().subtract(29, 'days'), moment()],
        '本月': [moment().startOf('month'), moment().endOf('month')],
        '上月': [moment().subtract(1, 'month').startOf('month'), moment().subtract(1, 'month').endOf('month')],
        '今年': [moment().startOf('year'), moment().endOf('year')],
    },
    "alwaysShowCalendars": true,
    "startDate": moment().subtract(6, 'days'),  //7天前
    "endDate": new Date(),                      //现在
    "opens": "right",
}, function (start, end, label) {
    console.log(start.format('YYYY-MM-DD'))
    console.log(end.format('YYYY-MM-DD'))
    console.log(label)
});

```

## 使用技巧
```html
<input id="filter-daterangepicker" data-placeholder="All dates" name="daterange" data-format="DD MMM YYYY" data-separator=" - " data-days="Sun,Mon,Tue,Wed,Thu,Fri,Sat" data-months="Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec" class="form-control daterange-picker js-filter-daterangepicker filter-daterangepicker" placeholder="All dates">

<script>
  // 一些format可以放在标签的data属性里,不同语言使用i18n配置
  var days = selector.data('days').split(',');
  var months = selector.data('months').split(',');
  var formatDate = selector.data('format');
  var separator = selector.data('separator');
  var placeholder = selector.data('placeholder');
</script>
```
