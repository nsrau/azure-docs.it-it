---
title: Microsoft Azure Stack Edge Pro con le specifiche tecniche e la conformità della GPU | Microsoft Docs
description: Informazioni sulle specifiche tecniche e sulla conformità per il dispositivo Azure Stack Edge Pro con GPU
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: alkohli
ms.openlocfilehash: 225cb9a31b73f330d8b4ed5790caacc4fa729477
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839946"
---
# <a name="technical-specifications-and-compliance-for-azure-stack-edge-pro-with-gpu"></a>Specifiche tecniche e conformità per Azure Stack Edge Pro con GPU 

I componenti hardware di Azure Stack Edge Pro con una GPU (Graphics Processing Unit) onboard rispettano le specifiche tecniche e gli standard normativi descritti in questo articolo. Le specifiche tecniche descrivono hardware, unità di alimentazione (PSU), capacità di archiviazione, enclosure e standard ambientali.

## <a name="compute-and-memory-specifications"></a>Specifiche di calcolo e memoria

Il dispositivo Pro Azure Stack Edge presenta le specifiche seguenti per le risorse di calcolo e di memoria:

| Specifiche           | valore                  |
|-------------------------|----------------------------|
| CPU                     | 2 X CPU Intel Xeon Silver 4214 (Cascade Lake)            |
| Memoria                  | 128 (8x16 GB) GB di RAM                     |


## <a name="compute-acceleration-specifications"></a>Specifiche di accelerazione di calcolo

Una GPU (Graphics Processing Unit) è inclusa in ogni dispositivo Azure Stack Edge Pro che consente scenari Kubernetes, Deep Learning e machine learning.

| Specifiche           | valore                  |
|-------------------------|----------------------------|
| GPU   | Una o due GPU nVidia T4 <br> Per ulteriori informazioni, vedere [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).| 


## <a name="power-supply-unit-specifications"></a>Specifiche per le unità di alimentazione

Il dispositivo Pro Azure Stack Edge dispone di due unità di alimentazione (PSU) 100-240 V con ventilatori a prestazioni elevate. Le due unità forniscono una configurazione di alimentazione ridondante. In caso di guasto di un'unità, il dispositivo continua a funzionare normalmente con l'altra fino alla sostituzione del modulo guasto. La tabella seguente contiene le specifiche tecniche delle unità di alimentazione.

| Specifiche           | Unità di alimentazione da 750 W                  |
|-------------------------|----------------------------|
| Potenza massima in uscita    | 750 W                     |
| Frequenza               | 50/60 Hz                   |
| Selezione intervallo di voltaggio | Selezione automatica della tensione di alimentazione: CA 100-240 V |
| Collegabile "hot"           | Sì                        |


## <a name="network-interface-specifications"></a>Specifiche dell'interfaccia di rete

Il dispositivo Azure Stack Edge Pro ha sei interfacce di rete, PORT1-PORT6.

| Specifiche           | Descrizione                 |
|-------------------------|----------------------------|
|  Interfacce di rete    | **2 interfacce X 1 GbE** : 1 la porta 1 dell'interfaccia di gestione viene usata per l'installazione iniziale ed è statica per impostazione predefinita. Al termine dell'installazione iniziale, è possibile usare l'interfaccia per i dati con qualsiasi indirizzo IP. Tuttavia, in caso di reimpostazione, l'interfaccia torna all'indirizzo IP statico. <br>L'altra porta di interfaccia 2 è configurabile dall'utente, può essere usata per il trasferimento dei dati ed è DHCP per impostazione predefinita. <br>**4 interfacce X 25 GbE** : queste interfacce dati, porta da 3 a porta 6, possono essere configurate dall'utente come DHCP (impostazione predefinita) o static. Possono anche funzionare come interfacce da 10 GbE.  | 

Il dispositivo Azure Stack Edge Pro dispone dei seguenti componenti hardware di rete:

* **Scheda Microsoft QLogic Cavium 25 NDC personalizzata** - Dalla porta 1 alla porta 4.
* **Scheda di rete Mellanox Dual Port 25G ConnectX-4** -Porta 5 e porta 6.

Ecco i dettagli per la scheda Mellanox:

| Parametro           | Descrizione                 |
|-------------------------|----------------------------|
| Modello    | Scheda di interfaccia di rete ConnectX®-4 LX                      |
| Descrizione modello               | 25GbE Dual-Port SFP28; PCIe 3.0 x8; ROHS R6                    |
| Numero di parte del dispositivo (r640) | MCX4121A-ACAT  |
| PSID (R640)           | MT_2420110034                         |

Per un elenco completo dei cavi, dei commutatori e dei ricetrasmettitori supportati per tali schede di rete, vedere:

- [Qlogic Cavium 25G NDC adapter interoperability matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) (Matrice di interoperabilità della scheda Qlogic Cavium 25G NDC).
- [Mellanox dual port 25G ConnectX-4 channel network adapter compatible products](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products) (Prodotti compatibili con la scheda di rete Mellanox Dual Port 25G ConnectX-4).  

## <a name="storage-specifications"></a>Specifiche di archiviazione

I dispositivi Pro Azure Stack Edge hanno cinque SSD 2,5 "NVMe DC P4610", ognuno con una capacità di 1,6 TB. L'unità di avvio è un'unità SSD SATA da 240 GB. La capacità totale utilizzabile per il dispositivo è circa 4,19 TB. La tabella seguente elenca la capacità di archiviazione del dispositivo.

|     Specifiche                          |     valore             |
|--------------------------------------------|-----------------------|
|    Numero di unità SSD NVMe                     |    5                  |
|    Capacità SSD NVMe singola                |    1,6 TB             |
|    Unità SSD (Solid-State) SATA di avvio      |    1                  |
|    Capacità SSD di avvio                       |    240 GB             |
|    Capacità totale                          |    8 TB             |
|    Capacità utilizzabile totale*                  |    ~ 4,19 TB          |
|    Controller SAS                          |    HBA330 12 Gbps     |


**Dopo la resilienza di parità e riservando spazio per uso interno.*

<!--Remove based on feedback from Ravi
## Other hardware specifications

Your Azure Stack Edge Pro device also contains the following hardware:

* iDRAC baseboard management
* Performance fans
* Custom ID module-->

## <a name="enclosure-dimensions-and-weight-specifications"></a>Specifiche su peso e dimensioni dello chassis

Nelle tabelle seguenti vengono elencate varie specifiche dello chassis per dimensioni e peso.

### <a name="enclosure-dimensions"></a>Dimensioni dello chassis

La tabella seguente elenca le dimensioni dell'enclosure del dispositivo 1U in millimetri e pollici.

|     Chassis     |     Millimetri     |     Pollici     |
|-------------------|---------------------|----------------|
|    Altezza:         |    44,45            |    1,75"       |
|    Larghezza          |    434,1            |    17,09"      |
|    Length         |    740,4            |    29,15"      |

Nella tabella seguente sono elencate le dimensioni dell'imballaggio di spedizione in millimetri e pollici.

|     Pacchetto     |     Millimetri     |     Pollici     |
|-------------------|---------------------|----------------|
|    Altezza:         |    311,2            |    12,25"          |
|    Larghezza          |    642,8            |    25,31"          |
|    Length         |    1\.051,1          |    41,38"          |

### <a name="enclosure-weight"></a>Peso chassis

Il pacchetto del dispositivo pesa 66 lbs. e per maneggiarlo sono necessarie due persone. Il peso del dispositivo dipende dalla configurazione dello chassis.

|     Chassis                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Peso totale incluso l'imballaggio       |    ~ 28 kg (61 libbre)          |
|    Peso del dispositivo                       |    ~ 16 kg (35 libbre)          |

## <a name="enclosure-environment-specifications"></a>Specifiche ambientali dello chassis

In questa sezione sono elencate le specifiche relative all'ambiente dello chassis, come temperatura, umidità e altitudine.

### <a name="temperature-and-humidity"></a>Temperatura e umidità

|     Chassis         |     Intervallo di temperatura ambientale     |     Umidità relativa ambientale     |     Punto di rugiada massimo     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operativo        |    Da 10 °C a 35 °C (50 °F - 86 °F)         |    Dal 10% al 80% senza condensa         |    29°C (84°F)            |
|    Non operativo    |    Da -40 °C a 65 °C (-40 °F - 149 °F)     |    Dal 5% al 95% senza condensa          |    33 °C (91 °F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Flusso d'aria, altitudine, scosse, vibrazione, orientamento, sicurezza ed EMC

|     Chassis                           |     Specifiche operative                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Flusso d'aria                              |    Il flusso d'aria di sistema va dalla parte anteriore a quella posteriore. Il sistema deve essere utilizzato con un'installazione a bassa pressione e scarico posteriore. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Altitudine massima, operativa        |    3\.048 metri (10.000 piedi) con temperatura operativa massima ridotta in base a quanto determinato dalle [specifiche di riduzione della temperatura operativa](#operating-temperature-de-rating-specifications).                                                                                |
|    Altitudine massima, non operativa    |    12.000 metri (39.370 piedi)                                                                                                                                                                                         |
|    Scossa, operativo                   |    6 G per 11 millisecondi in 6 orientamenti                                                                                                                                                                         |
|    Scossa, non operativo               |    71 G per 2 millisecondi in 6 orientamenti                                                                                                                                                                           |
|    Vibrazione, operativa               |    0,26 G<sub>RMS</sub> da 5 Hz a 350 Hz casuali                                                                                                                                                                                     |
|    Vibrazione, non operativa           |    1,88 G<sub>RMS</sub> da 10 Hz a 500 Hz per 15 minuti (test di tutti e sei i lati)                                                                                                                                                  |
|    Orientamento e montaggio             |    Montaggio su rack standard 19 (1U)                                                                                                                                                                                       |
|    Sicurezza e approvazioni                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (classe D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energia             |    Regolamento della Commissione (UE) n. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>Specifiche di riduzione della temperatura operativa

|     Riduzione della temperatura operativa     |     Intervallo di temperatura ambientale                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Fino a 35 °C (95 °F)                       |    La temperatura massima si riduce di 1 °C/300 m (1 °F/547 piedi) sopra i 950 m (3.117 piedi).    |
|    Da -35 °C a 40 °C (-95 °F - 104 °F)            |    La temperatura massima si riduce di 1 °C/175 m (1 °F/319 piedi) sopra i 950 m (3.117 piedi).    |
|    Da -40 °C a 45 °C (-104 °F - 113 °F)           |    La temperatura massima si riduce di 1 °C/125 m (1 °F/228 piedi) sopra i 950 m (3.117 piedi).    |

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)
