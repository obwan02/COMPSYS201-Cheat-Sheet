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
These interrupts can detect falling edges, rising edges or a low level on pins INT0 and INT1.

- ISR C Name: `INT0_vect`
- Enable Mask: `INT0/INT1` in `EIMSK` 
- Control Register: `ISC0[1:0]/ISC1[1:0]` in `EICRA`
- Flag Register: `INTF0/INTF1` are set when the `INT0/INT1` interrupt occurs. 

### Control Bit Table
`ISCn0` | `ISCn1` | Description
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

- ISR C Name: `PCINT0_vect/PCINT1_vect/PCINT2_vect`
- Mask Register: `PCINT[7:0]` in `PCMSK0`, `PCINT[14:8]` in `PCMSK1` and `PCINT[23:16]` in `PCMSK2`.
- Control Register: `PCIE0/PCIE1/PCIE2` in `PCICR` 
- Flag Register: `PCIF0/PCIF1/PCIF2` are set when the `PCINT0/PCINT1/PCINT2` interrupt occurs. 


### Control Bit Table
`PCIEn` | Description
--------|--------------------
1       | Enable interrupts for `PCINTn`
 
### C Code Usage
```c
PCMSK1 |= (1 << PCINT12); // Detect changes on PCINT12
PCICR  |= (1 << PCEI1); // Enable interrupts for PCINT0  
```