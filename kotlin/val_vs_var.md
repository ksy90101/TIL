# Kotlin의 val vs var

## 🧐 **Declaring properties**

속성을 정의하는 방법으로 총 2개의 키워드가 있습니다.

바로 `var` 와 `val` 인데요.

두개의 키워드를 알아보겠습니다.

## 💎 val

```kotlin
// kotlin
class Person() {
	val name = "marco"
}

// java
public final class Person {
	@NotNull
	private final String name = "marco";

	@NotNull
	public final String getName() {
		return this.name;
	}
}
```

위 코드에서 kotlin과 java에서의 값을 보면 val은 불변(immutable)이라는 것을 볼수 있으며, getter만 생성되는 것을 볼수 있습니다다.

## 🍰 var

```kotlin
// kotlin
class Person() {
	var name = "marco"
}

// java
public final class Person {
	private String name = "marco";

	public final String getName() {
		return this.name;
	}

	public final String setName(String name) {
		this.name = name;
	}
}
```

가변(mutable)이기 때문에 getter, setter가 모두 생성됩니다.

## 📝 공통점

1. is-가 붙으면 is를 제거한 이름으로 getter, setter가 만들어 집니다.

  ```kotlin
  // kotlin
  class Person() {
    val name = "marco"
    val isStudent = false
  }

  // java
  public final class Person {
    @NotNull
    private final String name = "marco";

    private boolean isStudent;

    @NotNull
    public final String getName() {
      return this.name;
    }

    public final boolean isStudent() {
        return this.isStudent;
    }

    public final void setStudent(boolean isStudnet) {
        this.isStudent = isStudent;
    }
  }
  ```

2. private 변수는 getter / setter가 생성되지 않습니다.

3. 커스텀 getter, setter를 만들수 있습니다.
    
    ```kotlin
    // kotlin
    class Person() {
    	var name = "marco"
    		get() = this.name + " Kim"
    		set(value) {
    			if(value.length >= 0) this.name = value 
    			} 
    }
    
    // java
    public final class Person {
    	private String name = "marco";
    
    	public final String getName() {
    		return this.name + " Kim";
    	}
    
    	public final String setName(String name) {
    		if(value.length >= 0 {
    			this.name = name;
    		}
    	}
    }
    ```
    

4. 컴파일러가 변수 타입을 추론할 수 있습니다.
    
    가변인 경우 주의점은 처음 초기화를 한 뒤에 다른 타입의 값을 넣을 순 없습니다. 예를들어 String 타입을 넣었다가, int 타입을 넣으면 Type mismatch Error가 발생합니다.
