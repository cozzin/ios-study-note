# iOS Study Note
부스트캠프 iOS 6기 리뷰어 진행하면서 내용 업데이트 중 입니다.

# 공식문서
* [API Design Guidelines](https://swift.org/documentation/api-design-guidelines/)
* [About App Development with UIKit](https://developer.apple.com/documentation/uikit/about_app_development_with_uikit)
* [App and Environment](https://developer.apple.com/documentation/uikit/app_and_environment)

# Architecture
## MVC
![image](https://user-images.githubusercontent.com/11647461/132593358-187369d3-0dd4-4042-9644-043a0f508eae.png)

### 역할분리
* Model: 비즈니스로직 처리. 데이터/알고리즘/네트워킹
* View: 디스플레이/이벤트 캡쳐/비주얼 표현
* Controller: 조합/델리게이션/특이한 작업

### Loose Coupling 지향
* 메세지를 보낼 때 MVC 계층을 건너띄지 않기
* 하나의 오브젝트에 MVC 역할을 섞지 않기
* 뷰 클래스에서 모델 데이터를 선언하지 않기

### 메세지 관리
* 모델끼리 직접적인 양방향 데이터 전송 X
* 모델에서 Notify 할 Controller가 여러개일 때 Key-Value Observing 사용

### 출처
* https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html
* 코드스쿼드 학습자료

## MVP
![image](https://user-images.githubusercontent.com/11647461/132594410-2750da4c-7268-4077-a1dd-e9fa277b9dbb.png)

### 역할분리
* Model: 비즈니스로직 처리
* View: UIKit 요소들. UIView, UIViewController
* Presenter: Model, View 사이의 Mediator.

### MVC와 다른점
* MVC와는 다르게 Presenter가 View를 참조하지 않는다.
* UIView를 Presenter가 직접 다루지 않고 Delegate를 통해서 View 업데이트가 필요할 때 알려주는 방법

### 출처
* https://medium.com/ios-os-x-development/ios-architecture-patterns-ecba4c38de52
* https://velog.io/@chagmn/iOS-Architecture-iOS-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-%ED%8C%A8%ED%84%B4-2-MVP

# Swift
## Lazy Stored Properties
view가 load된 후에 특정 객체를 생성해서 사용하고 싶은 경우 Optional Type 사용 + viewDidLoad 에서 할당해서 사용하는 경우가 많음
```swift
final class ViewController: UIViewController {
    private var model: Model?

    override func viewDidLoad() {
        super.viewDidLoad()
        model = Model()
    }

    func useModel() {
        model?.use()
    }
}
```

`lazy var`를 사용하면 Optional Type이 아니고 기존 처럼 필요한 시점에 init 됨.

```swift
final class ViewController: UIViewController {
    private lazy var model: Model = Model()

    func useModel() {
        model.use()
    }
}
```

##

# UIKit
## IBInspectable

IBDesignable + IBInspectable 사용하면 인터페이스 빌더에서 Layer 속성도 간편하게 변경 가능

![image](https://user-images.githubusercontent.com/11647461/132595579-bdb4d92a-96e9-4564-8cf8-1e5cbfd5b286.png)

```Swift
import UIKit

@IBDesignable extension UIView {
    @IBInspectable var borderColor: UIColor? {
        set {
            layer.borderColor = newValue?.cgColor
        }
        get {
            guard let color = layer.borderColor else {
                return nil
            }
            return UIColor(cgColor: color)
        }
    }
    @IBInspectable var borderWidth: CGFloat {
        set {
            layer.borderWidth = newValue
        }
        get {
            return layer.borderWidth
        }
    }
    @IBInspectable var cornerRadius: CGFloat {
        set {
            layer.cornerRadius = newValue
            clipsToBounds = newValue > 0
        }
        get {
            return layer.cornerRadius
        }
    }
}
```

https://stackoverflow.com/a/35372610
