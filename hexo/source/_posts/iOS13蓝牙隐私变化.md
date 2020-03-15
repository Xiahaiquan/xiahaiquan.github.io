#### 1. 框架简要说明

iOS中`Core Bluetooth` 框架是一个抽象层，使开发人员可以访问BLE硬件。

 苹果在WWDC 2019引入了许多改进措施。除了快速传输数据和节能连接之外，用户隐私也得到了很多重视。在iOS 12之前，应用程序可以在用户不知情的情况下访问蓝牙，例如连接到智能手表或无线耳机。但这样的做法有一些缺陷，会在用户的隐私中制造了一个漏洞。 开发人员可以利用此并跟踪诸如位置数据之类的信息。

<!-- more -->

但从iOS 13开始，如果在应用程序使用任何`Core Bluetooth` API，它都需要用户的许可。 当然用户可以从设置中开启、关闭蓝牙权限。

![步骤](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/iOS13CoreBluetoothFeatures/1-1.jpeg)

#### 2. 隐私权限和使用

从iOS 13开始，开发人员必须通过在其info.plist文件中包含`NSBluetoothAlwaysUsageDescription`来指定蓝牙的隐私使用说明。 在没有使用说明的情况下访问`Core Bluetooth`将导致运行时崩溃。
为了向后支持较旧的iOS版本，还需要定义`NSBluetoothPeripheralUsageDescription`。

![屏幕快照 2020-01-01 下午5.52.50](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/iOS13CoreBluetoothFeatures/1-2.png)

#### 3. API变更

`CBManagerAuthorisation`是iOS 13的一个新添加的属性。它用于确定蓝牙权限的授权状态。
授权属性可以具有以下任意状态：

- `allowedAlways`
- `restricted`
- `notDetermined`
- `denied`

![IMG_4152](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/iOS13CoreBluetoothFeatures/1-3.jpeg)



#### 4. 使用步骤

`import  CoreBluetooth`以便在代码库中使用 Core Bluetooth Framework。
为了使用Core Bluetooth功能，我们需要实现`CBPeripheralDelegate和CBCentralManagerDelegate`协议。

##### 4.1 初始化蓝牙管理器

`CBCentralManager`负责扫描并连接到设备。 连接完成后，往后的操作都是围绕`CBPeripheral`进行的。

```swift
var centralManager: CBCentralManager?
var peripheral: CBPeripheral?
    
override func viewDidLoad() {
    super.viewDidLoad()
    centralManager = CBCentralManager(delegate: self, queue: nil) 
}`
```



就在初始化`CentralManager`时，立即触发`centralManagerDidUpdateState（_central：CBCentralManager`）委托方法以检查蓝牙连接的状态。

如果蓝牙关闭，`CBCentralManager`不能被实例化，系统会自动提示抛出一个对话框，要求您启用它。

我们可以使用`central.state.authorization`属性检查用户授权状态。

```swift
func centralManagerDidUpdateState(_ central: CBCentralManager) {
           switch central.state {
           case .unauthorized:
               switch central.authorization {
               case .allowedAlways:
               case .denied:
               case .restricted:
               case .notDetermined:
           }
           case .unknown:
           case .unsupported:
           case .poweredOn:
       self.centralManager?.scanForPeripherals(withServices: nil, options: [CBCentralManagerScanOptionAllowDuplicatesKey:true])
           case .poweredOff:
           case .resetting:
           @unknown default:
           }
 }
```

仅当状态更改为`poweredOn`时才可以扫描设备



> 注意：Core Bluetooth仅扫描BLE设备。
>



##### 4.2 连接到扫描的设备

一旦发现BLE设备，它将以下方法的委托方法回调：

```swift
func centralManager(_ central: CBCentralManager, didDiscover peripheral: CBPeripheral, advertisementData: [String : Any], rssi RSSI: NSNumber) {

        self.peripheral = peripheral
        self.peripheral?.delegate = self
        
        centralManager?.connect(peripheral, options: nil)
        centralManager?.stopScan()
 }
```

然后，我们可以从`peripheral.name`属性访问蓝牙设备名称。 

##### 4.3 发送数据到设备

```swift
public func peripheral(_ peripheral: CBPeripheral, didWriteValueFor characteristic: CBCharacteristic, error: Error?) {
        print(error ?? "data send successfully");
    }
```

##### 4.4 设备中数据有更新

```swift
public func peripheral(_ peripheral: CBPeripheral, didUpdateValueFor characteristic: CBCharacteristic, error: Error?) {
        
    }
```

 在这个代理方法中，可以上报给上层作进一步的数据处理。

#### 5. 结论

苹果通过新的iOS 13权限提高了蓝牙安全性，以提供信息权限透明性并改善用户体验。
