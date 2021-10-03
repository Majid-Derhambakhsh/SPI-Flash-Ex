# SPI-Flash-Ex
The SPI Flash Extension library to merges a collection of flash memories as a memory bank in MCU

```diff  
- Notice : This library has not free in this time
```

## Release
- #### Version : 0.0.0

- #### Type : Embedded Software.

- #### Support :  
               - ARM LPC/STM32 series  

- #### Program Language : C/C++

- #### Properties :

- #### Changes :  

- #### Required Library/Driver :
	- SPI Flash library as base library : [Download](https://github.com/Majid-Derhambakhsh/SPI-Flash)  

## Overview 
### Initialization and de-initialization functions:
```c++
uint8_t SPI_FlashEx_Init(SPI_Flash_BankTypeDef *_flashBank);
``` 

### Operation functions:
```c++
/* :::::::::::::::::::: Flash Erase :::::::::::::::::::: */
uint8_t SPI_FlashEx_ChipErase(SPI_Flash_BankTypeDef *_flashBank);

uint8_t SPI_FlashEx_SectorErase(SPI_Flash_BankTypeDef *_flashBank, uint32_t _sector);

uint8_t SPI_FlashEx_BlockErase(SPI_Flash_BankTypeDef *_flashBank, uint32_t _block);

/* :::::::::::::::::::: Flash Write :::::::::::::::::::: */
uint8_t SPI_FlashEx_Write(SPI_Flash_BankTypeDef *_flashBank, uint8_t _data, uint32_t _address);

uint8_t SPI_FlashEx_PageWrite(SPI_Flash_BankTypeDef *_flashBank, uint8_t *_pData, uint32_t _page, uint32_t _offset, uint32_t _size);

uint8_t SPI_FlashEx_SectorWrite(SPI_Flash_BankTypeDef *_flashBank, uint8_t *_pData, uint32_t _sector, uint32_t _offset, uint32_t _size);

uint8_t SPI_FlashEx_BlockWrite(SPI_Flash_BankTypeDef *_flashBank, uint8_t* _pData, uint32_t _block, uint32_t _offset, uint32_t _size);

uint8_t SPI_FlashEx_BurstWrite(SPI_Flash_BankTypeDef *_flashBank, uint8_t *_pData, uint32_t _address, uint32_t _size);

/* :::::::::::::::::::: Flash Read ::::::::::::::::::::: */
uint8_t SPI_FlashEx_Read(SPI_Flash_BankTypeDef *_flashBank, uint8_t *_data, uint32_t _address);

uint8_t SPI_FlashEx_PageRead(SPI_Flash_BankTypeDef *_flashBank, uint8_t *_pData, uint32_t _page, uint32_t _offset, uint32_t _size);

uint8_t SPI_FlashEx_SectorRead(SPI_Flash_BankTypeDef *_flashBank, uint8_t *_pData, uint32_t _sector, uint32_t _offset, uint32_t _size);

uint8_t SPI_FlashEx_BlockRead(SPI_Flash_BankTypeDef *_flashBank, uint8_t *_pData, uint32_t _block, uint32_t _offset, uint32_t _size);

uint8_t SPI_FlashEx_BurstRead(SPI_Flash_BankTypeDef *_flashBank, uint8_t *_pData, uint32_t _address, uint32_t _size);

``` 

### Macros:
```diff  
None   
```

## Guide

#### This library can be used as follows:
#### 1.  Add SPI Flash library as a base library and config it
#### 2.  Add SPI Flash Extension library Header and Source file in your project    
#### 3.  Config the library in “spi_flash_ex_conf.h”, for example:     
  ```c++
  /* ----------- Configuration ------------ */
  #define _SPI_FLASH_EX_USE_LAST_WRITE_ADD
  ``` 
  
#### 4.  Create array of flash memory object, set specific GPIO, for example:           
* Example:
  ```c++  
  SPI_Flash_TypeDef   MainFlashList[3];

  MainFlashList[0].CS_GPIO_Port = 0;
  MainFlashList[0].CS_GPIO_Pin  = (1 << 2);
  
  MainFlashList[1].CS_GPIO_Port = 0;
  MainFlashList[1].CS_GPIO_Pin  = (1 << 3);
  
  MainFlashList[2].CS_GPIO_Port = 0;
  MainFlashList[2].CS_GPIO_Pin  = (1 << 4);
  
  ``` 
  
#### 5.  Create flash memory bank object, set FlashList, NumberOfChip, and initialize it, for example:  
* Initializer:
  ```c++
  SPI_FlashEx_Init(SPI_Flash_BankTypeDef *_flashBank);
  ``` 
* Parameters:  
     * _flashBank: pointer to flash bank struct
          
          
* Example:
  ```c++  
  Code of step 4 ...
  
  SPI_Flash_BankTypeDef   FlashBank;

  FlashBank.FlashList    = MainFlashList;
  FlashBank.NumberOfChip = 3;
  
  SPI_FlashEx_Init(&FlashBank);
  ``` 
  
#### 6.  Use flash memory extension operation functions  
```c++
SPI_FlashEx_ChipErase(&FlashBank);
SPI_FlashEx_SectorWrite(&FlashBank, Tx_Buf, 0, 10, 44);
```  
      
## Examples  

#### Example 1: Initialize and use external flash memory with LPC1768
```c++  
#include "lpc17xx.h"
#include "lpc17xx_gpio.h"
#include "lpc17xx_spi.h"
#include "lpc17xx_libcfg.h"
#include "lpc17xx_pinsel.h"

#include "lpc_spi_ex.h"
#include "spi_flash.h"

SPI_CFG_Type SPI_ConfigStruct;

uint8_t Tx_Buf[44] = "Hello from master!, this is an test program\n";
uint8_t Rx_Buf[50];

int main()
{
	
	SystemInit();
	
	/* ---------- Setup GPIO --------- */
	PINSEL_CFG_Type PinCfg;
	
	/*
	* Initialize SPI pin connect
	* P0.15 - SCK;
	* P0.0 / P0.1 - SSEL - used as GPIO
	* P0.17 - MISO
	* P0.18 - MOSI
	*/
	
	PinCfg.Funcnum   = 3;
	PinCfg.OpenDrain = 0;
	PinCfg.Pinmode   = 0;
	PinCfg.Portnum   = 0;
	PinCfg.Pinnum    = 15;
	PINSEL_ConfigPin(&PinCfg);
	
	PinCfg.Pinnum = 17;
	PINSEL_ConfigPin(&PinCfg);
	
	PinCfg.Pinnum = 18;
	PINSEL_ConfigPin(&PinCfg);
	
	/* Set GPIO Direction */
	GPIO_SetDir(0, (1 << 16), 1);
	GPIO_SetDir(0, (1 << 19), 1);
	GPIO_SetDir(0, (1 << 7), 1);
	
	GPIO_SetValue(0, (1 << 16));
	GPIO_SetValue(0, (1 << 19));
	GPIO_SetValue(0, (1 << 7));
	
	/* ---------- Setup SPI ---------- */
	SPI_ConfigStruct.CPHA      = SPI_CPHA_FIRST;
	SPI_ConfigStruct.CPOL      = SPI_CPOL_HI;
	SPI_ConfigStruct.ClockRate = 300000;
	SPI_ConfigStruct.DataOrder = SPI_DATA_MSB_FIRST;
	SPI_ConfigStruct.Databit   = SPI_DATABIT_8;
	SPI_ConfigStruct.Mode      = SPI_MASTER_MODE;
	
	SPI_Init(LPC_SPI, &SPI_ConfigStruct);

	/* --------- Wait to init --------- */
	SPI_Delay(1);

	/* ~~~~~~~~~~~~~~~~ Flash Bank Example ~~~~~~~~~~~~~~ */
	/* --------- Setup Flash ---------- */
	SPI_Flash_TypeDef       MainFlashList[3];
	SPI_Flash_BankTypeDef   FlashBank;
  
	MainFlashList[0].CS_GPIO_Port = 0;
	MainFlashList[0].CS_GPIO_Pin  = (1 << 16);
  
	MainFlashList[1].CS_GPIO_Port = 0;
	MainFlashList[1].CS_GPIO_Pin  = (1 << 19);
  
	MainFlashList[2].CS_GPIO_Port = 0;
	MainFlashList[2].CS_GPIO_Pin  = (1 << 7);
	
	FlashBank.FlashList    = MainFlashList;
	FlashBank.NumberOfChip = 3;
  
	/* ----------- Commands ----------- */
	SPI_FlashEx_Init(&FlashBank);

	SPI_FlashEx_ChipErase(&FlashBank);
	
	SPI_FlashEx_SectorWrite(&FlashBank, Tx_Buf, 0, 10, 44);
	SPI_FlashEx_SectorRead(&FlashBank, Rx_Buf, 0, 10, 44);
	
	while(1)
	{
		
	}
	/* Loop forever */
	
}
   
``` 

## Tests performed:
- [x] Run on LPC17 xx cores 
- [x] Run on STM32 Fx cores 

## Developers: 
- ### Majid Derhambakhsh
