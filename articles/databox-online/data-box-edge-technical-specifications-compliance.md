---
title: Bordo casella dei dati di Microsoft Azure su specifiche tecniche e conformità | Microsoft Docs
description: Informazioni sulle specifiche tecniche e conformità per i dati di Azure casella Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/12/2019
ms.author: alkohli
ms.openlocfilehash: 8ef35709e90c0a58cc0ff8df1afb6e864adc0a23
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2019
ms.locfileid: "57994708"
---
# <a name="azure-data-box-edge-technical-specifications-preview"></a>Azure specifiche tecniche bordo casella dei dati (anteprima)

I componenti hardware del dispositivo Edge casella dei dati di Microsoft Azure rispettano le specifiche tecniche e agli standard normativi descritti in questo articolo. Le specifiche tecniche descrivono le unità di alimentazione (PSU), capacità di archiviazione, le enclosure e standard dell'ambiente. 

> [!IMPORTANT]
> Data Box Edge è disponibile in anteprima. Rivedere le [condizioni del servizio per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) prima di ordinare e distribuire la soluzione. 

## <a name="power-supply-unit-specifications"></a>Specifiche unità di alimentazione Power

Il dispositivo perimetrale casella dei dati ha due 100-240 V unità di alimentazione (PSU) con i tifosi ad alte prestazioni. I due PSU fornire una configurazione di alimentazione ridondante. Se un PSU non riesce, il dispositivo continua a funzionare normalmente con l'altro PSU fino a quando non viene sostituito il modulo non riuscito. La tabella seguente elenca le specifiche tecniche del PSU.

| Specifiche           | 750 W PSU                  |
|-------------------------|----------------------------|
| Potenza massima in uscita    |  750 W                     |
| Frequenza               | 50/60 Hz                   |
| Selezione intervallo di voltaggio | Intervallo automatico: 100-240 V CA |
| Collegabile "hot"           | Sì                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>Specifiche di archiviazione

I dispositivi Edge finestra dati dispongono di 10 X 2.5" SSD NVMe, ognuno con una capacità di 1,6 TB. Di queste unità SSD, 2 sono dischi del sistema operativo e di altri 8 sono dischi dati. La capacità totale usabile per il dispositivo è all'incirca 12,5 TB. La tabella seguente include i dettagli per la capacità di archiviazione del dispositivo.

|     Specifiche                          |     Valore             |
|--------------------------------------------|-----------------------|
|    Numero di unità SSD     |    8                  |
|    Capacità della singola unità SSD                     |    1,6 TB             |
|    Capacità totale                          |    12.8 TB            |
|    Capacità utilizzabile totale*                  |    ~ 12.5 TB            |

**Parte dello spazio viene riservato per uso interno.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Specifiche su peso e dimensioni dello chassis

Nelle tabelle seguenti vengono elencate varie specifiche dello chassis per dimensioni e peso.

### <a name="enclosure-dimensions"></a>Dimensioni dello chassis

Nella tabella seguente vengono elencate le dimensioni dello chassis in millimetri e pollici.

|     Chassis     |     Millimetri     |     Pollici     |
|-------------------|---------------------|----------------|
|    Altezza:         |    304.8            |    12          |
|    Larghezza          |    660.4            |    26          |
|    profondità          |    1041.4           |    41          |

### <a name="enclosure-weight"></a>Peso chassis

Il pacchetto dispositivo pesa 66 servizi Location based. e richiede due persone per gestirla. Il peso del dispositivo dipende dalla configurazione dell'enclosure.

|     Chassis                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Peso totale tra le creazione di pacchetti       |    servizi Location based 66.          |
|    Peso del dispositivo                       |    servizi Location based 48.3.        |

## <a name="enclosure-environment-specifications"></a>Specifiche ambientali dello chassis

Questa sezione elenca le specifiche relative all'ambiente dello chassis, ad esempio temperatura e umidità, altitudine.

### <a name="temperature-and-humidity"></a>Temperatura e umidità

|     Chassis         |     Intervallo di temperatura ambientale     |     Umidità ambientale relativa     |     Punto di rugiada massimo     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operativo        |    10° C - 35° C (50° F - 86° F)         |    10% - 80% senza condensa.         |    29°C (84°F)            |
|    Non operativo    |    -40 ° C a 65 ° C (da-40 ° F - 149 ° F)     |    5% al 95% senza condensa.          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Flusso d'aria, altitudine, scosse, vibrazione, orientamento, sicurezza ed EMC

|     Chassis                           |     Specifiche operative                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Flusso d'aria                              |    Il flusso d'aria di sistema va dalla parte anteriore a quella posteriore. Il sistema deve essere utilizzato con un'installazione a bassa pressione e scarico posteriore. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Massimo altitudine, operativa        |    contatori 3048 (10.000 piedi) con temperatura operativo massimo determinato dal [temperatura di deprovisioning rating specifiche](#operating-temperature-de-rating-specifications).                                                                                |
|    Massimo altitudine, non operativa    |    12.000 (39,370 metri)                                                                                                                                                                                         |
|    Scossa, operativo                   |    6 G 11 millisecondi in 6 orientamenti                                                                                                                                                                         |
|    Scossa, non operativo               |    71 G per 2 millisecondi in 6 orientamenti                                                                                                                                                                           |
|    Vibrazione, operativa               |    G 0,26<sub>RMS</sub> 5 Hz per 350 Hz casuali                                                                                                                                                                                     |
|    Vibrazione, non operativa           |    1,88 G<sub>RMS</sub> 10 Hz fino a 500 Hz per 15 minuti (tutti i sei lati testati.)                                                                                                                                                  |
|    Orientamento e montaggio             |    montaggio rack da 19"                                                                                                                                                                                        |
|    Sicurezza e approvazioni                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014 / IEC 61000-3-2:2014 (classe D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energia             |    No. il regolamento (UE) Commission 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Temperatura di deprovisioning rating specifiche

|     Funzionamento della temperatura rating deprovisioning     |     Intervallo di temperatura ambientale                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Fino a 35 ° C (95°f)                       |    Temperatura massima è ridotto dal 1° C/300m (1° F/547 ft) sopra m 950 (3,117 ft).    |
|    35° C-40° C (95°f per 104° C)            |    Temperatura massima è ridotto dal 1° C/175 m (1° F/319 ft) sopra m 950 (3,117 ft).    |
|    40° C-45° C (104° F a 113° F)           |    Temperatura massima è ridotto dal 1° C/125 m (1° F/228 ft) sopra m 950 (3,117 ft).    |


## <a name="next-steps"></a>Passaggi successivi

- [Distribuire Azure Data Box Edge](data-box-edge-deploy-prep.md)
