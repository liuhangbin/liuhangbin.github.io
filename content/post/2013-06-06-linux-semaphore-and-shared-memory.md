---
layout: post
Title: Linux semaphore and shared memory
Date: 3013-06-02
tags: [Linux, C]
---

Semaphore
---
When we want to use semaphre to setup a mutex

sem_open()                                sem_close(),sem_unlink()  //有名信号量  
         \ |sem_wait(),sem_post()       |/  
         / |sem_trywait(),sem_getvalue()|\sem_destroy()  //无名信号量  
sem_init()

You can `man sem_init` and you can see
int sem_init(sem_t *sem, int pshared, unsigned int value);

When set pshared to 0, then the sem will shared between threads.
When set pshared to 1, then shared between process.

But there is a problem, we must put sem in shared memory, so lets see how to
create a shared memory.


Share\ Memory
---
shm_open() : open an shared memory
shm_unlink() : delete the shared memory
mmap() : map the shared memory to process
munmap() : opposited to mmap

OK, examples
---
``` c
#include        <stdio.h>
#include        <limits.h>
#include        <stdlib.h>
#include        <sys/time.h>
#include        <semaphore.h>
#include        <unistd.h>
#include        <sys/mman.h>
#include        <sys/stat.h>
#include        <fcntl.h>

int main(int argc, char *argv[])
{
        shm();
        int fd;
        void *sem_id;
        int num = 1;
        int err;
        size_t large = sizeof(sem_t);

        if ((fd = shm_open("SHARE_MEMORY", O_CREAT | O_TRUNC | O_RDWR, 0666)) < 0) {
                perror("shm_open error ");
                exit(EXIT_FAILURE);
        }

        if (ftruncate(fd, large) < 0) {
                perror("ftruncate error ");
                exit(EXIT_FAILURE);
        }

        if ((sem_id = mmap(0, large, PROT_READ | PROT_WRITE , MAP_SHARED, fd, 0)) < 0) {
                perror("mmap error ");
                exit(EXIT_FAILURE);
        }

        close(fd);

        if ( sem_init(sem_id, 1, 0) < 0 ) {
                perror("sem_init error");
                exit(1);
        }

        if (fork() == 0) {
                /* child */

                printf("I'm child\n");
                sleep(1);
                sem_post(sem_id);
		printf("OK, child done\n");

                if (munmap(sem_id, large) < 0) {
                        perror("munmap error ");
                        exit(EXIT_FAILURE);
                }

                exit(0);
        }

        printf("I'm father\n");
        sem_wait(sem_id);
        printf("OK, child done\n");

        if (munmap(sem_id, large) < 0) {
                perror("munmap error ");
                exit(EXIT_FAILURE);
        }

        if (shm_unlink("SHARE_MEMORY") < 0) {
                perror("shm_unlink error ");
                exit(EXIT_FAILURE);
        }

        return 0;
}

```

Reference : 
http://blog.csdn.net/feiyinzilgd/article/details/6765764
http://blog.csdn.net/laojing123/article/details/6109154
