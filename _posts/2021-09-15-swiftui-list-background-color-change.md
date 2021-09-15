---
layout: post
title:  "[SwiftUI] List(테이블 뷰) 배경색 변경 및 row 간격 조정"
date:   2021-09-15 15:30:00 +0900
categories: swift swiftUI
---

- SwiftUI에서 List를 사용하는 경우 배경색을 변경하고자 할 때 .background 수식어 적용 X
- 따라서, 아래와 같이 변경 필요
```swift
List {
    ForEach(booVM.books) { book in
        HStack {
            ...
        }
        .listRowBackground(Color.clear)  // row background 투명하게
    }
}
.onAppear {
    UITableView.appearance().backgroundColor = .clear  // 테이블 뷰 배경 투명
}
```

- 추가적으로 리스트의 row 별로 내부 컨텐츠에 의도치 않은 padding이 적용되어 있는데 다음 코드를 적용하여 padding 제거 가능
```swift
List {
    ForEach(booVM.books) { book in
        HStack {
            ...
        }
        .listRowBackground(Color.clear)
        .listRowInsets(EdgeInsets())  // 이 부분 추가!!!
    }
}
```

- 추가 이슈!
	+ 각 row 사이의 구분선이 아래와 같은 코드를 적용하였음에도 제거되지 않는 문제가 있음
	```swift
	List {
        ...
    }
    .onAppear {
    	UITableView.appearance().backgroundColor = .clear
    	UITableView.appearance().separatorStyle = .none  // 이 부분 추가!!!
    }
	```