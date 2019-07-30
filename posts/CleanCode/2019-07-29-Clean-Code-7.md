#  클래스

> 클래스는 데이터와 연산으로 이루어진 코드 블럭이다.
>
> 깨끗한 클래스, 응집도높은 클래스에 대한 고민을 해보자





#### 클래스는 작아야한다

클래스를 만들 때 첫번째 규칙은 크기이다. 

클래스는 작아야한다.  하지만 얼마나 작아야 하는지가 중요하다.

여기서 말하는 크기는 물리적인 크기보다는 책임의 크기이다.

책임을 적게 가진다는 것을 어떻게 표현할 수 있을까?

클래스의 이름은 클래스의 책임을 표현한다. 

이름에 드러나는 책임이 모호하거나, 많은 책임을 내포한다면, 여러 책임을 가진 클래스일 확률이 높다.

책임을 분리하는 것은 클래스가 변경될 수 있는 이유를 분리하는 것이고, 관심사를 분리하는 것이다.



#### 응집도

클래스에는 인스턴스 변수가 많아서는 안된다.

또 메소드는 적어도 하나이상의 인스턴스 변수를 내부에서 사용해야한다.

연관있는 코드를 클래스내에 묶어두는 것이 바로 응집도를 높이는 길이다.

이상적인 클래스는 인스턴스 변수가 존재하고 모든 인스턴스변수를 모든 메소드에서 사용하는 클래스는

응집도가 높은 클래스이다.

```swift
class StopWatch {
  var sec: Int
  
  func set(_ sec: Int){
    self.sec = sec
  }
  
  func run() {
    sec+=1
  }
  
  func stop() {
    sec = 0
  }
  
}
```

초시계 클래스를 보자.

인스턴스 변수가 적고 모든 메소드에서 인스턴스 변수를 사용하고 있다.

꼭 서로 꼭 필요한 데이터와 연산끼리 묶어둔 클래스이다.

내부에서 변수와 메서드가 강하게 의존하고 있다.

응집도가 높다는 말은 클래스에 속한 메서드와 변수가 서로 의존하며 논리적인 단위로 묶인 다는 의미이다.



```swift
class AlarmStopWatch {
  var sec: Int
  var alarmTime: Int
  
  func set(_ sec: Int){
    self.sec = sec
  }
  
  func setAlarm(_ alermTime: Int) {
    self.alermTime = alermTime
  }
  func cancelAlarm() {
    alarmTime = -1
  }
  
  func run() {
    sec+=1
    if sec == alermTime { 
      alarm()
    }
  }
  
  func stop() {
    sec = 0
  }
  
  func alarm() {
    print("알람시간입니다.")
  }
  
}
```



알람기능을 추가한 초시계이다.

이름은 나름 명확해 보일 수 있으나, 이름이 두가지의 역할을 내포하고 있다.

또 클래스내부의 메서드들이 프로퍼티를 사용하지 않는 메서드도 있으며,

`stop()` 과 `alarmTime` 변수는 어찌보면 서로 관련이 하나도 없어 보인다.

같은 블록내에 존재하지만 서로가 굳이 같이 있어야할 이유가 없는 코드들이다.

확실히 `StopWatch` 클래스보다는 응집도가 떨어져 보인다.

책임이 늘어난 결과이다.



그러면 클래스를 어떻게 작게 잘 유지할 수있을까?



#### 응집도를 유지하면 작은 클래스 여럿이 나온다



- **큰 함수를 작은 함수 여럿으로 나누자.**
  - 작은 함수로 나누면 상위에서 주입한 인수를 다음 함수로 넘기는 모습을 볼 수 있다.
- **인수를 그대로 다음 인수로 넘기는 게 많아진다면 인스턴스 변수로 승격시켜라.**
  - 인스턴스 변수가 많아진다는 것은 클래스가 높은 응집도를 유지하기 어려워진다는 것이다.
- **클래스가 응집력을 잃었나? 그렇다면 클래스를 쪼개라**



위의 절차를 따르면 작은 책임을 가진 클래스를 여러 개 얻어낼 수있다.



일부러 더 극단

```swift
class Game {
	func play(numOfPlayer: Int) {
    
  	var players = [Player]()
    var dice = Dice()
    var results = [Int]()
    
    for i in 0..<numOfPlayer {
    	players[i] = Player()
    }
    
    for j in 0..<numOfPlayer {
      let result = dice.role()
    	results.[j] = result
    }
    
    for q in 0..<numOfPlayer {
       players[q].score += results[q]
    }
   
     for k in 0..<numOfPlayer {
       print(players[k].score)
    }
}
```

```swift
class Game {
  
  func play(numOfPlayer: Int, dice: Dice) {
    var players = setupPlayers(numOfPlayer)
    let result = setupResults(numOfPlayer, dice)
    players = addResults(numOfPlayer, players, results)
    print(numOfPlayer, players)
  }
  
  private func setupPlayers(_ numOfPlayer: Int) -> [Player] {
    for i in 0..<numOfPlayer {
    	players[i] = Player()
    }
    return players
  }
  
  private func setupResults(_ numOfPlayer: Int, dice: Dice) -> [Int] {
    for j in 0..<numOfPlayer {
      let result = dice.role()
    	results.[j] = result
    }
    return results
  }
  
   private func addResults(_ numOfPlayer: Int,_ players: [Player],_ results: [Int]) -> [Player] {
    for q in 0..<numOfPlayer {
      players[q].score += results[q]
    }
     return players
  }
  
  private func printPlayerScore(_ numOfPlayer: Int, _ players: [Player]){
    for k in 0..<numOfPlayer {
       print(players[k].score)
    }
  }
}
```



**조금 극단적인 예시를 사용하긴 했다.**

**`numOfPlayer`는 `players`라는 배열의 `count` 프로퍼티로 대체가능하다.**



함수를 작게 쪼개다 보니,  반복되는 인자가 있는 게 눈에 보인다.

이제 프로퍼티로 승격을 시켜서 인자를 줄여보자.



```swift
class Game {
  
  let numOfPlayer: Int
  var players: [Player]
  var results: [Int]
  var dice: Dice
  
  func play() {
    var players = setupPlayers()
    let result = setupResults()
    players = addResults()
    printPlayerScore()
  }
  
  private func setupPlayers(){
    for i in 0..<numOfPlayer {
    	players[i] = Player()
    }
  }
  
  private func setupResults(){
    for j in 0..<numOfPlayer {
      let result = dice.role()
    	results.[j] = result
    }
  }
  
   private func addResults(){
    for q in 0..<numOfPlayer {
      players[q].score += results[q]
    }
  }
  
  private func printPlayerScore(){
    for k in 0..<numOfPlayer {
       print(players[k].score)
    }
  }
}
```

위와 같이 메소드의 인수가 가벼워지고 인스턴스 변수로 두게 되었다.

하지만 문제는 응집도가 높은 클래스가 아니라는 점이다.



```swift
class Game {
  var players: [Player]
  var results: Results
  
  func play(numOfPlayer: Int) {
    var players = PlayerFactory.createPlayers(numOfPlayer)
    let result = ResultFactory.createResults(dice)
    players = addResults()
    Printer.print(players)
  }
  
  private func addResults(){
    for q in 0..<numOfPlayer {
      players[q].score += results[q]
    }
  }
  
  
}

class PlayerFactory {
   static func createPlayers() -> [Player] {
     var players = [Player]()
   	 for i in 0..<numOfPlayer {
    		players[i] = Player()
      }
     return players
  }
}

class ResultFactory {
   static func createResults(dice: Dice){
     var scores = [Int]()
     for j in 0..<numOfPlayer {
      let result = dice.role()
    	scores.[j] = result
     }
     return scores
   }
}

class Printer {
   static func print(player: [Player]){
    for k in 0..<numOfPlayer {
       print(players[k].score)
    }
  }
}
```



이런 식으로 클래스를 분할하게되면,  꽤나 각각이 응집도 있고, 역할을 하나만 하는 여러개의 작은클래스를 얻을 수있다.



#### 변경으로부터 격리

요구사항은 끈임없이 변한다.

요구사항에 따라 코드가 변했을 때, 변한 코드에 의존하는 다른 코드도 역시나 수정되어야한다.

또 다른 코드의 수정에 따라 또 그 코드에 의존하는 다른 코드도 수정이 필요하다.

이렇게 의존은 꼬리에 꼬리를 물며 수정의 연쇄가 이뤄질 수 있다.

이렇게 바람직하지 못한 의존성들을 **결합도**라고 한다.

이 결합도를 줄이기 위해서 할 수 있는 노력은 변하는 부분과 변하지 않는 부분을 격리시키는 것이다.

구체적일 수록 수정에 대상이 된다.

하지만 추상적인 부분은 쉽게 변하지 않는다.

상세한 구현이 아닌 인터페이스에 의존하게 함으로써 결합도를 낮출 수 있다.





