# [Immutability in Java(자바의 불변성)](https://medium.com/swlh/immutability-in-java-26e07406a0eb)

불변 클래스란 한번 초기화 되면 상태를 변경할 수 없는 클래스 입니다.

즉, 불변 객체의 인스턴스를 참조하고 있다면 해당 인스턴스에 대한 변경이 일어나면 새로운 객체를 생성해 새로운 메모리 공간으로 가리키도록 참조를 변경합니다.

불변 클래스는 아래와 같은 규칙을 따라야 합니다.

> 클래스는 확장이 불가능 하도록 `final` 키워드를 붙여야 합니다. 자식 클래스를 인스턴스화를 할 수 있으므로 원하지 않은 가변적인 구현을 할 수도 있기 때문입니다.
모든 속성 필드는 캡슐화 원칙에 따라 `private` 이어야 합니다.
불변 클래스 인스턴스를 올바르게 생성하기 위해 클래스 상태를 초기에 세탕할 수 있도록 매개변수가 있는 생성자가 있어야 합니다.
클래스 속성 변경 가능성을 막기 위해 초기화 이후 `setter` 메서드를 포함하면 안됩니다.
컬렉션 필드의 전체 복사본을 만들어 컬렉션 유형 필드의 상태 변경을 할 수 없도록 해야합니다.

## **Immutability in Action**

먼저 변경 할 수 없을 것 처럼 보이는 클래스를 만들어 보겠습니다.

```java
import java.util.Map;

public final class MutableClass {
  private String field;
  private Map<String, String> fieldMap;

	public MutableClass(String field, Map<String, String> fieldMap) {
	  this.field = field;
	  this.fieldMap = fieldMap;
	}
	
	public String getField() {
	  return field;
	}
	
	public Map<String, String> getFieldMap() {
	  return fieldMap;
	}
}
```

클래스를 구현했으니, 실제로 실행해보겠습니다.

```java
import java.util.HashMap;
import java.util.Map;

public class App {

	public static void main(String[] args) {
	  Map<String, String> map = new HashMap<>();
	  map.put("key", "value");
	
	  // Initializing our new "immutable" class
	  MutableClass mutable = new MutableClass("this is not immutable", map);  //we can easily add new elements to the map == changing the state
	  mutable.getFieldMap().put("unwanted key", "another value");
	  mutable.getFieldMap().keySet().forEach(e ->  System.out.println(e));}
	}

//console output
unwanted key
key
```

우리는 분명 MutableClass가 가지고 있는 컬렉션 필드에 요소를 추가할 의도가 없었지만, 값이 들어간걸 확인할 수 있습니다. 이 자체가 바로 MutableClass의 상태가 변경된 것이므로 불변성의 특징이 전혀 아닙니다.

```java
import java.util.HashMap;
import java.util.Map;

public class AlmostMutableClass {
  private String field;
  private Map<String, String> fieldMap;

	public AlmostMutableClass(String field, Map<String, String> fieldMap) {
	  this.field = field;
	  this.fieldMap = fieldMap;
	}
	
	public String getField() {
	  return field;
	}
	
	public Map<String, String> getFieldMap() {
	  Map<String, String> deepCopy = new HashMap<String, String>();
	  for(String key : fieldMap.keySet()) {
	    deepCopy.put(key, fieldMap.get(key));
	  }
	  return deepCopy;
	}
}
```

위에서는 getter 메서드를 변경했는데, 컬렉션 필드를 Map getter를 호출하면 전체 복사본을 반환합니다. getter 메서드를 호출해 Map에 값을 추가해도 class 상태에 변화는 없고 사용되는 곳에서 복사된 Map에만 추가가 됩니다.

그러나 생성자 매개변수로 초기 Map에 접근할 수 있기 때문에 수정 할 수가 있습니다. 그래서 거의 가변 클래스의 상태를 변경 할 수 있습니다.

```java
import java.util.HashMap;
import java.util.Map;

public class App {

	public static void main(String[] args) {
	  Map<String, String> map = new HashMap<>();
	  map.put("good key", "value");
	
	  // initializing our new "immutable" class
	  AlmostMutableClass almostMutable = new AlmostMutableClass("this is not immutable", map);
	
	  //we cannot change the state
	  //if we try to add to the map we got from the object
	  System.out.println("Result after modifying the map after we get it from the object");
	  almostMutable.getFieldMap().put("bad key", "another value");
	  almostMutable.getFieldMap().keySet().forEach(e -> System.out.println(e));
	
	  System.out.println("Result of the object's map after modifying the initial map");
	  map.put("bad key", "another value");
	  almostMutable.getFieldMap().keySet().forEach(e -> System.out.println(e));
	
	}
}

//console output
Result after modifying the map after we get it from the object
good keyResult of the object's map after modifying the initial map
good key
bad key
```

하나 더 추가해야 하는걸 잊어버렸습니다. 즉, 생성자에서 값을 넣을때 getter 메서드에서 수행한 것과 동일한 작업을 수행해야 하므로 아래와 같은 생성자가 되어야 합니다.

```java
public AlmostMutableClass(String field, Map<String, String> fieldMap) {
  this.field = field;
  Map<String, String> deepCopy = new HashMap<String, String>();
  for(String key : fieldMap.keySet()) {
    deepCopy.put(key, fieldMap.get(key));
  }
  this.fieldMap = deepCopy;
}

//console output
Result after modifying the map after we get it from the object
good keyResult of the object's map after modifying the initial map
good key
```

그러나 불변 객체를 사용할 때 많은 이점을 주지만, 항상 이 코드는 모범 사례는 아닙니다.

일반적으로 시스템이 진행 중인 변경사항을 반영하기 위해 객체를 생성하고 객체의 상태도 변경해야 합니다.

간단히 말하면, 데이터를 수정해야 하고, 메모리에 큰 영향을 미치기 때문에 약간의 변경이 일어날 때마다 새로운 객체를 생성하는 것이 좋아보이지 않으며, 최적의 매개변수 내에서 애플리케이션을 수행해야 하므로 시스템의 자원을 주의 깊게 사용할 필요가 있습니다.

## **자바 String 불변성**

String 클래스는 Java언어에서 널리 사용되는 클래스이며, 문자열(sequential collection of characters)입니다.

이 클래스의 목적은 문자 시퀀스와 상호 작용하는데 다양한 메서드를 제공해 문자 시퀀스를 사용을 용이하게 하는 것입니다.

예를들어, String 클래스는 문자를 추출할 수 있거나, 시퀀스를 분할하거나, 시퀀스를 변경하거나 찾을수 있거나 등등 다양한 메서드를 제공합니다.

다른 Java의 래퍼 클래스(Integer, Boolean 등)와 같이 String class는 불변입니다. String이 불변이여서 장점인 부분은 아래와 같습니다.

> 문자열은 JVM에서 "String constant pool"에서 특별히 관리되고 있어 스레드에 안정적입니다. 즉, 두개의 같은 String 변수가 동일한 값을 가지면 동일한 메모리를 갖습니다.
String Class는 실수로 수정할 수 없으므로 컬렉션 기반의 키에 대한 휼륭한 후보입니다.
String Class의 HashCode 구현은 캐싱에 최적화 되어 있어 문자열을 사용하는 해시 기반 컬렉션의 성능을 향상시킵니다.
사용자 이름, 패스워드와 같이 민감한 문자열을 다른 메서드에 대한 참조로 전달해 실행해도 실행중에 실수로 변경할 수가 없습니다.
