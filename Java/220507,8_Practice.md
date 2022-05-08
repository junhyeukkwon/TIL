# 220507~08 Practice

## Collection
* 저번 review에서는 ArrayList 클래스만 예시를 들었다. 여러 Collection 클래스를 예시 코드와 함께 각 해당 클래스의 개념과 활용을 이해해보려 했다.
---

### ArrayList, Vector, LinkedList 클래스
#### ArrayList, Vector 

* 두 클랫는 배열을 구현한 클래스이다.
* 가장 큰 차이점은 동기화 지원 여부이다.
* 두 개 이상의 스레드가 동시에 Vector를 사용할 때 오류가 나지 않도록 실행 순서를 보장하는 것이다.
	* 스레드란, 작업 단위이다. 프로그램이 메모리에서 수행되려면 스레드 작업이 생성 되어야 한다.
	* 수행 스레드의 개수에 따라 단일, 멀티 스레드로 나뉘는데, 멀티 스레드 같은 경우에는 동시에 실행되면 메모리 공간에 접근할 수 없음.
	* 그때 메모리에 동기에 접근하지 못하도록 순서를 맞추는 것이 **동기화** 이다.
#### Vector 클래스를 무조건 쓰는것이 좋은가?
* 사실 멀티스레드 환경이 지속되지 않은 경우에는 ArrayList를 사용하는 것이 좋다고한다.
* 동기화를 구현하기 위해서는 동시에 작업이 이루어지는 자원에 대해 잠금을 수행함.
* 메서드를 호출할 때 배열 객체에 잠금하고, 메서드수행이 끝나면 잠금이 해제가 된다. 
* ArrayList보다는 수행 속도가 느리기 때문에, 만약에 동기화가 필요하다면 **Collection.synchronizedList(new ArrayList<String>())** 생성 코드를 쓰면됨.

#### LinkedList 
* 배열은 생성 시, 정적 크기로 선언하고, 물리적 순서와 논리적 순서가 동일함.
* 처음 선언한 배열 크기 이상으로 요소가 추가되는 경우에는 크기가 큰 배열을 생성, 요소를 복사 해야함. 
* 이러한 부분을 개선한 것이 LinkedList 이다.
* 각 요소는 다음 요소를 가르키는 주소 값을 가짐.
* 물리적인 메모리는 떨어져 있어도 논리적으로는 앞뒤 순서가 있다.
* 중간에 자료를 넣고 제거하는 데 시간이 적게 걸림.
* 만약에 요소를 배열 위치 중 원하는 곳으로 위치하고 싶다면 기존에 가르키고 있는 주소값만 변경하면 된다.
* 또한 마찬가지로 요소를 제거할 때에도 요소를 가르키고 있는 주소값을 변경하면 되고, 그때에 제거된 요소의 메모리는 자바의 가비지컬렉터에 의해수거됨.

#### LinkedList를 배열대신 사용하는 것이 좋을까?
* 결론적으로는 자료의 변동(삭제, 삽입)이 많은 경우에 사용하기가 좋다.  자료의 변동이 적은 경우에는 배열을 사용하는 것이 좋다.
* 사실 여러면에서 LinkedList 가 동적으로 요소의 메모리를 생성하기 때문에 배열처럼 용량을 늘리고 요소 값을 복사하는 번거러움이 없다.
* 하지만 배열은 물리적으로 연결된 자료구조이기 때문에 어떤 요소의 위치를 찾을때에 바로 계산이 용이하다.
* 그리고 구현이 쉽기때문에 아무래도 배열을 사용하는감이 없지않아 있는것 같다.

**LinkedListTest**
```java
public class LinkedListTest {
	public static void main(String[] args) {
		LinkedList<String> myList  = new LinkedList<String>();
		
		//배열 요소 추가
		myList.add("A");
		myList.add("B");
		myList.add("C");
		myList.add("D");
		
		System.out.println(myList);
		//인덱스 위치 [1] 에 E 추가
		myList.add(1, "E");
		System.out.println(myList);
		
		//요소의 맨 앞에 0 추가
		myList.addFirst("0");
		System.out.println(myList);
		
		//연결리스트의 맨 뒤 요소 삭제 후 해당 요소를 출력
		System.out.println(myList.removeLast());
		System.out.println(
myList);
	}
}
```
실행 결과
>[A, B, C, D]   
>[A, E, B, C, D]   
>[0, A, E, B, C, D]   
>D   
>[0, A, E, B, C]   

### ArrayList를 활용한 Stack, Queue 구현

#### Stack 
* 나중에 추가된 데이터를 먼저 꺼내는 방식인 (Last In First Out; LIFO)
#### Queue
* 단어의 의미로 줄을 선다는 의미로 먼저 추가된 데이터부터 꺼내서 사용하는 방식(First In First Out; FIFO)

```java
//ArrayList로 스택 구현하기
class Mystack {
	//ArrayList에서 객체 생성 
	private ArrayList<String> arraystack = new ArrayList<>();
	
	//스택의 맨 뒤에서 요소를 추가
	public void push(String data) {
		arraystack.add(data);
	}
	
	public String pop() {
		//ArrayList에서 저장된 유효한 자료의 개수
		int len = arraystack.size();
		if (len == 0) {
			System.out.println("스택이 비었습니다.");
			return null;
		}
		//맨 뒤에 있는 자료 반환하고 배열에서 제거 
		return arraystack.remove(len-1);
	}
}

public class StackTest {
	public static void main(String[] args) {
		Mystack mystack = new Mystack();
		//A->B->C 순으로 배열에 담겼다.
		mystack.push("A");
		mystack.push("B");
		mystack.push("C");
		
		//꺼낼때 pop()매서드를 써서 Last in First out 으로 C->B->A 순으로 배열에서 빠진다.
		System.out.println(mystack.pop());
		System.out.println(mystack.pop());
		System.out.p
rintln(mystack.pop());
	}
}

//ArrayList로 Queue 구현하기
class MyQueue {
	private ArrayList<String> arrayQueue = new ArrayList<String>();
	
	//큐의 맨뒤에 추가
	public void enQueue(String data) {
		arrayQueue.add(data);
	}
	
	//큐의 맨 앞에서 꺼냄
	public String deQueue() {
		int len = arrayQueue.size();
		if (len == 0) {
			System.out.println("큐가 비었습니다.");
			return null;
		}
		//맨 앞의 자료 반환하고, 배열에서 제거
		return arrayQueue.remove(0);
	}
}


public class QueueTest {
	public static void main(String[] args) {
		MyQueue myQueue = new MyQueue();
		myQueue.enQueue("A");
		myQueue.enQueue("B");
		myQueue.enQueue("C");
		
		System.out.println(myQueue.deQueue());
		System.out.println(myQueue.deQueue());
		System.out.println(mQueue.deQueue());
	}
```
실행결과
> //StackTest
> C   
>B   
>A   
>//QueueTest   
>A   
>B   
>C   
---

### Set 인터페이스
* 순서와 상관 없이 중복을 허용하지 않은 경우에 Set 인터페이스를 구현한 클래스를 사용한다.
* 주민등록번호, 사번, 주문번호 등 중복이 되면 안되는 데이터를 다룰 때에 사용하는 좋다.

#### HashSet 클래스
* HashSet 클래스를 활용하여 회원 관리프로그램을 구현했다.
* 여기서 사용되는 메서드인 hashCode(), equals() 메서드를 오버라이딩하는 것을 왜하는지에 대해 이해하기가 어려웠다. 그 부분에 대해 찾아보았다.
 ![enter image description here](https://tecoble.techcourse.co.kr/static/c248e8d79140c18ed9895d1c95dd7ad0/54e75/2020-07-29-equals-and-hashcode.png)

* 결국에는 중복이 허용되지 않은 클래스이기 때문에 이러한 Object 클래스에서 메서드를 재정의를 통해 불필요한 잡음을 제거하기 위함인 것 같다.

```java
public class MemberHashSet {
	//HashSet 선언
	private HashSet<Member> hashSet;
	
	public MemberHashSet() {
		//Member형으로 선언한 HashSet 생성
		hashSet = new HashSet<Member>();
	}
	
	//HashSet 에 회원을 맨 뒤에 추가하는 메서드
	public void addMember(Member member) {
		hashSet.add(member);
	}
	
	//Collection에 있는 iterator() 메서드를 이용하여 set인터페이스는 순서가 없기 때문에 iterator를 활용하여 참조한다.
	public boolean removeMember(int memberId) {
		Iterator<Member> ir = hashSet.iterator();
		while (ir.hasNext()) { // 요소가 있는 동안 
			Member member = ir.next(); // 다음 회원을 반환받음.
			int tempId = member.getMemberId();
			if (tempId == memberId) {// 회원 아이디가 매개변수와 일치하면
				hashSet.remove(member);// 해당 회원을 삭제해라
				return true; // 잘 제거를 하면 true을 반환
			}
		}
		//반복문이 끝날 때까지 해당 아이디를 찾지 못한 경우
		System.out.println(memberId + "가 존재하지 않습니다.");
		return false; // 그러지 못한 경우 false
	}
	
	//전체 회원을 출력하는 메서드
	public void showAllMember() {
		for (Member member : hashSet) {
			System.out.println(member);
		}

		System.out.println( );
	}
}

//Member.java
public class Member {
	//멤버필드 선언
	private int memberId;
	private String memberName;
	
	//생성자함수 
	public Member(int memberId, String memberName) {
		this.memberId = memberId;
		this.memberName = memberName;
	}
	
	//getter & setter
	public int getMemberId() {
		return memberId;
	}
	public void setMemberId(int memberId) {
		this.memberId = memberId;
	}
	public String getMemberName() {
		return memberName;
	}
	public void setMemberName(String memberName) {
		this.memberName = memberName;
	}

	//회원정보 출력를 위한 toString 
	@Override
	public String toString() {
		return memberName + "회원님의 아이디는  " + memberId + "입니다.";
	}
	
	// 두 객체가 같은 객체인지 확인하는 method 
	@Override
	public int hashCode() {

		return memberId;
	}
	// 두 객체의 내용이 같은지 확인하는 method => 이걸 하는 이유는 memberId가 동일하면 같은 memberId라고 판단하겠다는 의미이다.
	@Override
	public boolean equals(Object obj) {
		if (obj instanceof Member) { 
			Member member  = (Member)obj;
			
			if (this.memberId == member.memberId) {
				return true;
			}
			else
 return false;
		}
		return false;
	}		
}

//MemberHashSetTest.java
public class MemberHashSetTest {

	public static void main(String[] args) {
		MemberHashSet memberHashSet = new MemberHashSet();

		Member memberLee = new Member(101, "이순신");
		Member memberKim = new Member(102, "김유신");
		Member memberShin = new Member(103, "신사임당");

		memberHashSet.addMember(memberLee);
		memberHashSet.addMember(memberKim);
		memberHashSet.addMember(memberShin);

		memberHashSet.showAllMember();
		Member memberLim = new Member(101, "임몽룡");
		// hashCode,equals() 메서드를 통해 MemberId가 중복되어 추가되는 것을 방지
		// 결국 memberLim은 데이터가 추가가 되지 않는다.

		memberHashSet.addMember(memberLim);

		memberHashSet.showAllMember();
	}
}
```
출력결과
>이순신회원님의 아이디는  101입니다.   
>김유신회원님의 아이디는  102입니다.   
>신사임당회원님의 아이디는  103입니다.   
>   
>이순신회원님의 아이디는  101입니다.   
>김유신회원님의 아이디는  102입니다.   
>신사임당회원님의 아이디는  103입니다.  

#### TreeSet 클래스
* 자바의 Collection 인터페이스나 Map인터페이스를 구현한 클래스 중 Tree로 시작하는 클래스는 데이터를 추가한 후 결과를 출력하면 결과 값이 정렬.
* TreeSet 자료의 중복을 허용하지 않으면서 출력 결과 값을 정렬하는 클래스이다.

```java
public class TreeSetTest {
	public static void main(String[] args) {
		TreeSet<String> treeSet = new TreeSet<String>();
		treeSet.add("홍길동");
		treeSet.add("강감찬");
		treeSet.add("권준혁");
		
		for (String string : treeSet) {
			System.out.println(string); //정렬이 ㄱㄴㄷ순으로 정렬이됨. String 클래스 안에 정렬 방식이 이미 구현되어 있기 때문이다.
			
			/*Binary Search Tree: 이진 검색 트리
			 * 트리 자료 구조에서 각 자료가 들어가는 공간을 노드라고 한다.
			 * 그리고 위아래로 연결된 노드의 관계를 '부모-자식 노드'라고한다.
			 * 이진 검색 트리는 노드에 저장되는 자료의 중복을 허용하지 않고, 부모가 가지는 자식의 노드의 수가 2개 이하이다.
			 * 또한 왼쪽에 위치하는 자식 노드는 부모 노드보다 항상 작은 값을 가진다.
			 * 반대로 오른쪽에 놓인 자식 노드는 부모 노드보다 항상 큰 값을 가진다.
			 * 따라서 어떤 특정 값을 찾으려 할 때 한 노드 와 비교해 비교한 노드보다 작은 값이면 왼쪽 자식 노드 방향으로, 그렇지 않으면 오른쪽 자식 노드 방향으로 이동.
			 * 따라서 비교 범위가 평균 1/2만큼씩 줄어들어 효과적으로 자료를 검색할 수 있다.
			 * 왼쪽->부모->오른쪽순으로 순회하면서 오름차순이 된다.
			 * 그 반대로 오른쪽->부모->왼쪽으로 순회하면 내림차순으로 된다.

			 */
		}
	}
}
```
출력결과
>강감찬
>권준혁
>홍길동   

#### Comparable, Comparator 인터페이스
*  HashSet과 마찬가지로 회원을 관리하는 프로그램을 만들었을 때에 문제없이 진행이 되려면 TreeSet클래스에 있는 이진분류트리 처럼 무언가를 나누는 기준이 들어가야한다.
* 그럴 때에 필요한 인터페이스인 Comparable인터페이스를 사용하는 것이다.
* Member클래스에서 Comparable 인터페이스에 있는 추상메서드 compareTo() 메서드를 구현하여 판단 기준을 만들어 주는 것이다.
* Comparator 인터페이스에는 compare() 메서드를 구현해야한다.
* 동시에는 잘 쓰질 않지만, 이미 정렬이 compareTo()로 정의가 되어있을때, 다른 정렬을 하고 싶을 때, compare() 매서드로 정의해주면 된다.
```java
//Member.java
	//TreeSet클래스에서 활용될 Comparable 인터페이스에서 compareTo()메서드를 재정의(구현)
	@Override
	public int compareTo(Member member) {
		//비교 대상은 this의 회원 아이디, 즉 새로 추가한 회원의 이름와 compareTo() 메서드의 매개변수로 전달된 회원이름를 비교함.
		//이름을 순으로 정렬하는 compareTo()메서드 구현
		return this.memberName.compareTo(member.memberName);
	}
	// mem1은 나, mem2는 넘어오는 애 - Comparator를 구현 할때는 TreeSet 생성자에 Comparator를 구현한 객체를 매개변수로 전달해야함.(new member()) 또한 default 생성자 함수를 만들어야함.
	//이미 comparable 구현된 부분에서 다른 정렬을 하고 싶다하면 comparator를 쓴다.
	@Override
	public int compare(Member mem1, Member mem2) {
		return mem1.memberId - mem2.memberId;
	}	

//MemberTreeSet.java
public class MemberTreeSet {
	private TreeSet<Member> treeSet;
	
	public MemberTreeSet() {
		treeSet = new TreeSet<Member>(); // 인스턴스생성
	}
	
	//TreeSet에 회원을 추가하는 메서드
	public void addMember(Member member) {
		treeSet.add(member);
	}
	
	//TreeSet에 회원을 제거하는 메서드
	public boolean removeMember(int memberId) {
		Iterator<Member> ir = treeSet.iterator();
		
		while(ir.hasNext()) {
			Member member = ir.next();
			int tempId = member.getMemberId();
			if (tempId == memberId) {
				treeSet.remove(member);
				return true;
			}
		}
		System.out.println(memberId +"가 존재하지 않습니다.");
		return false;
	}
	
	//전체 회원을 출력하는 메서드 
	public void showAllMember() {
		for (Member member : treeSet) {
			System.out.println(member);
		}
		ystem.out.println( );
	}
}

//MemberTreeSetTest.java
public class MemberTreeSetTest {
	public static void main(String[] args) {
		//TreeSet을 사용할꺼라는 선언
		MemberTreeSet memberTreeSet = new MemberTreeSet();
		
		//Member 인스턴스 생성
		Member memberPark = new Member(1003, "박인용");
		Member memberKim = new Member(1001, "김서인");
		Member memberKwon = new Member(1002, "권인율");
		
		//memberTreeSet에 인스턴스 추가 ->추가하는 순서는 무작위로 넣음
		memberTreeSet.addMember(memberKim);
		memberTreeSet.addMember(memberPark);
		memberTreeSet.addMember(memberKwon);
		//정보값을 출력
		memberTreeSet.showAllMember();
		
		//member 인스턴스 생성 하는데 MemberId를 중복해서 회원 추가
		Member memberLee = new Member(1003, "이준수");
		
		//정보값을 출력
		memberTreeSet.showAllMember();
		//오류발생
		//Exception in thread "main" java.lang.ClassCastException: class collection.Member cannot be cast to class java.lang.Comparable 
		//발생 이유: Member 클래스가 Comparable 인터페이스를 구현하지 않았다는 뜻
		//Member클래스를 TreeSet의 요소로 추가할 때 어떤 기준으로 노드를 비교하여 트리를 형성해야 하는지 구현을 하지 않았다는 뜻이다.
		//Member클래스에 Comparable interface - compareTo() 메서드 구현하여 문제 해결
	
```
출력결과

>compareTo()만 한결과
>권인율회원님의 아이디는  1002입니다.   
김서인회원님의 아이디는  1001입니다.   
박인용회원님의 아이디는  1003입니다.    
>compare() 정렬한 결과
>김서인회원님의 아이디는  1001입니다.   
권인율회원님의 아이디는  1002입니다.   
박인용회원님의 아이디는  1003입니다.    




