#collision
~~~c
#include <stdio.h>
#include <string.h>
unsigned long hashcode = 0x21DD09EC;
unsigned long check_password(const char* p){
	int* ip = (int*)p;
	int i;
	int res=0;
	for(i=0; i<5; i++){
		res += ip[i];
	}
	return res;
}

int main(int argc, char* argv[]){
	if(argc<2){
		printf("usage : %s [passcode]\n", argv[0]);
		return 0;
	}
	if(strlen(argv[1]) != 20){
		printf("passcode length should be 20 bytes\n");
		return 0;
	}

	if(hashcode == check_password( argv[1] )){
		system("/bin/cat flag");
		return 0;
	}
	else
		printf("wrong passcode.\n");
	return 0;
}
~~~
res값에 넘겨준 값들을 축적한다. 0x21dd09ec는 113626824(0x06c5cec8)*5+4이다. 아래와 같이 exploit 해준다. 
~~~ 
col@pwnable:~$ ./col `python -c 'print "\xC8\xCE\xC5\x06"*4+"\xcc\xce\xc5\x06"'`
~~~
