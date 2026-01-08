# 📁Django 성능 최적화 (Optimization)

> "기능을 켜는 건 쉽지만, 성능을 잡는 건 어렵다."
> Django ORM은 편리하지만, 무심코 사용하면 심각한 성능 저하(N+1 문제)를 유발한다.

---

## 1. N+1 문제 (The N+1 Problem)

#### 1) 정의
목록을 조회하기 위해 **1번의 쿼리(1)**를 날렸는데, 각 데이터의 연관된 데이터를 가져오기 위해 **N번의 추가 쿼리(N)**가 발생하는 현상.

#### 2) 발생 원인 (Lazy Loading)
 Django ORM의 QuerySet은 **지연 로딩(Lazy Loading)**을 기본으로 한다.
* `posts = Post.objects.all()`을 실행할 때는 실제 DB 쿼리가 날아가지 않는다.
* `for post in posts:` 루프를 돌며 데이터를 사용할 때 쿼리가 실행되는데,
* 만약 루프 안에서 `post.author.name`처럼 **연관 객체(Foreign Key)**에 접근하면, 매 반복마다 DB 쿼리를 새로 날린다.

#### 3) 예시 시나리오
* 게시글(Post) 100개를 가져와서 작성자(Author) 이름을 출력한다.
* **Bad Code**:
    ```python
    # 쿼리 1번 (게시글 100개 조회)
    posts = Post.objects.all() 
    
    for post in posts:
        # 쿼리 100번 추가 실행 (각 게시글의 작성자 조회)
        print(post.author.name) 
    
    # 총 1 + 100 = 101번의 SQL 발생! (DB 사망)
    ```

---

## 2. 해결 방법: Eager Loading

데이터를 가져올 때, 연관된 데이터까지 **한 번에(Join 등)** 미리 가져오는 기술.

### 1) select_related (SQL JOIN)
* **동작 원리**: SQL의 `JOIN` 문을 사용하여 **하나의 쿼리**로 두 테이블의 데이터를 합쳐서 가져온다.
* **사용 대상**: 
    * **1:1 (OneToOneField)** 관계
    * **N:1 (ForeignKey)** 관계 (자식이 부모를 참조할 때)
* **코드 예시**:
    ```python
    # INNER JOIN을 사용하여 한 번에 가져옴
    posts = Post.objects.select_related('author').all()
    
    for post in posts:
        print(post.author.name) # DB 조회 X (이미 메모리에 있음)
    # 총 1번의 쿼리만 발생
    ```

### 2) prefetch_related (Additional Query + Python Joining)
* **동작 원리**: 
    1. 메인 쿼리를 실행한다 (Post 조회).
    2. 연관된 데이터를 가져오는 **별도의 쿼리를 1번 더 실행**한다 (WHERE id IN (...)).
    3. 파이썬 메모리 상에서 두 데이터를 매칭(Mapping)시킨다.
* **사용 대상**: 
    * **M:N (ManyToManyField)** 관계
    * **1:N (Reverse ForeignKey)** 관계 (부모가 자식 역참조할 때)
* **코드 예시**:
    ```python
    # 쿼리 총 2번 실행 (Post 조회 1번 + Tag 조회 1번)
    posts = Post.objects.prefetch_related('tags').all()
    
    for post in posts:
        # DB 조회 X
        for tag in post.tags.all():
            print(tag.name)
    ```

### 3) 요약 비교표

| 구분 | select_related | prefetch_related |
| :--- | :--- | :--- |
| **SQL 방식** | **JOIN** (단일 쿼리) | **IN 절** (추가 쿼리) |
| **지원 관계** | 1:1, N:1 (정참조) | M:N, 1:N (역참조) |
| **메모리** | DB 부하 큼 / 메모리 사용 적음 | DB 부하 분산 / 메모리 사용 큼 |
| **주의사항** | 불필요하게 많이 걸면 JOIN 비용 증가 | 데이터가 너무 많으면 파이썬 처리 속도 저하 |

---

## 3. QuerySet은 언제 평가(Evaluate)되는가?

면접에서 자주 나오는 질문입니다. ORM을 작성한다고 바로 SQL이 나가는 것이 아닙니다.

1. **Iteration**: `for x in queryset` 루프를 돌 때.
2. **Slicing**: `queryset[0]` 또는 스텝 파라미터 `queryset[::2]` 사용 시.
3. **Pickling/Caching**: 쿼리셋을 캐시할 때.
4. **Repr/Len/List**: `print(queryset)`, `len(queryset)`, `list(queryset)` 호출 시.
5. **Raw**: `bool(queryset)`, `if queryset:` 등 존재 여부 확인 시.

> **Tip**: `exists()` 메서드는 전체 데이터를 다 가져오지 않고 `LIMIT 1`로 존재 여부만 최적화해서 체크하므로, 단순히 데이터 유무만 볼 때는 `if queryset:` 보다 `if queryset.exists():`가 훨씬 빠르다.
