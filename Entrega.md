#T2: Programação Paralela Multithread
-------------------------------------

Nome: Thiago Bellotti Pavin

## Threads POSIX

O **particionamento**, esse problema é possível ser realizado paralelamente, pois não tem nenhuma dependência que impeça de ser otimizado para uma ou mais threads.

A **comunicação** é a proteção de memória dada pelo mutex com as funções ‘pthread_mutex_lock’ que impede outras threads de acessar essa parte de código até ser chamada a função ‘pthread_mutex_unlock’ para liberar para as outras threads.
  
  ```c
  nthreads = atoi(argv[1]); 
  wsize = atoi(argv[2]);  // worksize = tamanho do vetor de cada thread
  repeat = atoi(argv[3]); // numero de repeticoes dos calculos (para aumentar carga)
  ```


O **aglomeração** ocorre pelo argumento ‘arg’ passado para a função, onde é colocado na variável ‘offset’, que determinará o começo de cada thread no vetor, fazendo com que cada thread trabalhe em diferentes partes do vetor.

  ```c
  void *dotprod_worker(void *arg)
  {
	   int i, k;
	   long offset = (long) arg;
	   double *a = dotdata.a;
	   double *b = dotdata.b;
	   int wsize = dotdata.wsize;
	   int start = offset*wsize;
	   int end = start + wsize;
	   double mysum;

	   for (k = 0; k < dotdata.repeat; k++) {
	      mysum = 0.0;
	      for (i = start; i < end ; i++)  {
	         mysum += (a[i] * b[i]);
	      }
	   }
	   pthread_mutex_lock (&mutexsum);
	   dotdata.c += mysum;
	   pthread_mutex_unlock (&mutexsum);

	   pthread_exit((void*) 0);
  }
  ```

O **mapeamento** é atribuído estaticamente, passando os argumentos como o número de threads que serão usadas e o tamanho dos vetores de cada thread.

  ```c
  pthread_mutex_lock (&mutexsum);
  dotdata.c += mysum;
  pthread_mutex_unlock (&mutexsum);
  ```

| tool     | nthreads | size    | repetitions | usec      | 
|----------|----------|---------|-------------|-----------| 
| Pthreads | 1        | 1000000 | 2000        | 5155442   | 
| Pthreads | 2        | 500000  | 2000        | 2594616   | 
| Pthreads | 4        | 250000  | 2000        | 1376082   | 
| Pthreads | 8        | 125000  | 2000        | 982020    | 
| Pthreads | 1        | 1000000 | 10000       | 25501023  | 
| Pthreads | 2        | 500000  | 10000       | 12820386  | 
| Pthreads | 4        | 250000  | 10000       | 6505304   | 
| Pthreads | 8        | 125000  | 10000       | 5164627   | 
| Pthreads | 1        | 1000000 | 100000      | 256540044 | 
| Pthreads | 2        | 500000  | 100000      | 129372584 | 
| Pthreads | 4        | 250000  | 100000      | 66561632  | 
| Pthreads | 8        | 125000  | 100000      | 48149206  | 


### Aceleração


![](/imagens/ImagemGraficoMicro.png)

![](/imagens/ImagemGraficoAcele.png)

![](/imagens/ImagemGraficoAceleRep.png)


### Diferença entre os dois programas 

A diferença dos dois programas é que no primeiro se utiliza as funções ‘pthread_mutex_lock’ e ‘pthread_mutex_unlock’ para garantir que apenas uma das threads acesse a parte do código com memória compartilhada, já no segundo essas funções não são utilizadas, podendo então ocorrer o um erro na soma dos resultados na variável compartilhada, portanto o segundo programa não está correto.



## OpenMP

| tool   | nthreads | size    | repetitions | usec      | 
|--------|----------|---------|-------------|-----------| 
| OpenMP | 1        | 1000000 | 2000        | 5153131   | 
| OpenMP | 2        | 500000  | 2000        | 2590801   | 
| OpenMP | 4        | 250000  | 2000        | 1357895   | 
| OpenMP | 8        | 125000  | 2000        | 1148986   | 
| OpenMP | 1        | 1000000 | 10000       | 25648348  | 
| OpenMP | 2        | 500000  | 10000       | 13005462  | 
| OpenMP | 4        | 250000  | 10000       | 6771948   | 
| OpenMP | 8        | 125000  | 10000       | 5262701   | 
| OpenMP | 1        | 1000000 | 100000      | 257229220 | 
| OpenMP | 2        | 500000  | 100000      | 129190889 | 
| OpenMP | 4        | 250000  | 100000      | 68391661  | 
| OpenMP | 8        | 125000  | 100000      | 53442906  | 

![](/imagens/ImagemGraficoAceleOPM.png)

![](/imagens/ImagemGraficoAceleOPMRep.png)

