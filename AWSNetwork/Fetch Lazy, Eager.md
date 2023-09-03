# Fetch Lazy, Eager

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


    public void addGif(Gif gif) {
        // gif의 writer설정은 gif에서 함
        gifList.add(gif);
    }

```

</br>


- Gif과 Member는 양방향 관계이고, 연관관계의 주인은 Gif이다(무조건 다(N))쪽
- 참고로 ```@ManyToOne``` 맵핑의 기본 fetch는 EAGER여서 생략 가능하다
- EAGER로 설정되어 있다면 Gif 조회시 Member의 정보까지 join되어서 쿼리가 나간다
- EAGER가 언뜻보면 더 좋아보이지만, 서비스 상에서 Member같이 여러 관계가 맵핑되어 있을 경우 DB에 부하가올 수 있다

##### 비즈니스 로직 상 Gif 데이터가 필요한 곳에 대부분 Member의 데이터가 같이 사용할 필요 EAGER가 유리
##### Gif 데이터가 필요한 곳에 대부분 Member의 데이터가 필요하지 않는다면 LAZY로 설정해서 부하를 줄이는 것이 좋

</br>




[참고][https://velog.io/@jin0849/JPA-%EC%A6%89%EC%8B%9C%EB%A1%9C%EB%94%A9EAGER%EA%B3%BC-%EC%A7%80%EC%97%B0%EB%A1%9C%EB%94%A9LAZY]
