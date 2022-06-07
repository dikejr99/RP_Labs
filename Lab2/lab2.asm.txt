include win64a.inc

.data
	titl2 db "Result of the equation",0
	buf1 db 1024 dup (0)
	result_temp db "Calculate the equation (18c/2b) :	%d ",0dh,0ah,
	"Time (Ticks):	%d ",0dh,0ah,0
	_c dq 2
	b dq 4
.code
WinMain proc
	sub rsp,28h
	mov rbp,rsp
	;;;;;;;;;;;;;;;;;;;;;;
	rdtsc			;set current timestamp into r15
	xchg rax, r15
	;Calculate equation
	mov rax, _c
	mov rbx, 18
	mul rbx
	
	mov rbx, b
	shl rbx, 1
	div rbx
	
	mov r9, rax
	rdtsc 			 ;Substracting previous timestamp with the current give us the execution time
	sub rax,r15
	invoke wsprintf, ADDR buf1, ADDR result_temp, r9, rax
	invoke MessageBox,0,addr buf1,addr titl2,MB_ICONINFORMATION

	;e:
	invoke ExitProcess,0 ;invoke RtlExitUserProcess,0 ;ExitProcess,0
	
WinMain endp
end

	