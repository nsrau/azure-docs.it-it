---
title: Specifiche tecniche e conformità di Microsoft Azure Stack Edge | Microsoft Docs
description: Informazioni sulle specifiche tecniche e sulla conformità per Azure Stack Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: 054f1449d6f06225633bf0647cac93ca42f2b59b
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82929010"
---
# <a name="azure-stack-edge-technical-specifications"></a>Specifiche tecniche di Azure Stack Edge

I componenti hardware del dispositivo Microsoft Azure Stack Edge rispettano le specifiche tecniche e gli standard normativi descritti in questo articolo. Le specifiche tecniche descrivono le unità di alimentazione (PSU), la capacità di archiviazione, le enclosure e gli standard ambientali. 

## <a name="compute-memory-specifications"></a>Specifiche di calcolo, memoria

Il dispositivo Azure Stack Edge presenta le specifiche seguenti per le risorse di calcolo e di memoria:

| Specifiche           | Value                  |
|-------------------------|----------------------------|
| CPU    | CPU 2 X 10 core                     |
| Memoria              | 128 GB di RAM                  |


## <a name="fpga-specifications"></a>Specifiche FPGA

Un FPGA (Field Programmable Gate Array) è incluso in ogni dispositivo Azure Stack Edge che Abilita Machine Learning (ML) scenari. 

| Specifiche           | Value                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> I modelli DNN (Deep Neural Network) disponibili sono gli stessi [di quelli supportati dalle istanze di FPGA cloud](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure).| 


## <a name="power-supply-unit-specifications"></a>Specifiche di unità alimentatore

Il dispositivo Azure Stack Edge dispone di due unità di alimentazione (PSU) 100-240 V con ventilatori a prestazioni elevate. I due PSU forniscono una configurazione di alimentazione ridondante. Se un alimentatore ha esito negativo, il dispositivo continua a funzionare normalmente sull'altro PSU fino a quando non viene sostituito il modulo non riuscito. La tabella seguente elenca le specifiche tecniche dei PSU.

| Specifiche           | PSU 750 W                  |
|-------------------------|----------------------------|
| Potenza massima in uscita    | 750 W                     |
| Frequenza               | 50/60 Hz                   |
| Selezione intervallo di voltaggio | Con intervallo automatico: 100-240 V AC |
| Collegabile "hot"           | Sì                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Azure Stack Edge device.-->

## <a name="network-interface-specifications"></a>Specifiche dell'interfaccia di rete

Il dispositivo Azure Stack Edge ha 6 interfacce di rete, PORT1-PORT6.

| Specifiche           | Descrizione                 |
|-------------------------|----------------------------|
|  Interfacce di rete    | 2 interfacce da 1 GbE: 1 per la gestione, non configurabile dall'utente, usata per la configurazione iniziale. L'altra interfaccia è configurabile dall'utente, può essere usata per il trasferimento dei dati ed è DHCP per impostazione predefinita. <br>2 interfacce da 25 GbE: utilizzabili anche come interfacce da 10 GbE. Queste interfacce dati possono essere configurate dall'utente come statiche o DHCP (impostazione predefinita). <br> 2 interfacce da 25 GbE: queste interfacce dati possono essere configurate dall'utente come statiche o DHCP (impostazione predefinita).                  |

## <a name="storage-specifications"></a>Specifiche di archiviazione

I dispositivi Azure Stack Edge hanno NVMe SSD 9 X 2,5 ", ognuno con una capacità di 1,6 TB. Di questi SSD, 1 è un disco del sistema operativo e gli altri 8 sono dischi dati. La capacità totale utilizzabile per il dispositivo è circa 12,5 TB. La tabella seguente contiene i dettagli relativi alla capacità di archiviazione del dispositivo.

|     Specifiche                          |     Value             |
|--------------------------------------------|-----------------------|
|    Numero di unità SSD     |    8                  |
|    Capacità della singola unità SSD                     |    1,6 TB             |
|    Capacità totale                          |    12,8 TB            |
|    Capacità utilizzabile totale*                  |    ~ 12,5 TB            |

**Alcuni spazi sono riservati per uso interno.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Specifiche su peso e dimensioni dello chassis

Nelle tabelle seguenti vengono elencate varie specifiche dello chassis per dimensioni e peso.

### <a name="enclosure-dimensions"></a>Dimensioni dello chassis

Nella tabella seguente vengono elencate le dimensioni dello chassis in millimetri e pollici.

|     Chassis     |     Millimetri     |     Pollici     |
|-------------------|---------------------|----------------|
|    Altezza:         |    44,45            |    1,75 "          |
|    Larghezza          |    434,1           |    17,09 "          |
|    Length          |    740,4           |    29,15 "          |

Nella tabella seguente sono elencate le dimensioni del pacchetto di spedizione in millimetri e pollici.

|     Pacchetto     |     Millimetri     |     Pollici     |
|-------------------|---------------------|----------------|
|    Altezza:         |    311,2            |    12,25"          |
|    Larghezza          |    642,8          |    25,31"          |
|    Length          |   1.051,1          |    41,38"          |

### <a name="enclosure-weight"></a>Peso chassis

Il pacchetto del dispositivo pesa 61 lbs. e richiede due persone per gestirlo. Il peso del dispositivo dipende dalla configurazione dell'enclosure.

|     Chassis                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Peso totale, inclusa la creazione di pacchetti       |    61 libbre.          |
|    Peso del dispositivo                       |    35 libbre.          |

## <a name="enclosure-environment-specifications"></a>Specifiche ambientali dello chassis

In questa sezione vengono elencate le specifiche relative all'ambiente dell'enclosure, ad esempio temperatura, umidità e altitudine.

### <a name="temperature-and-humidity"></a>Temperatura e umidità

|     Chassis         |     Intervallo temperatura ambiente     |     Umidità relativa ambiente     |     Punto di rugiada massimo     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operativo        |    10 ° C-35 ° C (50 ° F-86 ° F)         |    dal 10% al 80% senza condensa.         |    29°C (84°F)            |
|    Non operativo    |    da-40 ° a 65 ° c (-40 ° f-149 ° f)     |    5%-95% non condensato.          |    33 °C (91 °F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Flusso d'aria, altitudine, scosse, vibrazione, orientamento, sicurezza ed EMC

|     Chassis                           |     Specifiche operative                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Flusso d'aria                              |    Il flusso d'aria di sistema va dalla parte anteriore a quella posteriore. Il sistema deve essere utilizzato con un'installazione a bassa pressione e scarico posteriore. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Altitudine massima, operativa        |    3048 metri (10.000 piedi) con temperature operative massime denominate determinate dalle [specifiche di declassificazione della temperatura operativa](#operating-temperature-de-rating-specifications).                                                                                |
|    Altitudine massima, non operativa    |    12.000 metri (39.370 piedi)                                                                                                                                                                                         |
|    Scossa, operativo                   |    6 G per 11 millisecondi in 6 orientamenti                                                                                                                                                                         |
|    Scossa, non operativo               |    71 G per 2 millisecondi in 6 orientamenti                                                                                                                                                                           |
|    Vibrazione, operativa               |    0,26 G<sub>RMS</sub> da 5 Hz a 350 Hz casuali                                                                                                                                                                                     |
|    Vibrazione, non operativa           |    1,88 G<sub>RMS</sub> da 10 hz a 500 Hz per 15 minuti (tutti e sei i lati testati).                                                                                                                                                  |
|    Orientamento e montaggio             |    19 "montaggio su rack                                                                                                                                                                                        |
|    Sicurezza e approvazioni                 |    EN 60950-1:2006 + a1:2010 + a2:2013 + a11:2009 + A12:2011/IEC 60950-1:2005 ED2 + a1:2009 + a2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (classe D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energia             |    Regolamento della Commissione (UE) n. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Specifiche di declassificazione della temperatura operativa

|     Valutazione della temperatura operativa     |     Intervallo temperatura ambiente                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Fino a 35 ° c (95 ° f)                       |    La temperatura massima è ridotta di 1 ° c/300 m (1 ° f/547 ft) superiore a 950 m (3.117 ft).    |
|    da 35 ° c a 40 ° c (da 95 a 104 ° f)            |    La temperatura massima è ridotta di 1 ° c/175 m (1 ° f/319 ft) superiore a 950 m (3.117 ft).    |
|    da 40 a 45 ° c (da 104 a 113 ° f)           |    La temperatura massima è ridotta di 1 ° c/125 m (1 ° f/228 ft) superiore a 950 m (3.117 ft).    |


## <a name="next-steps"></a>Passaggi successivi

- [Distribuire il Azure Stack Edge](azure-stack-edge-deploy-prep.md)
