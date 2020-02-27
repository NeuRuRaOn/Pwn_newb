#l33t-ness
* 
~~~c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  setup(*(_QWORD *)&argc, argv, envp);
  puts("The l33t-ness level.");
  if ( (unsigned __int8)round_1() && (unsigned __int8)round_2() && (unsigned __int8)round_3() )
    win();
  return 0;
}
~~~
main 문으로 각 round를 통과하면 flag를 보여주는 것 확인
* 
~~~c
_BOOL8 round_1()
{
  _BOOL8 result; // rax
  int v1; // [rsp+8h] [rbp-38h]
  int v2; // [rsp+Ch] [rbp-34h]
  char s; // [rsp+10h] [rbp-30h]
  __int64 v4; // [rsp+20h] [rbp-20h]
  unsigned __int64 v5; // [rsp+38h] [rbp-8h]

  v5 = __readfsqword(0x28u);
  puts("=== 1eet ===");
  memset(&s, 0, 0x20uLL);
  printf("x: ", 0LL);
  read(0, &s, 0x10uLL);
  printf("y: ", &s);
  read(0, &v4, 0x10uLL);
  if ( strchr(&s, 45) || strchr((const char *)&v4, 45) )
    return 0LL;
  v1 = atoi(&s);
  v2 = atoi((const char *)&v4);
  if ( v1 <= 1336 && v2 <= 1336 )
    result = v1 - v2 == 1337;
  else
    result = 0LL;
  return result;
}
~~~
round 1인데 시간이 가장 오래걸림. realsung님의 풀이를 참고했더니 str() 함수로 묶어서 strchr()가 -를 체크하는 것을 우회
* 
~~~c
_BOOL8 round_2()
{
  int v1; // [rsp+0h] [rbp-10h]
  int v2; // [rsp+4h] [rbp-Ch]
  unsigned __int64 v3; // [rsp+8h] [rbp-8h]

  v3 = __readfsqword(0x28u);
  puts("=== t00leet ===");
  v1 = 0;
  v2 = 0;
  _isoc99_scanf("%d %d", &v1, &v2);
  return v1 > 1 && v2 > 1337 && v1 * v2 == 1337;
}
~~~
맞는 풀이인지는 모르겠지만 정수론 풀듯이 2의 32승 값을 1337에 더해준 후  소인수 분해함. 3이 소인수길래 3과 함께 나머지 값을 넣어줌
*
~~~c
_BOOL8 round_3()
{
  signed int i; // [rsp+0h] [rbp-30h]
  __int64 v2; // [rsp+10h] [rbp-20h]
  __int64 v3; // [rsp+18h] [rbp-18h]
  int v4; // [rsp+20h] [rbp-10h]
  unsigned __int64 v5; // [rsp+28h] [rbp-8h]

  v5 = __readfsqword(0x28u);
  puts("=== 3leet ===");
  v2 = 0LL;
  v3 = 0LL;
  v4 = 0;
  _isoc99_scanf("%d %d %d %d %d", &v2, (char *)&v2 + 4);
  for ( i = 1; i <= 4; ++i )
  {
    if ( *((_DWORD *)&v2 + i) < *((_DWORD *)&v2 + i - 1) )
      return 0LL;
  }
  return HIDWORD(v3) + (_DWORD)v3 + HIDWORD(v2) + (_DWORD)v2 + v4 == HIDWORD(v3)
                                                                   * (_DWORD)v3
                                                                   * HIDWORD(v2)
                                                                   * (_DWORD)v2
                                                                   * v4;
}
~~~
0값을 5개 넣어주고 flag 값 획득
*
exploit code
~~~python
from pwn import *

r=remote('svc.pwnable.xyz',30008)
e=ELF('./leet')

r.sendlineafter('x: ','1336')
r.sendlineafter('y: ',str(2**32-1))

r.sendlineafter('===\n','3 '+str(1431656211))

r.sendlineafter('===\n','0 0 0 0 0')

r.interactive()
                           
~~~

