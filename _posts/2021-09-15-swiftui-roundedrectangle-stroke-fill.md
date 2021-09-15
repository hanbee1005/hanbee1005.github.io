---
layout: post
title:  "[SwiftUI] RoundedRectangle stroke, fill 수식어 동시 적용"
date:   2021-09-15 16:10:00 +0900
categories: swift swiftUI
---

RoundedRectangle에 배경색과 테두리를 동시에 적용
```swift
RoundedRectangle(cornerRadius: 7)
	.strokeBorder(Color.gray.opacity(0.6), lineWidth: 1)  // 테두리 지정
	.background(RoundedRectangle(cornerRadius: 7).fill(Color.white))  // 배경에 RoundedRectangle을 하나 더 그리고 배경색 채우기
```

