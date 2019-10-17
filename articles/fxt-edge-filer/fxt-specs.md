---
title: Specifiche di Microsoft Azure FXT Edge Filer | Microsoft Docs
description: Specifiche fisiche e ambientali per l'hardware di Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 90d56ad0cec3d187d4c7307f8c004b14c3860390
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254703"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Specifiche di Azure FXT Edge Filer

Questo articolo illustra le specifiche hardware per i nodi hardware di Azure FXT Edge Filer. In pratica, tre o più nodi sono configurati insieme per fornire il sistema di cache in cluster.

## <a name="hardware-specifications"></a>Specifiche hardware

| Componente | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| Core CPU |  16 | 16 |
| DRAM  | 1536 GB | 768 GB |
| Porte di rete | 6 x 25/10 Gb + 2 x 1 Gb | 6 x 25/10 Gb + 2 x 1 Gb |
| Capacità dell'unità SSD NVMe | 25,6 TB | 12,8 TB |

## <a name="drive-specifications"></a>Specifiche delle unità

Il sistema dispone di dieci alloggiamenti per le unità, accessibili dalla parte anteriore. Ogni unità inserita è etichettata sulla destra con le informazioni sulla capacità. 

I numeri delle unità sono stampati nello spazio tra le unità. In Azure FXT Edge Filer, l'unità 0 è quella in alto a sinistra, mentre l'unità 1 è quella immediatamente al di sotto.

![Foto di un alloggiamento del disco rigido nello chassis FXT, con i numeri di unità e le etichette della capacità](media/fxt-drives-photo.png)

| Numeri di unità    |  Uso   |  Specifiche |
|------------------|--------|-----------------|
| 0, 1             | OS     | Unità SSD SATA da 480 GB |
| 2, 3, 4, 5, 6, 7, 8, 9 | Dati   | FXT 6600: unità SSD NVMe da 3,2 TB <br> FXT 6400: unità SSD NVMe da 1,6 TB |


## <a name="dimensions-and-weight"></a>Dimensioni e peso

Azure FXT Edge Filer è progettato per l'inserimento in un rack standard per apparecchiature da 19 pollici, con un'altezza di un'unità rack (1U). 

<!-- 10x2.5 inches version -->

| Dimensioni del filer           |                          |
|-----------------------------|--------------------------|
| Altezza:                      | 42,8 mm (1,68 pollici)    |
| Larghezza (incluse le staffe per il rack) | 482,0 mm (18,97 pollici)  |
| Larghezza - chassis principale      | 434,0 mm (17,08 pollici) |
| Profondità - dalle staffe per il rack alla parte posteriore dello chassis principale                   | 733,82 mm (29,61 pollici) |
| Profondità - dalle staffe per il rack alla massima sporgenza posteriore                 | 772,67 mm (30,42 pollici) |
| Profondità - dalle staffe per il rack alla massima sporgenza anteriore, senza cornice | 22,0 mm (0,87 pollici)  |
| Profondità - dalle staffe per il rack alla massima sporgenza anteriore, con cornice    | 35,84 mm (1,41 pollici) |

| Peso | |
|-----------------|----------------------|
| Peso del nodo (senza imballaggio, senza accessori) | 18,1 kg (40 libbre) |
| Peso netto (senza imballaggio, con accessori) | 23,1 kg (51 libbre)|
| Peso lordo (come spedito, incluso l'imballaggio) |  29,0 kg (64 libbre) |

### <a name="shipping-dimensions"></a>Dimensioni di spedizione

| Dimensioni del pacchetto | Millimetri | Pollici |
|-------------------|-------------|--------|
| Altezza:            | 311,2       | 12,25" |
| Larghezza             | 642,8       | 25,31" |
| Length            | 1\.051,1     | 41,38" |

## <a name="power-and-thermal-specifications"></a>Specifiche termiche e di alimentazione

Questa sezione contiene i valori nominali e le misurazioni di potenza per Azure FXT Edge Filer.

### <a name="nameplate-ratings"></a>Valori nominali

| Valori nominali per i modelli serie FXT 6000 |
|----------------|
| 100 - 240 V~    |
| 10 A - 5 A (X2)  |
| 50/60Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Misurazioni termiche e di alimentazione 

I nodi di Azure FXT Edge Filer usano ventole a velocità variabile, di conseguenza l'alimentazione dipende dalla temperatura e dal carico. Le velocità massime delle ventole possono essere raggiunte in determinate combinazioni di carico elevato e temperature ambiente elevate. 

Di seguito sono riportate le misurazioni del consumo energetico e dell'emissione termica per le combinazioni di tensione e frequenza usate più comunemente. 

| Potenza di FXT 6600 a temperatura ambiente <br />(22 °C, 71,6 °F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Tensione (V) | 100 | 120 | 208 | 230 | 240 | 
| Frequenza (Hz) | 60 | 60 | 60 | 50 | 50 |
| Corrente (A) | 5,02 | 4,16 |2,40 | 2,20 | 2.16 |
| Potenza apparente (VA) | 502 | 499 | 499 | 506 | 518|
| Fattore di potenza | 0,99 | 0,99 |0.98 | 0.98 | 0.98 |
| Potenza effettiva (W) | 497 |494 | 489 | 496 | 508 |
| Dissipazione termica (BTU/ora) |1696 | 1686 | 1669 | 1692 | 1733 |

| Potenza di FXT 6600 alle massime velocità delle ventole | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Tensione (V) | 100 |120 | 208 | 230 | 240| 
| Frequenza (Hz) | 60 | 60 | 60 | 50 | 50 |
| Corrente (A) | 5,98 | 5,01 | 2.81 | 2.55 | 2,48 |
| Potenza apparente (VA) | 598 | 601 | 584 | 587 | 595 |
| Fattore di potenza | 0,99 | 0,99 | 0.98 | 0.98 | 0.98 |
| Potenza effettiva (W) | 592 | 595 | 573 | 575 | 583 |
| Dissipazione termica (BTU/ora) | 2020 |2031 | 1954 | 1961 | 1990 |

| Potenza di FXT 6400 a temperatura ambiente <br />(22 °C, 71,6 °F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Tensione (V) | 100 | 120 | 208 | 230 | 240 |
| Frequenza (Hz) |60 | 60 | 60 | 50 | 50 |
| Corrente (A) | 4,63 | 3,86 | 2,24 | 2,04 | 1,94 |
| Potenza apparente (VA) | 463 | 463 | 466 | 469 | 466 |
| Fattore di potenza | 0,99 | 0,99 | 0.98 | 0.98 | 0.98 | 
| Potenza effettiva (W) | 458 | 459 | 457 | 460 | 456 |
| Dissipazione termica (BTU/ora) | 1564 | 1565 | 1558 | 1569 | 1557 |

| Potenza di FXT 6400 alle massime velocità delle ventole | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Tensione (V) | 100 | 120 | 208 | 230 | 240 |
| Frequenza (Hz) | 60 | 60 | 60 | 50 | 50 |
| Corrente (A) | 5.15 | 4,28 | 2,48 | 2,28 | 2,13 |
| Potenza apparente (VA) | 515 | 514 | 516 | 524 | 511 |
| Fattore di potenza | 0,99 | 0,99 | 0.98 | 0.98 | 0.98 |
| Potenza effettiva (W) | 510 | 508 | 506 | 514 | 501 |
| Dissipazione termica (BTU/ora) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Requisiti dell'ambiente

Questa sezione contiene le specifiche per l'ambiente dell'hardware.

### <a name="temperature-and-humidity"></a>Temperatura e umidità

| Caratteristica ambientale   | Intervallo operativo                   | Intervallo non operativo         |
|---------------------------|-----------------------------------|-----------------------------|
| Intervallo di temperatura ambientale | Da 10 °C a 35 °C (50 - 86 °F)          | Da -40 °C a 65 °C (-40 - 149 °F) |
| Umidità relativa all'ambiente | Dal 10% all'80% senza condensa          | Dal 5% al 95% senza condensa     |
| Punto di rugiada massimo         | 29°C (84°F)                       | 33 °C (91 °F)                 |
| Altitudine                  | fino a 3.048 metri (10.000 piedi), soggetto alla riduzione della temperatura specificata di seguito | fino a 12.000 metri (39.370 piedi) |

> [!NOTE] 
> **Riduzione della temperatura in base all'altitudine:** la temperatura massima si riduce di 1 °C/300 m (1 °F/547 piedi) sopra i 950 m (3.117 piedi).

### <a name="airflow-shock-and-vibration"></a>Flusso d'aria, urti e vibrazioni 

| Attributo         | Specifiche |
|-------------------|---------------|
| Flusso d'aria                    | Il flusso d'aria di sistema va dalla parte anteriore a quella posteriore. Il sistema deve essere usato con un'installazione a bassa pressione e scarico posteriore. |
| Scossa, operativo         | 6 G per 11 millisecondi (testato in 6 orientamenti) |
| Scossa, non operativo     | 71 G per 2 millisecondi (testato in 6 orientamenti) |
| Vibrazione, operativa     | 0,26 G<sub>RMS</sub> da 5 Hz a 350 Hz casuali         |
| Vibrazione, non operativa | 1,88 G<sub>RMS</sub> da 10 Hz a 500 Hz per 15 minuti (test di tutti e sei i lati)  |

## <a name="safety-regulation-compliance"></a>Conformità alle normative di sicurezza 

Azure FXT Edge Filer è conforme alle normative elencate. 

| Categoria       | Specifica della normativa | 
|----------------|--------------------------|
| Sicurezza generale | EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 <br>EN 62311:2008 | 
| EMC            | FCC A, ICES-003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (classe D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energia         | Regolamento della Commissione (UE) n. 617/2013  |
| RoHS           |    EN 50581:2012   |