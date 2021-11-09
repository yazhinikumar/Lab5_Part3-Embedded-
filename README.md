# Lab5_Part3-Embedded-
#include <msp430.h>
#define RED LED BIT6 // Defines the red LED  

void main(void)
{
    P1DIR |= BIT6;
    WDTCTL = WDTPW | WDTHOLD;   // stop watchdog timer
    TACCR0 =  31250-1;          // PWM Time Period/ frequency (1 KHz)
    TACCTL1 = OUTMOD_7;          // reset/set mode 7 for output signal
    TACCR1 = 6250-1;                // PWM Duty cycle is 20%
    TACTL = TASSEL_2 + MC_1 + ID_3;   // SMCLK and Up Mode then clock divide by 8

    TA0CCTL0 |= CCIE;
    TA0CCTL1 |= CCIE;
    TA0CCTL0 &=~CCIFG; // flags when the interrupt is not TA0CCTL0
    TA0CCTL1 &=~CCIFG;// flags when the interrupt is not TA0CCTL1

    _enable_interrupts(); // Enables the interrupt 

    while(1);
}

#pragma vector = TIMER0_A0_VECTOR       //define the interrupt service vector
interrupt void TA0_ISR (void)    // interrupt service routine
{
    P1OUT |=BIT6; // output the red LED 
    TA0CCTL0 &=~CCIFG; // Flags the interprut  
}

#pragma vector = TIMER0_A1_VECTOR       //define the interrupt service vector
interrupt void TA1_ISR (void)    // interrupt service routine
{
    P1OUT &=~BIT6;// output the red LED 
    TA0CCTL1 &=~CCIFG; // Flags the interprut 
}
