---
title: "Set di disponibilità per macchine virtuali Linux in Azure | Microsoft Docs"
description: "Informazioni sulle principali linee guida di progettazione e implementazione per la distribuzione dei set di disponibilità nei servizi di infrastruttura di Azure."
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 24f1d91c-8cc0-4251-bb67-ac4c4c37e8cd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: c5fad478a8fbbdeef2fe72f0b8f2ebe32852bbc5
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017


---
# <a name="azure-availability-sets-guidelines-for-linux-vms"></a>Linee guida per i set di disponibilità di Azure per macchine virtuali Linux

[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Questo articolo è incentrato sulla comprensione dei passaggi di pianificazione necessari per i set di disponibilità per garantire che le applicazioni rimangano accessibili in caso di eventi pianificati o non pianificati.

## <a name="implementation-guidelines-for-availability-sets"></a>Linee guida di implementazione per i set di disponibilità
Decisioni:

* Quanti set di disponibilità sono necessari per i diversi ruoli e livelli nell’infrastruttura dell'applicazione?

Attività:

* Definire il numero di macchine virtuali in ogni livello dell'applicazione desiderata.
* Determinare se è necessario modificare il numero di domini di errore o aggiornamento da usare per l'applicazione.
* Definire i set di disponibilità richiesti tramite la convenzione di denominazione scelta e le macchine virtuali che vi risiederanno. Una macchina virtuale può risiedere soltanto in un set di disponibilità. 

## <a name="availability-sets"></a>Set di disponibilità
Azure consente di inserire le macchine virtuali in un gruppo logico, definito set di disponibilità. Quando si creano macchine virtuali all'interno di un set di disponibilità, la piattaforma Azure ne distribuirà il posizionamento nell'infrastruttura sottostante. Nel caso di un evento di manutenzione pianificato sulla piattaforma di Azure o di un errore dell'hardware o dell'infrastruttura sottostante, l'uso di set di disponibilità assicura che almeno una macchina virtuale rimanga in esecuzione.

La procedura consigliata prevede che le applicazioni non risiedano in una singola macchina virtuale. Un set di disponibilità contenente un'unica macchina virtuale non può offrire alcuna protezione da eventi pianificati o non pianificati nella piattaforma Azure. Il [Contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines) richiede la presenza di due o più macchine virtuali nell'ambito di un set di disponibilità per consentire la distribuzione delle macchine virtuali nell'infrastruttura sottostante. Se si usa [Archiviazione Premium di Azure](../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), il Contratto di servizio di Azure si applica a un'unica macchina virtuale.

In Azure, l'infrastruttura sottostante è suddivisa in più cluster hardware. Ogni cluster hardware può supportare una gamma di dimensioni delle macchine virtuali. Un set di disponibilità può essere ospitato solo in un singolo cluster hardware in un determinato momento. L'intervallo di dimensioni delle macchine virtuali che possono esistere in un singolo set di disponibilità è quindi limitato all'intervallo di dimensioni delle macchine virtuali supportate dal cluster hardware. Il cluster hardware per il set di disponibilità è selezionato quando viene distribuita la prima macchina virtuale nel set o quando si avvia la prima macchina virtuale in un set di disponibilità dove tutte le macchine si trovano al momento nello stato arrestato-deallocato. È possibile usare il comando di interfaccia della riga di comando seguente per determinare l'intervallo di dimensioni delle macchine virtuali disponibili per un set di disponibilità: "az vm list-sizes --location \<string\>"

Ogni cluster hardware è suddiviso in più domini di aggiornamento e domini di errore. Tali domini sono definiti in base agli host che condividono un ciclo di aggiornamento comune o un'infrastruttura fisica simile, ad esempio quella di alimentazione e rete. Azure distribuirà automaticamente le macchine virtuali in un set di disponibilità nell'ambito dei domini per garantire la disponibilità e la tolleranza di errore. A seconda della dimensione dell'applicazione e del numero di macchine virtuali all'interno di un set di disponibilità, è possibile modificare il numero di domini che si desidera usare. Altre informazioni su [gestione della disponibilità e uso dei domini di aggiornamento e di errore](manage-availability.md).

Quando si progetta l'infrastruttura dell'applicazione, pianificare anche i livelli applicazione da usare. Raggruppare nei set di disponibilità le macchine virtuali che svolgono la stessa funzione, creando ad esempio un set di disponibilità per le macchine virtuali front-end che eseguono Apache o nginx. Creare un set di disponibilità distinto per le macchine virtuali back-end che eseguono MongoDB o MySQL. L'obiettivo è garantire che ogni componente dell'applicazione sia protetto da un set di disponibilità e che almeno un'istanza rimanga sempre in esecuzione.

In ogni livello di applicazione è possibile usare servizi di bilanciamento del carico a supporto di un set di disponibilità, per garantire che il traffico venga sempre indirizzato a un'istanza in esecuzione. Senza bilanciamento del carico, le macchine virtuali possono restare in esecuzione durante gli eventi di manutenzione pianificata e non pianificata, ma gli utenti finali potrebbero non essere in grado di risolvere i problemi se la macchina virtuale primaria non è disponibile.

Progettare l'applicazione per la disponibilità elevata a livello di archiviazione. La procedura consigliata consiste nell'[usare Managed Disks per le macchine virtuali in un set di disponibilità](manage-availability.md#use-managed-disks-for-vms-in-an-availability-set). Se si usano macchine virtuali con dischi non gestiti, è fortemente consigliabile [convertire le macchine virtuali nel set di disponibilità per l'uso di Managed Disks](convert-unmanaged-to-managed-disks.md#convert-vms-in-an-availability-set).

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]


