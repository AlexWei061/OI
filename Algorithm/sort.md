# 插入排序
```cpp
#include<bits/stdc++.h>
using namespace std;

#define MAXN 100100

int n = 0;
int a[MAXN] = { 0 };

void insertionSort(int n, int a[]){
	int temp = 0;
	for (int i = 1; i <= n; i++){
		int j = 0; temp = a[i];
		for (j = i - 1; j >= 0 and a[j] > temp; j--){
			a[j + 1] = a[j];
		}
		a[j + 1] = temp;
	}
}

int main(){
	scanf("%d", &n);
	for(int i = 1; i <= n; i++){
		scanf("%d", &a[i]);
	}
	
	insertionSort(n, a);
	
	for(int i = 1; i <= n; i++){
		printf("%d ", a[i]);
	}
	puts("");
	
	return 0;
}
```
----
# 冒泡排序
```cpp
#include<bits/stdc++.h>
using namespace std;

#define MAXN 100100

int n = 0;
int a[MAXN] = { 0 };

void bubleSort(int n, int a[]){
	for(int i = 1; i <= n; i++){
		for(int j = 1; j <= n; j++){
			if(a[i] < a[j]){
				swap(a[i], a[j]);
			}
		}
	}
}

int main(){
	scanf("%d", &n);
	for(int i = 1; i <= n; i++){
		scanf("%d", &a[i]);
	}
	
	bubleSort(n, a);
	
	for(int i = 1; i <= n; i++){
		printf("%d ", a[i]);
	}
	puts("");
	
	return 0;
}
```
----
# 快速排序
```cpp
#include<bits/stdc++.h>
using namespace std;

#define MAXN 10010

int n = 0;
int arr[MAXN] = { 0 };

void quickSort(int L, int R){
	int mid = (L + R) >> 1;
	int pivot = arr[mid];
	int i = L; int j = R;
	do{
		while(arr[i] < pivot){
			i++;
		}
		while(arr[j] > pivot){
			j--;
		}
		if(i <= j){
			swap(arr[i], arr[j]);
			i++; j--;
		}
	}while(i <= j);
	
	if(L < j){
		quickSort(L, j);
	}
	if(i < R){
		quickSort(i, R);
	}
}

int main(){
	scanf("%d", &n);
	for(int i = 1; i <= n; i++){
		scanf("%d", &arr[i]);
	}

    quickSort(1, n);

    for(int i = 1; i <= n; i++){
        printf("%d ", arr[i]);
    }
    puts("");
	return 0;
}
```
----
# 归并排序
```cpp
#include<bits/stdc++.h>
using namespace std;

#define MAXN 10010

int n = 0;
int a[MAXN] = { 0 };
int b[MAXN] = { 0 };

void merge(int l, int mid, int r){
	int i = l; int j = mid + 1;
	for(int k = l; k <= r; k++){
		if(j > r or i <= mid and a[i] <= a[j]){
			b[k] = a[i++];
		}
		else{
			b[k] = a[j++];
		}
	}
	for(int k = l; k <= r; k++){
		a[k] = b[k];
	}
}

void mergeSort(int l, int r){
	int mid = (l + r) >> 1;
	if(l < r){
		mergeSort(l, mid);
		mergeSort(mid + 1, r);
		merge(l, mid, r);
	}
}

int main(){
	scanf("%d", &n);
	for(int i = 1; i <= n; i++){
		scanf("%d", &a[i]);
	}
	
	mergeSort(1, n);
	
	for(int i = 1; i <= n; i++){
		printf("%d ", a[i]);
	}
	puts("");
	
	return 0;
}
```
----
# 桶排序
```cpp
#include<bits/stdc++.h>
using namespace std;

#define MAXN 100100
#define MAXA 10010000

int n = 0;
int a[MAXN] =  { 0 };

int A = 0;
int buk[MAXA] = { 0 };

void buketSort(int n, int a[]){
	for(int i = 1; i <= n; i++){
		buk[a[i]]++;
	}
}

int main(){
	scanf("%d", &n);
	for(int i = 1; i <= n; i++){
		scanf("%d", &a[i]);
		A = max(A, a[i]);
	}
	
	buketSort(n, a);
	
	for(int i = 1; i <= A; i++){
		while(buk[i]--){
			printf("%d ", i);
		}
	}
	
	return 0;
}
```
----
# 堆排序（priority_queue）
&emsp; ~~主要是懒得手写一个堆qwq~~ 
```cpp
#include<bits/stdc++.h>
using namespace std;

#define MAXN 100100

int n = 0;
int a[MAXN] = { 0 };
priority_queue<int> q;

void heapSort1(int n, int a[]){
	for(int i = 1; i <= n; i++){
		q.push(a[i]);
	}
}

void heapSort2(int n, int a[]){
	for( int i = 1; i <= n; i++){
		q.push(-a[i]);
	}
}

int main(){
	scanf("%d", &n);
	for(int i = 1; i <= n; i++){
		scanf("%d", &a[i]);
	}
	
	heapSort1(n, a);
	
	for(int i = 1; i <= n; i++){
		int temp = q.top();
		q.pop();
		printf("%d ", temp);
	}
	puts("");
	
	heapSort2(n, a);
	
	for(int i = 1; i <= n; i++){
		int temp = q.top();
		q.pop();
		printf("%d ", -temp);
	}
	puts("");
	
	return 0;
}
```