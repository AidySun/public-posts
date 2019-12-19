# 如何在 SwiftUI 手势中更新属性值

1. 更新临时状态

要在手势更改时更新*临时属性*，使用 `@GestureState` ，然后在 `update（_：body :)` 的回调中对其进行更新。 

`update（_：body :)` 的回调会在两种情况下被调用：
  - 在识别到手势后
  - 手势值更改时

结束或取消手势时，SwiftUI 会自动将 `@GuestureState` 属性的状态**重置为其初始值**。

如文末示例代码中的注释`// 1`的行。

2. 在手势期间更新永久状态

要跟踪对手势的更改，并在**手势结束后不重置**，请在 `onChanged（_ :)` 的回调中修改 `@State` 属性。 

如文末示例代码中的注释`// 2`的行，计算长按的次数。

3. 手势结束时更新永久状态

要识别手势成功完成的时间并获取手势的最终值，请使用`onEnded（_ :)`函数在回调中更新 `@State` 属性的状态。

SwiftUI**仅在手势成功时**才调用`onEnded（_ :)`回调。 

如下面示例代码中的注释`// 3`的行。

```Swift
struct CounterView: View {
    @GestureState var isDetectingLongPress = false // 1
    @State var totalNumberOfTaps = 0 // 2
    @State var doneCounting = false // 3
    
    var body: some View {
        let press = LongPressGesture(
            minimumDuration: 1
        ).updating($isDetectingLongPress) // 1
            { currentState, gestureState, transaction in
                gestureState = currentState
            }.onChanged { _ in
                self.totalNumberOfTaps += 1 // 2
            }
            .onEnded { _ in
                self.doneCounting = true // 3
            }
        
        return VStack {
            Text("\(totalNumberOfTaps)") // 2
                .font(.largeTitle)
            
            Circle()
                .fill(doneCounting // 3
                    ? Color.red :
                    isDetectingLongPress // 1
                    ? Color.yellow : Color.green)
                .frame(width: 100,
                       height: 100,
                       alignment: .center)
                .gesture(doneCounting // 3
                    ? nil : press) 
        }
    }
}
```

References:
```
https://developer.apple.com/documentation/swiftui/gestures/adding_interactivity_with_gestures
```
