---
title: Specifiche tecniche e conformità per Microsoft Azure Stack Edge | Microsoft Docs
description: Informazioni sulle specifiche tecniche e sulla conformità per Azure Stack Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: 5a4ac2342ca36d83d1a579851c090c4713814c8d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652047"
---
# <a name="azure-stack-edge-technical-specifications"></a>Specifiche tecniche di Azure Stack Edge

I componenti hardware del dispositivo Microsoft Azure Stack Edge sono conformi alle specifiche tecniche e agli standard normativi descritti in questo articolo. Le specifiche tecniche descrivono le unità di alimentazione, la capacità di archiviazione, gli chassis e gli standard ambientali.

## <a name="compute-memory-specifications"></a>Specifiche di calcolo e memoria

Il dispositivo Azure Stack Edge presenta le specifiche seguenti per le risorse di calcolo e memoria:

| Specifiche           | valore                  |
|-------------------------|----------------------------|
| CPU    | 2 X 10 CPU core                     |
| Memoria              | 128 GB di RAM                  |

## <a name="fpga-specifications"></a>Specifiche FPGA

Un Field Programmable Gate Array (FPGA) è incluso in ogni dispositivo Azure Stack Edge che supporta gli scenari di Machine Learning (ML).

| Specifiche           | valore                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> I modelli di rete neurale profonda disponibili corrispondono a quelli [supportati dalle istanze FPGA cloud](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure).|

## <a name="power-supply-unit-specifications"></a>Specifiche per le unità di alimentazione

Il dispositivo Azure Stack Edge dispone di due unità di alimentazione da 100-240 V con ventole a prestazioni elevate. Le due unità forniscono una configurazione di alimentazione ridondante. In caso di guasto di un'unità, il dispositivo continua a funzionare normalmente con l'altra fino alla sostituzione del modulo guasto. La tabella seguente contiene le specifiche tecniche delle unità di alimentazione.

| Specifiche           | Unità di alimentazione da 750 W                  |
|-------------------------|----------------------------|
| Potenza massima in uscita    | 750 W                     |
| Frequenza               | 50/60 Hz                   |
| Selezione intervallo di voltaggio | Selezione automatica della tensione di alimentazione: CA 100-240 V |
| Collegabile "hot"           | Sì                        |

### <a name="azure-stack-edge-power-cord-specifications-by-region"></a>Specifiche del cavo di alimentazione di Azure Stack Edge per area

Il dispositivo Azure Stack Edge deve essere dotato di un cavo di alimentazione che varia a seconda dell'area di Azure.
Per le specifiche tecniche di tutti i cavi di alimentazione supportati, vedere [Specifiche del cavo di alimentazione di Azure Stack Edge per area](azure-stack-edge-technical-specifications-power-cords-regional.md).

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Azure Stack Edge device.-->

## <a name="network-interface-specifications"></a>Specifiche dell'interfaccia di rete

Il dispositivo Azure Stack Edge ha 6 interfacce di rete, da PORT1 a PORT6.

| Specifiche           | Descrizione                 |
|-------------------------|----------------------------|
|  Interfacce di rete    | 2 interfacce da 1 GbE: 1 per la gestione, non configurabile dall'utente, usata per la configurazione iniziale. L'altra interfaccia è configurabile dall'utente, può essere usata per il trasferimento dei dati ed è DHCP per impostazione predefinita. <br>2 interfacce da 25 GbE: utilizzabili anche come interfacce da 10 GbE. Queste interfacce dati possono essere configurate dall'utente come statiche o DHCP (impostazione predefinita). <br> 2 interfacce da 25 GbE: queste interfacce dati possono essere configurate dall'utente come statiche o DHCP (impostazione predefinita).                  |

## <a name="storage-specifications"></a>Specifiche di archiviazione

I dispositivi Azure Stack Edge sono dotati di 9 dischi SSD NVMe da 2,5", ognuno con una capacità di 1,6 TB. Di questi dischi SSD, 1 è un disco del sistema operativo e gli altri 8 sono dischi dati. La capacità utilizzabile totale del dispositivo è di circa 12,5 TB. La tabella seguente contiene i dettagli relativi alla capacità di archiviazione del dispositivo.

|     Specifiche                          |     valore             |
|--------------------------------------------|-----------------------|
|    Numero di unità SSD     |    8                  |
|    Capacità della singola unità SSD                     |    1,6 TB             |
|    Capacità totale                          |    12,8 TB            |
|    Capacità utilizzabile totale*                  |    ~ 12,5 TB            |

**Parte dello spazio è riservato per uso interno.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Specifiche su peso e dimensioni dello chassis

Nelle tabelle seguenti vengono elencate varie specifiche dello chassis per dimensioni e peso.

### <a name="enclosure-dimensions"></a>Dimensioni dello chassis

Nella tabella seguente vengono elencate le dimensioni dello chassis in millimetri e pollici.

|     Chassis     |     Millimetri     |     Pollici     |
|-------------------|---------------------|----------------|
|    Altezza:         |    44,45            |    1,75"          |
|    Larghezza          |    434,1           |    17,09"          |
|    Length          |    740,4           |    29,15"          |

Nella tabella seguente sono elencate le dimensioni dell'imballaggio di spedizione in millimetri e pollici.

|     Pacchetto     |     Millimetri     |     Pollici     |
|-------------------|---------------------|----------------|
|    Altezza:         |    311,2            |    12,25"          |
|    Larghezza          |    642,8          |    25,31"          |
|    Length          |   1\.051,1          |    41,38"          |

### <a name="enclosure-weight"></a>Peso chassis

L'imballaggio del dispositivo pesa quasi 28 kg e per maneggiarlo sono necessarie due persone. Il peso del dispositivo dipende dalla configurazione dello chassis.

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
|    Orientamento e montaggio             |    Montaggio su rack 19"                                                                                                                                                                                        |
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

- [Distribuire Azure Stack Edge](azure-stack-edge-deploy-prep.md)
