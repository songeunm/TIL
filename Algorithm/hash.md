[Velog](https://velog.io/@semoon/%ED%95%B4%EC%8B%9C)

## 정의와 성질

key에 대응되는 value를 저장하는 자료구조

- **insert, erase, find, update 연산이 모두 O(1)**
- 해시 함수
    
    임의 길이의 데이터를 고정된 길이의 데이터로 대응시키는 함수
    
- key를 해시함수를 통해 적당한 인덱스로 변경 후 테이블로 관리를 하는 것! ⇒ 해시 테이블

## 충돌 회피

서로 다른 key가 같은 해시 값(인덱스)을 가지게 된 경우 충돌이 발생

- Chaining
    
   각 인덱스마다 연결리스트를 둔다.
    
   - insert
   key의 인덱스의 연결리스트에 값을 추가
   - erase·find·update
   key의 인덱스의 연결리스트를 찾아가서 해당 연결리스트를 key값 일치 여부를 확인하며 순회
	<img src=https://velog.velcdn.com/images/semoon/post/833bfc48-829d-4423-9df5-4a9a0c9b9d20/image.png width=90%>

    
    ⇒ 충돌이 잦아질수록 연결리스트 순회(O(n))이 잦아지므로 성능 저하
    
    → 충돌이 잦아지지 않도록 좋은 해시 함수가 필요
    
- Open Addressing
    
    충돌시 바로 다음 인덱스에 key-value 저장
    
    - insert
        
        해당 인덱스가 비어있으면 바로 key-value 저장, 아니면 다음 인덱스에 key-value 저장
        
        dummy를 만나면 바로 삽입이 가능하다.
        
    - find·update
        
        인덱스를 찾고 key값 일치 여부를 확인하며 다음 인덱스들을 확인
        
        dummy를 만나도 다음 탐색으로 이어진다.
        
    - erase
        
        해당 key를 find 한 뒤 **dummy값을 넣어 ‘값이 삭제된 상태’임을 명시**
        
        → 그냥 삭제할 경우 나중에 find시 제대로 작동하지 않을 수 있다.
        
    - Linear Probing
        
        <img src=https://velog.velcdn.com/images/semoon/post/e4e03137-953a-4cf5-ac1c-759c695a327e/image.png width=90%>

        
        충돌 발생 시 오른쪽으로 1칸씩 이동하는 방식
        
        장점: Cache hit rate가 높다.
        단점: Clustering이 생겨 성능에 영향을 줄 수 있다.
        
    - Quadratic Probing
		<img src=https://velog.velcdn.com/images/semoon/post/c69b4249-ee2b-4021-b93a-902e8ffc224b/image.png width=90%>

        
        충돌 발생 시 오른쪽으로 1, 3, 5, … 칸씩(처음 칸 기준 1, 4, 9칸씩) 이동하는 방식
        
        장점: Cache hit rate가 나쁘지 않다. Clustering을 어느정도 회피 가능하다.
        단점: 해시 값이 동일하면 여전히 Clustering이 발생한다.
        
    - Double Hashing
        <img src=https://velog.velcdn.com/images/semoon/post/a5ef1f6f-f66d-48c4-ad5a-82c55633603e/image.png width=90%>
        
        충돌 발생 시 이동할 칸의 수를 새로운 해시함수로 계산하는 방식
        
        장점: Clustering을 효과적으로 회피할 수 있다.
        단점: Cache hit rate가 낮다.