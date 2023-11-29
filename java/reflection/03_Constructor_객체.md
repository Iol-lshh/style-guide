# Constructor<?>
- `java.lang.reflect.Constructor<?>`
- 생성자에 대한 모든 정보
    - 매개변수의 개수에 한정되지 않는다.
    - 매개변수 타입에 한정되지 않는다.
        - 클래스는 생성자 오버로딩이 가능하기 때문에

## 메서드
### Class.getDeclaredConstructors()
- 클래스의 모든 생성자를 반환
    - 접근 제한자를 무시하고 모두 반환

### Class.getConstructors()
- public 생성자만 반환

### 매개변수 타입을 알 때
- 매개변수 타입 목록을 전달하여, 특정 클래스를 반환받을 수 있다.
    - 없다면, NoSuchMethodException
- `Class.getConstructor(Class<?> ... parameterTypes)`
- `Class.getDeclaredConstructor(Class<?> ... parameterTypes)`

```java
Constructor<?> [] constructors = Person.class.getConstructors();
Constructor<?> defaultConstructor = Person.class.getDeclaredConstructor();
```

## 객체 생성
- `Constructor.newInstance(Object ... arguments)`
    - 생성자에 선언된 순서대로 생성자 매개변수를 가변인수로 받는 메서드
    - 접근 가능한 생성자라면, 객체를 생성해준다.

```java
public static void main(String [] args) throws IllegalAccessException, InstantiationException, InvocationTargetException {

    Address address = createInstanceWithArguments(Address.class, "First Street", 10);

    Person person = createInstanceWithArguments(Person.class,  address, "John", 20);
    System.out.println(person);
}

public static <T> T createInstanceWithArguments(Class<T> clazz, Object ... args) throws IllegalAccessException, InvocationTargetException, InstantiationException {

    for (Constructor<?> constructor : clazz.getDeclaredConstructors()) {
        if(constructor.getParameterTypes().length == args.length) {

            return (T) constructor.newInstance(args);
        }
    }
    System.out.println("An appropriate constructor was not found");
    return null;
}
```

### 접근 제한 Constructor 객체 접근
- `constructor.setAccessible(true)`
    - 특별한 경우에만 사용한다.
```java
Constructor<ServerConfiguration> constructor = ServerConfiguration.class.getDeclaredConstructor(int.class, String.class);

constructor.setAccessible(true);
constructor.newInstance(8080, "Good Day!");
```
