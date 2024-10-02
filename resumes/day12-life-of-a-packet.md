# Day 12 - Life of a Packet

- Each interface on a network device has a different MAC Address

## Answers to the lab

### PC1 ping PC4

- **PC1 - SW1:** 
    - **src MAC:** Mac of PC1 
    - **dst MAC:** Mac of R1 G0/0
- **SW1 - R1:** 
    - **src MAC:** Mac of PC1 G0/1
    - **dst MAC:** Mac of R1 G0/0
- **R1 - R2:** 
    - **src MAC:** Mac of R1 G0/1
    - **dst MAC:** Mac of R2 G0/0
- **R2 - R3:** 
    - **src MAC:** Mac of R1 G0/1
    - **dst MAC:** Mac of R3 G0/0
- **R3 - SW2:** 
    - **src MAC:** Mac of R3 G0/1
    - **dst MAC:** Mac of PC4
- **SW2 - PC4:** 
    - **src MAC:** Mac of R3
    - **dst MAC:** Mac of PC4

### PC1 pings PC2
- **PC1 - SW1:** 
    - **src MAC:** Mac of PC1
    - **dst MAC:** Mac of PC3
- **SW1 - PC3:** 
    - **src MAC:** Mac of PC1
    - **dst MAC:** Mac of PC3

### PC4 ping PC1
- **PC4 - SW2:** 
    - **src MAC:** Mac of PC4
    - **dst MAC:** Mac of R3 G0/1
- **SW2 - R3:** 
    - **src MAC:** Mac of PC4
    - **dst MAC:** Mac of R3 G0/1
- **R3 - R2:** 
    - **src MAC:** Mac of R3 G0/0
    - **dst MAC:** Mac of R2 G0/1
- **R2 - R1:** 
    - **src MAC:** Mac of R2 G0/0
    - **dst MAC:** Mac of R1 G0/1
- **R1 - SW1:** 
    - **src MAC:** Mac of R1 G0/0
    - **dst MAC:** Mac of PC1
- **SW1 - PC1:** 
    - **src MAC:** Mac of R1 G0/0
    - **dst MAC:** Mac of PC1 