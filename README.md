# iOS Study Note
부스트캠프 iOS 6기 리뷰어 진행하면서 내용 업데이트 중 입니다.

# 공식문서
* [API Design Guidelines](https://swift.org/documentation/api-design-guidelines/)
* [About App Development with UIKit](https://developer.apple.com/documentation/uikit/about_app_development_with_uikit)
* [App and Environment](https://developer.apple.com/documentation/uikit/app_and_environment)

# OOP
## 명령-쿼리 분리
- `Query`: 질문해서 답을 요청
- `Command `: 변경을 요청

코드를 사용하는 입장에서 Query - Command 가 분리되지 않는다면 혼란을 겪을 수 있습니다.
단순한 예를 들어보면

```swift
func isEmpty() -> Bool {
    count += 1
    return myArray.isEmpty
}
```

isEmpty()를 호출한 사용자는 count가 증가되는 것을 인지하지 못할 수 있습니다.
만약에 count가 증가한다는 것을 염두해두고 있더라도
`1. empty 인가?` + `2. count가 증가하겠군` 으로 생각해야해서 코드를 이해하고 유지보수 하는데 어려움을 겪을 수 있습니다.

위의 코드를 나눠보면 아래와 같이 작성할 수 있습니다.
```swift
func isEmpty() -> Bool {
    return myArray.isEmpty
}

func increase() {
    count += 1
}
```

상황에 따라 이게 번거로울 수는 있는데, 유지보수에 큰 도움이 된다고 생각합니다.

# 디자인패턴
## 팩토리 패턴
### 심플 팩토리
파라미터로 주어진 type에 따라 객체를 다르게 생성
http://ufx.kr/blog/191

### 추상 팩토리
상황에 따라 팩토리를 갈아끼우면서 결과물을 다르게 생성
https://johngrib.github.io/wiki/abstract-factory-pattern/

## Mediator

https://ko.wikipedia.org/wiki/%EC%A4%91%EC%9E%AC%EC%9E%90_%ED%8C%A8%ED%84%B4

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
## Access Control
https://docs.swift.org/swift-book/LanguageGuide/AccessControl.html


# Swif
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

## Enum
### Associated Values
enum에 값을 담아서 표현할 수 있음
```swift
enum Barcode {
    case upc(Int, Int, Int, Int)
    case qrCode(String)
}
```

패턴 매칭해서 case를 나누고 Associated Values를 가져올 수 있음
```swift
switch productBarcode {
case .upc(let numberSystem, let manufacturer, let product, let check):
    print("UPC: \(numberSystem), \(manufacturer), \(product), \(check).")
case .qrCode(let productCode):
    print("QR code: \(productCode).")
}
// Prints "QR code: ABCDEFGHIJKLMNOP."
```

https://docs.swift.org/swift-book/LanguageGuide/Enumerations.html

## 고차함수
* 매칭되는 첫번째 Element 가져오기: https://developer.apple.com/documentation/swift/array/1848165-first
* 매칭되는 Element 존재 여부: https://developer.apple.com/documentation/swift/array/2297359-contains

## Delegation
* 이벤트가 발생한 객체를 같이 담아서 보내주면 delegate 구현할 때 편리함
* `delegate?.doSomething(self, additional:)` 이런식으로 호출함

```swift
protocol DiceGame {
    var dice: Dice { get }
    func play()
}
protocol DiceGameDelegate: AnyObject {
    func gameDidStart(_ game: DiceGame)
    func game(_ game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int)
    func gameDidEnd(_ game: DiceGame)
}
```

```swift
class SnakesAndLadders: DiceGame {
    let finalSquare = 25
    let dice = Dice(sides: 6, generator: LinearCongruentialGenerator())
    var square = 0
    var board: [Int]
    init() {
        board = Array(repeating: 0, count: finalSquare + 1)
        board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
        board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
    }
    weak var delegate: DiceGameDelegate?
    func play() {
        square = 0
        delegate?.gameDidStart(self)
        gameLoop: while square != finalSquare {
            let diceRoll = dice.roll()
            delegate?.game(self, didStartNewTurnWithDiceRoll: diceRoll)
            switch square + diceRoll {
            case finalSquare:
                break gameLoop
            case let newSquare where newSquare > finalSquare:
                continue gameLoop
            default:
                square += diceRoll
                square += board[square]
            }
        }
        delegate?.gameDidEnd(self)
    }
}
```
https://docs.swift.org/swift-book/LanguageGuide/Protocols.html

# Foundation
## NotificationCenter
### [addObserver(_:selector:name:object:)](https://developer.apple.com/documentation/foundation/notificationcenter/1415360-addobserver)
object 파라미터의 역할: 특정 객체로부터 발생한 노티만 필터해서 수신. 만약 nil 이면 필터 없이 수신. 필터링이 필요 없으면 nil로 사용 가능.

> `anObject`
The object that sends notifications to the observer. Specify a notification sender to deliver only notifications from this sender.
>
>When nil, the notification center doesn’t use sender names as criteria for delivery.

### [post(name:object:userInfo:)](https://developer.apple.com/documentation/foundation/notificationcenter/1410608-post)
object 파라미터의 역할: 노티를 발생시킨 객체를 전달. (sender 개념). 수신하는 쪽에서 구분이 필요 없으면 nil 전달해도 정상작동함.

 > `anObject`
The object posting the notification.

### object 전달하는 예제

```swift
extension NSNotification.Name {
    static let modelDidUpdateTitle: NSNotification.Name = .init("didUpdateTitle")
}
```

```swift
class ViewController: UIViewController {

    private lazy var model: Model = Model()

    override func viewDidLoad() {
        NotificationCenter.default.addObserver(self, selector: #selector(didUpdateTitle(_:)), name: .modelDidUpdateTitle, object: model)
    }

    @objc private func didUpdateTitle(_ notification: Notification) {
        guard let updatedTitle = notification.userInfo?["updatedTitle"] as? String else {
            return
        }
        // 추가 작업 ...
    }

    private func updateTitle() {
        model.update(title: "testTitle")
    }
}
```

```swift
final class Model {
    private var title: String = ""

    func update(title: String) {
        self.title = title
        NotificationCenter.default.post(name: .modelDidUpdateTitle, object: nil, userInfo: ["updatedTitle": title])
    }
}
```

### object에 struct를 넣으면 작동하지 않음
- 이유: https://stackoverflow.com/a/56826646
- objective-c 시절에 만들어진 API 인데 `id` 타입을 Swift로 가져오다보니 `Any`로 포팅됨.
- 실제로는 `class`를 전달해줘야함

# UIKit
## UITouch
* 터치한 View 가져오기: https://developer.apple.com/documentation/uikit/uitouch/1618109-view

## UIGestureRecognizer
* [UIGestureRecognizerDelegate](https://developer.apple.com/documentation/uikit/uigesturerecognizerdelegate) 통해서 이벤트 조작 가능

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

## UIGraphicsImageRenderer
* 이미지를 직접 그릴 때 `UIGraphicsBeginImageContextWithOptions` / `UIGraphicsEndImageContext`로 Context를 열고 닫아줘야 함

```swift
let rect = CGRect(origin: .zero, size: size)
UIGraphicsBeginImageContextWithOptions(rect.size, false, 0.0)
UIColor.darkGray.setFill()
UIRectFill(CGRect(x: 1, y: 1, width: 140, height: 140))
let image = UIGraphicsGetImageFromCurrentImageContext()
UIGraphicsEndImageContext()
```

* `UIGraphicsImageRenderer`를 사용하면 클로저 안에서 작업을 수행하고 따로 열고 닫는 함수 호출이 필요없는 장점이 있음: 가독성 증가, 실수하지 않을 수 있음

```swift
let renderer = UIGraphicsImageRenderer(size: CGSize(width: 200, height: 200))
let image = renderer.image { (context) in
  UIColor.darkGray.setFill()
  context.fill(CGRect(x: 1, y: 1, width: 140, height: 140))
}
```

https://developer.apple.com/documentation/uikit/uigraphicsimagerenderer

# Networking

## Http 통신
* [NSAllowsArbitraryLoads](https://developer.apple.com/documentation/bundleresources/information_property_list/nsapptransportsecurity/nsallowsarbitraryloads): 모든 도메인 HTTP 통신 허용
* [NSExceptionDomains](https://developer.apple.com/documentation/bundleresources/information_property_list/nsapptransportsecurity/nsexceptiondomains): 특정 도메인만 HTTP 통신 허용

## Throttle
- 엄밀히 말하면 네트워크는 아니지만 네트워크 기능과 밀접해서 사용하는 경우가 많음
https://eunjin3786.tistory.com/80

# Xcode
## Debugging

- https://github.com/sujinnaljin/Improving_Productivity
