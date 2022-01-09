
				area finalproject, code, readonly


				export __main
__main	proc
								; Configuration START
				ldr r0, =0x40004C00		
				add r0,#0x40			
				
				mov r1, #0x00			; Set P5DIR registers to input 
				strb r1, [r0, #0x04]		
					
				mov r1, #0x01			
				strb r1, [r0, #0x06]		; P5REN: P5.0 is resistor enabled
				strb r1, [r0, #0x02]		; P5OUT: P5.0 is enabled as a pull-up resistor 
				
				ldr r4, =0x40004C21		; Load register to Port 4 base address
				
				mov r1, #0x3F			; Set P4.5, P4.4, P4.3, P4.2, P4.1, P4.0 to output
				strb r1, [r4, #0x04]		
				
				mov r1, #0x10			
				strb r1, [r4, #0x02]		; Set blue LED to HI

repeat			
				mov r8, #0x80			; Set outer counter for rotation
				ldrb r7, [r0, #0x00] 		
				and r7, #0x40 			
				cmp r7, #00			
				beq dispense			
				b repeat
				endp
				
dispense		function
				mov r1, #0x20			
				strb r1, [r4, #0x02]		; Red LED is turned HI and Blue LED is turned LO On Port 4
				
				push {lr}			
				bl rotate_cw			
				pop {lr}			
				
				mov r1, #0x10			
				strb r1, [r4, #0x02]		; Red LED is turned to LO and Blue LED is turned to HI on Port 4
				
				b repeat			
				endp
				
rotate_cw		
				
				mov r1, #0x21			; Begin rotation of stepper motor
				strb r1, [r4, #0x02]
				
				push {lr}				
				bl delay			
				pop {lr}
				
				mov r1, #0x22			
				strb r1, [r4, #0x02]
				
				push {lr}
				bl delay			
				pop {lr}
				
				mov r1, #0x24			
				strb r1, [r4, #0x02]
				
				push {lr}
				bl delay			
				pop {lr}
				
				mov r1, #0x28			
				strb r1, [r4, #0x02]
				
				push {lr}
				bl delay			
				pop {lr}
				
				sub r8, #1			; Decrement outer rotation counter
				cmp r8, #0			
				bne rotate_cw			
				bx lr				
				
delay			function				
				mov r6, #50
outer			mov r5, #255
inner			subs r5, #1
				bne inner
				subs r6, #1
				bne outer
				bx lr
				endp
					
				end				
