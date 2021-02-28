---  
title: Linear algebra with python 
excerpt:  Python으로 선형 대수 표현하기     
date: 2021-01-01 02:00:00 +0800
last_modified_at: 2021-01-01 02:00:00 +0800
categories: python
header:
  teaser: /assets/images/01_teaser/home.jpg
  overlay_image: /assets/images/01_teaser/home.jpg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/home.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
---

## python으로 vector 표현 방법 

```sh 
vector_a = [1,2,3] # List 
vector_b = (1,2,3) # Tuple
vector_c = {'x':1, 'y':2, 'z':3} #dict
```

## vector 계산 

- bad case 

  ```sh 
  u = [2,2]
  v = [2,3]
  z = [3,5]
  
  result = []
  
  for i in range( len(u)) :
      result.append(u[i] + v[i] + z[i])
      
  print(result)
  ```

- python 다운 코드

  ```sh 
  u = [2,2]
  v = [2,3]
  z = [3,5]
  
  result = [sum(t) for t in zip(u,v,z)]
  print(result)
  ```

## scala-vector 연산

- 코드 

  ```sh
  u = [1,2,3]
  v = [4,4,4]
  alpha = 2
  
  result = [alpha*sum(t) for t in zip(u,v)]
  print(result)
  ```
  
- 결과
  ```sh
  2([1,2,3]+[4,4,4]) = 2[5,6,7] = [10,12,14]
  ```
  
## python으로 Matrix 표현 방법 

```shell
matrix_a = [[3,6], [4,5]]
matrix_b = [(3,6), (4,5)]
matrix_c = {(0,0):3, (0,1):6, (1,0):4, (1,1): 5}
```

## Matrix 덧셈

```sh
matrix_a = [[3,6], [4,5]]
matrix_b = [[5,8], [6,7]]

result = [[sum(row) for row in zip(*t)] for t in zip(matrix_a, matrix_b)]

print(result)

[t for t in zip(matrix_a, matrix_b)] [([3, 6], [5, 8]), ([4, 5], [6, 7])]

[[row for row in zip(*t)] for t in zip(matrix_a, matrix_b)] [[(3, 5), (6, 8)], [(4, 6), (5, 7)]]
```

## scalar-Matrix

```sh 
matrix_a = [[3,6], [4,5]]
alpha = 4
result = [[alpha * element for element in t] for t in matrix_a]

print(result)
```

## Matrix Transpose

```sh 
matrix_a = [[1,2,3],[4,5,6]]
result = [[element for element in t] for t in zip(*matrix_a)]

[t for t in zip(*matrix_a)]
```

## Matrix Product

```sh 
matrix_a = [[1,1,2], [2,1,1]]
matrix_b = [[1,1],[2,1],[1,3]]
result = [[sum(a*b for a,b in zip(row_a, column_b)) for column_b in zip(*matrix_b)] for row_a in matrix_a]
print(result)

[row_a for row_a in matrix_a]

[column_b for column_b in zip(*matrix_b)]
```

## Matrix Addition Function 만들기

```sh 
def vector_addition(*args):
    print([sum(t) for t in zip(*args)])

vector_addition([1,2],[2,3],[3,4])
```

## 참고

[w3schools.com-python example](https://www.w3schools.com/python/python_for_loops.asp)