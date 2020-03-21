# bof
* 메모리 주소가 붙어 있을 때 null byte가 들어가지 않으면 strcpy를 할 때 뒤에 있는 것을 분리하지 않고 복사하여 넣어준다.
~~~
#include <stdio.h>

int main()
{
	char buf[28];
	char secret[28];
	
	strcpy(secret, "XXXXXXXX");
	read(0, buf, 28);
	strcpy(secret, buf);

	return 0;
}
~~~
## rbp,rsp
rbp : stack base pointer - stack 맨 밑에, stack은 kernel을 침범하지 않기 위해 큰 주소값에서 작은 주소값으로 자란다
rsp : stack pointer 위쪽에,
prologue: 다음 함수를 호출할 때 ret 값 저장 후 원래 rbp 값을 저장
