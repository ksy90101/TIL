# FileReader가 File을 찾아올때 경로가 어떻게 될까?

> 최근 FileReader 클래스를 사용하면 경로가 어떻게 되는지에 대한 질문이 있어 직접 테스트를 해봤다.
과연 FileReader는 어디 경로를 기준으로 가져오는걸까?

```java
FileReader fr = new FileReader(filepath);
```

## FileRader 내부 살펴보기

- 위의 코드에서 FileRader에서 파일을 어떻게 찾아와서 읽는지 알아보려면 내부를 살펴봐야 할것 같습니다.
- 실제 우리가 부르는 생성자만 살펴보면 아래와 같습니다.

```java
/**
    * Creates a new {@code FileReader}, given the name of the file to read,
    * using the platform's
    * {@linkplain java.nio.charset.Charset#defaultCharset() default charset}.
    *
    * @param      fileName the name of the file to read
    * @throws     FileNotFoundException  if the named file does not exist,
    *             is a directory rather than a regular file,
    *             or for some other reason cannot be opened for
    *             reading.
    */
    public FileReader(String fileName) throws FileNotFoundException {
        super(new FileInputStream(fileName));
    }
```

- 위에 주석에서는 파일 이름을 제공해주면 파일을 읽어 FileReader 객체를 만들어 준다고 나와있습니다.
- 여기서 우리는 FileInputStream을 생성하는것을 볼수 있습니다.
- 그렇다면 이 객체의 내부를 봐야 하겠죠!?

## FileInputStream

```java
/**
     * Creates a {@code FileInputStream} by
     * opening a connection to an actual file,
     * the file named by the path name {@code name}
     * in the file system.  A new {@code FileDescriptor}
     * object is created to represent this file
     * connection.
     * <p>
     * First, if there is a security
     * manager, its {@code checkRead} method
     * is called with the {@code name} argument
     * as its argument.
     * <p>
     * If the named file does not exist, is a directory rather than a regular
     * file, or for some other reason cannot be opened for reading then a
     * {@code FileNotFoundException} is thrown.
     *
     * @param      name   the system-dependent file name.
     * @throws     FileNotFoundException  if the file does not exist,
     *             is a directory rather than a regular file,
     *             or for some other reason cannot be opened for
     *             reading.
     * @throws     SecurityException      if a security manager exists and its
     *             {@code checkRead} method denies read access
     *             to the file.
     * @see        java.lang.SecurityManager#checkRead(java.lang.String)
     */ㅇ
    public FileInputStream(String name) throws FileNotFoundException {
        this(name != null ? new File(name) : null);
    }
```

- 실제파일과 연결해서 열고 파일 이름을 제공받는다고 나와있습니다. 아울러 파일으로 찾아 존재하지 않는다면 에러를 발생시킨다는것까지 볼수 있습니다.
- 여기서 파일 이름이 null이 아니라면 File 객체를 생성하는 것을 확인 할수 있습니다.

## File 내부 살펴보기

```java
/**
     * Creates a new {@code File} instance by converting the given
     * pathname string into an abstract pathname.  If the given string is
     * the empty string, then the result is the empty abstract pathname.
     *
     * @param   pathname  A pathname string
     * @throws  NullPointerException
     *          If the {@code pathname} argument is {@code null}
     */
    public File(String pathname) {
        if (pathname == null) {
            throw new NullPointerException();
        }
        this.path = fs.normalize(pathname);
        this.prefixLength = fs.prefixLength(this.path);
    }
```

- 여기서 우리는 실제 파일을 찾는걸 확인할수가 있습니다.  그렇다면 우리는 어떻게 경로를 알 수 있을까요?

## File.getAbsolutePath()

```java
/**
     * Returns the absolute pathname string of this abstract pathname.
     *
     * <p> If this abstract pathname is already absolute, then the pathname
     * string is simply returned as if by the {@link #getPath}
     * method.  If this abstract pathname is the empty abstract pathname then
     * the pathname string of the current user directory, which is named by the
     * system property {@code user.dir}, is returned.  Otherwise this
     * pathname is resolved in a system-dependent way.  On UNIX systems, a
     * relative pathname is made absolute by resolving it against the current
     * user directory.  On Microsoft Windows systems, a relative pathname is made absolute
     * by resolving it against the current directory of the drive named by the
     * pathname, if any; if not, it is resolved against the current user
     * directory.
     *
     * @return  The absolute pathname string denoting the same file or
     *          directory as this abstract pathname
     *
     * @throws  SecurityException
     *          If a required system property value cannot be accessed.
     *
     * @see     java.io.File#isAbsolute()
     */
    public String getAbsolutePath() {
        return fs.resolve(this);
    }
```

- 기본적으로 파일 시스템을 가지고 있고 파일 시스템에서 경로를 찾는걸 알수가 있습니다.
- 파일 시스템은 윈도우와 유닉스 계열 마다 다르며 해당 메서드를 호출하면 현재 파일이 어느 경로에서 찾는지 알 수 있습니다.

## 결론

- 파일이 어느 경로부터 시작하는지 알기가 어렵다면 File 객체를 만들고 `getAbsolutePath()` 메서드를 실행하면 어디 경로에서 파일을 찾아오는지 알수 있다는게 결론입니다.
