---
title: 开发Tips
date: 2019-10-22 19:33:41
tags: iOS开发中的小技巧
---

# Swift

##### 对象数据转字典

通讯录中`UITableView`的数据源.

```swift
let arr = [ContactsModel]
let res = Dictionary(grouping: arr, by: { $0.name.prefix(1).uppercased()}) // [String:[ContactsModel]]
```

<!-- more -->

##### 汉字转拼音

```swift
extension String {

    func transformToPinYin() -> String {

        let mutableString = NSMutableString(string: self)
        CFStringTransform(mutableString, nil, kCFStringTransformToLatin, false)
        CFStringTransform(mutableString, nil, kCFStringTransformStripDiacritics, false)
        let string = String(mutableString)
        return string.replacingOccurrences(of: " ", with: "")
    }
}
```

#####  Hex to byte 

```swift
extension StringProtocol {
    var hexa2Bytes: [UInt8] {
        let hexa = Array(self)
        return stride(from: 0, to: count, by: 2).compactMap { UInt8(String(hexa[$0..<$0.advanced(by: 2)]), radix: 16) }
    }}
}
```

##### 获取UICollectionView显示的中间的Cell

遵守Scrollview的代理方法

```swift
func scrollViewDidEndDecelerating(_ scrollView: UIScrollView) {

 let centerCellIndexPath = dateCollectView!.getMidVisibleIndexPath()

 let  horDateSelectorCollectionViewCell = dateCollectView!.cellForItem(at: centerCellIndexPath!) as? FDHorDateSelectorCollectionViewCell
	}
```

对UICollectionViewCell的extension

```swift
protocol CollectionVisibleMidCell {}
extension CollectionVisibleMidCell where Self: UICollectionView {

    func getMidVisibleIndexPath() -> IndexPath? {
        var visibleRect = CGRect()
        visibleRect.origin = self.contentOffset
        visibleRect.size = self.bounds.size
        let visiblePoint = CGPoint(x: visibleRect.midX, y: visibleRect.midY)
        guard let indexPath = self.indexPathForItem(at: visiblePoint) else { return nil }
        return indexPath
    }
}
extension UICollectionView: CollectionVisibleMidCell {}


```

##### UITableView无刷新动画的刷新

```swift
//Reload data withoutAnimation
 DispatchQueue.main.async {
     UIView.performWithoutAnimation {
            self.tableView.reloadData()
          }
     }
```

##### 在合适的的时机移除通知和定时器

```swift
@available(iOS 5.0, *)
 open var isMovingFromParent: Bool { get }
```

在`viewWillDisappear`判断当前VC是由Pop,调用的，还是由Push调用的，可以再合适的时机调用移除通知或定时器。



##### swift的forin循环遍历

```swift
//定义一个Array的数组
var items = ["Swift", "OC", "Javascript"]
for _ in items {
    items.removeLast()
}
```

swift中的循环遍历删除不会崩溃的。([官方源码](https://github.com/apple/swift/blob/da61cc8cdf7aa2bfb3ab03200c52c4d371dc6751/stdlib/public/core/Sequence.swift))原因是在它的迭代器进行循环的，上面的代码可转化成如下代码

```swift
var items = ["Swift", "OC", "Javascript"]
var itemsIterator = items.makeIterator()
while let item = itemsIterator.next() {
    items.removeLast()//此处删除items的时候，itemsIterator并不会受到影响。
}

```

```swift
    //用Label显示Unicode

    let label = UILabel.init(frame: CGRect.init(x: 10, y: 86, width: 100, height: 100))

    label.text = "\u{1F32D}"

    self.view.addSubview(label)
```





# OC