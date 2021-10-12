## Try-with-resources

#### 나의 사례
자바를 사용하며 하기 쉬운 실수는 외부자원을 사용하고 해제를 하지않는것이다.   
나 또한, 프로젝트를 진행하고 실 배포를 할때, 약 7일~30일주기로 불 규칙적 이게 서버가 다운 되는 문제가 발생했었다. BufferedReader와, HttpUrlConnection을 해제 해주지 않은것이 원인 이였다.   
이런 사소하지만, 서버 다운이라는 치명적인 결과를 초래할 수 있는 자원 해제 실수를 try-with-resource를 사용하여 더 아름답게 해결 할 수 있다.   
   
### try-with-resources
try-with-resources는 자바7에 추가된 API로, 기존 try문에 AutoCloseable을 구현한 객체를 선언 할 수 있는 () 가 추가된 형태이다.

```JAVA
try(BufferedReader br = new BufferedReader(new InputStreamReader(System.in))){
	/*
	code
	*/
}catch(IOException IOE){}
```

위 코드를 보면, BufferedReader를 해제하는 코드가 없음에도 try문을 벗어나는 순간 BufferedReader에 할당된 메모리가 해제된다.     

### AutoCloseable Interface

try-with-resources의 자동 메모리해제 대상이 되기 위해선, AutoCloseable 인터페이스를 구현해야한다.     
AutoCloseable인터페이스는 아래와 같다.

```Java
public interface AutoCloseable{
	public void close() throws Exception
}
```
위 코드를 보면, AutoCloseable은 단 하나의 추상 메서드를 정의한 함수형 인터페이스이다.   
즉, AutoCloseable는 다음처럼 람다식으로 간단하게 활용할 수도 있다.     
HttpUrlConnection은 AutoCloseable을 구현하지 않은 클래스로, 자동으로 해제되기 위해선, 아래와 같은 방법으로 사용해야 한다.
```JAVA
try(AutoCloseable autoCloseable = () -> httpUrlConnection.close()){
	/*
	code
	*/
}catch(Exception E){}
```

### 주의할 점
다음과 같은 코드를 한번쯤은 봤을것 이다.

```JAVA
try(BufferedReader br = new BufferedReader(new InputStreamReader(System.in))){
	/*
	code
	*/
}catch(IOException IOE){}
```
위 코드에서는 try문을 빠져나갈시 BufferedReader와 InputStreamReader모두 메모리가 해제된다. 하지만, 이는 try-with-resources의 기능이 아님에 유의하자.     
   
   
try-with-resources는 정의된 BufferedReader에 대한 메모리 해제만 주관하며, 이 후의 InputStreamReader에 대한 메모리 해제는 자바의 Stream구현이 진행한다.   
만일, 위 메모리 해제 과정을 try-with-resources주관으로 변경하고 싶다면, 아래와 같이 코드를 변경해야 한다.
```JAVA
try(InputStreamReader isr = new InputStreamReader(System.in);
	BufferedReader br = new BufferedReader(isr)){
	/*
	code
	*/
}catch(IOException IOE){}
```
