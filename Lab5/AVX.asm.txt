include win64a.inc
.data
buf1 dq 500 dup(0), 0
bit1 db 0
bit2 db 0
.code
WinMain proc
sub rsp, 28h ; stack alignment: 28h=32d+8; 8 return address
mov rbp, rsp
.data
titl db "Test if AVX is supported",0 
szInf db "AVX IS SUPPORTED!",0 ;
inf db "AVX IS NOT SUPPORTED!",0

.code
; Test for support
mov eax, 1 
cpuid
and ecx, 10000000h
jnz exit1 ; 
invoke MessageBox, 0, addr inf, addr titl, MB_OK
mov bit1,1
jmp exit2
exit1:
invoke MessageBox, 0, addr szInf, addr titl, MB_ICONINFORMATION
exit2:

.data
tit2 db "Test if AVX2 is supported",0 ;
szInf2 db "AVX2 IS SUPPORTED!",0 ;
inf2 db "AVX2 IS NOT SUPPORTED!",0
tit3 db "Results",0
res2 dw 200 dup(0)

.code
mov eax, 7
mov ecx, 0
cpuid 
and rbx, 20h ; (5 bit)
jnz @ex1
invoke MessageBox, 0, addr inf2, addr tit2, MB_OK
mov bit2,1
jmp @ex2
@ex1:
invoke MessageBox, 0, addr szInf2, addr tit2, MB_ICONINFORMATION
@ex2:

exit12:
invoke ExitProcess, 0
WinMain endp
end