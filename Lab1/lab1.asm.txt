include win64a.inc

.data
	mas1 dd 1,2,3,4,5,6,  ;4 x 6 matrix 
			7,8,5,10,2,12,
			13,1,15,16,9,18,
			19,20,21,22,10,24
	cWritten dq 0 ;
	author db " Developed by: ayoub",0dh,0ah,0 ;
	pass db "Password: *****",0dh,0ah,0 ,0dh,0ah,0;
	Msg db "Please enter your password: -- 5 characters --",0dh,0ah,0 ;
	stdout dq 0
	len equ ($-mas1) / 4 / 2
	titl1 db "Program Condition",0 
	titl2 db "Result",0
	buf1 db 1024 dup (0)
	buf2 db 1024 dup (0)
	buf3 db 1024 dup (0)
	;stroka db "%s",10,10,"%s",0
	mas_template db "Matrix 4x6",0dh,0ah,\
	"1   2  3   4   5   6  ",10,10,\
	"7   8  5   10  2   12 ",10,10,\
	"13  1  15  16  9   18 ",10,10,\
	"19  20 21  22  10  24 ",0
	min_value db "Sum of minimum values: ",0dh,0ah,\
	" %d ",0
	curr_value db "curr min: ",0dh,0ah,\
	" %d, row %d",0
	szPas db "ayoub",0   ;##The password##
	pass_len equ ($-szPas)
	szStr1 db "Congratulations! Correct password.",0
	szStr2 db "Wrong password! Retry please.",0
	ttl db "Elementary console password entry program",0
	cRead dq ?
	buf0 dq ?
	min dd 0
	curr_min dd ?
	row_len dd ?
	Errp dq 0
.code
WinMain proc
	sub rsp,28h
	mov rbp,rsp
	invoke GetStdHandle,STD_OUTPUT_HANDLE
	mov stdout,rax
	invoke WriteConsole,stdout,ADDR author,sizeof author,ADDR cWritten,0
	invoke WriteConsole,stdout,ADDR pass,sizeof pass,ADDR cWritten,0
	invoke WriteConsole,stdout,ADDR Msg,sizeof Msg,ADDR cWritten,0
	invoke GetStdHandle,STD_INPUT_HANDLE
	invoke ReadConsole,rax,addr buf0,5,addr cRead,0
	;invoke lstrcmp,addr szPas,addr buf0 ;
	lea rsi, szPas
	lea rdi, buf0
	mov rcx, pass_len
	password_verification:
		mov al, [rsi]
		mov bl, [rdi]
		cmp al, bl
		JZ checked
			inc Errp
		checked:
			add rsi, 1
			add rdi, 1
	loop password_verification
	.if (Errp==0); && (cRead == pass_len)
		invoke MessageBox,0,addr szStr1,addr ttl,MB_ICONINFORMATION
	.else
		invoke MessageBox,0,addr szStr2,addr ttl,MB_ICONERROR
		jmp e
	.endif
	;Define a row with the minimum sum of elements
	mov ecx, len        ; move array length to counter
	lea rsi,mas1 	; address of array beginning
	mov eax,[rsi] 	; number load
	mov ebx, eax
	mov edx, 0
	mov row_len, 1
	;;;;;;;;;;;;;;;;;;;;;;
	L1:
		.if eax } 24
			jmp end_loop
		.endif
		
		CMP eax, ebx; Compare the eax value with current min
		JNLE  L2      ; If yes, then jump to label L2
			mov curr_min, eax 	;current minimum value
			mov ebx, eax
		L2:
		CMP row_len, 6
		JNE L3
			invoke wsprintf, ADDR buf3, ADDR curr_value, eax, row_len
			add min, ebx	
			mov curr_min, eax 	;Initialization for next row;
			mov ebx, eax
			mov row_len, 0
			
		L3:
		
		add rsi, 4 ; address of new element
		mov eax,[rsi]
		add row_len, 1
		
	loop L1
	
	end_loop:
	invoke wsprintf, ADDR buf1, ADDR mas_template
	invoke MessageBox,0,addr buf1,addr titl1,MB_ICONEXCLAMATION
	invoke wsprintf, ADDR buf2, ADDR min_value, min
	invoke MessageBox,0,addr buf2,addr titl2,MB_ICONINFORMATION

	e:
	invoke ExitProcess,0 ;invoke RtlExitUserProcess,0 ;ExitProcess,0
WinMain endp

end

	