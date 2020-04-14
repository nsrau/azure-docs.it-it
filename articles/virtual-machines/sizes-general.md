---
title: Dimensioni delle macchine virtuali di Azure - Scopo generale Documenti Microsoft
description: Elenca le diverse dimensioni generiche disponibili per le macchine virtuali in Azure. Elenca le informazioni sul numero di vCPU, dischi dati e schede di interfaccia di rete, nonché la velocità effettiva di archiviazione e la larghezza di banda di rete per le dimensioni in questa serie.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: fc263eb6fbe6c6402aaf529229bb7025f070b8d9
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269670"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali di utilizzo generico

Le dimensioni delle macchine virtuali di utilizzo generico offrono un rapporto CPU-memoria equilibrato. Soluzione ideale per test e sviluppo, database medio-piccoli e server Web con traffico da medio a ridotto. In questo articolo vengono fornite informazioni sulle offerte per l'elaborazione di uso generale.

- Le macchine virtuali [di serie Av2](av2-series.md) possono essere distribuite in diversi tipi di hardware e processori. Le VM serie A offrono configurazioni di memoria e prestazioni della CPU ideali per i carichi di lavoro di base, ad esempio quelli di sviluppo e test. La dimensione è limitata in base all'hardware per offrire prestazioni del processore coerenti per l'istanza in esecuzione, indipendentemente dall'hardware in cui è distribuita. Per determinare l'hardware fisico in cui viene distribuita questa dimensione, eseguire una query nell'hardware virtuale dall'interno della macchina virtuale. I casi d'uso di esempio includono server di sviluppo e test, server Web con poco traffico, database da piccoli a medi, modelli di prova e repository di codice.

  > [!NOTE]
  > Le macchine virtuali A8 – A11 sono previste per il ritiro il 3/2021. Per ulteriori informazioni, consultate Guida alla [migrazione HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

- [Serie B scoppiabile](sizes-b-series-burstable.md) Le macchine virtuali sono ideali per carichi di lavoro che non richiedono le prestazioni complete della CPU in modo continuo, ad esempio server Web, database di piccole dimensioni e ambienti di sviluppo e test. Questi carichi di lavoro hanno in genere requisiti relativi alle prestazioni che prevedono burst. La serie B offre a questi clienti la possibilità di acquistare dimensioni per la VM con prestazioni baseline in relazione al prezzo, che consentono all'istanza della VM di accumulare crediti quando la VM utilizza meno prestazioni di quelle base. Quando la VM ha accumulato crediti, può eseguire il burst rispetto alla baseline della VM, usando fino al 100% della CPU quando l'applicazione richiede prestazioni superiori per la CPU.

- [Le serie Dav4 e Dasv4](dav4-dasv4-series.md) sono nuove dimensioni che utilizzano il processore AMD 2.35Ghz EPYC<sup>TM</sup> 7452 in una configurazione multi-thread con una cache L3 fino a 256 MB che dedica 8 MB di tale cache L3 a ogni 8 core aumentando le opzioni dei clienti per l'esecuzione dei carichi di lavoro di uso generale. La serie Dav4 e la serie Dasv4 hanno le stesse configurazioni di memoria e disco della serie D & Dsv3.

- La [serie DCv2](dcv2-series.md) consente di proteggere la riservatezza e l'integrità dei dati e del codice durante l'elaborazione nel cloud pubblico. Queste macchine sono supportate dall'ultima generazione di processore Intel XEON E-2288G con tecnologia SGX. Con la tecnologia Intel Turbo Boost queste macchine possono raggiungere i 5,0 GHz. Le istanze della serie DCv2 consentono ai clienti di creare applicazioni sicure basate su enclave per proteggere il codice e i dati mentre sono in uso.

- [Serie Dv2 e Dsv2](dv2-dsv2-series.md) Le macchine virtuali, un follow-on della serie D originale, sono dotate di una CPU più potente e di una configurazione ottimale da CPU a memoria che le rende adatte per la maggior parte dei carichi di lavoro di produzione. La serie Dv2 è circa 35% più veloce rispetto alla serie D. La serie Dv2 viene eseguita sui processori Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 a 2,3 GHz (Broadwell) o Intel® Xeon® E5-2673 v3 da 2,4 GHz (Haswell) con Intel Turbo Boost Technology 2.0. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.

- [Serie Dv3 e Dsv3](dv3-dsv3-series.md) Le macchine virtuali vengono eseguite sui processori Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 da 2,3 GHz (Broadwell) o sui processori Intel® Xeon® E5-2673 v3 da 2,4 GHz (Haswell) in una configurazione con hyperthreading, fornendo una migliore proposizione di valore per i carichi di lavoro più adatti ai carichi di lavoro generici. La memoria è stata estesa (da ~3,5 GiB/vCPU a 4 GiB/vCPU) mentre i limiti di rete e dei dischi sono stati modificati in base al core per consentire l'allineamento con il passaggio all'hyperthreading. La serie Dv3 non ha più le dimensioni di VM ad alta memoria della serie D/Dv2, queste sono state spostate nella [serie Ev3 ed Esv3](ev3-esv3-series.md)ottimizzata per la memoria.

Esempi di casi d'uso della serie D includono applicazioni di livello enterprise, database relazionali, memorizzazione nella cache in memoria e analisi.

## <a name="other-sizes"></a>Altre dimensioni

- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
