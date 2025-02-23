# Ultrasonic Security System using VSD Squadron Mini


## Working
This project is a simple security system using an ultrasonic sensor and a buzzer. It measures the distance to an object using the ultrasonic sensor. If the object is within 10 cm, the buzzer will turn ON as an alert.


## Components 
VSD Squadron Mini (CH32V)
HC-SR04 Ultrasonic Sensor
Buzzer
Jumper wires


### Source Code
```c
#include <stdio.h>
#include <ch32v00x_gpio.h>
#include <ch32v00x_rcc.h>
#include <ch32v00x_tim.h>
#include <ch32v00x_usart.h>
#include <stdint.h>

#define TRIG_PIN GPIO_Pin_0   // Trigger pin for ultrasonic sensor
#define ECHO_PIN GPIO_Pin_1   // Echo pin for ultrasonic sensor
#define BUZZER_PIN GPIO_Pin_2 // Buzzer pin

void delayMicroseconds(uint32_t us) {
    while (us--) {
        for (int i = 0; i < 8; i++) __asm__("nop");
    }
}

void setup() {
    // Enable Clocks
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1, ENABLE);
    RCC_APB1PeriphClockCmd(RCC_APB1Periph_TIM2, ENABLE);

    // Initialize GPIO for TRIG and BUZZER as Output
    GPIO_InitTypeDef GPIO_InitStructure;
    GPIO_InitStructure.GPIO_Pin = TRIG_PIN | BUZZER_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(GPIOC, &GPIO_InitStructure);

    // Initialize GPIO for ECHO as Input
    GPIO_InitStructure.GPIO_Pin = ECHO_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;
    GPIO_Init(GPIOC, &GPIO_InitStructure);

    // Initialize Timer2 for measuring pulse width
    TIM_TimeBaseInitTypeDef TIM_TimeBaseStructure;
    TIM_TimeBaseStructure.TIM_Period = 0xFFFF;
    TIM_TimeBaseStructure.TIM_Prescaler = SystemCoreClock / 1000000 - 1;
    TIM_TimeBaseStructure.TIM_ClockDivision = 0;
    TIM_TimeBaseStructure.TIM_CounterMode = TIM_CounterMode_Up;
    TIM_TimeBaseInit(TIM2, &TIM_TimeBaseStructure);
    TIM_Cmd(TIM2, ENABLE);

    // Initialize USART1 for Serial Communication
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_2; // TX Pin (PA2)
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(GPIOA, &GPIO_InitStructure);

    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_3; // RX Pin (PA3)
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;
    GPIO_Init(GPIOA, &GPIO_InitStructure);

    USART_InitTypeDef USART_InitStructure;
    USART_InitStructure.USART_BaudRate = 9600;
    USART_InitStructure.USART_WordLength = USART_WordLength_8b;
    USART_InitStructure.USART_StopBits = USART_StopBits_1;
    USART_InitStructure.USART_Parity = USART_Parity_No;
    USART_InitStructure.USART_HardwareFlowControl = USART_HardwareFlowControl_None;
    USART_InitStructure.USART_Mode = USART_Mode_Rx | USART_Mode_Tx;
    USART_Init(USART1, &USART_InitStructure);
    USART_Cmd(USART1, ENABLE);
}

uint32_t getDistance() {
    GPIO_ResetBits(GPIOC, TRIG_PIN);
    delayMicroseconds(2);
    GPIO_SetBits(GPIOC, TRIG_PIN);
    delayMicroseconds(10);
    GPIO_ResetBits(GPIOC, TRIG_PIN);

    while (GPIO_ReadInputDataBit(GPIOC, ECHO_PIN) == 0);

    TIM_SetCounter(TIM2, 0);
    while (GPIO_ReadInputDataBit(GPIOC, ECHO_PIN) == 1);
    uint32_t duration = TIM_GetCounter(TIM2);

    uint32_t distance = duration / 58;

    return distance;
}

void loop() {
    uint32_t distance = getDistance();

    printf("Distance: %lu cm\n", distance);

    if (distance > 0 && distanc



## PlatformIO Configuration
```ini
[env:vsdsquadronMini]
platform = ch32v
board = vsdsquadronMini
framework = noneos-sdk
upload_protocol = wch-link
monitor_port = COM5  ; Update to your COM port
monitor_speed = 9600
build_flags = -I$PROJECT_CORE_DIR/packages/framework-noneos-sdk/include

