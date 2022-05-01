#  220501_Practice :pencil:

## 객체 간의 협력(Cooperation) :open_hands:

1. **구현 내용** :statue_of_liberty:
	- 각각 Student, Bus, Subway, Taxi에 클래스 생성
	-  Student클래스에는 운송수단의 클래스를 타서 자신의 가진 돈을 지불하는 기능을 구현
	- 각 운송 수단에 해당하는 클래스인 Bus, Subway, Taxi의 기본 정보를 가지고 있는 멤버필드생성.
	- 수입과 승객의 수를 증가하는 take라는 메서드를 생성
	- 각 운송수단이 승객을 태우고 나서 정보를 출력하는 메서드인   showInfo() 메서드 생성
	- 마지막으로 각 클래스의 인스턴스를 생성하고 정보값을 출력

2. **구현 코드** :factory:
```java
//Student.java
public class Student {
	//Student의 멤버 필드 선언
	public String studentName;
	public int grade;
	public int money;

	// 학생 이름과 가진돈을 매개변수로 받는 생성자
	public Student(String name, int money) {
		this.studentName = name;
		this.money = money;
	}

	// takeBus메서드 생성 학생이 버스를 타면 1000원을 지불하는 기능을 구현
	public void takeBus(Bus bus) {
		bus.take(1000);
		this.money -= 1000; // 가진 돈에서 1000원만큼 줄어듬
	}

	// takeSubway메서드는 학생이 지하철을 타면 1500원을 지불하는 기능을 구현한 메서드
	public void takeSubway(Subway subway) {
		subway.take(1500);
		this.money -= 1500;
	}

	// takeTaxi메서드는 학생이 택시를 타면 10000원을 지불하는 기능 메서드
	public void takeTaxi(Taxi taxi) {
		taxi.take(10000);
		this.money -= 10000;
	}

	// 학생의 남은 돈 정보를 출력하는 메서드 생성
	public void showInfo() {
		System.out.println(studentName + "학생의 남은 돈은 " + money + "입니다.");
	}
}
```
```java
//Bus.java
//버스클래스 생성
public class Bus {
	int busNumber; // 버스번호
	int passengerCount; // 승객수
	int money; // 버스 수입
	// 버스 번호를 매개변수로 받는 생성자

	public Bus(int busNumber) {
		this.busNumber = busNumber;
	}

	// 승객이 버스에 탄 경우에 구현한 메서드
	public void take(int money) {
		this.money = money; // 버스 수입 증가
		passengerCount++;// 승객수 증가
	}

	// 지금의 버스정보를 출려하는 showInfo()메서드 생성
	public void showInfo() {
		System.out.println("버스" + busNumber + "번의 승객은 " + passengerCount + "명 이고, 수입은 " + money + "입니다.");
	}
}
```
```java
//Subway.java
public class Subway {
	String lineNumber; // 지하철 노선
	int passengerCount; // 승객수
	int money; // 수익액 (지하철)

	// 지하철 노선 번호를 매개변수로 받는 생성자 함수
	public Subway(String lineNumber) {
		this.lineNumber = lineNumber;
	}

	// 승객이 지하철에 탄경우를 구현한 메서드
	public void take(int money) {
		this.money += money; // 수입이 증가
		passengerCount++;// 승객 수가 증가
	}

	// 지하철의 정보를 출력하는 메서드 만듬
	public void showInfo() {
		System.out.println(lineNumber + "노선의 승객은" + passengerCount + "명이고, 수입은 " + money + "입니다.");
	}
}
```
```java
//Taxi.java
public class Taxi {
	String taxiNumber;// 택시 차번호
	String passengerName; // 택시 승객 이름
	int passengerCount; // 택시 승객수
	int money; // 택시 수입
	// 택시 차 번호와 택시 승객이름을 매개변수로 받는 생성자

	public Taxi(String taxiNumber, String passengerName) {
		this.passengerName = passengerName;
		this.taxiNumber = taxiNumber;
	}

	// 승객이 택시를 탄 경우 택시의 수입이 증가, 승객수도 증가
	public void take(int money) {
		this.money = money;
		passengerCount++;
	}

	// 택시의 정보를 출력
	public void showInfo() {
		System.out.println("택시의 차 번호는 " + taxiNumber + "입니다. 승객의 이름은 " + passengerName + "이고, 승객의 수는" + passengerCount
				+ "명입니다. 수입은" + money + "입니다.");
	}
}
```
```java
public class TakeTrans {

	public static void main(String[] args) {
		// Student 클래스 인스턴스 생성, 생성자 함수에서 인자값으로 studentName, money 값설정
		Student studentGlory = new Student("김영광", 5000);
		Student studentJack = new Student("잭잭", 10000);
		Student studentEdward = new Student("Edward", 40000);

		// bus에다가 버스 번호부여, 버스 정보 출력 , 학생이 타서 bus에다가 승객수,수입 카운팅되고 학생의 남은돈 출력
		Bus bus333 = new Bus(333);
		studentGlory.takeBus(bus333);
		// Student클래스에 있는 takeBus메서드에서 매개변수를 타입이 Bus인 참조변수 넣음. 그 참조변수의 인스턴스에는 333이라는
		// busNumber 인자값이 들어가 있음.
		studentGlory.showInfo();// 현재 남은돈 정보 출력 메서드 호출
		bus333.showInfo();// 버스 정보 출력 메서드 호출

		// subway에다가 노선 번호부여, 지하철 정보 출력(노선, 승객수, 수입), 학생이 타서 subway에다가 승객수,수입 카운팅되고 학생의
		// 남은돈 출력
		Subway line2 = new Subway("2호선");
		studentJack.takeSubway(line2);
		studentJack.showInfo();
		line2.showInfo();

		// taxi타입의 참조변수에다가 차번호, 승객이름 부여, 택시 정보 출력, 학생 남은돈 출력
		Taxi taxi = new Taxi("184바 2222", "애드워드");
		studentEdward.takeTaxi(taxi);
		studentEdward.showInfo();
		taxi.showInfo();
	}
```
**출력값**
>김영광학생의 남은 돈은 4000입니다.
버스333번의 승객은 1명 이고, 수입은 1000입니다.
잭잭학생의 남은 돈은 8500입니다.
2호선노선의 승객은1명이고, 수입은 1500입니다.
Edward학생의 남은 돈은 30000입니다.
택시의 차 번호는 184바 2222입니다. 승객의 이름은 애드워드이고, 승객의 수는1명입니다. 수입은10000입니다.

3. **코드 피드백**
student, bus, subway그리고 taxi까지 객체들 간의 협력을 하여 학생은 버스, 지하철,택시와 같이 운송수단을 이용할 수 있게 되었다.
 이용할 때에 학생이 가지고 있는 돈에서 지불 금액 만큼 차감이 되는 그리고 각각의 운송수단은 수입이 발생하고 승객수가 증가되는 기능을 만들었다.
 이렇게 객체 사이의 협력을 코드로 구현하고 출력값을 통해 어디서 값을 받아오는지 흐름을 이해할 수 있었다.

