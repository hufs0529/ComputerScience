# OneToMany, ManyToOne 단방향

## 다대일(N:1)
### 게시글(Gif)와 사용자(Member)의 관계로 예를 들겠다
- 한 유저(1)는 여러 게시글(N)을 작성할 수 있습니다.
- 하나의 게시글은 하나의 유저에 의해서만 작성할 수 있다.
- 게시글과 유저는 다대일 관계를 갖는다


###게시글(Gif)과 사용자(Member)의 관계
```bash
public class Member{

    @Id
    @Column(name = "member_id")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String email;

    private String password;

    @Enumerated(EnumType.STRING)
    @Column(length = 30)
    private Role role;

    private List<Gif> gifList = new ArrayList<>();
```

```bash
public class Gif {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "gif_id")
    private Long id;

    ```@ManyToOne(fetch = FetchType.LAZY, optional = true)```
    @JoinColumn(name = "writer_id")
    @JsonBackReference
    private Member writer;
```
- 다대일 단방향에서는 다쪽인 Gif쪽에서 ```@ManyToOne```만 추가했다
- 반대로 Member에서는 참조하지 않았다(단방향)

</br>
- 데이터베이스 입장에서는 무조건 ```다(N)```쪽에서 외래키를 관리한다



## 일대다(1:N)
### 하지만 ```일(1)```쪽에서 ```다(N)```쪽 객체를 관리한다면??
- ```일(1)```만 수정한거 같지만 다른 수정이 생겨 의도치 않은 쿼리가 나갈수도 있다
- ```Member```를 수정했는데 ```Gif```가 수정이 되네???
- ``` 일대다(1:N) 양방향``` -> 실무 사용 금지

  
## 일대일(1:1)
- 일대일 단방향은 JPA에서 지원하지 않는다



[출처][https://www.google.com](https://soojong.tistory.com/entry/JPA-ManyToOne-OneToMany-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0

