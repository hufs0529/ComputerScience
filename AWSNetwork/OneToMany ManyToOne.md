# JPA Eager, Lazy

[출처][https://www.google.com](https://velog.io/@jin0849/JPA-%EC%A6%89%EC%8B%9C%EB%A1%9C%EB%94%A9EAGER%EA%B3%BC-%EC%A7%80%EC%97%B0%EB%A1%9C%EB%94%A9LAZY)https://velog.io/@jin0849/JPA-%EC%A6%89%EC%8B%9C%EB%A1%9C%EB%94%A9EAGER%EA%B3%BC-%EC%A7%80%EC%97%B0%EB%A1%9C%EB%94%A9LAZY

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

    ```@OneToMany(mappedBy = "writer")```
    @JsonManagedReference
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

## ManyToOne VS OneToMany
### Gif
#### Gif entity에 존재하는 writer을 통해 Member entity에 있는 gifList의 정보를 채운다.
#### Gif 객체는 한 사람에 의해서만 생성되는 것이 아니므로 ```@ManyToOne``` 어노테이션을 추가한다.
#### 연관관계의 주인임을 나타내고 물리 테이블에 있는 writer을 채우기 위해서 ```@JoinColumn```을 작성한다.

</br>

### Member
#### Member entity는 다수의 Gif을 가질 수 있으므로 List<Gif>형태로 정의한다
#### 조회하려는 정보가 Member entity의 writer정보를 참고할 것인기 때문에 mappedby를 사용한다
#### ** mappedby 생략시 중간 테이블이 생성된다
#### 

</br>

### Column
```bash
public void addGif(Gif gif) {
        // gif의 writer설정은 gif에서 함
        gifList.add(gif);
    }
```
#### addGif 메소드에서 입력받은 writer의 사용자 정보(id)를 셋팅하는 추가적인 작업이 있어야 DB에 반영이 된다
#### 이를 위해 Member entity의 ```private Long id;``` 변수에 ```@Column```어노테이션을 추가해야 null이 발생하지 않는다


[출처][https://www.google.com](https://soojong.tistory.com/entry/JPA-ManyToOne-OneToMany-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0

