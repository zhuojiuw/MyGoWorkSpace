```c++
//pthread 三线程循环打印abc
#include<iostream>
#include<pthread.h>
using namespace std;
pthread_cond_t Aready,Bready,Cready;
pthread_mutex_t mutex;
int a=0;
void* funA(void*)
{
    for (int i=0;i<10;i++){
        pthread_mutex_lock(&mutex);
        while(a!=0){
            pthread_cond_wait(&Aready,&mutex);
        }
        cout<<'A'<<endl;
        a=1;
        pthread_cond_signal(&Bready);
        pthread_mutex_unlock(&mutex);
    }
}
void* funB(void*)
{
    for(int i=0;i<10;i++){
        pthread_mutex_lock(&mutex);
        while(a!=1){
            pthread_cond_wait(&Bready,&mutex);
        }
        cout<<'B'<<endl;
        a=2;
        pthread_cond_signal(&Cready);
        pthread_mutex_unlock(&mutex);
    }
}
void* funC(void*)
{
    for (int i=0;i<10;i++){
        pthread_mutex_lock(&mutex);
        while(a!=2){
            pthread_cond_wait(&Cready,&mutex);
        }
        cout<<'C'<<endl;
        a=0;
        pthread_cond_signal(&Aready);
        pthread_mutex_unlock(&mutex);
    }
}
int main ()
{
   pthread_t A,B,C;
   pthread_mutex_init(&mutex,NULL);
   pthread_cond_init(&Aready,NULL);
   pthread_cond_init(&Bready,NULL);
   pthread_cond_init(&Cready,NULL);
   void *status;
   pthread_create(&A,NULL,funA,NULL);
   pthread_create(&B,NULL,funB,NULL);
   pthread_create(&C,NULL,funC,NULL);
   pthread_join(A,&status);
   pthread_join(B,&status);
   pthread_join(C,&status);
   pthread_mutex_destroy(&mutex);
   pthread_cond_destroy(&Aready);
   pthread_cond_destroy(&Bready);
   pthread_cond_destroy(&Cready);
   return 0;
}
```

```go
//goroutine && channel 循环打印abc
package main

import (
	"fmt"
	"sync"
)

var ch1, ch2, ch3 chan int
var wg sync.WaitGroup

func Print1() {
	for i := 0; i < 10; i++ {
		<-ch3
		fmt.Printf("A\n")
		ch1 <- 1
	}
	wg.Done()
}
func Print2() {
	for i := 0; i < 10; i++ {
		<-ch1
		fmt.Printf("B\n")
		ch2 <- 1
	}
	wg.Done()
}
func Print3() {
	for i := 0; i < 10; i++ {
		<-ch2
		fmt.Printf("C\n")
		ch3 <- 1
	}
	wg.Done()
}

func main() {
	wg.Add(3)
	ch1 = make(chan int, 1)
	ch2 = make(chan int, 1)
	ch3 = make(chan int, 1)
	go Print1()
	go Print2()
	go Print3()
	ch3 <- 1
	wg.Wait()
}
```

