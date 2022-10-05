# Interrupts
This file contains a summary of all interrupts and how to configure them. To enable any interrupts `sei()`
 must be called.

## Global Interrupt Enable
Enables all interrupts on the ATMEGA328p. Is special in that it can be enabled by calling `sei()`

- Flag: I (bit 7)
- Register: SREG

### C Code Usage
```c
sei();
```

## External Interrupt
These interrupts can detect falling edges, rising edges or a low level on pins INT0 and INT1. Make sure to set `DDRxn` to input when using these interrupts 

- ISR C Name: `INT0_vect`
- Enable Mask: `INT0/INT1` in `EIMSK` 
- Control Register: `ISC0[1:0]/ISC1[1:0]` in `EICRA`
- Flag Register: `INTF0/INTF1` are set when the `INT0/INT1` interrupt occurs. 

### Control Bit Table

`ISCn1` | `ISCn0` | Description
--------|---------|------------
0       | 0       | Interrupt on low level on pin `INTn`
0       | 1       | Interrupt on any change on pin `INTn`
1       | 0       | Interrupt on falling edge on pin `INTn`
1       | 1       | Interrupt on rising edge on pin `INTn`
 
### C Code Usage
```c
EICRA  |= (1 << ISC10); // Detect rising edge
EICRA  |= (1 << ISC11); // Detect rising edge  
EICMSK |= (1 << INT1); // Enable external interrupt 1
```

## Pin Change Interrupt
These interrupts can detect changes on select pins. `PCINT0` detects changes on pins `PCINT[7:0]`, `PCINT1` detects changes on pins `PCINT[14:8]` and `PCINT2` detects changes on pins `PCINT[23:16]`. 

Note the that the `PCINT[7:0]` pins are the GPIO port B pins, `PCINT[14:8]` are the GPIO port C pins, and `PCINT[23:16]` are the GPIO port D pins. 

- ISR C Name: `PCINT0_vect/PCINT1_vect/PCINT2_vect`
- Mask Register: `PCINT[7:0]` in `PCMSK0`, `PCINT[14:8]` in `PCMSK1` and `PCINT[23:16]` in `PCMSK2`.
- Control Register: `PCIE0/PCIE1/PCIE2` in `PCICR` 
- Flag Register: `PCIF0/PCIF1/PCIF2` in `PCIFR` are set when the `PCINT0/PCINT1/PCINT2` interrupt occurs. 


### Control Bit Table

`PCIEn` | Description
--------|--------------------
1       | Enable interrupts for `PCINTn`
 
### C Code Usage
```c
PCMSK1 |= (1 << PCINT12); // Detect changes on PCINT12
PCICR  |= (1 << PCEI1); // Enable interrupts for PCINT0  
```

## Timer Overflow Interrupt
This interrupt occurs when a timer's internal clock overflows. This interrupt is available for timer 0, timer 1 and timer 2.

- ISR C Name: `TIMER0_OVF_vect/TIMER1_OVF_vect/TIMER2_OVF_vect`
- Mask Register: `TOIE0` in `TIMSK0`, `TOIE1` in `TIMSK1` and `TOIE2` in `TIMSK2`
- No Control Registers
- Flag Register: `TOV0` in `TIFR0`, `TOV1` in `TIFR1` and `TOV2` in `TIFR2`

### C Code Usage

```c
ISR(TIMER0_OVF_vect) {
    PORTB ~= (1 << PB3);
}

int main() {
    ...
    TCCR0B |= (1 << CS00); // Set no prescaler
    TIMSK0 |= (1 << TOIE0); /// Enable overflow interrupt
    ...
    sei();
} 
```

## Timer Compare A Interrupt
This interrupt occurs when a timers internal value == `OCRnA`.

- ISR C Name: `TIMER0_COMPA_vect/TIMER1_COMPA_vect/TIMER2_COMPA_vect`
- Mask Register: `OCIE0A` in `TIMSK0`, `OCIE1A` in `TIMSK1` and `OCIE2A` in `TIMSK2`
- No Control Registers
- Flag Register: `OCF0A` in `TIFR0`, `OCF1A` in `TIFR1` and `OCF2A` in `TIFR2`

### C Code Usage

```c
ISR(TIMER1_COMPA_vect) {
    PORTB ~= (1 << PB3);
}

int main() {
    ...
    OCR1A = 0xee; // Count up to 238
    //TODO: CTC MODE HERE
    TCCR1B |= (1 << CS00); // Set no prescaler
    TIMSK1 |= (1 << OCF1A); //  Enable comp a interrupt
    ...
    sei();
} 
```

## Timer Compare B Interrupt
This interrupt occurs when a timers internal value == `OCRnA`.

- ISR C Name: `TIMER0_COMPB_vect/TIMER1_COMPB_vect/TIMER2_COMPB_vect`
- Mask Register: `OCIE0B` in `TIMSK0`, `OCIE1B` in `TIMSK1` and `OCIE2B` in `TIMSK2`
- No Control Registers
- Flag Register: `OCF0B` in `TIFR0`, `OCF1B` in `TIFR1` and `OCF2B` in `TIFR2`

### C Code Usage

```c
ISR(TIMER1_COMPB_vect) {
    PORTB ~= (1 << PB3);
}

int main() {
    ...
    OCR1B = 0xee; // Interrupt when counter = 238
    TCCR1B |= (1 << CS00); // Set no prescaler
    TIMSK1 |= (1 << OCF1B); //  Enable comp a interrupt
    ...
    sei();
} 
```

## Timer 1 Input Capture Interrupt
This interrupt is triggered when a rising (or falling) edge is detected on pin `ICP1`. Depending on the value of some control registers, this interrupt can be triggered on either a rising or falling edge (not both).

- ISR C Name: `ICP1_vect`
- Mask Register: `ICIE1` in `TIMSK1`
- Control Registers: `ICES1` and `ICNC1` in `TCCR1B`
- Flag Registers: `ICF1` in `TIFR1`

### Control Registers

`ICES1` | `ICNC1` | Description
--------|---------|-------------
0       | x       | Interrupt is triggered on falling edge
1       | x       | Interrupt is triggered on rising edge
x       | 0       | Input noise canceller is deactivated
x       | 1       | Input noise canceller is activated

### C Code Usage

```c
ISR(ICP1_vect) {
	//TODO: Add signal counting functionality here
}

int main() {
	
}
```

## ADC Interrupt
This triggers when the ADC has completed a conversion.

- ISR C Name: `ADC_vect`
- Mask Register: `ADIE` in `ADCSRA`
- Control Registers: None
- Flag Register: `ADIF` in `ADCSRA`

### C Code Usage

```c

static volatile uint16_t adc_val; 

ISR(ADC_vect) {
    adc_val = ADC;
}

int main() {
    while(1) {
        printf("ADC Value: %d", adc_val);
    }
}
```

## USART TX Complete Interrupt
NOTHING HERE

## USART RX Complete Interrupt
NOTHING HERE

## USART Data Register Empty Interrupt
NOTHING HERE
