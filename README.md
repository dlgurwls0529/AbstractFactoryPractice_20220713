## 추상 팩토리 패턴(Abstract Factory Pattern)
### 의미
객체의 집합을 생성하는 Factory를 인터페이스로 선언하고, 특정 객체 집합이  
필요할 때마다 Concrete Factory를 만들어서 위 작업을 수행한다.  

### 효과
팩토리 패턴과 유사하다. 클라이언트 변경 시에 Concrete Factory 클래스를 만들고  
팩토리만 갈아끼워주면 되니까 OCP를 만족하게 된다. 따라서 Product를 생성하는 클래스  
(보통 Factory)가 Client에 의존하는 정도를 줄이게 된다. (요구사항 변화에 유연해진다.)    

예시를 들어보자면, 기존에 배를 만들고 그 배의 부품을 적용하는 상황을 생각해보자.    

기존 방식에서 상황에 따라 다르지만, 최악의 경우, 부품 교체를 위해 모든 line 을 고쳐야 한다.  

      <BlackShip을 생성하는 클래스, 변경 전>
      blackShip.setMotor(new WhiteShipMotor());
      blackShip.setDeck(new WhiteShipDeck());
      blackShip.setAnchor(new WhiteShipAnchor());
      
      <BlackShip을 생성하는 클래스, 변경 후>
      blackShip.setMotor(new BlackShipMotor());
      blackShip.setDeck(new BlackShipDeck());
      blackShip.setAnchor(new BlackShipAnchor());
      
요구변화에 대응하기 위해서 기존의 BlackShip 생성 클래스를 수정했을 뿐만 아니라(OCP x)  
부품 설정 작업하는 함수(setter)를 일일이 수정해줘야 했다.  
총 코드 변화는 3줄이다.  

추상 팩토리 패턴을 쓰면, 일괄로 부품 조합(ConcreteFactory)을 전달하기만 하면 된다. (OCP)  

      <BlackShip을 생성하는 클래스, 변경 전>  
      blackShip.setShipPartFactory(new WhiteShipPartFactory());
      blackShip.setMotor(this.shipPartFactory.createMotor());
      blackShip.setDeck(this.shipPartFactory.createDeck());
      blackShip.setAnchor(this.shipPartFactory.createAnchor());
      
      <BlackShip을 생성하는 클래스, 변경 후>
      [ShipPartFactory 인터페이스를 구현하는 BlackShipPartFactory 클래스를 생성한다.]
      blackShip.setShipPartFactory(new BlackShipPartFactory());
      blackShip.setMotor(this.shipPartFactory.createMotor());
      blackShip.setDeck(this.shipPartFactory.createDeck());
      blackShip.setAnchor(this.shipPartFactory.createAnchor());
      
클래스 하나(BlackShipPartFactory)를 생성(확장)하고, 기존 코드 1줄을 수정하였다.   
(여기에서는 추상 팩토리 변경을 Product 생성부에서 했지만, 생성자를 통해서  
이 의존성을 Client로부터 주입받아도 된다.)  

이처럼 추상 팩토리를 쓰면 일반적 상황에서 OCP 를 만족시키고, 결합도를 낮춘다.    
(Client클래스 변화 시에 Product 생성부의 코드 변화가 줄어든다.)  
추가적으로 이러한 추상 팩토리 패턴은 팩토리 메서드 패턴과  
같이 이용되어, Product 생성부는 보통 Concrete Factory의 객체 생성 메서드이다.  

### 팩토리 메서드 패턴과 비교
둘 다 구조가 비슷하다. 하지만 약간 다른 점이 있다.  

<팩토리 메소드 패턴>  
- 하나의 객체를 생성하는 과정을 ConcreteFactory에 위임한다.    
- 보통 Product 자체를 생성할 때 쓴다.  
- Client 클래스와 Factory 클래스의 결합도를 낮추기 위해 이용한다?  

무슨 말이냐면,  

      <Client 클래스, 변경 전>
      Ship blackShip = new ShipFactory.createShip("blackShip");
      
      <Client 클래스, 변경 후>
      [ShipFactory클래스의 createShip 메소드의 if-else 에다가 WhiteShip 생성 블록 추가]
      Ship whiteShip = new ShipFactory.createShip("whiteShip");
      
Client 클래스 변화에 의해서 ShipFactory 클래스의 수정 작업(조건 블록 추가)이 일어났으며,  
Client 클래스 자체가 1줄 변화하였다.  

      <Client 클래스, 변경 전>
      Ship blackShip = new BlackShipFactory.createShip();
      
      <Client 클래스, 변경 후>
      [WhiteShip 클래스와 WhiteShipFactory를 생성한다.]
      Ship whiteShip = new WhiteShipFactory.createShip();
      
Client 클래스 변화에 의해서 클래스 2개(WhiteShip, WhiteShipFactory)가 생성되었으며,  
Client 클래스 자체가 1줄 변화하였다.   
주목해야 할 점은, 기존 Factory 클래스를 수정했던 것과 달리 클래스 생성을 통해 Client 변화에   
대응하였고, 이에 따라 Client와 Factory의 결합도가 낮아졌다는 점이다.  

<추상 팩토리 패턴>  
- 여러 객체 집합을 생성하는 과정을 ConcreteFactory에 위임한다.   
- 주로 Product의 부품(필드)를 생성 혹은 설정하는 데 이용한다.  
- Client 클래스와 Factory 클래스의 결합도를 낮추기 위해 이용한다?  

      <Client 클래스, 변경 전>
      Ship blackShip = new BlackShipFactory("White 부품을 써라").createShip();
      
      <BlackShipFactory 클래스의 createShip(), 변경 전>
      BlackShip blackShip = new BlackShip();
      blackShip.setAnchor(new WhiteAnchor());
      blackShip.setMotor(new WhiteMotor());
      blackShip.setDeck(new WhiteDeck());
      blackShip.setLogo(new WhiteLogo());
      blackShip.setWheel(new WhiteWheel());
      return blackShip;
      
      <Client 클래스, 변경 후>
      Ship blackShip = new BlackShipFactory("Black 부품을 써라").createShip();
      
      <BlackShipFactory 클래스의 createShip(), 변경 후>
      BlackShip blackShip = new BlackShip();
      blackShip.setAnchor(new BlackAnchor());
      blackShip.setMotor(new BlackMotor());
      blackShip.setDeck(new BlackDeck());
      blackShip.setLogo(new BlackLogo());
      blackShip.setWheel(new BlackWheel());
      return blackShip;
      
Client 클래스가 1줄 변화함에 따라서 그것에 의존하는 Factory 클래스에서   
5줄이 변화한 것을 확인할 수 있다. (결합도가 높다.)  
반면, 추상 팩토리를 적용한 설계를 보자.  

      <Client 클래스, 변경 전>
      Ship blackShip = new BlackShipFactory("White 부품을 써라").createShip();
      
      <BlackShipFactory 클래스의 createShip(), 변경 전>
      setShipPartFactory(new WhiteShipPartFactory());
      BlackShip blackShip = new BlackShip();
      blackShip.setAnchor(this.shipPartFactory.createAnchor());
      blackShip.setMotor(this.shipPartFactory.createMotor());
      blackShip.setDeck(this.shipPartFactory.createDeck());
      blackShip.setLogo(this.shipPartFactory.createLogo());
      blackShip.setWheel(this.shipPartFactory.createWheel());
      return blackShip;
      
      <Client 클래스, 변경 후>
      Ship blackShip = new BlackShipFactory("Black 부품을 써라").createShip();
      
      <BlackShipFactory 클래스의 createShip(), 변경 후>
      [BlackShipPartFactory 및 BlackAnchor, BlackMotor... 를 생성한다.]
      setShipPartFactory(new BlackShipPartFactory());
      BlackShip blackShip = new BlackShip();
      blackShip.setAnchor(this.shipPartFactory.createAnchor());
      blackShip.setMotor(this.shipPartFactory.createMotor());
      blackShip.setDeck(this.shipPartFactory.createDeck());
      blackShip.setLogo(this.shipPartFactory.createLogo());
      blackShip.setWheel(this.shipPartFactory.createWheel());
      return blackShip;
      
Client 클래스가 1줄 변화함에 따라서, 클래스 6개를 추가하고  
그것에 의존하는 Factory는 단 1줄이 변화하였다. (낮은 결합도)  
