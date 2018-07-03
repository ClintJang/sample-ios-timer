# Sample iOS Timer
## 주요 설명
[![License](http://img.shields.io/badge/License-MIT-green.svg?style=flat)](https://github.com/clintjang/sample-ios-timer/blob/master/LICENSE) [![Swift 4](https://img.shields.io/badge/swift-4.0-orange.svg?style=flat)](https://swift.org) 
- [Timer](https://developer.apple.com/documentation/foundation/timer?changes=_3) : Objc 에서는 NSTimer, swift에서는 Timer Class 입니다.
- iOS 2.0+
- Timer 와 [Thread](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/AboutThreads/AboutThreads.html)는 전혀 다른 것 입니다.
- 타이머(Timer)는 [실행 루프](https://developer.apple.com/documentation/foundation/runloop)와 함께 작동합니다.
- 타이머는 별도의 스레드가 아니라 해당 런루프 내의 tick을 이용하는 것 입니다.
- 타이머 더이상 루프가 없어서 종료되기 전에는 반드시 invalidate() 를 실행해 줘야됩니다.
	- [invalidate()](https://developer.apple.com/documentation/foundation/runloop/1418468-add) 함수는 타이머가 다시 실행되는 것을 중지하고 실행 루프에서 타이머를 제거하도록 요청합니다. 그래서 실행(invalidate())을 시켜주지 않으면 메모리 leak이 발생합니다.
	- (The receiver retains aTimer. To remove a timer from all run loop modes on which it is installed, send an invalidate() message to the timer.)
- 실행 루프에서 타이머 예약하는 방법은 크게 3가지라고 할 수 있습니다. 자동으로 되는 경우(1)와 수동으로 등록하는 방법(2)도 있습니다. 그리고 실행루프에 추가할때도 멀티플하게 실행 루프 모드를 설정해서 추가할 수 있습니다.
	- 자동으로 Runloop에 등록되는 경우
	```swift
    // 현재의 실행 루프에 default mode로 스케쥴된 새로운 timer가 추가됩니다.
    class func scheduledTimer(timeInterval ti: TimeInterval, 
               invocation: NSInvocation, 
                  repeats yesOrNo: Bool) -> Timer
    ```
	- 수동으로 등록하는 경우 (두가지 인데 둘다 RunLoop 객체에 별도로 add해야됩니다.)
		- 수동이니 실행 루프에 예약하지 않고 타이머 객체를 만들고  RunLoop 객체의 add 매소드를 호출해서 추가하는 경우
		- 타이머 객체를 할당(Allocate)만하고 타이머 초기화는 메소드를 이용하는 경우
	```swift
    Timer 생성은 수동으로 하는 생성자로 생성하고 
    init(timeInterval: invocation: repeats:) 
    or 	
    init(timeInterval: target: selector: userInfo: repeats:)
    
    or
    init(fireAt: interval: target: selector: userInfo: repeats:)
     
   
    RunLoop:: func add(_ timer: Timer,  forMode mode: RunLoop.Mode)
    
    // 생성한 타이머 오브젝트 : timerObject
    ex) RunLoop.current.add(timerObject, forMode: RunLoopMode.commonModes)
    ```
    
## 단점

- 기본타임스케쥴어의 경우, 타임 스케쥴링이 정확한 적용한 시간마다 동작하지 않습니다. (No Guarantee)
	- 예를 들어 유저인터랙션이 들어가면 실행 루프에서 처리할 것이 많아지게 되고, 타이머 처리가 늦어져서 fire 처리가 늦어집니다.
- 중간에 어떠한 개입이 생기는 경우, 중지하고 다 끝나면 이어서 진행하는 개념입니다.
	- 예를 들어 3초뒤에 이벤트가 fire 되는 것이라면, 1초 지나서 중간에 2초의 개입이 있다면 그 2초 동안은 중지하고, 다 끝난 이후에 진행되서 5초 뒤에 실행되게 되는 것이죠.
- 타이머 사용시 오차가 있다는 부분은 인지를 하고 개발을 해야됩니다.
- common RunLoopMode로 등록시 위의 문제점을 어느정도는 막을 수 있습니다. 
  ```swift
  extension RunLoopMode {


  public static let defaultRunLoopMode: RunLoopMode

  @available(iOS 2.0, *)
  public static let commonModes: RunLoopMode
  }
  ```
  - 사용자의 이벤트(사용자 액션, 스와이프, 터치 등등)는 기본 실행 루프에서 동작하는데 이 이벤트가 들어와서 멈추는 이슈를 commonModes 등록하면 사용자의 이벤트에 따른 멈춤 현상은 없기때문에 보다 정확하게 동작할 수 있습니다.

# 자세히 여러 케이스 진행은 ..
.. 진행중
