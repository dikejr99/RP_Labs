include win64a.inc 
MSGBOXPARAMSA STRUCT
cbSize DWORD ?, ?
hwndOwner QWORD ?
hInstance QWORD ?
lpszText QWORD ?
lpszCaption QWORD ?
dwStyle DWORD ?, ?
lpszIcon QWORD ?
dwContextHelpId QWORD ?
lpfnMsgBoxCallback QWORD ?
dwLanguageId DWORD ?, ?
MSGBOXPARAMSA ENDS


.data 
password db "1234"
len1 equ ($-password)/type password
Buf dq 7 ;
Err1 dq 0
Msg1 db "Correct passoword",0
Msg2 db "Invalid password",0
Title1 db "Password Check",0
stdout dq 0   ;
stdin dq 0    ;
cRead dq 0    ;
cWritten dq 0 ;
Msg db "Please enter your password: *******",10,10,0 ;

.code

checkpass proc
lea rsi, password  
lea rdi, Buf 
mov rcx, len1
repe cmpsb 
jz m0 
inc Err1 
m0:
ret
checkpass endp


WinMain proc
sub rsp,28h
mov rbp,rsp
invoke GetStdHandle,STD_OUTPUT_HANDLE
mov stdout,rax
invoke GetStdHandle,STD_INPUT_HANDLE
mov stdin,rax
invoke WriteConsole,stdout,ADDR Msg,sizeof Msg,ADDR cWritten,0
invoke ReadConsole,stdin,ADDR Buf,7,ADDR cRead,0
invoke checkpass
.if Err1 == 0
invoke MessageBox,0,addr Msg1,addr Title1, MB_ICONINFORMATION
jmp success
.else
invoke MessageBox,0,addr Msg2,addr Title1, MB_ICONERROR
.endif
jmp quit


.data
mas1 real8 14., 21., 84., 63., 42. ;  A
len2 equ ($-mas1)/type mas1
mas2 real8 7., 2., 4., 4.   ; b, c, d, e
fileName db ".\AVX.exe", 0
params MSGBOXPARAMSA <>
tit1 db "Result of expression (sqrt(e) * d * c) - a/b",0
res dq len2 DUP(0), 0  ;
buf1 dd len2 DUP(0), 0  
ifmt db "Array A = 14  21  84  63  42 ", 10,
"Numbers: b, c, d, e := 7  2  4  4 ", 10,
"Results of calc: %d, %d, %d, %d, %d", 10, 10,
"Author: Ayoub El-Haddadi, gr. KH 919 i.e", 10, 0

.code 
success:
invoke WinExec, addr fileName, SW_SHOW

mov rcx, len2
lea rdx, res
lea rbx, mas1
vmovsd xmm1, mas2[0]  ; xmm1 b 
vmovsd xmm2, mas2[8]  ; xmm2 c
vmovsd xmm3, mas2[16] ; xmm3 d
vmovsd xmm4, mas2[24] ; xmm4 e
vmulsd xmm2, xmm2, xmm3  ; xmm2 - d * c
vsqrtsd xmm5, xmm5, xmm4 ; xmm5 - sqrt(e)
vmulsd xmm5, xmm5, xmm2 ; xmm5 - sqrt(e) * d * c
vcvttsd2si eax, xmm5
;###########################

m1:
vmovsd xmm0, qword ptr[rbx] ; xmm0 - a
vdivsd xmm0, xmm0, xmm1     ; xmm0 - a/b
vcvttsd2si eax, xmm0        ; for debug
vsubsd xmm5, xmm5, xmm0 ;(sqrt(e) * d * c) - a/b
vcvttsd2si eax, xmm5
movsxd r15, eax ;
mov [rdx], eax ; push result into res 
add rbx, 8
add rdx, 8
dec rcx
jnz m1


invoke wsprintf, addr buf1, addr ifmt, res, res[8], res[16], res[24], res[32]
mov params.cbSize, SIZEOF MSGBOXPARAMSA ; size of struction
mov params.hwndOwner, 0 ; window handle
invoke GetModuleHandle, 0 ; get program handle
mov params.hInstance, rax ; save program handle
lea rax, buf1 ; address of results
mov params.lpszText, rax
lea rax, tit1 ;Caption
mov params.lpszCaption, rax
mov params.dwStyle, MB_ICONINFORMATION ; windows style
mov params.dwContextHelpId, 0 ; 
mov params.lpfnMsgBoxCallback, 0 ;
mov params.dwLanguageId, LANG_NEUTRAL ; 
lea rcx, params
invoke MessageBoxIndirect


quit:
invoke ExitProcess,0 ;ExitProcess,0
WinMain endp
end

