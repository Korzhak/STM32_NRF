[English](REAMDE.md) | **[Українська](README_UA.md)**

# Проект лічильника одиниць за допомогою передачі через NRF24 на базі STM32

## Що потрібно мати для роботи

**Елементи**

* 2х BluePill

* 2х NRF24l01

* 4 цифровий 7 сегментний індикатор з 74HC595 нижнім регістром

* 2х Кнопка мікровимикач SMD 4 контакту 6х6х11мм

* Переключатель SS-5GL

* Монолітний монтажний конденсатор `0.1uF`

* 3х Резистор `10kOm`

* 2х Резистор `2.2kOm`

* Мікросхема AT24C16N SOIC8

* 2х Плата заряду MH-CD42

* Вимикач перемикач без підсвічування RY106s

* 2x Тримач для батарей 18650 (2p)

* Макетна плата

**Програмна частина**

* ST-LINK V2 програматор для STM32

* STM32CubeIDE

**Бібліотеки**

* [NRF24l01](https://github.com/controllerstech/NRF24L01)

* [4 цифровий 7 сегментний індикатор з 74HC595 нижнім регістром](https://github.com/Korzhak/STM32_7Segment_74HC595_Library)

* [EEPROM for STM32](https://github.com/controllerstech/STM32/tree/034758ed85bfca74d367c31dec64c32939faecf3/EEPROM_STM32)

## Як налаштований проект

**RX**

1. Налаштування проекту:
   - `SYS` (Debug->Serial Wire)

   - `RCC` (HSE->Crystal/Ceramic Resonator)

   - `SPI1` (Mode->Full-Duplex Master)

   - `USART2` (Mode->Asynchronous)

   - `I2C` (I2C->I2C)
   ![](img/I2C.png)

   - `FREERTOS` (Interface->CMSIS_V2)
   ![](img/FREERTOS_CONFIG.png)

   - `GPIO->`
   ![](img/GPIO_RX.png)

   - `Clock Configuration->`
   ![](img/Clock_Configuration.png)

2. Під'єднання:

   - STM32BluePill до NRF24l01: `3.3V - VCC`, `GND - GND`, `PB0 - CE`, `PA7 - MOSI`, `PA6 - MISO`, `PA5 - SCK`, `PA4 - CSN`
   
   - STM32BluePill до LED: `5V - VCC`, `GND - GND`, `PA11 - SCLK`, `PA10 - RCLK`, `PA9 - DIO` 

   - STM32BluePill до AR24C16N: `VCC - 3.3V`, `GND,A0-A2,WP - GND`, `SCL - PB6`, `SDA - PB7`

   - STM32BluePill до BTN+: `BTN - PA8`, `GND with Resisitor - GND`, `VCC - VCC`

   - STM32BluePill до BTN-: `BTN - PA9`, `GND with Resisitor - GND`, `VCC - VCC`

   - STM32BluePill до MH-CD42: `OUT - 5V`, `GND - GND`, `BAT - Batbattery+`, `GND - Batbattery-`, `Power+ - Vin`, `Power- - GND` 

   ![](img/RX.bmp)

3. Переходимо в ***NRF24L01.c*** і робимо заміну на свої значення, якщо потрібно

```cpp
#define NRF24_CE_PORT   GPIOB // Вказуємо свій порт
#define NRF24_CE_PIN    GPIO_PIN_0 // Вказуємо свій пін

#define NRF24_CSN_PORT   GPIOA // Вказуємо свій порт 
#define NRF24_CSN_PIN    GPIO_PIN_4 // Вказуємо свій пін
```

4. Переходимо в ***main.c*** і робимо заміну на свої значення, якщо потрібно

```cpp
/* USER CODE BEGIN 2 */
  NRF24_Init();

  NRF24_RxMode(RxAddress, 10);

  NRF24_ReadAll(data);
 //                 SCLK PIN   SCLK Port   RCLK PIN   RCLK Port   DIO PIN   DIO PORT
  setUp(&ledStruct, GPIO_PIN_11, GPIOB, GPIO_PIN_13, GPIOB, GPIO_PIN_9, GPIOA); // замінюємо на свої значення 

  /* USER CODE END 2 */
```

5. Переходимо в ***EEPROM.c*** і робимо заміну на свої значення, якщо потрібно

```cpp
// Define the I2C
extern I2C_HandleTypeDef hi2c1;
#define EEPROM_I2C &hi2c1 // Підключити свій інтерфейс

// EEPROM ADDRESS (8bits)
#define EEPROM_ADDR 0xA0 // Ввести свій адрес. В залежності від підключення

// Define the Page Size and number of pages
#define PAGE_SIZE 64     // В байтах
#define PAGE_NUM  512    // Кількість сторінок
```

**TX**

1. Налаштування проекту:
   - `SYS` (Debug->Serial Wire)

   - `RCC` (HSE->Crystal/Ceramic Resonator)

   - `SPI1` (Mode->Full-Duplex Master)

   - `USART2` (Mode->Asynchronous)

   - `GPIO->`
   ![](img/GPIO_TX.png)

   - `Clock Configuration->`
   ![](img/Clock_Configuration.png)

2. Під'єднання:

   - STM32BluePill до NRF24l01: `3.3V - VCC`, `GND - GND`, `PB0 - CE`, `PA7 - MOSI`, `PA6 - MISO`, `PA5 - SCK`, `PA4 - CSN`
   
   - STM32BluePill до Макетної плати і SS-5GL: `3.3V - +`, `GND - -`, `PB1 - Макетна плата` 

   - STM32BluePill до MH-CD42: `OUT - 5V`, `GND - GND`, `BAT - Batbattery+`, `GND - Batbattery-`, `Power+ - Vin`, `Power- - GND` 

   ![](img/TX.bmp)

3. Переходимо в ***NRF24L01.c*** і робимо заміну на свої значення, якщо потрібно

```cpp
#define NRF24_CE_PORT   GPIOB // Вказуємо свій порт
#define NRF24_CE_PIN    GPIO_PIN_0 // Вказуємо свій пін

#define NRF24_CSN_PORT   GPIOA // Вказуємо свій порт 
#define NRF24_CSN_PIN    GPIO_PIN_4 // Вказуємо свій пін
```
