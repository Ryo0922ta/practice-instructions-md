```java
    if (title != "" || title != " ")
```java

このコードだともし仮に　title = "" がわたされたとしても title!=" "が正のため正とみなされてしまう。

falseになるのは title ="" かつ　title= " "のときだけだ
だがこの二つが同時に存在することはありえない。

また、javaは　`!=` では中身ではなく参照先を比較する

```java
String a = "";
String b = new String("");
System.out.println(a == b);      // false（参照先が異なるためエラー）
System.out.println(a.equals(b)); // true（中身は同じ）
```

```java
    if (title != "" || title != " ")
```

上記の意図のように空文字または空白の場合をはじきたいのであれば

```java
    if (title == Null || title.isBlank())
```

