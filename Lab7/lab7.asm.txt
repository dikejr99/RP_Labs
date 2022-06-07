include win64a.inc

.data
arrA real8 9., 25., 33., 17.
len1 equ ($-arrA)/type arrA
arr2 real8 1., 7., 5., 4.   ; b, c, d, e
tit1 db "Calculate . c * sqrt(e) + a/b + d  .  AVX",0
res dq len1 DUP(0), 0  ;
buf1 dd len1 DUP(0), 0  ;
ifmt db "Array A = 9., 25., 33., 17.", 10,
"Numbers: b, c, d, e := 1., 7., 5., 4.",10,
"Result of calc: %d, %d, %d, %d", 10, 10,
"Author: Ayoub El-Haddadi, gr. KH 919 i.e", 10, 0

_Aelhaddadi segment READ WRITE EXECUTE alias("EAY")
	WinMain proc
		sub rsp,28h 
		mov rbp,rsp
		mov rcx,0E1h
		mov rax,$+1Eh
		mov r12, rax
		mov rbx,1828h 
		lp:
			sub word ptr [rax], bx
			add rax, 2
		loop lp 
		;##############################################################################################
		; Next instruction will be in encrypted state
		mov rcx, len1
		lea rdx, res
		lea rbx, arrA
		vmovsd xmm1, arr2[0]  ; xmm1 — b —  real8
		vmovsd xmm2, arr2[8]  ; xmm2 — c
		vmovsd xmm3, arr2[16] ; xmm3 — d
		vmovsd xmm4, arr2[24] ; xmm3 — e

		vsqrtsd xmm5, xmm5, xmm4 ; xmm5 - sqrt(e)
		vmulsd xmm5, xmm5, xmm2  ; xmm5 - c * sqrt(e)

		vcvttsd2si eax, xmm5

		m1:
		vmovsd xmm0, qword ptr[rbx] ; xmm0 — a
		vdivsd xmm0, xmm0, xmm1     ; xmm0 - a/b 
		vaddsd xmm0, xmm0, xmm5		; xmm0 += (c * sqrt(e))
		vaddsd xmm0, xmm0, xmm3		; xmm0 += d
		vcvttsd2si eax, xmm0   
		movsxd r15, eax ;
		mov [rdx], r15 
		add rbx, 8
		add rdx, 8
		dec rcx
		jnz m1 


		invoke wsprintf, addr buf1, addr ifmt, res, res[8], res[16], res[24]
		invoke MessageBox, 0, addr buf1, addr tit1, MB_ICONINFORMATION

		mov rcx,5Eh
		mov rax,r12
		mov rbx,1828h 
		lp2:
			add word ptr [rax], bx
			add rax, 2
		loop lp2 
		;exit12:
		invoke ExitProcess, 0
	WinMain endp
_Aelhaddadi ends
end
