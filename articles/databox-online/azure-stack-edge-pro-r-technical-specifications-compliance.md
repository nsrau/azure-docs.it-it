---
title: Specifiche tecniche e conformità di Microsoft Azure Stack Edge Pro R | Microsoft Docs
description: Informazioni sulle specifiche tecniche e sulla conformità per il dispositivo R Azure Stack Edge Pro
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 56d301762fde41f727b0b425d6c41a423f08103c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467297"
---
# <a name="azure-stack-edge-pro-r-technical-specifications"></a>Specifiche tecniche di Azure Stack Edge Pro R

I componenti hardware del dispositivo R Azure Stack Edge Pro rispettano le specifiche tecniche descritte in questo articolo. Le specifiche tecniche descrivono le unità di alimentazione, la capacità di archiviazione, gli chassis e gli standard ambientali.


## <a name="compute-memory-specifications"></a>Specifiche di calcolo e memoria

Il dispositivo R Pro Azure Stack Edge presenta le specifiche seguenti per le risorse di calcolo e di memoria:

| Specifiche       | valore                  |
|---------------------|------------------------|
| CPU    | CPU 2 X 10 core, Intel Xeon Silver 4114 |
| Memoria              | 256 GB di RAM (2666 MT/s)     |


## <a name="compute-acceleration-specifications"></a>Specifiche di accelerazione di calcolo

Una GPU (Graphics Processing Unit) è inclusa in tutti i dispositivi che consentono scenari Kubernetes, Deep Learning e machine learning.

| Specifiche           | valore                  |
|-------------------------|----------------------------|
| GPU   | Una GPU nVidia T4 <br> Per ulteriori informazioni, vedere [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).| 

## <a name="power-supply-unit-specifications"></a>Specifiche per le unità di alimentazione

Il dispositivo R Pro Azure Stack Edge dispone di due unità di alimentazione (PSU) 100-240 V con ventilatori a prestazioni elevate. Le due unità forniscono una configurazione di alimentazione ridondante. In caso di guasto di un'unità, il dispositivo continua a funzionare normalmente con l'altra fino alla sostituzione del modulo guasto. La tabella seguente contiene le specifiche tecniche delle unità di alimentazione.

| Specifiche           | PSU 550 W                  |
|-------------------------|----------------------------|
| Potenza massima in uscita    | 550 W                      |
| Dissipazione termica (valore massimo)                   | 2891 BTU/HR                |
| Frequenza               | 50/60 Hz                   |
| Selezione intervallo di voltaggio | Con intervallo automatico: 115-230 V AC |
| Collegabile "hot"           | Sì                        |

## <a name="network-specifications"></a>Specifiche di rete

Il dispositivo R Azure Stack Edge Pro ha quattro interfacce di rete, PORT1-PORT4. 


|Specifiche  |Descrizione                              |
|----------------------|----------------------------------|
|Interfacce di rete    |**2 x 1 GbE RJ45** <br> La porta 1 viene utilizzata come interfaccia di gestione per l'installazione iniziale ed è statica per impostazione predefinita. Al termine dell'installazione iniziale, è possibile usare l'interfaccia per i dati con qualsiasi indirizzo IP. Tuttavia, in caso di reimpostazione, l'interfaccia torna all'indirizzo IP statico. <br>L'altra porta di interfaccia 2 è configurabile dall'utente, può essere usata per il trasferimento dei dati ed è DHCP per impostazione predefinita.     |
|Interfacce di rete    |**2 x 25 GbE SFP28** <br> Queste interfacce dati porta 3 e porta 4 possono essere configurate come DHCP (impostazione predefinita) o static.            |

Il dispositivo R Azure Stack Edge Pro dispone dei seguenti componenti hardware di rete:

* **Mellanox Dual Port 25 ConnectX-4 Channel Network Adapter** -Port 3 e Port 4. 

<!--Here are the details for the Mellanox card: MCX4421A-ACAN

| Parameter           | Description                 |
|-------------------------|----------------------------|
| Model    | ConnectX®-4 Lx EN network interface card                      |
| Model Description               | 25GbE dual-port SFP28; PCIe3.0 x8; ROHS R6                    |
| Device Part Number (XR2) | MCX4421A-ACAN  |
| PSID (R640)           | MT_2420110034                         |-->
<!-- confirm w/ Ravi what is this-->

Per un elenco completo dei cavi, dei commutatori e dei ricetrasmettitori supportati per queste schede di rete, vedere la pagina relativa ai [prodotti compatibili con la scheda di rete Mellanox Dual Port 25 ConnectX-4 Channel](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).

## <a name="storage-specifications"></a>Specifiche di archiviazione

I dispositivi R Azure Stack Edge Pro hanno 8 dischi dati e 2 M. 2 dischi SATA usati come dischi del sistema operativo. Per altre informazioni, vedere [M. 2 dischi SATA](https://en.wikipedia.org/wiki/M.2).

#### <a name="storage-for-1-node-device"></a>Archiviazione per un dispositivo a 1 nodo

Nella tabella seguente sono riportati i dettagli relativi alla capacità di archiviazione del dispositivo a 1 nodo.

|     Specifiche                          |     valore             |
|--------------------------------------------|-----------------------|
|    Numero di unità SSD     |    8                  |
|    Capacità della singola unità SSD                     |    8 TB               |
|    Capacità totale                          |    64 TB              |
|    Capacità utilizzabile totale*                  |    ~ 42 TB          |

**Parte dello spazio è riservato per uso interno.*

<!--#### Storage for 4-node device

The following table has the details for the storage capacity of the 4-node device.

|     Specification                          |     Value             |
|--------------------------------------------|-----------------------|
|    Number of solid-state drives (SSDs)     |    32 (4 X 8 disks for 4 devices)                |
|    Single SSD capacity                     |    8 TB               |
|    Total capacity                          |    256 TB              |
|    Total usable capacity*                  |    ~ 163 TB          |

**After mirroring and parity, and reserving some space for internal use.* -->


## <a name="enclosure-dimensions-and-weight-specifications"></a>Specifiche su peso e dimensioni dello chassis

Nelle tabelle seguenti vengono elencate varie specifiche dello chassis per dimensioni e peso.

### <a name="enclosure-dimensions"></a>Dimensioni dello chassis 

La tabella seguente elenca le dimensioni del dispositivo e il gruppo di continuità con la maiuscola e minuscola in millimetri e pollici.

|     Chassis     |     Millimetri     |     Pollici     |
|-------------------|---------------------|----------------|
|    Altezza:         |    301,2            |    11,86       |
|    Larghezza          |    604,5            |    23,80       |
|    Length         |    740,4            |    35,50       |

<!--#### For the 4-node system

For the 4-node system, the servers and the heater are shipped in a 5U case and the UPS are shipped in a 4U case.

The following table lists the dimensions of the 5U device case:  

|     Enclosure     |     Millimeters   |     Inches     |
|-------------------|-------------------|----------------|
|    Height         |    387.4          |    15.25       |
|    Width          |    604.5          |    23.80       |
|    Length         |    901.7          |    35.50       |

The following table lists the dimensions of the 4U UPS case: 

|     Enclosure     |     Millimeters   |     Inches    |
|-------------------|-------------------|---------------|
|    Height         |    342.9          |    13.5       |
|    Width          |    604.5          |   23.80       |
|    Length         |    901.7          |   35.50       |
-->

### <a name="enclosure-weight"></a>Peso chassis 

Il peso del dispositivo dipende dalla configurazione dello chassis.

|     Chassis                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Peso totale del dispositivo a 1 nodo + caso robusto con estremità finali     |    ~ 114 lbs.          |

<!--#### For the 4-node system

|     Enclosure                                 |     Weight          |
|-----------------------------------------------|---------------------|
|   Approximate weight of fully populated 4 devices + heater in 5U case     |    ~200 lbs.          |
|   Approximate weight of fully populated 4 UPS in 4U case    |    ~145 lbs.          |
-->

## <a name="enclosure-environment-specifications"></a>Specifiche ambientali dello chassis

In questa sezione vengono elencate le specifiche correlate all'ambiente dell'enclosure, ad esempio temperatura, vibrazione, shock e altitudine.


|     Specifiche              |     valore    |
|--------------------------------|-------------------------------------------------------------------|
|     Intervallo di temperature          |     0 – 43 ° C (operativo)    |
|     Vibrazione                  |     MIL-STD-810 Metodo 514,7 *<br>Procedura I CAT 4, 20                  |
|     Shock                      |     MIL-STD-810 metodo 516,7 *<br>Procedura IV, logistica                 |
|     Altitudine                   |     Operativo: 10.000 metri<br>Non operativo: 40.000 metri          |

**Tutti i riferimenti sono a MIL-STD-810G Change 1 (2014)*

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire Azure Stack Edge](azure-stack-edge-placeholder.md)
