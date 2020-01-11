---
title: Microsoft Azure Data Box Edge specifiche tecniche e conformità | Microsoft Docs
description: Informazioni sulle specifiche tecniche e sulla conformità per la Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/26/2019
ms.author: alkohli
ms.openlocfilehash: 9e1a7f7cd2643aae61e60d77ad74f4a08266a977
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863598"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Azure Data Box Edge specifiche tecniche

The hardware components of your Microsoft Azure Data Box Edge device adhere to the technical specifications and regulatory standards outlined in this article. The technical specifications describe the Power supply units (PSUs), storage capacity, enclosures, and environmental standards. 

## <a name="compute-memory-specifications"></a>Compute, memory specifications

The Data Box Edge device has the following specifications for compute and memory:

| Specifiche           | Valore                  |
|-------------------------|----------------------------|
| CPU    | 2 X 10 core CPU                     |
| Memoria              | 128 GB di RAM                  |


## <a name="fpga-specifications"></a>FPGA specifications

A Field Programmable Gate Array (FPGA) is included on every Data Box Edge device that enables Machine Learning (ML) scenarios. 

| Specifiche           | Valore                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> Available Deep Neural Network (DNN) models are the same as those [supported by cloud FPGA instances](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure).| 


## <a name="power-supply-unit-specifications"></a>Power supply unit specifications

The Data Box Edge device has two 100-240 V Power supply units (PSUs) with high-performance fans. The two PSUs provide a redundant power configuration. If a PSU fails, the device continues to operate normally on the other PSU until the failed module is replaced. The following table lists the technical specifications of the PSUs.

| Specifiche           | 750 W PSU                  |
|-------------------------|----------------------------|
| Potenza massima in uscita    | 750 W                     |
| Frequenza               | 50/60 Hz                   |
| Selezione intervallo di voltaggio | Auto ranging: 100-240 V AC |
| Collegabile "hot"           | Sì                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>Specifiche di archiviazione

The Data Box Edge devices have 9 X 2.5” NVMe SSDs, each with a capacity of 1.6 TB. Of these SSDs, 1 is an operating system disk, and the other 8 are data disks. The total usable capacity for the device is roughly 12.5 TB. The following table has the details for the storage capacity of the device.

|     Specifiche                          |     Valore             |
|--------------------------------------------|-----------------------|
|    Numero di unità SSD     |    8                  |
|    Capacità della singola unità SSD                     |    1,6 TB             |
|    Capacità totale                          |    12,8 TB            |
|    Capacità utilizzabile totale*                  |    ~ 12.5 TB            |

**Some space is reserved for internal use.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Specifiche su peso e dimensioni dello chassis

Nelle tabelle seguenti vengono elencate varie specifiche dello chassis per dimensioni e peso.

### <a name="enclosure-dimensions"></a>Dimensioni dello chassis

Nella tabella seguente vengono elencate le dimensioni dello chassis in millimetri e pollici.

|     Chassis     |     Millimetri     |     Pollici     |
|-------------------|---------------------|----------------|
|    Altezza:         |    44.45            |    1.75"          |
|    Larghezza          |    434.1           |    17.09"          |
|    Length          |    740.4           |    29.15"          |

The following table lists the dimensions of the shipping package in millimeters and inches.

|     Pacchetto     |     Millimetri     |     Pollici     |
|-------------------|---------------------|----------------|
|    Altezza:         |    311,2            |    12,25"          |
|    Larghezza          |    642,8          |    25,31"          |
|    Length          |   1\.051,1          |    41,38"          |

### <a name="enclosure-weight"></a>Peso chassis

The device package weighs 66 lbs. and requires two persons to handle it. The weight of the device depends on the configuration of the enclosure.

|     Chassis                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Total weight including the packaging       |    61 lbs.          |
|    Weight of the device                       |    35 lbs.          |

## <a name="enclosure-environment-specifications"></a>Specifiche ambientali dello chassis

This section lists the specifications related to the enclosure environment such as temperature, humidity, and altitude.

### <a name="temperature-and-humidity"></a>Temperatura e umidità

|     Chassis         |     Ambient    temperature range     |     Ambient relative    humidity     |     Punto di rugiada massimo     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operativo        |    10°C - 35°C (50°F - 86°F)         |    10% - 80% non-condensing.         |    29°C (84°F)            |
|    Non operativo    |    -40°C to 65°C (-40°F - 149°F)     |    5% - 95% non-condensing.          |    33 °C (91 °F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Flusso d'aria, altitudine, scosse, vibrazione, orientamento, sicurezza ed EMC

|     Chassis                           |     Specifiche operative                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Flusso d'aria                              |    Il flusso d'aria di sistema va dalla parte anteriore a quella posteriore. Il sistema deve essere utilizzato con un'installazione a bassa pressione e scarico posteriore. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maximum altitude, operational        |    3048 meters (10,000 feet) with maximum operating temperature   de-rated determined by [Operating temperature de-rating specifications](#operating-temperature-de-rating-specifications).                                                                                |
|    Maximum altitude, non-operational    |    12,000 meters (39,370 feet)                                                                                                                                                                                         |
|    Scossa, operativo                   |    6 G for 11 milliseconds in 6   orientations                                                                                                                                                                         |
|    Scossa, non operativo               |    71 G for 2 milliseconds in 6 orientations                                                                                                                                                                           |
|    Vibrazione, operativa               |    0,26 G<sub>RMS</sub> da 5 Hz a 350 Hz casuali                                                                                                                                                                                     |
|    Vibrazione, non operativa           |    1.88 G<sub>RMS</sub> 10 Hz to 500 Hz for 15   minutes (all six sides tested.)                                                                                                                                                  |
|    Orientamento e montaggio             |    19" rack mount                                                                                                                                                                                        |
|    Sicurezza e approvazioni                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (classe D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energia             |    Regolamento della Commissione (UE) n. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Operating temperature de-rating specifications

|     Operating    temperature de-rating     |     Ambient    temperature range                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Up to 35°C (95°F)                       |    Maximum temperature is reduced by   1°C/300 m (1°F/547 ft) above 950 m (3,117 ft).    |
|    35°C to 40°C (95°F to 104°F)            |    Maximum temperature is reduced by   1°C/175 m (1°F/319 ft) above 950 m (3,117 ft).    |
|    40°C to 45°C (104°F to 113°F)           |    Maximum temperature is reduced by   1°C/125 m (1°F/228 ft) above 950 m (3,117 ft).    |


## <a name="next-steps"></a>Passaggi successivi

- [Distribuire Azure Data Box Edge](data-box-edge-deploy-prep.md)
