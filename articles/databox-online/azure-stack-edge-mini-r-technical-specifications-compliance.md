---
title: Specifiche tecniche e conformità di Microsoft Azure Stack Edge Mini R | Microsoft Docs
description: Informazioni sulle specifiche tecniche e sulla conformità per il dispositivo Mini R Azure Stack Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: e6dff06e92126e2fc4538273e229dcb0904e3101
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467453"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Specifiche tecniche di Azure Stack Edge Mini R

I componenti hardware del dispositivo Mini R di Microsoft Azure Stack Edge rispettano le specifiche tecniche e gli standard normativi descritti in questo articolo. Le specifiche tecniche descrivono CPU, memoria, unità di alimentazione, capacità di archiviazione, dimensioni enclosure e peso.


## <a name="compute-memory-specifications"></a>Specifiche di calcolo e memoria

Il dispositivo Mini R Azure Stack Edge presenta le specifiche seguenti per le risorse di calcolo e di memoria:

| Specifiche           | valore                  |
|-------------------------|------------------------|
| CPU    | CPU a 16 core, Intel Xeon-D 1577 |
| Memoria              | 48 GB di RAM (2400 MT/s)                  |


## <a name="compute-acceleration-specifications"></a>Specifiche di accelerazione di calcolo

Un'unità di elaborazione della visione (VPU) è inclusa in ogni dispositivo Mini R Azure Stack Edge che Abilita Kubernetes, reti neurali profonde e applicazioni basate su computer.

| Specifiche           | valore                  |
|-------------------------|------------------------|
| Scheda accelerazione calcolo         | Intel Movidius miriade X VPU <br> Per altre informazioni, vedere [Intel Movidius miriade X VPU](https://www.movidius.com/MyriadX) |


## <a name="storage-specifications"></a>Specifiche di archiviazione

Il dispositivo Mini R Azure Stack Edge ha 1 disco dati e 1 disco di avvio (che funge da archivio del sistema operativo). La tabella seguente illustra i dettagli relativi alla capacità di archiviazione del dispositivo.

|     Specifiche                          |     valore             |
|--------------------------------------------|-----------------------|
|    Numero di unità SSD     |    2 dischi da 1 TB <br> Un disco dati e un disco di avvio                  |
|    Capacità della singola unità SSD                     |    1 TB               |
|    Capacità totale (solo dati)              |    1 TB              |
|    Capacità utilizzabile totale*                  |    ~ 750 GB        |

**Parte dello spazio è riservato per uso interno.*

## <a name="network-specifications"></a>Specifiche di rete

Il dispositivo Mini R Azure Stack Edge presenta le specifiche seguenti per la rete:


|Specifiche  |valore  |
|---------|---------|
|Interfacce di rete    |2 x 10 GbE SFP + <br> Visualizzato come porta 3 e porta 4 nell'interfaccia utente locale           |
|Interfacce di rete    |2 x 1 GbE RJ45 <br> Visualizzato come porta 1 e porta 2 nell'interfaccia utente locale          |
|Wi-Fi   |802.11ac         |


## <a name="power-supply-unit-specifications"></a>Specifiche per le unità di alimentazione

Il dispositivo Mini R Azure Stack Edge include una scheda AC 85 W esterna per fornire energia e caricare la batteria.

La tabella seguente illustra le specifiche dell'unità alimentatore:

| Specifiche           | valore                      |
|-------------------------|----------------------------|
| Potenza massima in uscita    | 85 W                       |
| Frequenza               | 50/60 Hz                   |
| Selezione intervallo di voltaggio | Selezione automatica della tensione di alimentazione: CA 100-240 V |



## <a name="included-battery"></a>Batteria inclusa

Il dispositivo Mini R Azure Stack Edge include anche una batteria caricata dall'alimentatore. 

Per estendere l'uso del dispositivo tra gli addebiti, è possibile usare un tipo aggiuntivo di batteria 2590 insieme alla batteria di caricamento. Questa batteria deve essere conforme a tutte le normative di sicurezza, trasporto e ambiente applicabili al paese di uso.


| Specifiche           | valore                      |
|-------------------------|----------------------------|
| Carica Capacità batteria | 73 WHr                    |

## <a name="enclosure-dimensions-and-weight-specifications"></a>Specifiche su peso e dimensioni dello chassis

Nelle tabelle seguenti vengono elencate varie specifiche dello chassis per dimensioni e peso.

### <a name="enclosure-dimensions"></a>Dimensioni dello chassis

La tabella seguente elenca le dimensioni del dispositivo e l'USP con la maiuscola e minuscola in millimetri e pollici.

|     Chassis     |     Millimetri     |     Pollici     |
|-------------------|---------------------|----------------|
|    Altezza:         |    68            |    2.68          |
|    Larghezza          |    208          |      8,19          |
|    Length          |   259           |    10,20          |


### <a name="enclosure-weight"></a>Peso chassis

La tabella seguente elenca il peso del dispositivo, inclusa la batteria.

|     Chassis                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Peso totale del dispositivo     |    7 kg.          |

## <a name="enclosure-environment-specifications"></a>Specifiche ambientali dello chassis


In questa sezione sono elencate le specifiche relative all'ambiente dello chassis, come temperatura, umidità e altitudine.


|     Specifiche             |     Descrizione                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     Intervallo di temperature          |     0 – 43 ° C (operativo)                                              |
|     Vibrazione                  |     MIL-STD-810 Metodo 514,7 *<br> Procedura I CAT 4, 20                  |
|     Shock                      |     MIL-STD-810 metodo 516,7 *<br> Procedura IV, logistica                 |
|     Altitudine                   |     Operativo: 10.000 metri<br> Non operativo: 40.000 metri          |

**Tutti i riferimenti sono a MIL-STD-810G Change 1 (2014)*


## <a name="next-steps"></a>Passaggi successivi

- [Distribuire il mini R di Azure Stack Edge](azure-stack-edge-placeholder.md)
