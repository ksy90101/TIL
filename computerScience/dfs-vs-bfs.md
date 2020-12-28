# DFS(Depth First Search) VS BFS(Breadth First Search)

## 탐색 알고리즘이란?

- 그래프의 모든 정점들을 특정한 순서에 따라 방문하는 알고리즘
- 각 장점이 서로 연결되어 있는지, 주어진 시간 내에 탐색을 통해 확인하는 알고리즘이다.

## 그래프란?

![dfs-vs-bfs-1.png](https://github.com/ksy90101/TIL/blob/master/computerScience/image/dfs-vs-bfs-1.png?raw=true)

- 그래프는 정점과 간선으로 구성된, 한정된 자료구조를 의미한다.
- 각각의 지점을 정점이라고 하고, 정점과 정점의 연결을 간선이라고 한다.

## DFS(Depth First Search) - 깊이 우선 탐색

![https://upload.wikimedia.org/wikipedia/commons/thumb/7/7f/Depth-First-Search.gif/440px-Depth-First-Search.gif](https://upload.wikimedia.org/wikipedia/commons/thumb/7/7f/Depth-First-Search.gif/440px-Depth-First-Search.gif)

- 가장 직관적이고 구현하기 쉬운 탐색 방법
- 루트 노드(혹은 다른 임의의 노드)에서 시작해서 다음 분기로 넘어가기 전에 해당 분기를 완벽하게 탐색하는 방법
- 현재 정점과 연결된 정점들을 하나씩 갈 수 있는지 검사하고, 특정 정점으로 갈 수 있다면 그 정점에 가서 같은 행위를 반복한다.
- 같은 장점을 다시 방문하지 않도록 정점에 방문했다는 것을 표시해준다.
- 재귀 함수를 통해 구현한다.

### 작동 원리

- 시작점에서 한 갈래로 더 이상 갈 수 없을 때까지 탐색하고, 더 갈 곳이 없다면 이전의 경로로 되돌아간다.

### 의사 코드(pseudocode)

```java
void search(Node root) {
	if (root == null) return;
	visit(root); // 정점을 방문한 것으로 표시
	
	for each (Node n in root.adjecent) {
		search(n);
	}
}
```

### 2차원 이동 구현 방법

```java
void DFS_Coordinate(int x, int y) {
	visited(x, y);
	
	// 위로 이동
	if(isValid(x, y - 1); {
		DFS_Coordinate(x, y + 1)
	}
	
	// 아래 이동
	if(isValid(x, y + 1); {
		DFS_Coordinate(x, y + 1)
	}
	
	// 왼쪽으로 이동
	if(isValid(x - 1, y ); {
		DFS_Coordinate(x - 1, y)
	}
	
	// 오른쪽으로 이동
	if(isValid(x + 1, y); {
		DFS_Coordinate(x + 1, y)
	}
}
```

### 장점

- 코드가 직관적이고 구현하기 쉽다
- 재귀 깊이가 지나치게 깊어지면 메모리 비용을 예측하기 어렵다.
    - 같은 이유로, 데이터 사이즈가 일정 이상이면 DFS를 사용하지 않는 것이 좋다.
- 최단 경로를 알 수 없다.

## BFS(Breadth First Search) - 너비우선탐색

![https://upload.wikimedia.org/wikipedia/commons/4/46/Animated_BFS.gif](https://upload.wikimedia.org/wikipedia/commons/4/46/Animated_BFS.gif)

- 시작점에서 가까운 정점부터 순서대로 방문하는 탐색 알고리즘
- 루토 노드(혹은 다른 임의의 노드)에서 시작해서 인접한 노드를 먼저 탐색하는 방법
- 큐를 이용하여 구현한다.
- 출발점을 먼저 큐에 넣고, 다음 로직을 반복한다.
    - 큐에 저장된 정점을 하나 Dequeue한다.
    - 그리고 Dequeue된 정점과 연결된 모든 정점을 큐에 넣는다.
    - 큐가 비어있다면 반복을 종료한다.

### 작동 원리

- 시작점 기준으로 거리가 1인 모든 지점을 탐색하고, 이후 2인 지점을 탐색..하는 식으로 모든 지점을 탐색한다.
- 목표 지점을 찾으면 탐색을 종료하고, 찾지 못했다면 연결된 모든 지점을 탐색한다.

### 의사 코드(pseudocode)

```java
void search(Node root) {
	Queue queue = new Queue();
	root.marked = true;
	
	queue.enqueue(root);
	
	while(!queue.isEmpty()) {
		Node r = queue.dequeue();
		visit(r);
		foreach (Node n in r.adjacent) {
			if (n.marked == false) {
				n.marked = true;
				queue.enqueue(n);
		}
}
```

### 구현 방법 - 2차원 이동

```java
void BFS_Coordinate(int start_x, int start_y) {
	queue.push(new Position(start_x, start_y);

	while(!queue.isEmpty()) {
		Position position = queue.pop();
		visit(position.x, position.y);
		
		// 위로 이동
		if(isValid(position.x, position.y - 1)) {
			queue.push(position.x, position.y - 1);
		}
		
		// 아래로 이동
		if(isValid(position.x, position.y + 1)) {
			queue.push(position.x, position.y + 1);
		}

		// 왼쪽 이동
		if(isValid(position.x - 1, position.y)) {
			queue.push(position.x - 1, position.y);
		}

		// 오른쪽 이동
		if(isValid(position.x + 1, position.y)) {
			queue.push(position.x + 1, position.y);
		}
	}
}
```

### 장점

- 효율적인 운영이 가능하고, 시간/공간 복잡도 면에서 안정적이다.
- 간석의 비용이 모두 같을 경우, 최단 경로를 구할 수 있다.
    - 간선의 비용이 각각 다를 경우, 다익스트라 알고리즘 등의 최단거리 알고리즘을 활용해야 한다.

### 단점

- DFS이 비해 코드 구현이 어렵다
- 모든 지점을 탐색할 경우를 대비해, 큐의 메모리가 미리 준비되어 있어야 한다.

## Flood Fill Algorithm

- 최단 경로를 찾을 필요 없이, 연ㄹ결된 공간이 몇 개인지 알아야 할 때 활용
- 데이터의 크기가 크지 않은 경우, 단순한 DFS만으로도 문제를 풀 수 있다.
- 규모가 큰 경우 BFS를 쓰기도 한다.

## 참고자료

[[10분 테코톡] 📚카프카의 탐색 알고리즘](https://youtu.be/By77aC9Oe3Q)
