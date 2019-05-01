---
title: Benchmark delle prestazioni per file di Azure NetApp | Microsoft Docs
description: Descrive i risultati dei test di benchmark delle prestazioni per file di NetApp Azure a livello di volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: aca0668fc364518fe45d9fe94d089ee366b25676
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870883"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Benchmark delle prestazioni per file di Azure NetApp

Questo articolo descrive i risultati dei test di benchmark delle prestazioni per file di NetApp Azure a livello di volume. 

## <a name="sample-application-used-for-the-tests"></a>Applicazione di esempio usata per i test

Test delle prestazioni sono stati eseguiti con un'applicazione di esempio usando i file di Azure NetApp. L'applicazione presenta le caratteristiche seguenti: 

* Un'applicazione basata su Linux creata per il cloud
* Può essere ridimensionato in modo lineare con aggiuntive macchine virtuali (VM) per aumentare la potenza di calcolo in base alle esigenze
* Richiede l'accesso facilitato rapido di data lake
* Include modelli dei / o che sono a volte talvolta sequenziale e casuale 
    * Un modello casuale richiede a bassa latenza per grandi quantità dei / o. 
    * Un modello sequenza richiede grandi quantità di larghezza di banda. 

## <a name="about-the-workload-generator"></a>Sul generatore di carico di lavoro

I risultati provengono dai file di riepilogo Vdbench. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) è un'utilità della riga di comando che genera i carichi di lavoro dei / o disco per la convalida delle prestazioni di archiviazione. La configurazione del client-server utilizzata è scalabile.  Include un singolo misto master/client e client dedicati 14 macchine virtuali.

## <a name="about-the-tests"></a>Sui test

I test sono stati progettati per identificare i limiti che potrebbe avere l'applicazione di esempio e il tempo di risposta che curve fino ai limiti.  

Sono stati eseguiti i test seguenti: 

* Lettura di 8 KB casuale 100%
* 100% 8 scrittura casuali KiB
* Lettura 64 KiB sequenziale del 100%
* Scrittura 100% 64 KiB sequenziale
* 50% 64 KiB sequenziali in lettura, scrittura 50% 64 KiB sequenziale
* 50%, 50% di lettura 8 KiB casuale scrittura casuali di 8 KB

## <a name="bandwidth"></a>Larghezza di banda

File di Azure NetApp offre più [livelli di servizio](azure-netapp-files-service-levels.md). Ogni livello di servizio offre una quantità diversa di larghezza di banda per ogni TiB di capacità con provisioning (la quota del volume). Il limite di larghezza di banda per un volume viene eseguito il provisioning in base alla combinazione del livello di servizio e la quota per il volume. Si noti che il limite di larghezza di banda è solo un fattore che determina la quantità effettiva di velocità effettiva che verrà realizzata.  

Attualmente, MiB 4.500 è la velocità effettiva massima che il risultato è stata ottenuta da un carico di lavoro in un singolo volume nel test.  Con il livello di servizio Premium, una quota di volume di TiB 70.31 verrà effettuato il provisioning di larghezza di banda sufficiente per realizzare questa velocità effettiva per il calcolo seguente: 

![Formula della larghezza di banda](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![A livello di quota e il servizio](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Carichi di lavoro a elevato utilizzo di velocità effettiva

Il test di velocità effettiva usato Vdbench e una combinazione di macchine virtuali di archiviazione di 12xD32s V3. Il volume di esempio del test di ottenuta i numeri di velocità effettiva seguenti:

![Test di velocità effettiva](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Carichi di lavoro dei / O intensivo

Il test dei / o usati Vdbench e una combinazione di macchine virtuali di archiviazione di 12xD32s V3. Il volume di esempio del test di ottenuta i numeri dei / o seguenti:

![Test dei / o](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latenza

La distanza tra le VM di test e il volume di file di NetApp Azure ha un impatto sulle prestazioni i/o.  Il grafico seguente confronta il numero di IOPS e curve di risposta della latenza per due diversi set di macchine virtuali.  Un set di macchine virtuali è in prossimità di file di Azure NetApp e l'altro è ulteriormente da subito.  Si noti che l'aumento della latenza per l'ulteriore set di macchine virtuali ha un impatto sulla quantità di operazioni IOPS ottenuta a un determinato livello di parallelismo.  Indipendentemente dal fatto che, operazioni di lettura di un volume può superare 300.000 IOPS, come illustrato di seguito: 

![Studio di latenza](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Riepilogo

I carichi di lavoro sensibili alla latenza (database) può avere un tempo di risposta un millisecondo. Le prestazioni transazionali possono essere oltre 300 KB IOPS per un singolo volume.

Le applicazioni sensibili alla velocità effettiva (per lo streaming e la creazione di immagini) possono avere 4.5GiB / s di velocità effettiva.
