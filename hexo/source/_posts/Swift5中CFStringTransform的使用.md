---
title: Swift5中CFStringTransform的使用
date: 2019-11-06 08:48:11
tags:
---

记一次需求

##### 需求：将歌曲排序。歌曲名可能纯为数字、纯中文、纯英文、纯日文、或者各种语言的混合。

##### 解决方案: 将歌曲名转换为英文字符，再排序。

<!-- more -->

##### 方案分解：

1. ###### 认识主要iOS中的系统方法。 在Swift中可以使用Core Foundation下的`CFStringTransform`方法转换。

```swift
// MARK: - 具体的转换方法
    /// 转换字符串
    /// - Parameter string: 要转的字符串，需是CFMutableString类型
    /// - Parameter range: 要转换的区间。全部转换可传入 nil
    /// - Parameter transform: 要应用的变换
    /// - Parameter reverse: 是否要反转
    public func CFStringTransform(_ string: CFMutableString!, _ range: UnsafeMutablePointer<CFRange>!, _ transform: CFString!, _ reverse: Bool) -> Bool
```

2. ###### 写出调用封装方法。

```swift
extension String {
    /// 将任一字符串转换为英文字母字符串
    func toLetters() -> String {

        let mutableString = NSMutableString(string: self)
        // 应用 kCFStringTransformToLatin 变换将所有非英文文本转换为拉丁字母表示。
        CFStringTransform(mutableString, nil, kCFStringTransformToLatin, false)
        // 应用 kCFStringTransformStripCombiningMarks 变换来去除变音符和重音。
        CFStringTransform(mutableString, nil, kCFStringTransformStripCombiningMarks, false)

        // 使用CFStringLowercase缩减文本的大小，并使用CFStringTokenizer将文本拆分为标记，以用作文本的索引。
        let tokenizer = CFStringTokenizerCreate(nil, mutableString, CFRangeMake(0, CFStringGetLength(mutableString)), 0, CFLocaleCopyCurrent())

        var mutableTokens: [String] = []
        var type: CFStringTokenizerTokenType
        repeat {
            type = CFStringTokenizerAdvanceToNextToken(tokenizer)
            let range = CFStringTokenizerGetCurrentTokenRange(tokenizer)
            let token = CFStringCreateWithSubstring(nil, mutableString, range) as NSString
            mutableTokens.append(token as String)
        } while type != []

        //生成最终字符串
        let joined = mutableTokens.joined()

        return joined

    }
}

```

3. ###### 使用封装的方法

   ```swift
   let string = "Hello! こんにちは! สวัสดี! مرحبا! 您好!"
   
    print(string.toLetters())
   ```

4. ###### 使用方法效果

   ```swift
   Hellokon'nichihaswasdimrhbaninhao
   ```

5. ###### 注意事项

   1. 核心的方法原理是通过指定[ICU](http://userguide.icu-project.org/transforms/general#TOC-ICU-Transliterators)`"Any-Latin; Latin-ASCII; Any-Lower"`一次转换完成的。就是拉丁字符转换为 ASCII，字母全部小写的格式。
   2. 在字符串的比较方法中要注意要么字符串全大写or全小写。

6. 参考链接

   1. [CFStringTransform 字符转换](https://imliaoyuan.com/2018/01/01/CFStringTransform-字符转换.html)
   2. [CFStringTransform](