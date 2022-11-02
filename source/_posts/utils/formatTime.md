---
title: 时间格式化
date: 2022-11-2 10:41:09
update: 2022-11-2 10:41:11
tags:
  - 时间
categories: [工具集]
---

# 简介

- 时间格式化函数
- @param { Nubmer } time 任意符合Date()的世时间格式
- @param { Nubmer } cFormat 格式化 默认 '{y}-{m}-{d} {h}:{i}:{s}'
# CODE
``` javascript
function parseTime(time, cFormat) {
	if (arguments.length === 0) {
	  return null
	}
  if (!time) return ''
  /* 修复IOS系统上面的时间不兼容*/
  if (time.toString().indexOf('-') > 0) {
    time = time.replace(/-/g, '/')
  }
  const format = cFormat || '{y}-{m}-{d} {h}:{i}:{s}'
  let date
  if (typeof time === 'object') {
    date = time
  } else {
    if ((typeof time === 'string') && (/^[0-9]+$/.test(time))) {
      time = parseInt(time)
    }
    if ((typeof time === 'number') && (time.toString().length === 10)) {
      time = time * 1000
    }
    date = new Date(time)
  }
  const formatObj = {
    y: date.getFullYear(),
    m: date.getMonth() + 1,
    d: date.getDate(),
    h: date.getHours(),
    i: date.getMinutes(),
    s: date.getSeconds(),
    a: date.getDay()
  }
  const time_str = format.replace(/{([ymdhisa])+}/g, (result, key) => {
    const value = formatObj[key]
    // Note: getDay() returns 0 on Sunday
    if (key === 'a') {
      return ['日', '一', '二', '三', '四', '五', '六'][value]
    }
    return value.toString().padStart(2, '0')
  })
  return time_str
}
```