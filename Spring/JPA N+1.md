# JPA N+1

### N+1문제란?
- 연관 관계가 설정된 엔티티를 조회할 경우에 조회된 데이터 갯수(n) 만큼 연관관계의 조회 쿼리가 추가로 발생하여 데이터를 읽어오는 현상

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

    ```@ManyToOne(fetch = FetchType.EAGER, optional = true)```
    @JoinColumn(name = "writer_id")
    @JsonBackReference
    private Member writer;
```

#### 다음과 같은 entity를 가질 때,
```bash
memberRepository.findAll();
```
#### 코드를 실행할 경우 Member 객체가 가지고 있는 모든 Gif가 한번씩 조회되는 N+1 문제가 발생한다

</br>

#### Fetch를 LAZY로 할 경우, N+1문제가 발생하는것 같지 ㅏㄶ지만, 
```bash
List<Member> all = memberRepository.findAll();
all.stream().forEach(member-> {
      member.getGifList.size();
}
```
#### 코드를 실행할 경우 다시 N+1문제가 발생한다.

### 해결 방법
1. Fectch Join
2. EntityGraph 어노테이션
3. Batch Size

#### Fetch Join
- JPQL을 사용하여 DB에서 데이터를 가져올 때 처음부터 연관된 데이터까지 같이 가져오는 방법이다(SQL JOIN)
```bash
@Query("SELECT NEW penterest.spring.domain.Like.dto.LikedGifDto(g.id, g.caption, g.url) " +
            "FROM Like l " +
            "JOIN l.gif g " +
            "JOIN l.member m " +
            "WHERE m.email = :email")
    List<LikedGifDto> findLikedGifDetailsByEmail(@Param("email") String email);
```
- Repository에 별도의 ```@Query```를 활용한 메소드를 생성해줘서 쿼리를 날리기 전에 JOIN해준다

#### EntityGraph 어노테이션
- ```@EntityGraph``` 어노테이션은 Fetch Join을 어노테이션으로 사용할 수 있는 방법으로, attributePaths 속성을 사용하여 원하는 엔티티 그래프를 정의한다.
- 이 속성을 통해 특정 엔티티나 연관된 엔티티들을 함께 로딩할 수 있다.
```bash
    @EntityGraph(attributePaths = "fromMember")
    List<Follow> findByToMember(@Param("fromMember") String fromMember);
```
#### Batch Size
- N+1 문제가 발생하더라도 select * from user where team_id = ? 이 아닌 select * from user where team_id in (?, ?, ? ) 방식으로 N+1 문제가 발생하게 하는 방법이다
- 이렇게 하면 1000번 일어날 N+1 문제를 1번만 더 조회하는 방식으로 성능을 최적화할 수 있다.
###### application.yml
```bash
spring:
  jpa:
    properties:
      hibernate:
        default_batch_fetch_size: 1000
```

#### 실무에서 N+1문제로 DB가 죽는 문제를 방지학 ㅣ위해서는?
1. 지연 로딩(LAZY)
2. Fetch Join
3. Batch size 설정

[참고][https://devhan.tistory.com/206]
[참][https://devhan.tistory.com/185?category=1066346]
[참고][https://programmer93.tistory.com/83]
