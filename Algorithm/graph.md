# 그래프

[Velog](https://velog.io/@semoon/%EA%B7%B8%EB%9E%98%ED%94%84)

## 정의

정점(Vertex/Node)과 간선(Edge)로 이루어진 자료구조

- 무방향 그래프(Undirected Graph)
    
    간선에 방향이 없는 그래프
    
    - 차수(Degree): 한 정점에 연결된 간선의 수
- 방향 그래프(Dricted Graph)
    
    간선에 방향이 있는 그래프
    
    - 진출차수(Outdgree): 한 정점에서 나가는 간선의 수
    - 진입차수(Indgree): 한 정점에서 들어오는 간선의 수
- 순환 그래프(Cyclic Graph)
    
    Cycle이 있는 그래프
    
    - 사이클(Cycle): 임의의 한 정점에서 출발해서 다시 자신으로 돌아올 수 있는 경로
- 비순환 그래프(Acyclic Graph)
    
    Cycle이 없는 그래프
    
- 완전 그래프(Complete Graph)
    
    모든 정점 쌍이 연결되어있는 그래프
    
- 연결 그래프(Connected Graph)
    
    임의의 두 정점 사이의 경로가 항상 존재하는 그래프
    
- 단순 그래프(Simple Graph)
    
    두 정점 사이의 간선이 1개 이하이고, 루프가 존재하지 않는 그래프
    
    - 루프(loop): 한 정점에서 나가서 자기 자신으로 들어오는 정점

## 표현법

1. 인접 행렬 이용
    - 두 정점 사이의 간선이 1개 이하라고 가정
    - 인접 그래프에서 연결이 되면 1, 안되면 0으로 표현
    - 특징
        - 공간복잡도 O(V^2)
        - 두 정점 사이의 간선 확인 시간복잡도 O(1)
        - 어떤 정점에 연결된 모든 정점 확인 시간복잡도 O(V)
2. 인접 리스트 이용
    - V개의 리스트를 만들어 각 리스트에 자신과 연결된 정점을 넣어 표현
    - 인접 행렬에 비해 정점이 많고 간선이 적은 상황에서 공간을 절약 가능
    - 특징
        - 공간 복잡도 O(V+E)
        - 두 정점 u, v 사이의 간선 확인 시간복잡도 O(min(deg(u), deg(v))
        → 정점 u, v 중 간선이 적은 정점을 확인
        - 어떤 정점 v에 연결된 모든 정점 확인 시간복잡도 O(deg(v))

## BFS

그래프에서 너비를 우선으로 방문하는 알고리즘

- 순서
    1. 시작 정점을 큐에 삽입 후 방문 표시
    2. 큐에서 정점을 꺼내 정점과 연결된 모든 정점에 대해 3번 진행
    3. 해당 정점을 이전에 방문했다면 아무 것도 하지 않고, 처음으로 방문했다면 방문 표시 후 해당 정점을 큐에 삽입
    4. 큐가 빌 때 까지 2번을 반복
- 시간복잡도
    
    모든 정점이 큐에 최대 1번씩 들어가기 때문에 
    인접 리스트에서 O(V+E) 
    인접 행렬에서 O(V^2)
    
- 특징
    - 모든 간선의 길이가 같지 않다면 사용 불가능

## DFS

그래프에서 깊이를 우선으로 방문하는 알고리즘

- 순서
    1. 시작 정점을 스택에 삽입 후 방문 표시
    2. 스택에서 정점을 꺼내 정점과 연결된 모든 정점에 대해 3번 진행
    3. 해당 정점을 이전에 방문했다면 아무 것도 하지 않고, 처음으로 방문했다면 방문 표시 후 해당 정점을 스택에 삽입
    4. 스택이 빌 때 까지 2번을 반복
- 시간복잡도
    
    모든 정점이 스택에 최대 1번씩 들어가기 때문에 
    인접 리스트에서 O(V+E) 
    인접 행렬에서 O(V^2)
    
- 특징
    - 재귀를 통해서도 구현 가능
    - 재귀의 경우 스택 메모리가 작은 경우 제한에 걸림
    - 재귀와 비재귀의 방문 순서에 차이가 생길 수 있음
