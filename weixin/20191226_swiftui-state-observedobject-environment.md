# SwiftUI 中 State、ObservableObject、EnvironmentObject 的区别

![](https://i.loli.net/2019/12/25/OVn5xbCm7whJ3KE.jpg)

## @State & @Binding

SwiftUI 会管理带有 `@State` 属性修饰的变量，当值改变时会重新计算关联视图（View）的 `body` 属性。

State 变量应该只在视图的 `body` 中（或被body调用的方法中）被访问，所以 State 属性声明为私有（private）。

`@Binding` 定义了一个真实源（source of truth）与其引用的双向连接，不需要指定初始值。

SwiftUI 中视图是值类型，在概念上可以理解为 `@Binding` 修饰器将 State 变量转化为引用方式进行传递，这样可以在模型与视图（及子视图）之间创建一个双向连接。

State 属性前面加上 `$` 前缀符可以得到其 `Binding<>` 对象，赋值给 `@Binding` 变量。

```Swift
// ProfileHost.swift
struct ProfileHost: View {
    @State private var draftProfile = Profile()
    
    var body: some View {

       // type of draftProfile is Profile
       // type of $draftProfile is Binding<Profile>
       ProfileEditor(profile: $draftProfile)
    }
}

// ProfileEditor.swift
struct ProfileEditor: View {
    @Binding var profile: Profile 
    // ...
}
```

## ObservableObject & @ObservedObject

`ObservableObject` 是一个协议（protocol）。

遵循 `ObservableObject` 协议的类中，被 `@Published` 修饰的属性值会被 SwiftUI 监听，值改变时会默认触发 `objectWillChange` 更新关联的视图。

`@ObservedObject` 属性包装器用于修饰遵循 `ObservableObject` 协议的对象，类似于 `@Binding`，提供了一种类似引用的值传递方式。

`@State` 用于处理私有状态； `@ObservedObject` 可用于外部数据，并可在多个独立视图间共享。

```Swift
// UserData.swift
final class UserData: ObservableObject {
    @Published var showFavoritesOnly = false
    @Published var landmarks = landmarkData
}

// MyView.swift
struct MyView: View {
  @ObservedObject var userData = UserData()
  // ...
}
```

## @EnvironmentObject

`@EnvironmentObject` 类似全局变量，整个App生命周期中、在任何试图中可以访问和使用的对象。作为 `environment object` 的类要遵循 `ObservableObject` 协议。

`@ObservedObject` 需要显示的参数传递；`@EnvironmentObject` 不是通过构造参数，而是用祖先视图的 `environmentObject()` 方法。

```Swift
// LandmarkList.swift
struct LandmarkList: View {
    @EnvironmentObject private var userData: UserData
    
    var body: some View {
       Toggle(isOn: $userData.showFavoritesOnly)
     }
}

// ScenceDelegate.swift
class SceneDelegate: UIResponder, UIWindowSceneDelegate {

    func scene(_ scene: UIScene,
         willConnectTo session: UISceneSession,
         options connectionOptions: UIScene.ConnectionOptions) {
          // ...
          LandmarkList().environmentObject(UserData())
          // ...
    }
}
```

**References:**

> State and Data Flow: https://developer.apple.com/documentation/swiftui/state_and_data_flow

