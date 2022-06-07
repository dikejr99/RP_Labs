include win64a.inc
.data
arr1 dw 10, 6, 8, 3, 2, 7, 19, 9, 11, 4, 22, 13, 41, 17; 14 numbers
len1 EQU ($-arr1)/type arr1; 10
res1 dd len1 dup(0), 0
buf1 dq 500 dup(0), 0

bit1 db 0
bit2 db 0
frmt db "Using command VPHMINPOSUW", 10, 10,
"Answer:  %hu  %hu  %hu  %hu", 10, 10,
"Author: Ayoub El-Haddadi, gr. KH-919 i.e", 10,
"Task #8, LR 3 - Injection with API", 0
frmt2 db "Answ:  %hu %hu %hu %hu", 0
;buf2 db 1024 dup(0)
;"10, 6, 8, 3, 2, 7, 19, 9, 11, 4, 22, 13, 41, 17", 10,
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
and ecx, 10000000h ; eсx:= eсx v 1000 0000h (28 bit)
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


mov rax, len1 ;
mov rbx, 8 ; 32 х 8 = 256
xor rdx, rdx
div rbx
mov rcx, rax
lea rsi, arr1
;lea rdi, arr2
lea rbx, res1

mov rcx, 2
m2:
cmp bit2,1
jz exit12
vmovups xmm2, [rsi]
vphminposuw xmm1, xmm2
vmovups [rbx], xmm1
add rsi, 16
add rbx, 4
loop m2

exit10:
lea rdx, res1
lea rdi, res2
mov rcx, 2

m5:
movsxd r15, dword ptr [rdx]
mov qword ptr [rdi], r15
add rdx, 4
add rdi, 8
loop m5
lea rcx, qword ptr [buf1]
lea rdx, qword ptr [frmt]
mov r8, 0
mov r8w, word ptr [res2]
mov r9w, word ptr [res2 + 2]
mov ax, res2[8]
mov word ptr [rbp + 32], ax
mov ax, res2[10]
mov qword ptr [rbp + 40], 0
mov word ptr [rbp + 40], ax

call wsprintf
;invoke wsprintf,ADDR buf1,ADDR frmt2,res2,res2[2],res2[8], res2[10];
invoke MessageBox, 0, ADDR buf1, ADDR tit3, MB_OK

exit12:
invoke ExitProcess, 0
WinMain endp
end