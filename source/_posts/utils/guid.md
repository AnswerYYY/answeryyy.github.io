---
title: 全局唯一标识符（Globally Unique Identifier）
date: 2022-3-22 10:45:16
update: 2022-3-22 10:45:18
tags:
  - guid
categories: [工具集]
---

# 简介

- 本算法来源于简书开源代码，详见：https://www.jianshu.com/p/fdbf293d0a85
- 全局唯一标识符（uuid，Globally Unique Identifier）,也称作 uuid(Universally Unique IDentifier)
- 一般用于多个组件之间,给它一个唯一的标识符,或者 v-for 循环的时候,如果使用数组的 index 可能会导致更新列表出现问题
- 最可能的情况是左滑删除 item 或者对某条信息流"不喜欢"并去掉它的时候,会导致组件内的数据可能出现错乱
- v-for 的时候,推荐使用后端返回的 id 而不是循环的 index
- @param {Number} len uuid 的长度
- @param {Nubmer} radix 生成 uuid 的基数(意味着返回的字符串都是这个基数),2-二进制,8-八进制,10-十进制,16-十六进制

# CODE
``` javascript
function guid(len = 32, radix = null) {
	let chars = '0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz'.split('');
	let uuid = [];
	radix = radix || chars.length;

	if (len) {
		// 如果指定uuid长度,只是取随机的字符,0|x为位运算,能去掉x的小数位,返回整数位
		for (let i = 0; i < len; i++) uuid[i] = chars[0 | Math.random() * radix];
	} else {
		let r;
		// rfc4122标准要求返回的uuid中,某些位为固定的字符
		uuid[8] = uuid[13] = uuid[18] = uuid[23] = '-';
		uuid[14] = '4';

		for (let i = 0; i < 36; i++) {
			if (!uuid[i]) {
				r = 0 | Math.random() * 16;
				uuid[i] = chars[(i == 19) ? (r & 0x3) | 0x8 : r];
			}
		}
	}
     return uuid.join('');
}
```