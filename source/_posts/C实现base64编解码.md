---
title: C实现base64编解码
date: 2020-12-10 07:54:21
tags: 
    - C语言
toc: true
categories: C语言
---

找到一份以前写的使用C语言进行编解码的代码。

## 头文件
```c
#ifndef BASE64_H__
#define BASE64_H__

// 对字符串进行base64加密
// str： 要进行加密的字符串
unsigned char* base64EncodeStr(unsigned char str[]);

// 对base64编码解密
// code: 要进行解密的字符串
unsigned char*base64DecodeStr(unsigned char code[]);

#endif
```

## 代码
```c
#include<stdio.h>
#include<stdlib.h>		// 内存
#include<string.h>		// strcmp()

// base64编码表
unsigned char* encodeTable = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";

// 对字符串进行base64加密
// str： 要进行加密的字符串
unsigned char* base64EncodeStr(unsigned char str[])
{
	// str_len,要计算的字符串长度
	// encode_len,加密后的字符串长度
	unsigned long str_len, encode_len;
	
	str_len = strlen(str);
	// 计算加密后的字符串长度
	// （字符串长度 + 2） / 3类似与取进一法，向上取整，
	encode_len = (str_len + 2) / 3 * 4;

	//分配内存
	unsigned char* encode;
	encode = (unsigned char*)malloc(sizeof(unsigned char) * encode_len);
	encode[encode_len] = '\0';

	// 进行编码
	int i, j;
	// 循环每三个字节执行操作
	for (i = 0, j = 0; i < encode_len - 2; i += 4, j += 3) {
		// 此处进行位操作
		// 字符串第一个字节右移两位，高位补零
		// 获取加密后的第一个字节        
		encode[i] = encodeTable[str[j] >> 2];		
		// 将字符串第一个字节与0x3(0000 0011)执行与操作，获取第一个字节的低两位
		// 第二个字节右移四位与执行或操作
		// 获取加密后第二个字节
		encode[i + 1] = encodeTable[(str[j] & 0x3) << 4 | str[j + 1] >> 4];		
		// 将字符串第二个字节与0xf(0000 1111)执行与操作，获取第二个字节的低四位
		// 第三个字节右移六位与执行或操作
		// 获取加密后的第三个字节
		encode[i + 2] = encodeTable[(str[j + 1] & 0xf) << 2 | str[j + 2] >> 6];
		// 将字符串第三个字节与0x3f(0011 1111)执行与操作，获取第三个字节的低六位
		// 获取加密后第四个字节
		encode[i + 3] = encodeTable[str[j + 2] & 0x3f];
	}

	// 补=
	if (str_len % 3 == 1) {
		encode[encode_len - 2] = '=';
		encode[encode_len - 1] = '=';
	}
	else if (str_len % 3 == 2) {
		encode[encode_len - 1] = '=';
	}


	return encode;
}


// 将字符转成编码表里的位置并返回索引值，不在返回*，=号返回64
// code： 要转换的字符
// index： 在编码表里的位置
// return: 索引值，不在返回*，=号返回64
char changeToArr(char code,int index)
{
	if(code >= 65 && code<=90){
		code -= 65;
	}else if(code >= 97 && code <= 122){
		code -= 71;
	}else if(code >= 48 && code <= 57){
		code += 4;
	}else if(code == '+'){
		code += 19;
	}else if(code == '/'){
		code += 16;
	}else if(code == '='){
		code = 64;
	}else{
		printf("不存在字符!%d位,%c\n",index+1,code);
		return '*';
	}
	return code;
}

// 对base64编码解密
// code: 要进行解密的字符串
unsigned char*base64DecodeStr(unsigned char code[])
{
	// str_len： 解码后字符串的长度
	// code_len: 编码的长度
    unsigned long str_len,code_len;
    code_len = strlen(code);
    str_len = code_len / 4 * 3;
    
    // 分配内存
    unsigned char* decode;
    decode = (unsigned char*)malloc(sizeof(unsigned char) * code_len);
    decode[code_len] = '\0';
    
	// 等于号的个数
	int EqualsignNum = 0;

	if(code[code_len -1] == '='){
		EqualsignNum += 1;
	}
	if (code[code_len - 2] == '='){
		EqualsignNum += 1;
	}

    // 进行解码
    int i,j;

    for(i = 0,j = 0;i < code_len - 3;i += 3,j += 4){
		// 解码		
		for(int t=j;t<=j+3;t++){
			if(changeToArr(code[t],t) != '*'){
				code[t] = changeToArr(code[t],t);
			}else{				
				return "请重试";
			}
		}
		
		decode[i] = code[j] << 2 | code[j+1] >> 6;			
		
		if(i+1 == str_len -2 && EqualsignNum == 2){
			decode[i+1] = ' ';
		}else{
			decode[i+1] = code[j+1] << 4 | (code[j+2]& 0x3c) >> 2;
		}			

		if(i+2 == str_len - 1 && EqualsignNum != 0 ){
			decode[i+2] = ' ';
		}else{
			decode[i+2] = (code[j+2] & 0x3) << 6 | code[j+3];
		}	
    }

    return decode;
}
```