---
title: Specifiche tecniche e conformità di Microsoft Azure Data Box Edge Documenti Microsoft
description: Informazioni sulle specifiche tecniche e sulla conformità per Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: b646ee9b727d5adf4ec1c8b5c769b3d8f5c0fc1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252039"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Specifiche tecniche di Azure Data Box Edge

I componenti hardware del dispositivo Microsoft Azure Data Box Edge rispettano le specifiche tecniche e gli standard normativi descritti in questo articolo. Le specifiche tecniche descrivono le unità di alimentazione (PSU), la capacità di stoccaggio, gli chassis e gli standard ambientali. 

## <a name="compute-memory-specifications"></a>Calcolo, specifiche di memoria

Il dispositivo Data Box Edge ha le seguenti specifiche per il calcolo e la memoria:

| Specifiche           | valore                  |
|-------------------------|----------------------------|
| CPU    | 2 Cpu x 10 core                     |
| Memoria              | 128 GB di RAM                  |


## <a name="fpga-specifications"></a>Specifiche FPGA

Un Field Programmable Gate Array (FPGA) è incluso in ogni dispositivo Data Box Edge che abilita gli scenari di Machine Learning (ML). 

| Specifiche           | valore                  |
|-------------------------|----------------------------|
| Fpga   | Intel Arria 10 <br> I modelli DNN (Deep Neural Network) disponibili sono gli stessi [supportati dalle istanze FPGA cloud.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure)| 


## <a name="power-supply-unit-specifications"></a>Specifiche dell'unità di alimentazione

Il dispositivo Data Box Edge dispone di due unità di alimentazione da 100-240 V (PSU) con ventole ad alte prestazioni. Le due PSU forniscono una configurazione di alimentazione ridondante. Se si verifica un errore in una PSU, il dispositivo continua a funzionare normalmente sull'altra PSU finché non viene sostituito il modulo in errore. La tabella seguente elenca le specifiche tecniche delle PSU.

| Specifiche           | 750 W PSU                  |
|-------------------------|----------------------------|
| Potenza massima in uscita    | 750 W                     |
| Frequenza               | 50/60 Hz                   |
| Selezione intervallo di voltaggio | Auto ranging: 100-240 V AC |
| Collegabile "hot"           | Sì                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="network-interface-specifications"></a>Specifiche dell'interfaccia di rete

Il dispositivo You Data Box Edge ha 6 interfacce di rete, PORT1- PORT6.

| Specifiche           | Descrizione                 |
|-------------------------|----------------------------|
|  Interfacce di rete    | 2 interfacce da 1 GbE: 1 per la gestione, non configurabile dall'utente, usata per la configurazione iniziale. L'altra interfaccia è configurabile dall'utente, può essere usata per il trasferimento dei dati ed è DHCP per impostazione predefinita. <br>2 interfacce da 25 GbE: utilizzabili anche come interfacce da 10 GbE. Queste interfacce dati possono essere configurate dall'utente come statiche o DHCP (impostazione predefinita). <br> 2 interfacce da 25 GbE: queste interfacce dati possono essere configurate dall'utente come statiche o DHCP (impostazione predefinita).                  |

## <a name="storage-specifications"></a>Specifiche di archiviazione

I dispositivi Data Box Edge dispongono di 9 unità SSD NVMe da 9, 2,5", ciascuno con una capacità di 1,6 TB. Di questi SSD, 1 è un disco del sistema operativo e gli altri 8 sono dischi dati. La capacità utilizzabile totale per il dispositivo è di circa 12,5 TB. Nella tabella seguente sono riportati i dettagli per la capacità di archiviazione del dispositivo.

|     Specifiche                          |     valore             |
|--------------------------------------------|-----------------------|
|    Numero di unità SSD     |    8                  |
|    Capacità della singola unità SSD                     |    1,6 TB             |
|    Capacità totale                          |    12,8 TB            |
|    Capacità utilizzabile totale*                  |    12,5 TB            |

**Alcuni spazi sono riservati per uso interno.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Specifiche su peso e dimensioni dello chassis

Nelle tabelle seguenti vengono elencate varie specifiche dello chassis per dimensioni e peso.

### <a name="enclosure-dimensions"></a>Dimensioni dello chassis

Nella tabella seguente vengono elencate le dimensioni dello chassis in millimetri e pollici.

|     Chassis     |     Millimetri     |     Pollici     |
|-------------------|---------------------|----------------|
|    Altezza:         |    44.45            |    1.75"          |
|    Larghezza          |    434.1           |    17.09"          |
|    Length          |    740.4           |    29.15"          |

Nella tabella seguente sono elencate le dimensioni del pacco di spedizione in millimetri e pollici.

|     Pacchetto     |     Millimetri     |     Pollici     |
|-------------------|---------------------|----------------|
|    Altezza:         |    311,2            |    12,25"          |
|    Larghezza          |    642,8          |    25,31"          |
|    Length          |   1.051,1          |    41,38"          |

### <a name="enclosure-weight"></a>Peso chassis

Il pacchetto del dispositivo pesa 66 libbre. e richiede due persone per gestirlo. Il peso del dispositivo dipende dalla configurazione dell'alloggiamento.

|     Chassis                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Peso totale incluso l'imballaggio       |    61 libbre.          |
|    Peso del dispositivo                       |    35 libbre.          |

## <a name="enclosure-environment-specifications"></a>Specifiche ambientali dello chassis

In questa sezione sono elencate le specifiche relative all'ambiente dell'involucro, ad esempio temperatura, umidità e altitudine.

### <a name="temperature-and-humidity"></a>Temperatura e umidità

|     Chassis         |     Intervallo di temperatura ambiente     |     Umidità relativa ambiente     |     Punto di rugiada massimo     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operativo        |    10oC - 35oC         |    10% - 80% non condensa.         |    29°C (84°F)            |
|    Non operativo    |    -40oC a 65oC (-40oF - 149oF)     |    5% - 95% non condensatore.          |    33 °C (91 °F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Flusso d'aria, altitudine, scosse, vibrazione, orientamento, sicurezza ed EMC

|     Chassis                           |     Specifiche operative                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Flusso d'aria                              |    Il flusso d'aria di sistema va dalla parte anteriore a quella posteriore. Il sistema deve essere utilizzato con un'installazione a bassa pressione e scarico posteriore. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Altitudine massima, operativa        |    3048 metri (10.000 piedi) con temperatura di esercizio massima de-rated determinato dalle specifiche di [de-valutazione](#operating-temperature-de-rating-specifications)della temperatura di funzionamento .                                                                                |
|    Altitudine massima, non operativa    |    12.000 metri (39.370 piedi)                                                                                                                                                                                         |
|    Scossa, operativo                   |    6 G per 11 millisecondi in 6 orientamenti                                                                                                                                                                         |
|    Scossa, non operativo               |    71 G per 2 millisecondi in 6 orientamenti                                                                                                                                                                           |
|    Vibrazione, operativa               |    0,26 G<sub>RMS</sub> da 5 Hz a 350 Hz casuali                                                                                                                                                                                     |
|    Vibrazione, non operativa           |    1,88 G<sub>RMS</sub> da 10 a 500 Hz per 15 minuti (tutti e sei i lati testati).                                                                                                                                                  |
|    Orientamento e montaggio             |    Montaggio a rack da 19"                                                                                                                                                                                        |
|    Sicurezza e approvazioni                 |    EN 60950-1:2006 -A1:2010 - A2:2013 - A11:2009 - A12:2011/IEC 60950-1:2005 ed2 - A1:2009 A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (classe D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energia             |    Regolamento della Commissione (UE) n. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Specifiche di de-valutazione della temperatura di funzionamento

|     De-valutazione della temperatura di esercizio     |     Intervallo di temperatura ambiente                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Fino a 35 gradi centigradi                       |    La temperatura massima è ridotta di 1oC/300 m (1F/547 piedi) sopra i 950 m.    |
|    Da 35 a 40gradi (da 95 a 104 gradi)            |    La temperatura massima è ridotta di 1oC/175 m (1F/319 ft) sopra i 950 m.    |
|    Da 40 a 45gradi (da 104 a 113 gradi)           |    La temperatura massima è ridotta di 1oC/125 m (1F/228 piedi) sopra i 950 m.    |


## <a name="next-steps"></a>Passaggi successivi

- [Distribuire Azure Data Box Edge](data-box-edge-deploy-prep.md)
