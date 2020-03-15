---
title: CRC
date: 2019-10-23 19:32:05
tags: 知识点记录
---

##### CRC（循环冗余校验）

 循环冗余校验（Cyclic Redundancy Check， CRC）是一种根据网络数据包或计算机文件等数据产生简短固定位数校验码的一种[信道编码技术](https://baike.baidu.com/item/信道编码技术/2366288)，主要用来检测或校验数据传输或者保存后可能出现的错误。它是利用除法及余数的原理来作错误侦测的。

[在线CRC计算工具](http://www.ip33.com/crc.html)

在实际项目中，用于蓝牙数据通讯。一般的做法是在数据的末尾添加CRC校验，用于纠错、检错。

<!-- more -->

##### 计算CRC8的方法

```swift
import Foundation

final class CRC {

    static let poly = 0x07
    static let mask = 0xff

    static let table: [UInt8] = {

        var table = Array.init(repeating: UInt8(0), count: 256)

        for i in 0..<table.count {
            var c = i
            for _ in 0..<8 {
                if c & 0x80 != 0 {
                    c = (((c << 1) & mask) ^ poly)
                } else {
                    c <<= 1
                }
            }
            table[i] = UInt8(c)
        }
        return table
    }()

    static func makeCRC8(string: String) -> UInt8 {
        guard let data = string.data(using: String.Encoding.utf8) else { return 0 }
        return makeCRC8(data: data)
    }

    static func makeCRC8(data: Data) -> UInt8 {
        let dataArray = [UInt8](data)
        var crc8:UInt8 = 0x00
        for i in 0..<dataArray.count {
            crc8 = table[Int(crc8 ^ dataArray[i])]
        }
        return crc8
    }

    static func makeCRC8(data: [UInt8]) -> UInt8 {

        var crc8:UInt8 = 0x00
        for i in 0..<data.count {
            crc8 = table[Int(crc8 ^ data[i])]
        }
        return crc8
    }


    private init() {

    }
}

```

###### 使用方法

```swift
let bytes: [UInt8] = [0xAA, 0x01, 0x01, 0x03,0x05,0xFF]

let crcResult = CRC.makeCRC8(data: bytes)

print("crcResult:\(crcResult)")
```



##### 计算CRC-8/MAXIM的方法

```c
unsigned char dallas_crc8(const unsigned char * data, const unsigned int size)
{
    unsigned char crc = 0;
    for ( unsigned int i = 0; i < size; ++i )
    {
        unsigned char inbyte = data[i];
        for ( unsigned char j = 0; j < 8; ++j )
        {
            unsigned char mix = (crc ^ inbyte) & 0x01;
            crc >>= 1;
            if ( mix ) crc ^= 0x8C;
            inbyte >>= 1;
        }
    }
    return crc;
}
```

###### 使用方法

```swift
var bytes: [UInt8] = [0xAA, 0x01, 0x01, 0x03,0x05,0xFF]

let crcResult = dallas_crc8(&bytes, UInt32(Int32(bytes.count)))

 print("crcResult:\(crcResult)")
```

##### 在Swift中引用C代码

1. 新建文件并创建桥接文件，(可以不创建头文件)将新建的文件后缀名更改为`.m`。
2. 在`.m`文件中引入桥接文件`#import "XXTest-Bridging-Header.h"`, 在该文件中写入C代码。
3. 在桥接文件中写上C方法签名，`unsigned char dallas_crc8(const unsigned char * data, const unsigned int size)`;
4. 如上就可在Swift调用C代码了。