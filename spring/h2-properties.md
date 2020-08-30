# Spring H2 Database Properties

![h2-properties-1](https://github.com/ksy90101/TIL/blob/master/spring/img/h2-properties-1.png?raw=true)

- 위의 사진과 같이 Spring에서 제공해주는 h2의 Properties는 4개입니다.
- 각각의 Properties를 알아보도록 하겠습니다.
- 각 Property의 앞에서 보듯이 모두 console에 대한 Property만 존재한다.

## spring.h2.console.enabled

![h2-properties-2](https://github.com/ksy90101/TIL/blob/master/spring/img/h2-properties-2.png?raw=true)

- 기본값 : false
- 위의 사진과 같이 h2 Database의 접속하여 스키마들을 볼수 있는 console을 활성화 시키는 값이다.
- 기본값은 false이기 때문에 설정을 하지 않는다면 위의 사진처럼 console을 사용할 수 없다.
- console을 사용하여 테스트를 해야 할 경우에는 무조건 true를 사용하는 것이 좋다.

## spring.h2.console.path

- 기본값 : /h2-console
- 위의 console는 웹에서 나오는 것이다.
- 따라서 그에 맞는 URI를 지정해줘야 하는데, 기본 값은 `/h2-console` 이다.

![h2-properties-3](https://github.com/ksy90101/TIL/blob/master/spring/img/h2-properties-3.png?raw=true)

- 다른 경로로 사용하고 싶다면 값을 변경하면 된다.

```yaml
spring.h2.console.path=/h2-database
```

![h2-properties-4](https://github.com/ksy90101/TIL/blob/master/spring/img/h2-properties-4.png?raw=true)

## spring.h2.console.settings.trace

- 기본값 : fasle

```java
mimeType=text/html
index.do
process index.do
return index.jsp
getFile <index.jsp>
 size=923
mimeType=text/css
stylesheet.css
getFile <stylesheet.css>
 size=4924
mimeType=text/html
login.jsp
getFile <login.jsp>
 size=6610
mimeType=image/gif
background.gif
getFile <background.gif>
 size=169
mimeType=text/html
login.do
process login.do
return frame.jsp
getFile <frame.jsp>
 size=1447
mimeType=text/html
header.jsp
getFile <header.jsp>
mimeType=text/html
tables.do
process tables.do
 size=8330
mimeType=text/html
query.jsp
getFile <query.jsp>
mimeType=text/html
help.jsp
getFile <help.jsp>
 size=16433
 size=5193
mimeType=image/gif
icon_disconnect.gif
getFile <icon_disconnect.gif>
 size=114
mimeType=image/gif
icon_line.gif
getFile <icon_line.gif>
mimeType=image/gif
icon_refresh.gif
getFile <icon_refresh.gif>
 size=818
mimeType=image/gif
icon_rollback.gif
getFile <icon_rollback.gif>
mimeType=image/gif
icon_commit.gif
getFile <icon_commit.gif>
 size=327
mimeType=image/gif
icon_run_selected.gif
getFile <icon_run_selected.gif>
 size=331
 size=323
mimeType=image/gif
icon_run.gif
getFile <icon_run.gif>
mimeType=image/gif
icon_stop.gif
getFile <icon_stop.gif>
mimeType=image/gif
icon_help.gif
getFile <icon_help.gif>
mimeType=image/gif
icon_history.gif
getFile <icon_history.gif>
 size=312
 size=215
 size=379
 size=216
 size=373
return tables.jsp
getFile <tables.jsp>
 size=740
mimeType=text/javascript
tree.js
getFile <tree.js>
 size=3185
mimeType=image/gif
tree_minus.gif
getFile <tree_minus.gif>
mimeType=image/gif
tree_plus.gif
getFile <tree_plus.gif>
mimeType=image/gif
tree_folder.gif
mimeType=image/gif
tree_database.gif
getFile <tree_database.gif>
getFile <tree_folder.gif>
mimeType=image/gif
tree_empty.gif
getFile <tree_empty.gif>
mimeType=image/gif
tree_table.gif
getFile <tree_table.gif>
 size=861
 size=545
 size=870
 size=62
 size=372
 size=343
mimeType=image/gif
tree_users.gif
getFile <tree_users.gif>
mimeType=image/gif
tree_user.gif
getFile <tree_user.gif>
mimeType=image/gif
tree_info.gif
getFile <tree_info.gif>
mimeType=image/gif
tree_type.gif
getFile <tree_type.gif>
 size=601
 size=197
 size=267
 size=500
```

- 위와 같이 h2를 사용할 때마다 관련 정보들을 추적하여 log로 찍어주는 역햘이다.
- 크게 의미가 없어서 잘 사용하지 않는다.

## spring.h2.console.settings.web-allow-others

- 기본값 : false
- 다른 컴퓨터의 접속을 허용하는 것이다.
