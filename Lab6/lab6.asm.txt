include win64a.inc

DATA1 STRUCT ; Struct with 4 fields
    elem1 dq ? ; fields (8 bytes)
    elem2 dq ?
    elem3 dq ?
DATA1 ENDS

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
str1 DATA1 <1, 2, 9> ; Array of structs - matrix 3x6
     DATA1 <4, 10, 7>
     DATA1 <5, 5, 13>
	 DATA1 <0, 1, 3>
	 DATA1 <6, 8, 15>
	 DATA1 <2, 17, 1>
multiple dq 3
Err1 dq 0
params MSGBOXPARAMSA <>
tit1 db "Processing...", 0
buf1 dq 1024 dup(?), 0
buf2 dq 1024 dup(?), 0
buf3 dq 1024 dup(?), 0
res1 dq 18 DUP(0), 0

ifmt db "%s", 10, 10,"%s",0

ifmt1 db "Define the elements of multiples of 3 in each row", 0dh, 0ah,
"And replace it with row number: ", 0dh, 0ah,
"row1  : %d  %d  %d", 0dh, 0ah, 0ah,
"row2  : %d  %d  %d", 0dh, 0ah, 0ah,
"row3  : %d  %d  %d", 0

ifmt2 db "row4  : %d  %d  %d", 0dh, 0ah, 0ah,
"row5  : %d  %d  %d", 0dh, 0ah, 0ah,
"row6  : %d  %d  %d", 0dh, 0ah, 0ah,
"Author: Ayoub El Haddadi. Gr: KH 919 i.e", 0

.code 
WinMain proc
sub rsp, 28h
mov rbp, rsp

lea rsi, str1 ; loading first row of matrix

mov rbx, 0
mov r9, 3

lea rdx, res1
mov rcx, 18
vmovsd xmm2, multiple
m1:
    mov rax, qword ptr[rsi] ; rax = first element
	vmovsd xmm1, qword ptr[rsi]
	.if r9 == 3
		inc rbx
		mov r9, 0
	.endif
	mov r12, rax
	cmp rax, 3
	jl l2
	l1:
		vsubsd xmm1, xmm1, xmm2
		MOVD  rax, xmm1
		cmp rax, 3
		jl l2
		
	jmp l1
	l2:
	.if rax == 0
		mov [rdx], rbx
	.else
		mov [rdx], r12
	.endif
	
	add rsi, 8
	add rdx, 8
	inc r9
loop m1

quit:

invoke wsprintf, addr buf2, addr ifmt1, res1, res1[8], res1[16], res1[24], res1[32], res1[40], res1[48], res1[56], res1[64]
invoke wsprintf, addr buf3, addr ifmt2, res1[72], res1[80], res1[88], res1[96], res1[104], res1[112], res1[120], res1[128], res1[136]
invoke wsprintf,ADDR buf1, ADDR ifmt, ADDR buf2, ADDR buf3
mov params.cbSize, SIZEOF MSGBOXPARAMSA ; structure size
mov params.hwndOwner, 0 ; window handle
invoke GetModuleHandle, 0 ; program handle
mov params.hInstance, rax ; program handle saving
lea rax, buf1 ; addr of message
mov params.lpszText, rax
lea rax, tit1 ; Caption
mov params.lpszCaption, rax
mov params.dwStyle, MB_ICONINFORMATION ; window style
mov params.dwContextHelpId, 0  
mov params.lpfnMsgBoxCallback, 0 
mov params.dwLanguageId, LANG_NEUTRAL
lea rcx, params
invoke MessageBoxIndirect


invoke ExitProcess,0 ;RtlExitUserProcess, 0
WinMain endp
end

