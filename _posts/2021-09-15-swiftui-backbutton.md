---
layout: post
title:  "[SwiftUI] Custom BackButton 구현"
date:   2021-09-15 16:22:00 +0900
categories: swift swiftUI
---

swiftUI에서 NavigationView를 사용할 때 NavigationBar를 사용하지 않고 직접 구현한 Back Button을 사용할 수 있습니다.

1. Back Button 뷰 새로 구성하기     
```swift
import SwiftUI

struct NavBarBackButton: View {
    @Environment(\.presentationMode) var presentation
    
    var body: some View {
        HStack {
            Image(systemName: "arrow.left")
                .foregroundColor(.black)
                .onTapGesture {
                    // 뒤로가기 기능
                    self.presentation.wrappedValue.dismiss()
                }
            Spacer()
        }
        .padding(.vertical)
    }
}
```  


2. Back Button을 뷰 상단에 추가하고 원래 제공되는 NavigationBar 숨기기    

```swift
var addBookContent: some View {
    VStack {
        NavBarBackButton()
        ...
    }
    .navigationBarHidden(true)
    ...
}
```

참고: [custom-back-button](https://medium.com/@dinesh10c04/swiftui-adding-a-custom-back-button-to-navigation-bar-c96cd4e30f60)