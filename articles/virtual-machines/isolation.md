---
title: Isolamento per le macchine virtuali in Azure
description: Informazioni sull'isolamento delle macchine virtuali in Azure.
author: ayshakeen
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/18/2019
ms.author: ayshak
ms.openlocfilehash: 7732f83dfd44cbf686e2d076c665c980962b19d5
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292365"
---
# <a name="virtual-machine-isolation-in-azure"></a>Isolamento della macchina virtuale in Azure

Calcolo di Azure offre dimensioni delle macchine virtuali con piano Isolato per uno specifico tipo di hardware e dedicate a un singolo cliente. Le dimensioni isolate si trovano in un ambiente di generazione hardware specifico e verranno deprecate quando la generazione dell'hardware viene ritirata.

Le dimensioni delle macchine virtuali isolate sono ideali per i carichi di lavoro che richiedono un elevato livello di isolamento rispetto ai carichi di lavoro di altri clienti per motivi che includono requisiti di conformità e normativi.  L'utilizzo di dimensioni con piano Isolato garantisce che la macchina virtuale sia l'unica in esecuzione nella specifica istanza del server. 


Inoltre, poiché le macchine virtuali di dimensioni isolate sono di grandi dimensioni, i clienti possono scegliere di suddividere le risorse di queste macchine virtuali usando il [supporto di Azure per le macchine virtuali nidificate](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Le offerte di macchine virtuali con piano Isolato correnti includono:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_F72s_v2

> [!NOTE]
> Le dimensioni delle VM isolate hanno una durata limitata dell'hardware. Per informazioni dettagliate, vedere di seguito.

## <a name="deprecation-of-isolated-vm-sizes"></a>Deprecazione delle dimensioni delle VM isolate
Poiché le dimensioni delle VM isolate sono dimensioni associate a hardware, Azure fornirà ai promemoria 12 mesi prima della deprecazione ufficiale delle dimensioni.  Azure offrirà anche una dimensione isolata aggiornata nella prossima versione hardware che il cliente può prendere in considerazione per lo spostamento del carico di lavoro.

| Dimensione | Data di ritiro isolamento | 
| --- | --- |
| Standard_DS15_v2<sup>1</sup> | 15 maggio 2020 |
| Standard_D15_v2<sup>1</sup>  | 15 maggio 2020 |

<sup>1</sup> per informazioni dettagliate su Standard_DS15_v2 e Standard_D15_v2 programma di ritiro dell'isolamento, vedere Domande frequenti


## <a name="faq"></a>Domande frequenti
### <a name="q-is-the-size-going-to-get-retired-or-only-isolation-feature-is"></a>D: la dimensione verrà ritirata o solo la funzionalità "isolamento" è?
**R**: se le dimensioni della macchina virtuale non hanno l'indice "i", verrà ritirata solo la funzionalità di "isolamento". Se l'isolamento non è necessario, non viene eseguita alcuna azione e la macchina virtuale continuerà a funzionare come previsto. Alcuni esempi sono Standard_DS15_v2, Standard_D15_v2, Standard_M128ms e così via. Se le dimensioni della macchina virtuale includono pedice "i", le dimensioni verranno ritirate.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>D: si verifica un tempo di inattività quando la macchina virtuale si trova in un hardware non isolato?
**R**: se non è necessario l'isolamento, non è necessaria alcuna azione e non si verifica alcun tempo di inattività.

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>D: esiste una Delta di costo per il passaggio a una macchina virtuale non isolata?
**R**: No

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>D: quando le altre dimensioni isolate verranno ritirate?
**R**: verranno forniti promemoria 12 mesi prima della deprecazione ufficiale della dimensione isolata.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>D: sono un cliente di Azure Service Fabric che si basa sui livelli di durabilità Silver o Gold. Questa modifica ha effetto?
**R**: No. Le garanzie fornite dai livelli di [durabilità](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) di Service Fabric continueranno a funzionare anche dopo questa modifica. Se è necessario un isolamento hardware fisico per altri motivi, potrebbe essere comunque necessario eseguire una delle azioni descritte in precedenza. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>D: quali sono le attività cardine per D15_v2 o DS15_v2 il ritiro dell'isolamento? 
**A**: 
 
| Data | Azione |
|---|---| 
| 18 novembre 2019 | Disponibilità di D/DS15i_v2 (PAYG, 1 anno RI) | 
| 14 maggio 2020 | Ultimo giorno per l'acquisto di D/DS15i_v2 1 anno RI | 
| 15 maggio 2020 | Garanzia di isolamento D/DS15_v2 rimossa | 
| 15 maggio 2021 | Ritira D/DS15i_v2 (tutti i clienti ad eccezione di chi ha acquistato il RI 3 anni di D/DS15_v2 prima del 18 novembre 2019)| 
| 17 novembre 2022 | Ritira D/DS15i_v2 quando viene eseguito il RIs di 3 anni (per i clienti che hanno acquistato il RI di 3 anni di D/DS15_v2 prima del 18 novembre 2019) |

## <a name="next-steps"></a>Passaggi successivi

I clienti possono anche scegliere di suddividere ulteriormente le risorse di tali macchine virtuali con piano Isolato usando il [supporto di Azure per le macchine virtuali annidate](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).
