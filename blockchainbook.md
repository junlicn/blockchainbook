# 区块链设计与实现笔记

----------

拟定目录大纲:  
1. 前言
2. 区块链基本结构
3. 加解密算法实现
4. 区块数据结构从定义到初步实现
5. 区块节点读取与验证
6. 节点和应用程序之间协议
7. 节点之间沟通
8. 侧链设计原理
9. 侧链开发
10. 智能合约设计原理
11. 智能合约开发
12. 计算机基础
13. Linux相关说明
14. 程序开发语言相关说明
15. 密码学相关说明
16. 资料链接

若有更正请联系: 李君
邮件: junlicn@foxmail.com
2017.10.31

实际自动导航目录:

[toc]

目录不可见时访问: https://www.zybuluo.com/zhongdao/note/933849


# 1.前言

## 1.1 内容来源
区块链技术开发公开课:  
授课老师: 辕询, 李涛涛  
直播地址: http://www.itdks.com/dakashuo/playback/1428

## 1.2 笔记缘起
根据老师公开课上讲的内容, 构造一个完整笔记,便于学习和掌握. 随着课程的进展, 不断添加和整理.
此文档的完整内容来自:公开的课程内容、源代码，资料、搜索引擎搜到的资料、相关技术书籍上的资料等, 我对内容做了整理,使得易于阅读和条理化，对于部分代码添加了注释和自己的理解, 若有错误请沟通纠正。
其中引用到一些同学的笔记内容或者截图，其他笔记内容来源者包括: 程书芝, 袁丁逸涵, 郜晶, liuhongshuo, 谭东雷, 潘杰 ...
笔记编写过程中有张博成,pz100等对错误之处给出了的修订建议.
要特别感谢老师依据开源传统所教授的内容. 

### 1.3 内容概要
创造一个易于理解说明的区块链.从理论到程序实现. 
为了便于对编程不熟悉的同学学习和备查,也增加了所需要的相关的内容.

具体内容参考本文目录, 
```
1.     区块链的基本结构；
2.     为什么私钥最重要，以及随机数 –> 私钥 -> 公钥 -> 地址的生成过程；
3.     每个区块链程序都由三种基本协议组成
  a)     A协议：节点内部的规则，数据格式、交易规则、加密/解密算法等；
  b)     B协议：应用程序与节点沟通的协议；
  c)     C协议：节点之间沟通的协议；
4.     区块间隔和冲突处理机制；
5.     计算机的数据存储及区块链数据结构。
```


### 1.4 每次课程摘要

* 第1节:  
```
主要内容：
1.     区块链的基本结构；
2.     为什么私钥最重要，以及随机数 –> 私钥 -> 公钥 -> 地址的生成过程；
3.     文件系统的结构；
4.     每个区块链程序都由三种基本协议组成
a)     A协议：节点内部的规则，数据格式、交易规则、加密/解密算法等；
b)     B协议：应用程序与节点沟通的协议；
c)     C协议：节点之间沟通的协议；
5.     区块间隔和冲突处理机制；
6.     计算机的数据存储及区块链数据结构。
```
* 第2,3节:
本周的两次课程当中，辕询老师深入拆解比特币的结构，从代码层面向同学们展示了比特币的设计。并通过代码演示，详细的展示了私钥、公钥、地址和区块的生成过程。通过课后的作业练习，让同学们动手生产私钥、公钥和区块，从代码层面理解比特币的原理。
* 第4节
周三第4次课，网络编程。辕询老师讲解C语言中套接字函数的用法，并在稍后带同学们使用C语言套接字实现了客户端之间的简单通讯程序。
* 第5节
周六第5次课，使用C语言重构代码。辕询老师带同学们回顾区块结构，说明PHP加密函数的兼容性问题，并带领大家使用C语言重新实现区块结构的生产过程。
* 第6节
周日第6次课，由密码学主讲李涛涛老师，为同学们讲解区块链中使用的加密算法，并带领同学们深入探讨什么是HASH，为什么要使用HASH等问题。
* 第7课
使用C语言重写区块链生成过程。复习区块结构，Transaction的数据结构，Head的数据结构，使用C语言实现Transaction、Head的生成过程。
* 第8课
密码学第2次课，讲解了ECDSA（elliptical curve digital signaturealgorithm）椭圆曲线数字签名算法。
一.        什么是椭圆曲线数字签名算法ECDSA(EllipticalCurve digital Signature Algorithm)
二.        ECC (elliptical curve cryptography)和ECDSA的关系
三.        为什么使用ECDSA
四.        ECDSA在区块链中的应用
五.        ECDSA的编程实现
* 第9课，区块数据解析，如何将一个区块的数据读取出来。
一.        使用scan方法读取transaction的片段长度
二.        使用build方法构造一个length + data的数据结构
三.        将build方法生成的指针存储到无类型（void）链表
四.        使用循环读取链表内容
课程调整
本周确定了新增8次课时的上课时间，每周五、周日增加一次授课。调整后每周授课4次，分别为周三、周五、周六及周日。




# 2.区块链基本结构


![此处输入图片的描述][1]

或者如下图示意:
![image_1btodhc8onfa1mdp10u21pa11ckqm.png-83.1kB][2]

## 2.1 区块定义
```
HEAD - 5 attribute   108BYTE
BODY - 1 or more TRANSACTION
TRANSACTION  - 1 or more  INPUT and OUTPUT
            COINBASE  (创始, 挖矿奖励)
```

```
HEAD
   CURRENT: 32
   NONCE: 4
   PREVIOUS: 32 -> completely zero if genesis block
   TARGET: 32
   TIME: 4

BODY
   TRANSACTION #1
      INPUTS: 1个
         INDEX: 4 -> completely zero
         PUBLIC: 0
         SIGNATURE: 0
         TRANSACTION: 32 -> completely zero
      OUTPUTS: 1个
         ADDRESS: 灵活 -> miner's address
         AMOUNT: 8 -> 50 或者 100 (挖矿奖励)

   TRANSACTION #2
   TRANSACTION #3
      INPUTS: 1个或者多个
         INDEX: 4
         PUBLIC: 灵活
         SIGNATURE: 灵活
         TRANSACTION: 32
      OUTPUTS: 1个或者多个
         ADDRESS: 灵活
         AMOUNT: 8

```

## 2.2 密码学算法实现步骤简要说明
随机数字 ->  私钥 -> 公钥 -> 地址

加密和解密最大的危险就是是否产生真正的随机数.

```
a = open ("/dev/arandom")  打开文件
b = read (a 128)       随机数包括[0 - 256)
c = get.private (b)    私钥
d = get.public (c)     公钥
e = get.address (d)    地址
```


## 2.3 交易过程说明
### 2.3.1 交易过程图示
![image_1btoaked7g6m15uo19okr2tsirm.png-233.9kB][3]

### 2.3.2 区块中第1个交易用于矿工
```
第1个交易是矿工奖励, Transaction中的output要设为矿工的地址, 这里的实现用公钥代替.
所以构建时,要取得矿工的地址.
```


# 3. 加解密算法实现
## 3.1 安全库说明

1. Libtomcrypt
2. OpenSSL

## 3.2 代码实现说明


### 3.2.1 md5
md5 输出 128bit,  每8bit为1个字节, 也就是16个字节, 1个字节2^8 [0,256)用2个字母表示(00-ff), 共计32个字母
换成sha256时, 需要把16字节out数组改为32字节out
```
// MD5输出128bit  SHA1输出160bit SHA256输出256bit .    
// c语言里 %x表示16进制, 会输出2个字母.  
// SHA256 输出 256bit, 32个字节, 64个字符.  

#include "tomcrypt.h"

int main(int count, char **arguments)
{
  hash_state md;
  unsigned char *in = "lijun", out[16];

  /* setup the hash */
  md5_init(&md);

  /* add the message */
  md5_process(&md, in, strlen(in));

  /* get the hash in out[0..15] */
  md5_done(&md, out);

  for(int i = 0; i < 16; i++)
  {
     printf("%02x", out[i]);    // %02x 表示输出2个字符的16进制,否则0时会省略掉.
  }
  printf("\n");

  return 0;
}
}

```


### 3.2.2 sha256

```
#include "stdio.h"
#include "stdlib.h"
#include "string.h"
#include "tomcrypt.h"   //密码库
char *get_file(char *path){             //读取文件
        FILE *file;
        int length;
        char *contents;

        //获取文件长度
        file = fopen(path , "r+");
        fseek(file , 0 , SEEK_END);     //跳到文件结尾
        length = ftell(file);           //给出文件长度
        fseek(file , 0 ,SEEK_SET);      //再跳回文件开头

        //分配内存，读取文件 
        //! 最初的代码是length+1, 改为length
        contents = malloc(length);  //给文件分配内存
        fread(contents , 1 , length , file);

        //关闭文件
        fclose(file);
        return contents ;               //返回文件内容
}
void main(){                            //对文本进行Hash加密
        char *input , *output;
        int length;

        output = malloc(1000);          //分配1000字节的内存
        length = 1000;
        input = get_file("essay.txt");  //读取文件

        register_hash(&sha256_desc);    //激活哈希函数，必须！！
        hash_memory(find_hash("sha256") , input , strlen(input) , output , &leng)

        printf("%s" , output);
        //printf("%d%c" , length , 10); //打印长度
        //printf("%02x%c" , output[0] , 10);    //打印第一个字符，%02x：十六进制
}
```



### 3.2.3 获取私钥
```
unsigned char *get_private(unsigned int *length)
{
   prng_state random;  // 随机生产器的状态.
   ecc_key key;      //保存私钥和公钥的数据结构
   unsigned char *buffer;

   buffer = malloc(*length = 1000);
   // 随机生成器状态, 系统随机生成器, ?, 保存位置 
   ecc_make_key(&random, find_prng("sprng"), 32, &key); 
   ecc_export(buffer, length, PK_PRIVATE, &key);

   return buffer;
}
```

### 3.2.4 获取公钥
```
unsigned char *get_public(unsigned int *length, unsigned char *private)
{
   ecc_key key;
   unsigned char *buffer;

   ecc_import(private, *length, &key);
   buffer = malloc(*length = 1000);
   ecc_export(buffer, length, PK_PUBLIC, &key);

   return buffer;
}

```

### 3.2.5 编码输出
Extra libraries:  TomsFastMath library
每次使用tomcryp函数库时, 必须要有如下一行,这是它的固定规则:
ltc_mp = tfm_desc;


将private, public输出的生字节, 用base64转换成ASCII.  便于和其他人共享. 
```
void main()
{
   unsigned char *private, *public, *buffer;
   unsigned int length_private, length_public, length_buffer;

   ltc_mp = tfm_desc;
   register_prng(&sprng_desc);   //激活函数
   register_hash(&sha256_desc);

   private = get_private(&length_private);
   length_public = length_private;
   public = get_public(&length_public, private);

   buffer = malloc(1000);
   // tomcrypt库函数_ecc_xxx 生产出自己的私钥,  生成的是生字节.

   memset(buffer, 0, 1000);
   base64_encode(private, length_private, buffer, &length_buffer);
   printf("%s%c%c", buffer, 10, 10);

   memset(buffer, 0, 1000);
   base64_encode(public, length_public, buffer, &length_buffer);
   printf("%s%c", buffer, 10);
}
```


# 4. 区块数据结构从定义到初步实现

## 4.1 Head

### 4.1.1 结构说明
```
HEAD
   CURRENT: 32
   NONCE: 4
   PREVIOUS: 32 -> completely zero if genesis block
   TARGET: 32
   TIME: 4
   VALENCE: 4
```

### 4.1.2 程序数据结构

Head的长度固定是108字节, 所以不需要保存长度.

**字段说明**| current | nounce | previous | target | time | valence  
--- | --- | --- | --- | --- | --- | ---| 
**说明** | BODY的sha256输出,当前block的ID   | 挖矿难度   |  前个区块的ID  |  越大难度越小  |  ?时间  | 交易个数
**长度(字节)**|32   | 4   |  32  |  32  |  4  | 4


nounce , target 用于pow, 在链的最初不需要pow, 当有很多个节点创造block时才用到pow, 

### 4.1.3 程序代码
```
unsigned char *get_head(unsigned char *root)
{
   unsigned char *complete, *y;
   unsigned int *x;

   complete = malloc(104);

   y = malloc(32);
   memcpy(y, root, 32);
   memcpy(complete, y, 32);

   x = malloc(4);
   *x = 0;
   memcpy(complete + 32, x, 4);

   y = malloc(32);
   memset(y, 0, 32);
   memcpy(complete + 36, y, 32);

   y = malloc(32);
   memset(y, 255, 32);
   memcpy(complete + 68, y, 32);

   x = malloc(4);
   *x = time(0);
   memcpy(complete + 100, x, 4);

   return complete;
}

```

## 4.2 Body

### 4.2.1 Transaction-Input

#### 4.2.1.1 结构说明
```
   TRANSACTION #1 // 第一个区块.
      INPUTS: 1
         INDEX: 4 -> completely zero
         PUBLIC: 0
         SIGNATURE: 0
         TRANSACTION: 32 -> completely zero

   TRANSACTION #2,#3,...  // 之后的区块
      INPUTS: 1个或者以上
         INDEX: 4
         PUBLIC: linghuo
         SIGNATURE: linghuo
         TRANSACTION: 32
```


#### 4.2.1.2 程序数据结构

**字段名**| A (总长) | index | B | public | C | Signature | Transaction的sha256  
--- | --- | --- | --- | --- | --- | --- |--- |
**说明**|总长   | index?   |  public长度  |  公钥  |  签名长度  |  签名  |  交易的hash, merkle root |
**长度(字节)**|4   | 4   |  4  |  B  |  4  |  C  |  32 |


#### 4.2.1.3 程序代码

```
unsigned char *get_input(unsigned int *total)
{
   // A index B public C signature transaction

   char *private, *public, *complete;
   int length;

   int *x;
   unsigned char *y;

   private = get_private(&length);
   public = get_public(&length, private);

   complete = malloc(*total = 48 + length);

   x = malloc(4);
   *x = 48 + length;
   memcpy(complete, x, 4); // 放置总长

   x = malloc(4);
   *x = 0;
   memcpy(complete + 4, x, 4);  //放置Index长

   x = malloc(4);
   *x = length;
   memcpy(complete + 8, x, 4); // 放置public的长度 

   y = malloc(length);
   memcpy(y, public, length);
   memcpy(complete + 12, y, length); // 放置公钥public 

    //!! 应该放置signature 的长度, 和 signature, 因为还未讲到signature的用法, 暂时先不放.
   x = malloc(4);
   *x = 0;
   memcpy(complete + 12 + length, x, 4); //放置Signature长度.

   y = malloc(32);
   memset(y, 0, 32);
   memcpy(complete + 16 + length, y, 32); // 放置Transaction ??

   return complete;
}

```

### 4.2.2 Transaction-Output

#### 4.2.2.1 结构说明

第1个交易中的Output
```
      OUTPUTS: 1个
         ADDRESS: 大小不定 -> 矿工地址
         AMOUNT: 8字节 -> 100或者50
```
第2+个交易中的Output
```
      OUTPUTS: 1个
         ADDRESS: 大小不定 -> 地址
         AMOUNT: 8字节 
```

#### 4.2.2.2 程序数据结构

**字段名**| A  | B  | Address | Amount 
--- | --- | --- | --- | ---
**说明**| 总长度 | 地址长度 | 交易地址 | 交易数量
**长度(字节)**| 4   | 4   |  B  | 8


#### 4.2.2.3 代码

```
unsigned char *get_output(unsigned int *total)
{
   unsigned char *private, *public, *complete, *y;
   unsigned int length, *x, z;

   private = get_private(&length);
   public = get_public(&length, private);

   // len_A len_B address amount
   // 4 + 4 + B + 8

   complete = malloc(*total = 16 + length);

   x = malloc(4);
   *x = 16 + length;
   memcpy(complete, x, 4); // 设置总长 A

   x = malloc(4);
   *x = length;
   memcpy(complete + 4, x, 4); // 设地址长度 B

   y = malloc(length);
   memcpy(y, public, length);
   memcpy(complete + 8, y, length); // 设置地址, 暂时把公钥作为地址

   y = malloc(8);
   z = 1000;
   memcpy(y, &z, 4);
   memset(y + 4, 0, 4);
   memcpy(complete + 8 + length, y, 8); // 设置8字节的交易数量

   return complete;
}

```

### 4.2.3 Transaction

#### 4.2.3.1 结构说明
```
   TRANSACTION #1
      INPUTS: 1
         INDEX: 4 -> completely zero
         PUBLIC: 0
         SIGNATURE: 0
         TRANSACTION: 32 -> completely zero
      OUTPUTS: 1
         ADDRESS: linghuo -> miner's address
         AMOUNT: 8 -> 50 huozhe 100

   TRANSACTION #2
   TRANSACTION #3
      INPUTS: yige huozhe yishang
         INDEX: 4
         PUBLIC: linghuo
         SIGNATURE: linghuo
         TRANSACTION: 32
      OUTPUTS: yige huozhe yishang
         ADDRESS: linghuo
         AMOUNT: 8
```
#### 4.2.3.2 程序数据结构

Transaction
**字段名**| A  | B | C | Input*  | Output* 
---|--- | --- | --- | --- | --- |
**说明**| 总长度(含自己)   | 输入个数  |  输出个数   | 输入列表 | 输出列表  
**长度(字节)**|4   | 4   |  4  |  B |   C 

结构有变更. 


#### 4.2.3.3 程序代码(旧代码,待更改)
```
unsigned char *get_transaction(unsigned int *total)
{
   unsigned char *input, *output, *complete, *y;
   unsigned int length_input, length_output, *x;

   input = get_input(&length_input);
   output = get_output(&length_output);

   complete = malloc(*total = 12 + length_input + length_output);

   x = malloc(4);    // A
   *x = 12 + length_input + length_output;
   memcpy(complete, x, 4);

   x = malloc(4);
   *x = 1;          // B, 只有1个输入
   memcpy(complete + 4, x, 4);

   x = malloc(4);
   *x = 1;         // C 只有一个输出
   memcpy(complete + 8, x, 4);

   y = malloc(length_input);     // input
   memcpy(y, input, length_input);
   memcpy(complete + 12, y, length_input);

   y = malloc(length_output);    // output
   memcpy(y, output, length_output);
   memcpy(complete + 12 + length_input, y, length_output);

   return complete;
}

```

## 4.3 整体结构回顾与区块交易示例

### 4.3.1 头部和身部(交易)

头部

```
HEAD - 6 attribute   108BYTE
BODY - 1 or more TRANSACTION
TRANSACTION  - 1 or more  INPUT and OUTPUT
            COINBASE  (创始, 挖矿奖励)
```

身部

```
HEAD
   CURRENT: 32
   NONCE: 4
   PREVIOUS: 32 -> completely zero if genesis block
   TARGET: 32
   TIME: 4
   VALENCE: 4

BODY
   TRANSACTION #1
      INPUTS: 1个
         INDEX: 4 -> completely zero
         PUBLIC: 0
         SIGNATURE: 0
         TRANSACTION: 32 -> completely zero
      OUTPUTS: 1个
         ADDRESS: 灵活 -> miner's address
         AMOUNT: 8 -> 50 huozhe 100

   TRANSACTION #2
   TRANSACTION #3
      INPUTS: 1个或者多个
         INDEX: 4
         PUBLIC: 灵活
         SIGNATURE: 灵活
         TRANSACTION: 32
      OUTPUTS: 1个或者多个
         ADDRESS: 灵活
         AMOUNT: 8

```


### 4.3.2 各部分具体定义

HEAD:

**字段说明**| current | nounce | previous | target | time | valence  
--- | --- | --- | --- | --- | --- | ---| 
**说明** | BODY的sha256输出,当前block的ID   | 挖矿难度   |  前个区块的ID  |  越大难度越小  |  ?时间  | 交易个数
**长度(字节)**|32   | 4   |  32  |  32  |  4  | 4


Transaction
**字段名**| A  | B | C | Input*  | Output* 
---|--- | --- | --- | --- | --- |
**说明**| 总长度(含自己)   | 输入个数  |  输出个数   | 输入列表 | 输出列表  
**长度(字节)**|4   | 4   |  4  |  B |   C 


INPUT:

**字段名**| A (总长) | index | B | public | C | Signature | Transaction的sha256  
--- | --- | --- | --- | --- | --- | --- |--- |
**说明**|总长   | index?   |  public长度  |  公钥  |  签名长度  |  签名  |  交易的hash, merkle root |
**长度(字节)**|4   | 4   |  4  |  B  |  4  |  C  |  32 |

OUTPUT:

**字段名**| A  | B  | Address | Amount 
--- | --- | --- | --- | ---
**说明**| 总长度 | 地址长度 | 交易地址 | 交易数量
**长度(字节)**| 4   | 4   |  B  | 8

### 4.3.3 区块交易实例

```
#1: 3930
   [8629] -> YUANXUN(100)

#2: 4479
   [4140] -> YUANXUN(100)
   [7159] YUANXUN/8629/1 -> YUANXUN(99) PANPENG(1)

A index B public C signature transaction
```

## 4.4 程序开发实现

### 4.4.1 常用函数设计
为了方便处理区块字段, 设计了相关函数. 简化常见的操作.

```
char *string_create(char *old, int length)
char *string_create_improper(char *old)
char *string_create_progression(int a)
int string_measure(char *buffer)
void string_update(char *object, char *buffer, int length)
void string_update_4(char *object, int quantity)
void string_update_8(char *object, uint64_t quantity)
void string_update_initial(char *object, char *initial)
char *string_eject(char *object)
int scan(char *source)
```

### 4.4.2 区块及示例区块数据生成
```
#define TFM_DESC

#include "stdio.h"
#include "stdlib.h"
#include "string.h"
#include "tfm.h"
#include "tomcrypt.h"

char *string_create(char *old, int length)
{
   char *new;
   int *_6157;

   new = malloc(length + 4) + 4;
   memcpy(new, old, length);

   _6157 = (int *) (new - 4);
   *_6157 = length;

   return new;
}

char *string_create_improper(char *old)
{
   char *new;
   int *_7193;

   new = malloc(strlen(old) + 4) + 4;
   memcpy(new, old, strlen(old));

   _7193 = (int *) (new - 4);
   *_7193 = strlen(old);

   return new;
}

char *string_create_progression(int a)
{
   char *c;
   int *_3919;

   c = malloc(a + 4) + 4;

   _3919 = (int *) (c - 4);
   *_3919 = 0;

   return c;
}

int string_measure(char *buffer)
{
   int *_4721;

   _4721 = (int *) (buffer - 4);

   return *_4721;
}

void string_update(char *object, char *buffer, int length)
{
   int *_9492;

   _9492 = (int *) (object - 4);
   memcpy(object + *_9492, buffer, length);
   *_9492 += length;
}

void string_update_4(char *object, int quantity)
{
   int *_7751, *_2888;

   _7751 = (int *) (object - 4);
   _2888 = (int *) (object + *_7751);
   *_2888 = quantity;
   *_7751 += 4;
}

void string_update_8(char *object, uint64_t quantity)
{
   int *_7751;
   uint64_t *_2888;

   _7751 = (int *) (object - 4);
   _2888 = (uint64_t *) (object + *_7751);
   *_2888 = quantity;
   *_7751 += 8;
}

void string_update_initial(char *object, char *initial)
{
   int *_8478, *_5970;

   _8478 = (int *) (object - 4);
   _5970 = (int *) initial;

   memcpy(object + *_8478, initial, *_5970);
   *_8478 += *_5970;
}

char *string_eject(char *object)
{
   char *new;
   int *_6558;

   _6558 = (int *) (object - 4);
   new = malloc(*_6558);
   memcpy(new, object, *_6558);

   return new;
}

char *get_maximal()
{
   char *digest;

   digest = malloc(32);
   memset(digest, 255, 32);

   return digest;
}

char *get_minimal()
{
   char *digest;

   digest = malloc(32);
   memset(digest, 0, 32);

   return digest;
}

int scan(char *source)
{
   int *size;

   size = (int *) source;

   return *size;
}

char *get_file(char *path) {

   FILE *file;
   int length;
   char *contents;

   file = fopen(path, "r+");
   fseek(file, 0, SEEK_END);
   length = ftell(file);
   fseek(file, 0, SEEK_SET);

   contents = malloc(length + 1);
   fread(contents, 1, length, file);

   fclose(file);

   return contents;
}
// same info, after ecc signature, output different sig length because of random number.
char *get_signature(char *object, char *private)
{
   prng_state _1307;
   ecc_key _1317;
   int _1648;
   char *_5497;

   _5497 = malloc(1000);
   _1648 = 1000;

   ecc_import(private, string_measure(private), &_1317);
   ecc_sign_hash(object, string_measure(object), _5497, &_1648, &_1307, find_prng("sprng"), &_1317);

   return string_create(_5497, _1648);
}

unsigned char *get_private(unsigned int *length)
{
   prng_state random;
   ecc_key key;
   unsigned char *buffer;

   buffer = malloc(*length = 1000);
   ecc_make_key(&random, find_prng("sprng"), 32, &key);
   ecc_export(buffer, length, PK_PRIVATE, &key);

   return buffer;
}

unsigned char *get_public(unsigned int *length, unsigned char *private)
{
   ecc_key key;
   unsigned char *buffer;

   ecc_import(private, *length, &key);
   buffer = malloc(*length = 1000);
   ecc_export(buffer, length, PK_PUBLIC, &key);

   return buffer;
}

char *build_input(int _8626, char *_5409, char *_4051, char *_3775)
{
   char *_9016;
   int *_1933;

   _9016 = malloc(48 + string_measure(_5409) + string_measure(_4051));
   _1933 = (int *) _9016;
   *_1933 = 48 + string_measure(_5409) + string_measure(_4051);
   _1933 = (int *) (_9016 + 4);
   *_1933 = _8626;
   _1933 = (int *) (_9016 + 8);
   *_1933 = string_measure(_5409);
   memcpy(_9016 + 12, _5409, string_measure(_5409));
   _1933 = (int *) (_9016 + 12 + string_measure(_5409));
   *_1933 = string_measure(_4051);
   memcpy(_9016 + 16 + string_measure(_5409), _4051, string_measure(_4051));
   memcpy(_9016 + 16 + string_measure(_5409) + string_measure(_4051), _3775, 32);

   return _9016;
}

char *build_output(char *_7095, uint64_t _8432)
{
   char *_3516;
   int *_8347;
   uint64_t *_9910;

   _3516 = malloc(16 + string_measure(_7095));
   _8347 = (int *) _3516;
   *_8347 = 16 + string_measure(_7095);
   _8347 = (int *) (_3516 + 4);
   *_8347 = string_measure(_7095);
   memcpy(_3516 + 8, _7095, string_measure(_7095));
   _9910 = (uint64_t *) (_3516 + 8 + string_measure(_7095));
   *_9910 = _8432;

   return _3516;
}

char *get_sha256(char *buffer, int size)
{
   hash_state resource;
   char *digest;

   digest = malloc(32);
   sha256_init(&resource);
   sha256_process(&resource, buffer, size);
   sha256_done(&resource, digest);

   return digest;
}

char *get_md5(char *buffer, int size)
{
   hash_state resource;
   char *digest;

   digest = malloc(16);
   md5_init(&resource);
   md5_process(&resource, buffer, size);
   md5_done(&resource, digest);

   return digest;
}

char *get_merkle_root(char *old, int size)
{
   char *new, output[32], input[64];
   int index;

   if (size == 1) {

      return old;
   }

   if (size % 2) {

      new = malloc(32 * (size + 1));
      memcpy(new, old, size * 32);
      memcpy(new + size * 32, old + (size - 1) * 32, 32);

      return get_merkle_root(new, size + 1);
   }

   new = malloc(32 * size / 2);

   for (index = 0; index < size; index += 2) {

      memcpy(input, old + index * 32, 64);
      memcpy(new + (index / 2) * 32, get_sha256(input, 64), 32);
   }

   return get_merkle_root(new, size / 2);
}

void print_digest(char *digest)
{
   int index;

   for (index = 0; index < 32; index++)
      printf("%02hhx", digest[index]);

   printf("%c", 10);
}

// ------------------

char *transaction_7159(int A, char *B, char *C, char *D, char *lichangli, char *panpeng)
{
   char *input_0, *output_0, *output_1, *transaction;

   input_0 = build_input(A, B, C, D);
   output_0 = build_output(lichangli, 99);
   output_1 = build_output(panpeng, 1);
   transaction = string_create_progression(12 + scan(input_0) + scan(output_0) + scan(output_1));
   string_update_4(transaction, 12 + scan(input_0) + scan(output_0) + scan(output_1));
   string_update_4(transaction, 1);
   string_update_4(transaction, 2);
   string_update(transaction, input_0, scan(input_0));
   string_update(transaction, output_0, scan(output_0));
   string_update(transaction, output_1, scan(output_1));

   return transaction;
}

char *block_3930(char *A)
{
   char *block;

   block = string_create_progression(108 + string_measure(A)); 
   string_update(block, get_sha256(A, string_measure(A)), 32);
   string_update_4(block, 0);
   string_update(block, get_minimal(), 32);
   string_update(block, get_maximal(), 32);
   string_update_4(block, time(0));
   string_update_4(block, 1);
   string_update(block, A, string_measure(A));

   return block;
}

char *block_4479(char *previous, char *A, char *B)
{
   char *block, *tree;

   tree = string_create_progression(64);
   string_update(tree, get_sha256(A, string_measure(A)), 32);
   string_update(tree, get_sha256(B, string_measure(B)), 32);

   block = string_create_progression(108 + string_measure(A) + string_measure(B));
   string_update(block, get_merkle_root(tree, 2), 32);
   string_update_4(block, 0);
   string_update(block, previous, 32);
   string_update(block, get_maximal(), 32);
   string_update_4(block, time(0));
   string_update_4(block, 2);
   string_update(block, A, string_measure(A));
   string_update(block, B, string_measure(B));

   return block;
}

char *transaction_8629(char *lichangli)
{
   char *input_0, *output_0, *progression;

   input_0 = build_input(0, string_create_improper(""), string_create_improper(""), get_minimal());
   output_0 = build_output(lichangli, 100);

   progression = string_create_progression(12 + scan(input_0) + scan(output_0));
   string_update_4(progression, 12 + scan(input_0) + scan(output_0));
   string_update_4(progression, 1);
   string_update_4(progression, 1);
   string_update(progression, input_0, scan(input_0));
   string_update(progression, output_0, scan(output_0));

   return progression;
}

char *transaction_4140(char *lichangli)
{
   char *input_0, *output_0, *progression;

   input_0 = build_input(0, string_create_improper(""), string_create_improper(""), get_minimal());
   output_0 = build_output(lichangli, 100);

   progression = string_create_progression(12 + scan(input_0) + scan(output_0));
   string_update_4(progression, 12 + scan(input_0) + scan(output_0));
   string_update_4(progression, 1);
   string_update_4(progression, 1);
   string_update(progression, input_0, scan(input_0));
   string_update(progression, output_0, scan(output_0));

   return progression;
}
```

### 4.4.3 示例数据写入调用
完成了示例数据的写入, 就方便后面开发读取和验证的部分的代码的测试了.


```
int main(int count, char **arguments)
{
   ltc_mp = tfm_desc;
   register_prng(&sprng_desc);
   register_hash(&sha256_desc);

   char *A, *B, *C, *BLOCK_A, *BLOCK_B;
   char *_6301, *_1604, *_1215;
   char *buffer;
   int length;
   char *panpeng_address, *panpeng_public, *lichangli_address, *lichangli_public, *lichangli_private;

   _6301 = "MEwDAgcAAgEgAiEAy4Dqak+QdoTjLOneFcw45XLVvRo1pxlurO7CdnSiEmkCIEHKqUkf236XXyybfJWX8dhvHKJXHwJ2TKJ6f2J78OIiMEwDAgcAAgEgAiEAy
4Dqak+QdoTjLOneFcw45XLVvRo1pxlurO7CdnSiEmkCIEHKqUkf236XXyybfJWX8dhvHKJXHwJ2TKJ6f2J78OIi";   
   _1604 = "MEwDAgcAAgEgAiB/XlZGVRCrgg1QoZpLXP25dBxfE+l53GcrQT+rnOyMtQIhAPjOmLLKCkZeuk378KlQCLXGYcx3ZZNpYt2xqPTS6BC/";
   _1215 = "MG8DAgeAAgEgAiB/XlZGVRCrgg1QoZpLXP25dBxfE+l53GcrQT+rnOyMtQIhAPjOmLLKCkZeuk378KlQCLXGYcx3ZZNpYt2xqPTS6BC/AiEAuUgmCNAsw2ElO
5hxpvAzJpKcdSuyiHV84R2jTPFVu7o=";

   buffer = malloc(1000);

   length = 1000;
   base64_decode(_6301, strlen(_6301), buffer, &length);
   panpeng_public = string_create(buffer, length);

   length = 1000;
   base64_decode(_1604, strlen(_1604), buffer, &length);
   lichangli_public = string_create(buffer, length);

   length = 1000;
   base64_decode(_1215, strlen(_1215), buffer, &length);
   lichangli_private = string_create(buffer, length);

   panpeng_address = string_create(get_md5(panpeng_public, 32), 16);
   lichangli_address = string_create(get_md5(lichangli_public, 32), 16);

   A = transaction_8629(lichangli_address);
   B = transaction_4140(lichangli_address);
   C = transaction_7159(1, lichangli_public, get_signature(A, lichangli_private), get_sha256(A, string_measure(A)), lichangli_address
, panpeng_address);

   BLOCK_A = block_3930(A);
   BLOCK_B = block_4479(get_sha256(BLOCK_A, string_measure(BLOCK_A)), B, C);

   write(1, BLOCK_B, string_measure(BLOCK_B));
}

```



# 5. 区块节点读取与验证

##  5.1 merkle tree 说明
Hash的计算简要示意
![image_1btodkb4qm17lf2fj1q96gr13.png-52.9kB][4]
交易的hash计算与区块简要示意
![image_1btodfhca5dr15cg1s2760lm919.png-41.9kB][5]



### 5.1.1 递归函数定义
递归计算merkle root
先计算单个.
然后计算奇数的hash,
计算偶数的hash

```
  pseudo code, author by Lijun
  merkle_root(arr[])
  if(count(arr[]) ==1 ) return arr[0];
  if(count(arr[]) %2 ) 
      return sha256(merkle_root(arr[0,n-2]), arr[n-1])
  else
      return  shar256(merkle_root(arr[0,n/2-1]), merkle_root(arr[n/2, n-1]);
```


只有一个交易,merkle root就是对这个交易的hash.
> - get_merkle_root(X) = X
> - get_merkle_root(X0 , X1 ， …… ， X2n ) = get_merkle_root(X0 ,X1 ,…… ，X2n,X2n)
> - get_merkle_root(X0 , X1 ， …… ， X2n-1 ) = get_merkle_root(SHA256(X0+X1), SHA256(X2+X3) ……)

```
 function(x) {
    if (count(x) == 1 ) return x;
    if (count(x) %2) x.push[x, (
 }
 
```

### 5.1.2 php递归实现代码
```
<?php

function get_lines_file($file)
{
   $result = array();

   $contents = file_get_contents($file);
   $contents = explode(chr(10), $contents); //用换行符分割

   // linux 放10, windows会放13,10作为换行,所以删掉13
   foreach ($contents as $item) {
      $item = str_replace(chr(13), '', $item); // 去掉char13
      if (strlen($item) > 0)
         array_push($result, $item);
   }

   return $result;
}

/*  生成hash. 可以输出存到 文件里.
    $digests = array();
    foreach(range(1,100) as $number)
        $digests[] = hash('sha256', rand());
*/

$x = get_lines_file('digests.txt');   
var_dump(get_Merkle_root($x));

function get_Merkle_root($digests)
{
   if (count($digests) == 1) {

      echo sprintf('Case #1: %d%c', count($digests), 10);

      return $digests[0];
   }

   if (count($digests) % 2) {  // 奇数

      echo sprintf('Case #2: %d%c', count($digests), 10);

      $item = $digests[count($digests) - 1];
      array_push($digests, $item);   // push, 把最后1个item复制

      return get_Merkle_root($digests);
   }

   echo sprintf('Case #3: %d%c', count($digests), 10);

   for ($i = 0, $s = count($digests) / 2; $i < $s; $i++) {
      $a = array_shift($digests);   // 从左侧移出
      $b = array_shift($digests);   // 组成一对计算hash.
      array_push($digests, hash('sha256', $a . $b));  //从右侧插入
   }
   
   return get_Merkle_root($digests);
   
   /* 另外一种方法, 新建一个数组reduced存放
   $i = 0;
   while($i < count($digests)){
     $merged= sprintf('%s%s',$digests[i[,$digests[i+1]);
     array_push($reduced, hash('sha256',$merged));
     $i += 2;
   }
   return get_Merkle_root($reduced);
   */
   
}

```

digests.txt的内容是100个随机数的hash值, 每行一个hash值.

```
$ head -3 digests.txt                                                                                                                       
c5c77f852587da8a14d163e9be251c79590ac1354da79397b44b7e23e55d7470
4247c422e39497fc67e527dffe0ccbcd19dc9be0bd12ed2c9bc78ae4f64ce369
e9f07edbdbdc7403a93903840a096944b7194d38a73b497531fb65a56541cab7
```

## 5.2 区块节点读取说明
创造节点之后, 读取区块相关内容信息, 并进行验证, 确认都是正确的.

再次回顾下数据结构:

Head的长度固定, 所以不需要保存长度. 这里设计为104
**字段说明**| current | nounce | previous | target | time   
--- | --- | --- | --- | --- | --- | 
**说明** | BODY的sha256输出,当前block的ID   | 挖矿难度   |  前个区块的ID  |  越大难度越小  |  ?时间  | 
**长度(字节)**|32   | 4   |  32  |  32  |  4  | 

Transaction 旧设计
**字段名**| A  | B | Input* | C | Output* 
---|--- | --- | --- | --- | ---
**说明**| 总长度   | 所有输入长度   |  输入  | 所有output长度 |   多个输出 
**长度(字节)**|4   | 4   |  B  | 4 |   C 
Transaction 新设计
**字段名**| A  | B | C | Input*  | Output* 
---|--- | --- | --- | --- | --- |
**说明**| 总长度(含自己)   | 输入个数  |  输出个数   | 输入列表 | 输出列表  
**长度(字节)**|4   | 4   |  4  |  B |   C 


Head 后面跟多个Transaction. 其中第1个Transaction是创始交易, 用于矿工奖励.

Transaction 
为了方便, 将上表中的 B, Input*, C, Output* 统一表示为后继内容Content.
**字段名**| Head  | Transaction#1 | Transaction#2 | T#3 | ... 
---|--- | --- | --- | --- | ---
**说明**| 头部   | 总长度A和后继内容   |  总长度A和后继内容  | 总长度A和后继内容 |   ... 
**结构**| Head   | A,Content |  A,Content  | A,Content |   ... 
**长度(字节)**|104   | A |  A  | A |   ... 


### 5.2.1 基于字节的移动来读取内容
取得区块的生字节后,  可以通过指针来回移动, 获取相关内容. 指针到达的位置就是整数int长度的位置, 取\*就可以得到其长度.
> x+104,    // 到达body,第一个交易.  
> x +104 +(\*)   // 达到第2个交易.
> x +104 +(\*) + (\*)   // 达到第2个交易.

C语言实现:
```
x = x + 104;
x = x + (*x);
x = x + (*x);
```

### 5.2.2 指针移动获得区块的不同变量
回顾Input的结构:  
INPUT := A, INDEX, B, PUBLIC, C, SIGNATURE, TRANSACTION

通过指针移动获取相应变量地址
```
unsigned *A, *B, *C
A = input
B = input + 8
C = input + 12 + B
```

### 5.2.3 对区块里有多少交易进行计数

```
#include "stdio.h"
#include "stdlib.h"
#include "string.h"

// 这里加了length存 读取出文件内容的长度.
unsigned char *get_file(char *path, long *length) {

    FILE *file;
    unsigned char *contents;

    file = fopen(path, "r+");
    fseek(file, 0, SEEK_END);
    *length = ftell(file);
    fseek(file, 0, SEEK_SET);

    contents = malloc((size_t)*length);
    fread(contents, 1, (size_t)*length, file);
    fclose(file);

    return contents;
}

unsigned int slice(unsigned char *cursor, unsigned int length)
{
   unsigned char *limit;
   unsigned int *X;

   unsigned int count;

   limit = cursor + length;  // 尾部
   cursor += 104;
   count = 0;

   while (cursor < limit) {

      X = (unsigned int *)cursor;  //指针类型转换
      cursor += *X;   // 跳到下个交易
      count++;
   }

   return count;
}

int main(int count, char **arguments)
{
   unsigned char *block;
   unsigned int length;
   block = get_file(arguments[1], &length);
   printf("%d%c", slice(block, length), 10);
}
```



### 5.2.2 构造链表来读取交易

![image_1btodtl5s5t219q3dqaauf1gkl20.png-222kB][6]

在上一个slice的基础上, 一边遍历,一边分配空间, 同时创造linklist来指向内容空间.
```
unsigned int slice(unsigned char *cursor, unsigned int length)
{
   unsigned char *limit, *transaction;
   unsigned int *X;
   void *list, *link;

   unsigned int count;

   limit = cursor + length;  // 尾部
   cursor += 104;
   count = 0;

   while (cursor < limit) {

      X = (unsigned int *)cursor;  //指针类型转换
      transaction = malloc(*X + 4)  + 4; //获取新分配的内容空间的交易内容地址起始位置
      memcpy(transaction, cursor, *X); //保存内容, 长度为*X,即交易长度
      memcpy(transaction-4, X, 4);  // 保存内容的长度. 
      cursor += *X; 
      count++;
      
      // 相当2个4字节指针,前一个装内容地址, 后一个装后list的地址
      // 这是一种比较粗糙的方式来构造linklist, 后面会介绍通过函数来构造.
      link = malloc(8);
      memcpy(link, transaction, 4);
      memcpy(link + 4, list, 4); 
      
      list = link;
   }

   return count;
}
```

## 5.3 区块节点读取实现

### 5.3.1 存储区块结构的处理程序规划:
```
storage.add_block( $storage, $substance)
storage.remove_block($storage, $identify)
storage.get_block($storage, $identify)
storage.load($storage, $identify)     // 放内存 
storage.unload($storage, $identify)  // 从内存中卸载.  放硬盘上 缓存
storage.get_transaction($storage, $identify);
```
可以用heap list vector or tree 来落实上面的函数, 在效率上会有差别, 但是效果一样.

### 5.3.2 如果是网络读取,则tcp接收区块.
1) 阅读 108个字节, 保存valence为Y
2) 读 4个字节, 保存为X
3) 读 X 个字节
4) 用副函数来确认此交易格式
5) 递归向 步骤2 一共 Y次
6) 做验证. (格式已经确认)


## 5.4 区块节点验证说明
创造节点之后, 
* 读取区块相关内容信息, 并进行验证, 确认都是正确的.
* 验证第2个以上的交易(input, output, transaction balance),
* 然后验证第一个交易, 还有头部.

验证:
(1) 格式
(2) 输入:  此输入是否存在;  输入的签名是否有校验
(3) 输出:
(4) 交易平衡:  输入是否多于或者等于输出的总额? 
(5) 第一个交易
(6) 头部  交易的摘要是否正确?  劳动证明是否正确?

### 5.4.1 劳动证明与挖矿规则
劳动证明是看有没有做挖掘的过程, 挖掘的目的是为了防止一个人短时间制造大量的空区块, 也为了维持竞争性.
不同的网络上的节点, 每个区块的生产的过程, 相当于比赛的一局. 胜利者获利,其他人没有获得.

矿池联合挖, 自己制定一个规则,  矿主决定怎么分配利润. 

大多数规则:
一个成员要是挖掘了接近正确的结果, 虽然无效, 也上报,  
矿池主人根据劳动的难度的工作证明, 按照比例分配.
如A,B,C 分别挖掘了4,6,10的难度的工作, 则按此比例进行分配货币. 
除此之外, 矿池主人还会再扣一些类似赋税.

#### 节点与挖矿程序的沟通 
BTC官方节点 和 挖矿程序 是通过 http 和 json-rpc 进行沟通, 效率比较低.  
计算机更擅长生字节的处理. 需要转换.
好多矿池的程序修改或写了自己的节点程序, 直接用TCP 沟通.
 

 
### 5.4.2 区块的遍历与验证

#### 5.4.2.1 验证过程
##### 验证身部Body
要检查验证input, output, 还有transaction的正确性.

1. 每一个input内, 查找 transaction, index 是否存在
2. 每一个input内, 拿public, signature 和赌赢的output的address, 然后运行 ECC verify, 看true or false, false表示失败
3. 检查 sigma intput >= sigma output , 记录剩余
4. 检查 sigma 剩余  >= coinbase 的小费
5. 验证头部

##### 验证头部Head
1. 检查时间: 必须大于上7个区块的平均时间, 为了保持区块链的时间在不断往上升.
2. 检查target是否少于或者等于协议规定的target, target越小,难度越大
3. 计算整个头的SHA256是否少于target, 然后检查 merkle tree

##### 程序实现函数规划
```
check_transaction($substance, $length)
check_input($substance, $length)
check_output($substance, $length)
```

#### 5.4.2.2 程序实现
```
/* 数链表的节点个数 */
int count_list(void *cursor)
{
    int count;
    void *Y;

    count = 0;

    while (cursor != 0)
    {
        count++;
        Y = cursor + 4;
//        cursor = (void *) *Y;
    }

    return count;
}

// 用于构造一对, 这里可以用于构造2个指针对, 作为链表的节点.
void *build_pair(void *left, void *right)
{
    void *pair;

    pair = malloc(8);
    memcpy(pair, &left, 4);
    memcpy(pair + 4, &right, 4);

    return pair;
}

/* 对于指针指向字符存长度位置的正当字符串(pascal string), 返回字符串的长度, 也可用于区块数据大小的读取. */
int scan(unsigned char *source) // 返回大小
{
    int *size;

    size = (int *) source;

    return *size;
}
/* 对于指针指向字符内容起始位置的正当字符串(pascal string), 返回字符串的长度,  也可用于区块数据大小的读取. */
int measure(char *source)
{
    int *length;

    length = (int *) (source - 4);

    return *length;
}
/* 字符串分2种, 
    一种是\0结尾, 不记录长度. 不能存\0, 叫做非正当字符串 improper (c string) 
    一种记录长度, 可以存任意字符串. 叫做正当字符串 proper (pascal string) 
    其中int整数存储长度, 本身占用4个字节.
    下面是后一种  
*/
/* 构造包含自己长度的字符串, [length,string_content] */
unsigned char *build(unsigned char *source, int length) 
{
    unsigned char *destination;

    destination = malloc((size_t)length + 4); //比长度大4的空间
    memcpy(destination, &length, 4);  // 放长度
    memcpy(destination + 4, source, length);  //放内容

    return destination;
}

unsigned char *get_file(char *path, long *length) {

    FILE *file;
    unsigned char *contents;

    file = fopen(path, "r+");
    fseek(file, 0, SEEK_END);
    *length = ftell(file);
    fseek(file, 0, SEEK_SET);

    contents = malloc((size_t)*length);
    fread(contents, 1, (size_t)*length, file);
    fclose(file);

    return contents;
}

/* Slice 函数用于把一个区块block切成交易.
 实际内存过程展示: 
  形成head元素:
    head_pointer
      | 
     [length, head_content]
 
  形成链表的第1个节点.
  list_pointer --> [ head_pointer, \0]
                    |
                   [head]
  
  进入while循环后, 指向block的指针依次按照transaction的大小向后移动. 
  添加当前读取到的交易transaction到内存中, 
  然后添加第2个链表节点,节点中的指针指向 transaction.
    [head, transaction#1, transaction#2, .... ]
  len:104 |len,content   | len, content|...
          ^
        cursor  ---> 指针按照交易大小顺序移动 
  
  读取并构建交易到内存中, 同时构建指针链表保存交易地址       
  list_pointer --> [head_pointer, list_pointer] --> [head_pointer, \0]
                    |                               |
                   [transaction]                   [head]
  第3个及以后的transaction以及链表依次实现.
  
*/
// 返回一个指针链表的尾部. 链表中存的是指向交易和head的指针.
void *slice(unsigned char *cursor, long length)
{
    unsigned char *limit, *head;
    void *list;

    limit = cursor + length;
    list = 0;

    head = build(cursor, 104);  // 内存中构造head节点
    printf("head pointer: %d%c", (int)head, 10);

    list = build_pair(head, list); // 记录head指针,list指针为pair. 形成链表的第1个节点.   
    printf("list pointer: %d%c", (int)list, 10);

    cursor += 104;

    // 
    while (cursor < limit) {
        //内存中构造transaction.
        unsigned char *x = build(cursor, scan(cursor)); 

        printf("transaction pointer: %d%c", (int)x, 10); 
        //增加链表节点, 
        list = build_pair(x, list);
        cursor += scan(cursor);
    }

    return list;
}

/*
list[0] -> HEAD
list[1] -> COINBASE
list[2] -> TRANSACTION #2
list[3] -> TRANSACTION #3...
*/

// TRANSACTION := A, B, C, INPUT*, OUTPUT*
// 这里的Transaction的定义与前面不一致了! 

// 得到输出的交易金额
uint64_t get_output_amount(unsigned char *output)
{
    // A B ADDRESS AMOUNT

    int *B;
    uint64_t amount;  //交易金额数量.

    B = (int *) (output + 4);

    memcpy(&amount, output + 8 + *B, 8);
    return amount;
}

/*验证一个交易中的所有input,ouput的数量. */
int verify(unsigned char *transaction)
{
    int *A, *B, *C;
    unsigned char *cursor;
    int count;

    A = (int *) transaction;
    B = (int *) (transaction + 4);
    C = (int *) (transaction + 8);

    printf("%d%c", *A, 10);
    printf("%d%c", *B, 10);
    printf("%d%c", *C, 10);
    
    //8字节的整数定义.
    uint64_t input_total, output_total = 0;  // 用于记录input,output个数.

    cursor = transaction + 12;
    count = *B;

/*  查找输入的数量, 可以查看数据库??
   while (count--) {
      input_total += get_input_total(cursor); //得到输入的总数量.? 
      cursor += scan(cursor);
   }
*/

    while (count--)
        cursor += scan(cursor);

    count = *C;

    while (count--) {
        output_total += get_output_amount(cursor); //得到交易金额
        cursor += scan(cursor);
    }

    printf("number of outputs: %d%c", *C, 10);
    printf("volume of outputs: %d%c", (int) output_total, 10); //交易金额

    return 0;
}

int main(int count, char **arguments)
{
    unsigned char *block, *data;
    long length;
    void *list;
    char *a;

    block = get_file(arguments[1], &length);
    list = slice(block, length);  // 分割block文件, 分配空间并存指针到list中.
    
    /*  
    list += 4;
    */
    
    while ((int)list != 0 ) {
        data = (unsigned char *) *((int *) list); // 得到交易地址
        printf("value: %d%c", (int)data, 10);
        list = (void *)*((int *) (list + 4));   //下一个链表节点.
        printf("list: %d%c", (int)list, 10);
    }

    // verify(list + 4);
}
```


# 6. 应用和节点程序之间协议
# 7. 节点之间沟通

## 7.1 Sockets
###  7.1.1  Sockets 说明

C语言套接字. 
建议把相关函数及参数使用记录下来.
![image_1bto7u8pg1ot5qlgdi1cq71husg.png-23.9kB][7]

1. 系统呼叫：open(1),close(1),read(1),write(1)。
2. 套接字socket：几乎所有经过网络的过程都经过socket。
3. 每一个网络编程中，都包括连接者和被动连接者（监听者）。
*问题：一个监听者在单线程的情况下，如何监听多个连接者？
  其中一个解决方法是排队。在监听一个连接着的时候，其他连接者被中断，排队等待。
  三个方式实现并行处理：创建新的进程、创建新的线程、使用两个特殊的系统呼叫select(2)或者poll(2)。

* socket：制造一个新的套接字。三个参数，socket(family,type,protocol).
```
         family：家族，family的取值为2，表示互联网。
         type： 类型，取值为1时表示tcp，取值为2时表示udp。tcp更安全，但是udp更快。游戏和网络视频中，一般使用udp，因为更在乎速度。一般 上午数据使用tcp，因为更在乎安全。                       我们课程使用udp做一个钱包，很快，但是可能会丢包。使用udp再使用udp检查丢包，相当于是一个tcp，但是时间成本远低于tcp。
               udp没有连接的概念，不需要使用connect函数。
         protocol：协议，取值为0。
         domain socket：区域套接，不能上网的套接。
         netlink socket：网链套接，也不能上网，只有LInux存在，为了让程序和驱动器沟通。
         INET socket：即internet socket，互联网套接，我们正在使用的。
```
* connect： 连接，三个参数，connect(x,目标，目标的大小)。
```
          其中，x=socket（family，type，protocol）。
          呼叫connect时，系统会卡住等待连接，所以每次做connect时，需要做多线程。这样当系统等待连接卡住的时候，其他线程不会被影响。
         y=connect（x，目标，目标的大小）。表示是否连接成功。
```
* send：发送数据，三个参数，send(X,内容，内容的大小，flags)。其中，flags表示一些特殊的要求。
* recv：接收数据，四个参数，recv（X，缓冲，缓冲大小，flags）。其中，flags表示一些特殊的要求。
   返回 -1 表示网络线路的中断;  返回 0 表示发送者的中断.


### 7.1.2 Sockets 实现

#### 7.1.2.1 连接者程序(不添加参数)
 1.连接者程序(不添加参数)
        一端使用nc -l 127.0.0.1 6004启动监听程序，另一端使用tcc -run MyConnect.c 发送消息，则可以被监听到。

```
#include "stdio.h"
#include "sys/socket.h"
#include "arpa/inet.h"

void main()
{
  int s ;
  struct sockaddr_in a;

  s = socket(2,1,0);//2表示互联网，1表示tcp，0表示协议
  if (s == -1)//s== -1表示套接字失败
    exit(1);

  a.sin_family = 2;
  a.sin_port = htons(6004);
  a.sin_addr.s_addr = inet_addr("127.0.0.1");

  int f = connect(s, (struct sockaddr *) &a, sizeof a);//第一个参数是套接字的结果，第二个参数是连接目标，第三个参数是连接目标的大小。
  if (f == -1)
    exit(2);
  send(s,"hello ",6,0);//加空格，长度为6
  send(s,"world ",6,0);
}
```

#### 7.1.2.2 连接者程序(带参数)

        一端使用nc -l 127.0.0.1 6004启动监听程序，另一端使用tcc -run MyConnect.c 127.0.0.1 6004 发送消息，则可以被监听到。

```
#include "stdio.h"   //printf() fopen() fread() fwrite() fclose()
#include "stdlib.h"  //malloc(oc() free()
#include "string.h"  //strlen() strtok()
#include "sys/socket.h"   //socket
#include "arpa/inet.h"    //socket intnet

int main (int count, char **arguments)
{
  int s ;
  struct sockaddr_in a;
  if (count != 3){  //函数名也作为一个参数，所以一共需要三个参数。
    printf("error: there must be three arguments%c",10);
    exit(3);
  }

  s = socket(2,1,0);
  if (s == -1){//s==-1,unsuccessful
    exit(1);
  }

  a.sin_family = 2;
  a.sin_port = htons(atoi(arguments[2]));  //atoi把参数中的字符类型转换为整型
  a.sin_addr.s_addr = inet_addr(arguments[1]);  //两个点，是属性中的属性

  int f = connect(s,(struct sockaddr *) &a, sizeof a);
  if (f == -1)
    exit(2);

  send(s,"hello ",6,0);
  send(s,"world ",6,0);
}
```

#### 7.1.2.3 监听者程序
一端使用tcc -run MyListen.c 6006 运行监听者程序，另一端使用nc 127.0.0.1 6006 启动连接，则连接者的输入可以被监听者监听到。

```
#include "stdio.h"
#include "sys/socket.h"
#include "arpa/inet.h"

int main(int count, char **arguments)
{
  int s,error,length,opponent;
  struct sockaddr_in a;
  char buffer[1000];  //长度为1000的缓存，如果发送消息的长度超过1000，则被分割为很多个长度为1000的块。

  s = socket(2,1,0);  
  if (s == -1)
    exit(1);

  a.sin_family = 2;
  a.sin_port = htons(atoi(arguments[1]));
  a.sin_addr.s_addr = inet_addr("0.0.0.0");  //0.0.0.0表示可以监听任何地址。127.0.0.1表示只可以监听自己的地址。

  error = bind(s,(struct sockaddr *) &a,sizeof a);   //bind函数的目的是设置
  if (error == -1)
  {
    exit(1);
  }
  //listen函数的目的是开始监听
  error = listen(s,1024);   //1024表示允许监听的连接者的树木的最大值。第一个被监听，之后的1023个排队等待，第1025个被立即拒绝。
  if (error == -1)
  {
    exit(1);
  }
  //accept被呼叫之后，连接才会被收到
  opponent = accept(s,0,0);  //后面两个参数表示对方的地址和地址长度，0,0表示不关心对方的地址
  if (opponent == -1)
  {
    exit(1);
  }
  while(1)  //不断的循环，接听到一句话之后，依然继续链接，而不会立刻断开。
  {
     memset(buffer,0,1000);   
//使用缓冲之前，必须把之前的内容职位0，memset（buffer,0，1000）函数赋值为0
//因为c语言新申请到的内存不是空的，是之前的垃圾信息
     length = recv(opponent, buffer, 1000, 0);

     handle();

     if (length == -1 || length == 0)  //如果length的值为-1或者0的时候，则链接已经断开
    {
      break;
    }
    printf("%s",buffer);
  }
}

void handle()
{
// 商业逻辑处理过程.
}

```

## 7.2 有限状态机 finite-state machine

### 7.2.1 php的简易示例代码和运行结果
通过php示例代码和运行过程展示来了解有限状态机的机制.

```
$ cat 1-listen.php                                                                                                                  
<?php
/*  示例代码有限状态机设计:
   array(5410, $accumulation)
   array(9694, $result)
*/

error_reporting(E_ALL);   //加上这行代码就会汇报任何错误.

$input = "This is the fourteenth session of the blockchain course.";
$situation = array(5410, '');

while (1) {

   $head = substr($input, 0, 1);
   $tail = substr($input, 1);

   $situation = handle_8291($situation, ord($head));
   var_dump($situation);

   if ($situation[0] == 9694)
      break;

   var_dump($input);

   $input = $tail;
}

function handle_8291($situation, $byte)
{
   $situation[1] = $situation[1] . chr($byte);

   if (strlen($situation[1]) >= 24)
      return array(9694);

   return array(5410, $situation[1]);
}

function handle_5884($situation, $byte)
{
   if ($situation[0] == 3786) {

      if ($situation[2] == 0) {

         $integer = unpack_integer(substr($situation[1], strlen($situation[1]) - 4));

         return array(1188, $situation[1], $integer, 4);
      }

      return array(3786, $situation[1] . chr($byte), $situation[2] - 1);
   }

   if ($situation[0] == 1188) {

      if ($situation[2] == 0) {

         return array(5176, $situation[1]);
      }

      if ($situation[3] == 0) {

         $integer = unpack_integer(substr($situation[1], strlen($situation[1]) - 4));

         return array(6458, $situation[1], $situation[2] - 1, $integer);
      }

      return array(1188, $situation[1] . chr($byte), $situation[2], $situation[3] - 1);
   }

   if ($situation[0] == 6458) {

      if ($situation[3] == 0) {

         
      }

      return array(3933, $situation[1] . chr($byte), $situation[2], 4);
   }
}
```
运行展示有限状态机的变化过程
```
$ php 1-listen.php                                                                                                                  
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(1) "T"
}
string(30) "This is the blockchain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(2) "Th"
}
string(29) "his is the blockchain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(3) "Thi"
}
string(28) "is is the blockchain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(4) "This"
}
string(27) "s is the blockchain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(5) "This "
}
string(26) " is the blockchain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(6) "This i"
}
string(25) "is the blockchain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(7) "This is"
}
string(24) "s the blockchain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(8) "This is "
}
string(23) " the blockchain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(9) "This is t"
}
string(22) "the blockchain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(10) "This is th"
}
string(21) "he blockchain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(11) "This is the"
}
string(20) "e blockchain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(12) "This is the "
}
string(19) " blockchain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(13) "This is the b"
}
string(18) "blockchain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(14) "This is the bl"
}
string(17) "lockchain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(15) "This is the blo"
}
string(16) "ockchain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(16) "This is the bloc"
}
string(15) "ckchain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(17) "This is the block"
}
string(14) "kchain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(18) "This is the blockc"
}
string(13) "chain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(19) "This is the blockch"
}
string(12) "hain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(20) "This is the blockcha"
}
string(11) "ain course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(21) "This is the blockchai"
}
string(10) "in course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(22) "This is the blockchain"
}
string(9) "n course."
array(2) {
  [0]=>
  int(5410)
  [1]=>
  string(23) "This is the blockchain "
}
string(8) " course."
array(1) {
  [0]=>
  int(9694)
}
```

###  7.2.2 php实现接收区块的有限状态机设计
php代码容易理解, 而且翻译成c比较容易. 所以先写php代码.

#### 7.2.2.1 区块读取接收的fsm设计
```
/*  接收区块交易的有限状态机设计:
        (随机数: 状态;  $buffer: 字节缓存;   $valence: 剩余交易数, count:交易个数;  $length: 剩余读入长度)
        
   array(3786, $buffer, $length)  
   array(1188, $buffer, $valence, $count)  //接收交易个数4字节.
   array(3833, $buffer, $valence, $length)  //接收交易.
   array(5176, $buffer)   // 最后一个交易
*/
```

#### 7.2.2.2 区块接收读取的代码
```
$ cat listen.php                                                                                                                    
<?php

/*
   array(3786, $buffer, $length)
   array(1188, $buffer, $valence, $count)
   array(3833, $buffer, $valence, $length)
   array(5176, $buffer)

   create_3786();
   create_1188();
   create_3833();
   create_5176();
*/

error_reporting(E_ALL);

$input = file_get_contents('1.block');
$situation = array(3786, '', 108);
$count = 0;

while (++$count) {

   $head = substr($input, 0, 1);
   $tail = substr($input, 1);

   echo sprintf('%d: %s -> ', $count, print_situation($situation));
   $situation = handle_5884($situation, ord($head));
   echo sprintf('%s%c', print_situation($situation), 10);

   if ($situation[0] == 5176)
      break;

   $input = $tail;
}

function print_situation($situation)
{
   if (count($situation) == 1)
      return sprintf('array(%d)', $situation[0]);

   if (count($situation) == 2)
      return sprintf('array(%d, %s)', $situation[0], print_value($situation[1]));

   if (count($situation) == 3)
      return sprintf('array(%d, %s, %s)', $situation[0], print_value($situation[1]), print_value($situation[2]));

   if (count($situation) == 4)
      return sprintf('array(%d, %s, %s, %s)', $situation[0], print_value($situation[1]), print_value($situation[2]), print_value($situation[3]));
}

function print_value($value)
{
   if (is_integer($value))
      return sprintf('%d', $value);

   if (is_string($value))
      return sprintf('string(%d)', strlen($value));
}

function handle_5884($situation, $byte)
{
   if ($situation[0] == 3786) {

      $situation[1] = $situation[1] . chr($byte);
      $situation[2]--;

      if ($situation[2] == 0) {

         $valence = substr($situation[1], strlen($situation[1]) - 4);
         $valence = unpack('L', $valence);
         $valence = $valence[1];

         return array(1188, $situation[1], $valence, 4);
      }

      return array(3786, $situation[1], $situation[2]);
   }

   if ($situation[0] == 1188) {

      $situation[1] = $situation[1] . chr($byte);
      $situation[3]--;

      if ($situation[3] == 0) {

         $length = substr($situation[1], strlen($situation[1]) - 4);
         $length = unpack('L', $length);
         $length = $length[1];         

         return array(3833, $situation[1], $situation[2] - 1, $length - 4);
      }

      return array(1188, $situation[1], $situation[2], $situation[3]);
   }

   if ($situation[0] == 3833) {

      $situation[1] = $situation[1] . chr($byte);
      $situation[3]--;

      if ($situation[2] == 0 && $situation[3] == 0)
         return array(5176, $situation[1]);

      if ($situation[3] == 0)
         return array(1188, $situation[1], $situation[2], 4);

      return array(3833, $situation[1], $situation[2], $situation[3]);
   }
}

```

#### 7.2.2.2 区块接收读取的过程展示
```
$ php listen.php                                                                                                                    
1: array(3786, string(0), 108) -> array(3786, string(1), 107)
2: array(3786, string(1), 107) -> array(3786, string(2), 106)
3: array(3786, string(2), 106) -> array(3786, string(3), 105)
4: array(3786, string(3), 105) -> array(3786, string(4), 104)
5: array(3786, string(4), 104) -> array(3786, string(5), 103)
6: array(3786, string(5), 103) -> array(3786, string(6), 102)
7: array(3786, string(6), 102) -> array(3786, string(7), 101)
8: array(3786, string(7), 101) -> array(3786, string(8), 100)
9: array(3786, string(8), 100) -> array(3786, string(9), 99)
10: array(3786, string(9), 99) -> array(3786, string(10), 98)
11: array(3786, string(10), 98) -> array(3786, string(11), 97)
12: array(3786, string(11), 97) -> array(3786, string(12), 96)
13: array(3786, string(12), 96) -> array(3786, string(13), 95)
14: array(3786, string(13), 95) -> array(3786, string(14), 94)
15: array(3786, string(14), 94) -> array(3786, string(15), 93)
16: array(3786, string(15), 93) -> array(3786, string(16), 92)
17: array(3786, string(16), 92) -> array(3786, string(17), 91)
18: array(3786, string(17), 91) -> array(3786, string(18), 90)
19: array(3786, string(18), 90) -> array(3786, string(19), 89)
20: array(3786, string(19), 89) -> array(3786, string(20), 88)
21: array(3786, string(20), 88) -> array(3786, string(21), 87)
22: array(3786, string(21), 87) -> array(3786, string(22), 86)
23: array(3786, string(22), 86) -> array(3786, string(23), 85)
24: array(3786, string(23), 85) -> array(3786, string(24), 84)
25: array(3786, string(24), 84) -> array(3786, string(25), 83)
26: array(3786, string(25), 83) -> array(3786, string(26), 82)
27: array(3786, string(26), 82) -> array(3786, string(27), 81)
28: array(3786, string(27), 81) -> array(3786, string(28), 80)
29: array(3786, string(28), 80) -> array(3786, string(29), 79)
30: array(3786, string(29), 79) -> array(3786, string(30), 78)
31: array(3786, string(30), 78) -> array(3786, string(31), 77)
32: array(3786, string(31), 77) -> array(3786, string(32), 76)
33: array(3786, string(32), 76) -> array(3786, string(33), 75)
34: array(3786, string(33), 75) -> array(3786, string(34), 74)
35: array(3786, string(34), 74) -> array(3786, string(35), 73)
36: array(3786, string(35), 73) -> array(3786, string(36), 72)
37: array(3786, string(36), 72) -> array(3786, string(37), 71)
38: array(3786, string(37), 71) -> array(3786, string(38), 70)
39: array(3786, string(38), 70) -> array(3786, string(39), 69)
40: array(3786, string(39), 69) -> array(3786, string(40), 68)
41: array(3786, string(40), 68) -> array(3786, string(41), 67)
42: array(3786, string(41), 67) -> array(3786, string(42), 66)
43: array(3786, string(42), 66) -> array(3786, string(43), 65)
44: array(3786, string(43), 65) -> array(3786, string(44), 64)
45: array(3786, string(44), 64) -> array(3786, string(45), 63)
46: array(3786, string(45), 63) -> array(3786, string(46), 62)
47: array(3786, string(46), 62) -> array(3786, string(47), 61)
48: array(3786, string(47), 61) -> array(3786, string(48), 60)
49: array(3786, string(48), 60) -> array(3786, string(49), 59)
50: array(3786, string(49), 59) -> array(3786, string(50), 58)
51: array(3786, string(50), 58) -> array(3786, string(51), 57)
52: array(3786, string(51), 57) -> array(3786, string(52), 56)
53: array(3786, string(52), 56) -> array(3786, string(53), 55)
54: array(3786, string(53), 55) -> array(3786, string(54), 54)
55: array(3786, string(54), 54) -> array(3786, string(55), 53)
56: array(3786, string(55), 53) -> array(3786, string(56), 52)
57: array(3786, string(56), 52) -> array(3786, string(57), 51)
58: array(3786, string(57), 51) -> array(3786, string(58), 50)
59: array(3786, string(58), 50) -> array(3786, string(59), 49)
60: array(3786, string(59), 49) -> array(3786, string(60), 48)
61: array(3786, string(60), 48) -> array(3786, string(61), 47)
62: array(3786, string(61), 47) -> array(3786, string(62), 46)
63: array(3786, string(62), 46) -> array(3786, string(63), 45)
64: array(3786, string(63), 45) -> array(3786, string(64), 44)
65: array(3786, string(64), 44) -> array(3786, string(65), 43)
66: array(3786, string(65), 43) -> array(3786, string(66), 42)
67: array(3786, string(66), 42) -> array(3786, string(67), 41)
68: array(3786, string(67), 41) -> array(3786, string(68), 40)
69: array(3786, string(68), 40) -> array(3786, string(69), 39)
70: array(3786, string(69), 39) -> array(3786, string(70), 38)
71: array(3786, string(70), 38) -> array(3786, string(71), 37)
72: array(3786, string(71), 37) -> array(3786, string(72), 36)
73: array(3786, string(72), 36) -> array(3786, string(73), 35)
74: array(3786, string(73), 35) -> array(3786, string(74), 34)
75: array(3786, string(74), 34) -> array(3786, string(75), 33)
76: array(3786, string(75), 33) -> array(3786, string(76), 32)
77: array(3786, string(76), 32) -> array(3786, string(77), 31)
78: array(3786, string(77), 31) -> array(3786, string(78), 30)
79: array(3786, string(78), 30) -> array(3786, string(79), 29)
80: array(3786, string(79), 29) -> array(3786, string(80), 28)
81: array(3786, string(80), 28) -> array(3786, string(81), 27)
82: array(3786, string(81), 27) -> array(3786, string(82), 26)
83: array(3786, string(82), 26) -> array(3786, string(83), 25)
84: array(3786, string(83), 25) -> array(3786, string(84), 24)
85: array(3786, string(84), 24) -> array(3786, string(85), 23)
86: array(3786, string(85), 23) -> array(3786, string(86), 22)
87: array(3786, string(86), 22) -> array(3786, string(87), 21)
88: array(3786, string(87), 21) -> array(3786, string(88), 20)
89: array(3786, string(88), 20) -> array(3786, string(89), 19)
90: array(3786, string(89), 19) -> array(3786, string(90), 18)
91: array(3786, string(90), 18) -> array(3786, string(91), 17)
92: array(3786, string(91), 17) -> array(3786, string(92), 16)
93: array(3786, string(92), 16) -> array(3786, string(93), 15)
94: array(3786, string(93), 15) -> array(3786, string(94), 14)
95: array(3786, string(94), 14) -> array(3786, string(95), 13)
96: array(3786, string(95), 13) -> array(3786, string(96), 12)
97: array(3786, string(96), 12) -> array(3786, string(97), 11)
98: array(3786, string(97), 11) -> array(3786, string(98), 10)
99: array(3786, string(98), 10) -> array(3786, string(99), 9)
100: array(3786, string(99), 9) -> array(3786, string(100), 8)
101: array(3786, string(100), 8) -> array(3786, string(101), 7)
102: array(3786, string(101), 7) -> array(3786, string(102), 6)
103: array(3786, string(102), 6) -> array(3786, string(103), 5)
104: array(3786, string(103), 5) -> array(3786, string(104), 4)
105: array(3786, string(104), 4) -> array(3786, string(105), 3)
106: array(3786, string(105), 3) -> array(3786, string(106), 2)
107: array(3786, string(106), 2) -> array(3786, string(107), 1)
108: array(3786, string(107), 1) -> array(1188, string(108), 1, 4)
109: array(1188, string(108), 1, 4) -> array(1188, string(109), 1, 3)
110: array(1188, string(109), 1, 3) -> array(1188, string(110), 1, 2)
111: array(1188, string(110), 1, 2) -> array(1188, string(111), 1, 1)
112: array(1188, string(111), 1, 1) -> array(3833, string(112), 0, 88)
113: array(3833, string(112), 0, 88) -> array(3833, string(113), 0, 87)
114: array(3833, string(113), 0, 87) -> array(3833, string(114), 0, 86)
115: array(3833, string(114), 0, 86) -> array(3833, string(115), 0, 85)
116: array(3833, string(115), 0, 85) -> array(3833, string(116), 0, 84)
117: array(3833, string(116), 0, 84) -> array(3833, string(117), 0, 83)
118: array(3833, string(117), 0, 83) -> array(3833, string(118), 0, 82)
119: array(3833, string(118), 0, 82) -> array(3833, string(119), 0, 81)
120: array(3833, string(119), 0, 81) -> array(3833, string(120), 0, 80)
121: array(3833, string(120), 0, 80) -> array(3833, string(121), 0, 79)
122: array(3833, string(121), 0, 79) -> array(3833, string(122), 0, 78)
123: array(3833, string(122), 0, 78) -> array(3833, string(123), 0, 77)
124: array(3833, string(123), 0, 77) -> array(3833, string(124), 0, 76)
125: array(3833, string(124), 0, 76) -> array(3833, string(125), 0, 75)
126: array(3833, string(125), 0, 75) -> array(3833, string(126), 0, 74)
127: array(3833, string(126), 0, 74) -> array(3833, string(127), 0, 73)
128: array(3833, string(127), 0, 73) -> array(3833, string(128), 0, 72)
129: array(3833, string(128), 0, 72) -> array(3833, string(129), 0, 71)
130: array(3833, string(129), 0, 71) -> array(3833, string(130), 0, 70)
131: array(3833, string(130), 0, 70) -> array(3833, string(131), 0, 69)
132: array(3833, string(131), 0, 69) -> array(3833, string(132), 0, 68)
133: array(3833, string(132), 0, 68) -> array(3833, string(133), 0, 67)
134: array(3833, string(133), 0, 67) -> array(3833, string(134), 0, 66)
135: array(3833, string(134), 0, 66) -> array(3833, string(135), 0, 65)
136: array(3833, string(135), 0, 65) -> array(3833, string(136), 0, 64)
137: array(3833, string(136), 0, 64) -> array(3833, string(137), 0, 63)
138: array(3833, string(137), 0, 63) -> array(3833, string(138), 0, 62)
139: array(3833, string(138), 0, 62) -> array(3833, string(139), 0, 61)
140: array(3833, string(139), 0, 61) -> array(3833, string(140), 0, 60)
141: array(3833, string(140), 0, 60) -> array(3833, string(141), 0, 59)
142: array(3833, string(141), 0, 59) -> array(3833, string(142), 0, 58)
143: array(3833, string(142), 0, 58) -> array(3833, string(143), 0, 57)
144: array(3833, string(143), 0, 57) -> array(3833, string(144), 0, 56)
145: array(3833, string(144), 0, 56) -> array(3833, string(145), 0, 55)
146: array(3833, string(145), 0, 55) -> array(3833, string(146), 0, 54)
147: array(3833, string(146), 0, 54) -> array(3833, string(147), 0, 53)
148: array(3833, string(147), 0, 53) -> array(3833, string(148), 0, 52)
149: array(3833, string(148), 0, 52) -> array(3833, string(149), 0, 51)
150: array(3833, string(149), 0, 51) -> array(3833, string(150), 0, 50)
151: array(3833, string(150), 0, 50) -> array(3833, string(151), 0, 49)
152: array(3833, string(151), 0, 49) -> array(3833, string(152), 0, 48)
153: array(3833, string(152), 0, 48) -> array(3833, string(153), 0, 47)
154: array(3833, string(153), 0, 47) -> array(3833, string(154), 0, 46)
155: array(3833, string(154), 0, 46) -> array(3833, string(155), 0, 45)
156: array(3833, string(155), 0, 45) -> array(3833, string(156), 0, 44)
157: array(3833, string(156), 0, 44) -> array(3833, string(157), 0, 43)
158: array(3833, string(157), 0, 43) -> array(3833, string(158), 0, 42)
159: array(3833, string(158), 0, 42) -> array(3833, string(159), 0, 41)
160: array(3833, string(159), 0, 41) -> array(3833, string(160), 0, 40)
161: array(3833, string(160), 0, 40) -> array(3833, string(161), 0, 39)
162: array(3833, string(161), 0, 39) -> array(3833, string(162), 0, 38)
163: array(3833, string(162), 0, 38) -> array(3833, string(163), 0, 37)
164: array(3833, string(163), 0, 37) -> array(3833, string(164), 0, 36)
165: array(3833, string(164), 0, 36) -> array(3833, string(165), 0, 35)
166: array(3833, string(165), 0, 35) -> array(3833, string(166), 0, 34)
167: array(3833, string(166), 0, 34) -> array(3833, string(167), 0, 33)
168: array(3833, string(167), 0, 33) -> array(3833, string(168), 0, 32)
169: array(3833, string(168), 0, 32) -> array(3833, string(169), 0, 31)
170: array(3833, string(169), 0, 31) -> array(3833, string(170), 0, 30)
171: array(3833, string(170), 0, 30) -> array(3833, string(171), 0, 29)
172: array(3833, string(171), 0, 29) -> array(3833, string(172), 0, 28)
173: array(3833, string(172), 0, 28) -> array(3833, string(173), 0, 27)
174: array(3833, string(173), 0, 27) -> array(3833, string(174), 0, 26)
175: array(3833, string(174), 0, 26) -> array(3833, string(175), 0, 25)
176: array(3833, string(175), 0, 25) -> array(3833, string(176), 0, 24)
177: array(3833, string(176), 0, 24) -> array(3833, string(177), 0, 23)
178: array(3833, string(177), 0, 23) -> array(3833, string(178), 0, 22)
179: array(3833, string(178), 0, 22) -> array(3833, string(179), 0, 21)
180: array(3833, string(179), 0, 21) -> array(3833, string(180), 0, 20)
181: array(3833, string(180), 0, 20) -> array(3833, string(181), 0, 19)
182: array(3833, string(181), 0, 19) -> array(3833, string(182), 0, 18)
183: array(3833, string(182), 0, 18) -> array(3833, string(183), 0, 17)
184: array(3833, string(183), 0, 17) -> array(3833, string(184), 0, 16)
185: array(3833, string(184), 0, 16) -> array(3833, string(185), 0, 15)
186: array(3833, string(185), 0, 15) -> array(3833, string(186), 0, 14)
187: array(3833, string(186), 0, 14) -> array(3833, string(187), 0, 13)
188: array(3833, string(187), 0, 13) -> array(3833, string(188), 0, 12)
189: array(3833, string(188), 0, 12) -> array(3833, string(189), 0, 11)
190: array(3833, string(189), 0, 11) -> array(3833, string(190), 0, 10)
191: array(3833, string(190), 0, 10) -> array(3833, string(191), 0, 9)
192: array(3833, string(191), 0, 9) -> array(3833, string(192), 0, 8)
193: array(3833, string(192), 0, 8) -> array(3833, string(193), 0, 7)
194: array(3833, string(193), 0, 7) -> array(3833, string(194), 0, 6)
195: array(3833, string(194), 0, 6) -> array(3833, string(195), 0, 5)
196: array(3833, string(195), 0, 5) -> array(3833, string(196), 0, 4)
197: array(3833, string(196), 0, 4) -> array(3833, string(197), 0, 3)
198: array(3833, string(197), 0, 3) -> array(3833, string(198), 0, 2)
199: array(3833, string(198), 0, 2) -> array(3833, string(199), 0, 1)
200: array(3833, string(199), 0, 1) -> array(5176, string(200))

```

## 7.3  C语言实现接收区块

### 7.3.1 设计
状态机一致, 从php翻译过来, 用c语言实现.

### 7.3.2 程序
```
#define TFM_DESC

#include "stdio.h"
#include "stdlib.h"
#include "string.h"
#include "sys/socket.h"
#include "arpa/inet.h"
#include "tfm.h"
#include "tomcrypt.h"

char *string_create(char *old, int length)
{
   char *new;
   int *_6157;

   new = malloc(length + 4) + 4;
   memcpy(new, old, length);

   _6157 = (int *) (new - 4);
   *_6157 = length;

   return new;
}

char *string_create_improper(char *old)
{
   char *new;
   int *_7193;

   new = malloc(strlen(old) + 4) + 4;
   memcpy(new, old, strlen(old));

   _7193 = (int *) (new - 4);
   *_7193 = strlen(old);

   return new;
}

char *string_create_progression(int a)
{
   char *c;
   int *_3919;

   c = malloc(a + 4) + 4;

   _3919 = (int *) (c - 4);
   *_3919 = 0;

   return c;
}

int string_measure(char *buffer)
{
   int *_4721;

   _4721 = (int *) (buffer - 4);

   return *_4721;
}

void string_update(char *object, char *buffer, int length)
{
   int *_9492;

   _9492 = (int *) (object - 4);
   memcpy(object + *_9492, buffer, length);
   *_9492 += length;
}

void string_update_1(char *object, char quantity)
{
   int *_7751, *_2888;

   _7751 = (int *) (object - 4);
   _2888 = (int *) (object + *_7751);
   *_2888 = quantity;
   *_7751 += 1;
}

void string_update_4(char *object, int quantity)
{
   int *_7751, *_2888;

   _7751 = (int *) (object - 4);
   _2888 = (int *) (object + *_7751);
   *_2888 = quantity;
   *_7751 += 4;
}

void string_update_8(char *object, uint64_t quantity)
{
   int *_7751;
   uint64_t *_2888;

   _7751 = (int *) (object - 4);
   _2888 = (uint64_t *) (object + *_7751);
   *_2888 = quantity;
   *_7751 += 8;
}

void string_update_initial(char *object, char *initial)
{
   int *_8478, *_5970;

   _8478 = (int *) (object - 4);
   _5970 = (int *) initial;

   memcpy(object + *_8478, initial, *_5970);
   *_8478 += *_5970;
}

char *string_slice(char *subject, int start, int length)
{
   return string_create(subject + start, length);
}

char *string_eject(char *object)
{
   char *new;
   int *_6558;

   _6558 = (int *) (object - 4);
   new = malloc(*_6558);
   memcpy(new, object, *_6558);

   return new;
}

struct condition {

   int code;
};

struct condition_3786 {

   int code;
   char *A;
   int B;
};

struct condition_1188 {

   int code;
   char *A;
   int B;
   int C;
};

struct condition_3833 {

   int code;
   char *A;
   int B;
   int C;
};

struct condition_5176 {

   int code;
   char *A;
};

struct condition *create_3786(char *A, int B)
{
   struct condition_3786 *present;

   present = malloc(sizeof (struct condition_3786));
   present->code = 3786;
   present->A = A;
   present->B = B;

   return (struct condition *) present;
}

struct condition *create_1188(char *A, int B, int C)
{
   struct condition_1188 *present;

   present = malloc(sizeof (struct condition_1188));
   present->code = 1188;
   present->A = A;
   present->B = B;
   present->C = C;

   return (struct condition *) present;
}

struct condition *create_3833(char *A, int B, int C)
{
   struct condition_3833 *present;

   present = malloc(sizeof (struct condition_3833));
   present->code = 3833;
   present->A = A;
   present->B = B;
   present->C = C;

   return (struct condition *) present;
}

struct condition *create_5176(char *A)
{
   struct condition_5176 *present;

   present = malloc(sizeof (struct condition_5176));
   present->code = 5176;
   present->A = A;

   return (struct condition *) present;
}

struct condition *handle(struct condition *present, char byte)
{
   int X;
   char *Y;

   struct condition_3786 *present_3786;
   struct condition_1188 *present_1188;
   struct condition_3833 *present_3833;
   struct condition_5176 *present_5176;

   if (present->code == 3786) {

      present_3786 = (struct condition_3786 *) present;

      string_update_1(present_3786->A, byte);
      present_3786->B--;

      if (present_3786->B == 0) {

         Y = string_slice(present_3786->A, string_measure(present_3786->A) - 4, 4);
         memcpy(&X, Y, 4);

         return create_1188(present_3786->A, X, 4);
      }

      return create_3786(present_3786->A, present_3786->B);
   }

   if (present->code == 1188) {

      present_1188 = (struct condition_1188 *) present;

      string_update_1(present_1188->A, byte);
      present_1188->C--;

      if (present_1188->C == 0) {

         Y = string_slice(present_1188->A, string_measure(present_1188->A) - 4, 4);
         memcpy(&X, Y, 4);

         return create_3833(present_1188->A, present_1188->B - 1, X - 4);
      }

      return create_1188(present_1188->A, present_1188->B, present_1188->C);
   }

   if (present->code == 3833) {

      present_3833 = (struct condition_3833 *) present;

      string_update_1(present_3833->A, byte);
      present_3833->C--;

      if (present_3833->B == 0 && present_3833->C == 0)
         return create_5176(present_3833->A);

      if (present_3833->C == 0)
         return create_1188(present_3833->A, present_3833->B, 4);

      return create_3833(present_3833->A, present_3833->B, present_3833->C);
   }
}

int main(int count, char **arguments)
{
   ltc_mp = tfm_desc;
   register_prng(&sprng_desc);
   register_hash(&sha256_desc);

   int self, other, error, length;
   struct sockaddr_in address;
   char buffer[1000];

   self = socket(2, 1, 0);

   if (self == -1)
      exit(1);

   address.sin_family = 2;
   address.sin_port = htons(atoi(arguments[1]));
   address.sin_addr.s_addr = inet_addr("0.0.0.0");

   error = bind(self, (struct sockaddr *) &address, sizeof (address));

   if (error == -1)
      exit(2);

   error = listen(self, 1024);

   if (error == -1)
      exit(3);

while(1) {

   other = accept(self, 0, 0);

   if (other == -1)
      exit(4);

   struct condition *present;

   present = create_3786(string_create_progression(4000000), 108);

   while (1) {

      memset(buffer, 0, 1000);
      length = recv(other, buffer, 1, 0);

      if (length == -1 || length == 0)
         break;

      present = handle(present, buffer[0]);

      struct condition_3786 *U;
      struct condition_1188 *V;
      struct condition_3833 *W;
      struct condition_5176 *M;

      if (present->code == 3786){
         U = (struct condition_3786 *) present;
         printf("array(%d, string(%d), %d)%c", U->code, string_measure(U->A), U->B, 10);
      }

      if (present->code == 1188){
         V = (struct condition_1188 *) present;
         printf("array(%d, string(%d), %d, %d)%c", V->code, string_measure(V->A), V->B, V->C, 10);
      }

      if (present->code == 3833){
         W = (struct condition_3833 *) present;
         printf("array(%d, string(%d), %d, %d)%c", W->code, string_measure(W->A), W->B, W->C, 10);
      }

      if (present->code == 5176){
         M = (struct condition_5176 *) present;
         printf("array(%d, string(%d))%c", M->code, string_measure(M->A), 10);
      }

      if (present->code == 5176) {
         close(other);
         break;
      }
   }
}
}

```

### 7.3.3 运行结果
用nc传送字节, listen程序接收.

#### 7.3.3.1 第一个区块接收
```
cat 1.block | nc 127.0.0.1 6002
```

```
tcc -c listen.c -o listen.o  && tcc listen.o ../libtomcrypt.a ../libtfm.a -o listen 

./listen 6002

array(3786, string(1), 107)
array(3786, string(2), 106)
array(3786, string(3), 105)
..... 略
array(3786, string(98), 10)
array(3786, string(99), 9)
array(3786, string(100), 8)
array(3786, string(101), 7)
array(3786, string(102), 6)
array(3786, string(103), 5)
array(3786, string(104), 4)
array(3786, string(105), 3)
array(3786, string(106), 2)
array(3786, string(107), 1)
array(1188, string(108), 1, 4)
array(1188, string(109), 1, 3)
array(1188, string(110), 1, 2)
array(1188, string(111), 1, 1)
array(3833, string(112), 0, 88)
array(3833, string(113), 0, 87)
array(3833, string(114), 0, 86)
... 略
array(3833, string(190), 0, 10)
array(3833, string(191), 0, 9)
array(3833, string(192), 0, 8)
array(3833, string(193), 0, 7)
array(3833, string(194), 0, 6)
array(3833, string(195), 0, 5)
array(3833, string(196), 0, 4)
array(3833, string(197), 0, 3)
array(3833, string(198), 0, 2)
array(3833, string(199), 0, 1)
array(5176, string(200))

```

#### 7.3.3.2 第二个区块接收

```
cat 2.block | nc 127.0.0.1 6002
```

```

array(3786, string(1), 107)
array(3786, string(2), 106)
array(3786, string(3), 105)
.... 略
array(3786, string(98), 10)
array(3786, string(99), 9)
array(3786, string(100), 8)
array(3786, string(101), 7)
array(3786, string(102), 6)
array(3786, string(103), 5)
array(3786, string(104), 4)
array(3786, string(105), 3)
array(3786, string(106), 2)
array(3786, string(107), 1)
array(1188, string(108), 2, 4)
array(1188, string(109), 2, 3)
array(1188, string(110), 2, 2)
array(1188, string(111), 2, 1)
array(3833, string(112), 1, 88)
array(3833, string(113), 1, 87)
array(3833, string(114), 1, 86)
... 略
array(3833, string(190), 1, 10)
array(3833, string(191), 1, 9)
array(3833, string(192), 1, 8)
array(3833, string(193), 1, 7)
array(3833, string(194), 1, 6)
array(3833, string(195), 1, 5)
array(3833, string(196), 1, 4)
array(3833, string(197), 1, 3)
array(3833, string(198), 1, 2)
array(3833, string(199), 1, 1)
array(1188, string(200), 1, 4)
array(1188, string(201), 1, 3)
array(1188, string(202), 1, 2)
array(1188, string(203), 1, 1)
array(3833, string(204), 0, 269)
array(3833, string(205), 0, 268)
array(3833, string(206), 0, 267)
array(3833, string(207), 0, 266)
... 略
array(3833, string(462), 0, 11)
array(3833, string(463), 0, 10)
array(3833, string(464), 0, 9)
array(3833, string(465), 0, 8)
array(3833, string(466), 0, 7)
array(3833, string(467), 0, 6)
array(3833, string(468), 0, 5)
array(3833, string(469), 0, 4)
array(3833, string(470), 0, 3)
array(3833, string(471), 0, 2)
array(3833, string(472), 0, 1)
array(5176, string(473))

```

# 8. 侧链设计原理
# 9. 侧链开发
# 10. 智能合约设计原理
# 11. 智能合约开发 

# 12. 计算机基础

###  操作系统中字节表示
32位系统中, 标准数字用4个字节表示. 整数的范围是2的32次方.
字符串通过ASCII码来表示0-255范围内与字母等的对应.
中文字有其他的规则来对应. 例如utf8.

![image_1btoa8s9oog1chl4ct1c3t24e9.png-152.3kB][8]

### Windows研发思想的来龙去脉 
多进程的管理来自于UNIX

从系统的角度, 和从进程自己的角度来看内存. 
程序自己的地址是连续的, 实际系统中不是连续的, 是多个程序公用内存段落.

```
graph LR
unix-->OpenVMS
OpenVMS-->WindowsNT 
WindowsNT -->Windows2000
Windows2000-->WindowsXP
```

### 执行程序对比
 **系统文件对比** | 执行 | 动态链接库 |静态链接库 |编译结果|源文件
 ---|---|---|---|---|---|
 **windows:** | .exe | .dll |.lib |.obj|.c
**linux:** |无后缀 |.so |.a|.o|.c

> - 编译器是把非执行文件转换成执行文件。
> - .exe:直接运行程序，
> - .dll：一套函数不能运行，但可以在另一个程序运行的，更高级也可一共享，程序运行中可以引用的。
> - .lib：造一个程序可以运行，但必须重新编译才成实现，程序可以互相共享
> - .obj：链接以前的编译结果，一个程序有若干个源程序，一个源程序一个Obj文件。半成品文件编译还未处理
> - .a 是若干个 .o 组成的，.a 到 .so 需要加若干个源数据。.so是 .a加工后的。

### BASE64
Base64说明: 
[https://baike.baidu.com/item/base64/8545775?fr=aladdin][9]

Base64是网络上最常见的用于传输8Bit字节码的编码方式之一，Base64就是一种基于64个可打印字符来表示二进制数据的方法。可查看RFC2045～RFC2049，上面有MIME的详细规范。
Base64编码是从二进制到字符的过程，可用于在HTTP环境下传递较长的标识信息。例如，在Java Persistence系统Hibernate中，就采用了Base64来将一个较长的唯一标识符（一般为128-bit的UUID）编码为一个字符串，用作HTTP表单和HTTP GET URL中的参数。在其他应用程序中，也常常需要把二进制数据编码为适合放在URL（包括隐藏表单域）中的形式。此时，采用Base64编码具有不可读性，需要解码后才能阅读。在MIME格式的电子邮件中，base64可以用来将binary的字节序列数据编码成ASCII字符序列构成的文本。

### 执行速度

* register read = 1 nanosecond 纳秒
* memory read = 1 microsecond 微秒
* file read = 1 millisecond 毫秒 
* 

### TCP 接收 HTTP
以 GET, POST, PUT, DELETE, HEAD 开头.

### 内存中栈与堆的区别
堆和栈的第一个区别就是申请方式不同：栈（英文名称是stack）是系统自动分配空间的，例如我们定义一个 char a；系统会自动在栈上为其开辟空间。
而堆（英文名称是heap）则是程序员根据需要自己申请的空间，例如malloc（10）；开辟十个字节的空间。

由于栈上的空间是自动分配自动回收的，所以栈上的数据的生存周期只是在函数的运行过程中，运行后就释放掉，不可以再访问。
而堆上的数据只要程序员不释放空间，就一直可以访问到，不过缺点是一旦忘记释放会造成内存泄露。
从下面代码可以看出:
```
  int a = 0; //全局初始化区 
  char *p1; //全局未初始化区 
  main() 
  { 
      int b; //栈 
      char s[] = "abc"; //栈 
      char *p2; //栈 
      char *p3 = "123456"; //123456\0在常量区，p3在栈上。 
      static int c =0； //全局（静态）初始化区 
      p1 = (char *)malloc(10); //堆 
      p2 = (char *)malloc(20);  //堆 
```
![image_1bu134etlerc8641f4g1dagglep.png-74.9kB][10]

### 状态机
..

# 13. Linux相关说明

## 13.1 基础操作

### 设置文本编译器
```
alias nano = "nano -w -x -E -T 3 -I -S"
```

### 16进制展示与xxd
xxd 可用来查看生字节的16进制展示.

以下是个示例.
每行16个字节. 每4个字母数字为2个字节.
```
$ xxd 1.block                                           
0000000: 196f d33e b38e 61d5 4579 d98e 16d1 ac4e  .o.>..a.Ey.....N
0000010: 279e 065c 07d7 5461 42b7 fe60 d226 72bc  '..\..TaB..`.&r.
0000020: 0000 0000 0000 0000 0000 0000 0000 0000  ................
0000030: 0000 0000 0000 0000 0000 0000 0000 0000  ................
0000040: 0000 0000 0000 0000 0000 0000 0000 0000  ................
0000050: 0000 0000 0000 0000 0000 0000 0000 0000  ................
```

字节的计算过程:  
e8 : 232 
e803 = 232 + 256 *3 =  1000
图中所示, 前2个字节表示整数1000
```
00000e0: e803 0000 0000 0000 0000 0000 0000 0000  ................
```


### linux终端共享使用说明

作共享屏幕:
```
tmux -S $SOCKET new -t $SESSION
```
接入共享屏幕
```
tmux -S $SOCKET attach -t $SESSION -r
```
-r 参数表示只读, 去掉可以一起写.

示例:
```
tmux -S /tmp/330 attach -t 0 -r
or
sh /tmp/a
```


## 13.2 编译执行相关

### 编译执行源代码：

```
tcc -c program.c -o program.o
tcc -o program.elf program.o libtfm.a libtomcrypt.a  
./program.elf
```
注意: 编译时文件名的顺序不要变, 否则无法编译通过.


### 查看库
- 查看 .a 的命令 ： readedf -S libc.a
- 查看有哪些函数 ：readelf -S libtfm.a |grep File

```
查看库的函数名
$ readelf -S libtomcrypt.a  | grep File         
File: libtomcrypt.a(aes.o)
File: libtomcrypt.a(aes_enc.o)
File: libtomcrypt.a(anubis.o)
File: libtomcrypt.a(blowfish.o)
File: libtomcrypt.a(camellia.o)
File: libtomcrypt.a(cast5.o)
File: libtomcrypt.a(des.o)
```

### nc
- 一端使用nc -l 127.0.0.1 6000启动监听程序，另一端使用nc 127.0.0.1 6000 启动连接程序，则两端能互相看到对方的输入。
- ctrl+c可以退出监听。
- 一端使用nc -l 127.0.0.1 6000启动监听程序，另一端运行自己的程序，则可以被监听到。

* 监听测试 nc -l 127.0.0.1 6000
* 运行命令 tcc -run program.c 127.0.0.1 6000


# 14. 程序开发语言相关说明

## 14.1 C语言
### 14.1.1 C语言基础

* system call:  
   open, close, read, write
* gnu function:  
   fopen, fclose, fread, fwrite

```
#include "stdio.h" 包含输入和输出相关的函数 printf() fopne() fread() fwrite() fclose()
#include "stdlib.h" 包含内存管理的函数 malloc() free() 
#include "string.h" 包含字符串处理的函数 strlen() strtok() strcpy()
#include "sys/socket.h" 包含套接字的函数
#include "arpa/inet.h" 互联网套接字函数
```

### 14.1.2 注意点
#### 构造8字节, 64位的数字处理
32位的机器, 要想创造64位的整数, 
C语言里long long int 会分配一个64位的整数. 
用特定的函数来支持. 编译器支持.  虽然机器本身不支持.

```
unsigned char *y;
unsiged int z;
y = malloc(8);          // 分配8个字节              
z = 1000;                 // 4个字节的变量z
memcpy(y, &z, 4);    // 把 4个字节的z 复制到 y上.
memset(y + 4, 0, 4);  // 后4个字节补充0.  
```

#### memset与填充0
buffer 在创造时( malloc() ), 不会自动地填充成0.  所以可以用memset. 
```
buffer = malloc(1000);
memset(buffer,0,1000);
```

####  void 指针
课上bug已解，原因是C语言不能从void类型的指针中取值，需要进行两次强转，形式如下：
```
    while ((int)list != 0 ) {
        data = (unsigned char *) *((int *) list);
        printf("value: %d%c", (int)data, 10);
        list = (void *)*((int *) (list + 4));
        printf("list: %d%c", (int)list, 10);
    }
```

#### 文件结束符
当公钥存在一个文件里. 
文件里可能会增加一个跳行. 引起base64的函数转变的崩溃. 因为base64没有跳行字符.
最好是拿之前的函数来读文件, 判断最后一个字符是不是10, 若是10要去掉. 倒数第二个是10也要删掉.
或者把 钥匙 放在代码变量里.

#### signed char 与 unsigned char区别
signed char取值范围是 -128 到 127(有符号位)
unsigned char 取值范围是 0 到 255
char 到底是signed char还是unsigned char？这得看编译器.

```
#include <stdio.h>  
int main(void)  
{  
  
    signed char a = -1;  
    unsigned char b = -1;  
  
    printf("%%d:\n");  
    printf("signed: %d\n", a);  
    printf("unsiged:%d\n", b);  
  
    printf("%%u:\n");  
    printf("signed: %u\n", a);  
    printf("unsigned: %u\n", b);  
}  

```

```
%d:
signed: -1
unsiged:255
%u:
signed: 4294967295
unsigned: 255
```

## 14.2 php语言

### 函数说明 

#### ord()
把字母转成字节


#### hash
string hash ( string $algo , string $data [, bool $raw_output = false ] )
```
参数
algo
要使用的哈希算法，例如："md5"，"sha256"，"haval160,4" 等。
data
要进行哈希运算的消息。
raw_output
设置为 TRUE 输出原始二进制数据， 设置为 FALSE 输出小写 16 进制字符串。

返回值

如果 raw_output 设置为 TRUE， 则返回原始二进制数据表示的信息摘要， 否则返回 16 进制小写字符串格式表示的信息摘要。
```
http://php.net/manual/zh/function.hash.php

#### array_push()
将一个或多个单元压入数组的末尾(入栈) 
```
$a=array("Dog","Cat");
array_push($a,"Horse","Bird");//内容加到数组中
print_r($a);
```
输出：
```
Array
(
    [0] => Dog
    [1] => Cat
    [2] => Horse
    [3] => Bird
)
```
#### array_shift()
将数组开头的单元移出数组 
另外, array_unshift()在数组开头插入一个或多个单元 
```
$stack = array("Java", "Php", "C++", "C#", "Ruby");  
array_shift($stack);  
print_r($stack);  
```
```
Array
(
    [0] => Php
    [1] => C++
    [2] => C#
    [3] => Ruby
)
```

# 15. 密码学相关说明

密码学在区块链的应用：hash函数 椭圆曲线数字签名

![image_1btobo6mh1cpr16ui1lvo3erlvj1g.png-39kB][11]

## hash的应用
1.Tx的hash和Merkle root
2.地址生成 —>节约空间
3.挖矿 hash(x||N)<Target  —>难于解决易于验证
4.连接

![image_1btobljiu1bq0i1t1q3c1g5l12tn13.png-155.3kB][12]

##  椭圆曲线
Elliptal Cirue Digital Signature Algorthim
ECC: Elliptal Curve Crypeogaphy.  加解密
ECDSA: 签名和验证的.

超级计算机速度:   4万亿/s
找到碰撞 需要计算 4万亿年.



# 16. 资料链接
## 课程相关链接
第13次课程屏幕录屏: 
http://pan.baidu.com/s/1nviNO1b   

## 区块链技术文档
Bitcoin: A Peer-to-Peer Electronic Cash System, Satoshi Nakamoto
https://bitcoin.org/bitcoin.pdf

比特币白皮书：一种点对点的电子现金系统 中文翻译版, 中本聪
http://www.8btc.com/wiki/bitcoin-a-peer-to-peer-electronic-cash-system

GitHub - bitcoinbook/bitcoinbook: Mastering Bitcoin 2nd Edition - Programming the Open Blockchain https://github.com/bitcoinbook/bitcoinbook
精通比特币中文第1版
http://book.8btc.com/master_bitcoin

Merkle Tree（默克尔树）算法解析
http://blog.csdn.net/wo541075754/article/details/54632929

介绍几本关于比特币和区块链的书
https://www.zhihu.com/question/35541188

## 区块链开发
比特币开发:
https://bitcoin.org/en/development

比特币难度公式:
https://en.bitcoin.it/Difficulty
https://en.bitcoin.it/wiki/Target


## 网上比特币相关公开课程
比特币和数字货币技术-Princeton University课程
https://www.coursera.org/learn/cryptocurrency

斯坦福大学公开课MOOC：比特币工程学
https://bitcoin.stanford.edu/



## 计算机基础
内存堆和栈的区别
http://www.cnblogs.com/lln7777/archive/2012/03/14/2396164.html

## 编程语言
php file 函数教程:
http://www.w3school.com.cn/php/php_ref_filesystem.asp


## 网络编程
 beej's Guide to Network Programming(2016)
 UNIX Network Programming(1990) by W Richard Stevens
 https://www.kernel.org/doc/man-pages           Michael kerrish维护，网站分为8个部分

## markdown 编辑器
markdown editor:
https://github.com/cloose/CuteMarkEd
cmd markdown editor:
https://www.zybuluo.com/ 

## 区块链技术公开课的公众号报道
http://mp.weixin.qq.com/s/GKXUHEHd44mGqH05SgRkBA
http://mp.weixin.qq.com/s/d0hBmFFqxWl995ZbEKqkHw
http://mp.weixin.qq.com/s/qoVkB_xzaoRNAPkaPJWddw
http://mp.weixin.qq.com/s/kF_G9bkefJdbx24wcoJ0IA


  [1]: https://bitcoin.org/img/dev/en-blockchain-overview.svg
  [2]: http://static.zybuluo.com/zhongdao/xlucbkvbh2ulahikqif8935h/image_1btodhc8onfa1mdp10u21pa11ckqm.png
  [3]: http://static.zybuluo.com/zhongdao/1vvl7o34lgkdd7vuu4snyje4/image_1btoaked7g6m15uo19okr2tsirm.png
  [4]: http://static.zybuluo.com/zhongdao/8csxwjsg9do5zyvknpoqa4z5/image_1btodkb4qm17lf2fj1q96gr13.png
  [5]: http://static.zybuluo.com/zhongdao/10smnexnwsqydu2d17mh3rvl/image_1btodfhca5dr15cg1s2760lm919.png
  [6]: http://static.zybuluo.com/zhongdao/l4bkpkqvhqlkepokha1koqk8/image_1btodtl5s5t219q3dqaauf1gkl20.png
  [7]: http://static.zybuluo.com/zhongdao/ddrs9xpioi69bmyz8aswdtde/image_1bto7u8pg1ot5qlgdi1cq71husg.png
  [8]: http://static.zybuluo.com/zhongdao/uvtnd82se5f7gic01o4e37uj/image_1btoa8s9oog1chl4ct1c3t24e9.png
  [9]: Base64
  [10]: http://static.zybuluo.com/zhongdao/w7l2hsoegttzhi8w1j6rkqar/image_1bu134etlerc8641f4g1dagglep.png
  [11]: http://static.zybuluo.com/zhongdao/k16c75kg48xx3pxtffiqdswc/image_1btobo6mh1cpr16ui1lvo3erlvj1g.png
  [12]: http://static.zybuluo.com/zhongdao/1t3y3kmt7a1ukiw3tmsi9g4i/image_1btobljiu1bq0i1t1q3c1g5l12tn13.png