---
title: Linee guida sulle macchine virtuali Linux in Azure | Documentazione Microsoft
description: Informazioni sulle principali linee guida di progettazione e implementazione per la distribuzione di macchine virtuali Linux in Azure.
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 740767d7-9a40-407b-93e7-c29dd976ffd7
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: a99ab839ec9ade0049e1cc59054e333048e0208c
ms.openlocfilehash: 80044c0b16a16a214b4d8879a2d7f38012608ba0


---
# <a name="azure-virtual-machines-guidelines-for-linux"></a>Linee guida sulle macchine virtuali di Azure per Linux
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Questo articolo è incentrato sulla comprensione dei passaggi di pianificazione necessari per la creazione e la gestione di macchine virtuali all'interno dell'ambiente di Azure.

## <a name="implementation-guidelines-for-vms"></a>Linee guida per l'implementazione di macchine virtuali
Decisioni:

* Qual è il numero di macchine virtuali necessario per i vari componenti e livelli di applicazione dell'infrastruttura?
* Di quali risorse necessita una macchina virtuale in termini di CPU e di memoria, e quali sono i requisiti di archiviazione?

Attività:

* Definire i carichi di lavoro per l'applicazione e le risorse richieste dalla macchina virtuale.
* Adeguare le richieste di risorse per ogni macchina virtuale alle dimensioni e al tipo di archiviazione appropriati per la macchina virtuale.
* Definire i gruppi di risorse dei diversi livelli e componenti dell'infrastruttura.
* Definire la convenzione di denominazione della macchina virtuale.
* Creare le macchine virtuali tramite l'interfaccia della riga di comando di Azure, il portale Web o i modelli di Resource Manager.

## <a name="virtual-machines"></a>Macchine virtuali
Una delle principali risorse all'interno dell'ambiente Azure è probabilmente costituita dalle VM. In questa risorsa vengono infatti eseguiti database, applicazioni, servizi di autenticazione e così via.

È importante comprendere le [diverse dimensioni delle macchine virtuali](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) poiché questo consente di dimensionare correttamente l'ambiente dal punto di vista dei costi e delle prestazioni. Se le macchine virtuali non hanno una quantità sufficiente di core CPU o di memoria, le prestazioni dell'applicazione ne risentono indipendentemente da come la stessa è stata progettata e sviluppata. Esaminare i carichi di lavoro suggeriti per ogni serie di macchine virtuali quale punto di partenza per stabilire le dimensioni della macchina virtuale da usare per ogni componente dell'infrastruttura. Dopo la distribuzione sarà possibile [modificare le dimensioni della macchina virtuale](virtual-machines-linux-change-vm-size.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

L'archiviazione svolge un ruolo fondamentale nelle prestazioni della macchina virtuale. È possibile usare l'archiviazione standard che utilizza dischi in rotazione normali oppure l'archiviazione Premium per carichi di lavoro I/O elevati e prestazioni massime, che usa dischi SSD. Come per le dimensioni della macchina virtuale, quando si tratta di selezionare il supporto di archiviazione si devono considerare anche i costi. È possibile leggere le [linee guida sull'infrastruttura di archiviazione](virtual-machines-linux-infrastructure-storage-solutions-guidelines.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per comprendere come progettare l'archiviazione appropriata ai fini dell'ottimizzazione delle prestazioni delle macchine virtuali.

## <a name="resource-groups"></a>Gruppi di risorse
Componenti come le VM vengono raggruppati logicamente per facilitare la gestione e la manutenzione usando i [gruppi di risorse di Azure](../azure-resource-manager/resource-group-overview.md). L'uso dei gruppi di risorse consente di creare, gestire e monitorare tutte le risorse che costituiscono una determinata applicazione. È anche possibile implementare [i controlli degli accessi in base al ruolo](../active-directory/role-based-access-control-what-is.md) per consentire l'accesso ad altri utenti all'interno del team solo in relazione alle risorse necessarie. I gruppi di risorse e le assegnazioni dei ruoli devono essere pianificati in modo appropriato. Esistono diversi approcci che consentono di progettare ed implementare nel concreto i gruppi di risorse; a questo proposito, leggere le [linee guida sui gruppi di risorse](virtual-machines-linux-infrastructure-resource-groups-guidelines.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per comprendere come creare al meglio le macchine virtuali.

## <a name="templates"></a>Modelli
Per creare le macchine virtuali, è possibile creare modelli definiti tramite file JSON dichiarativi. In genere i modelli creano anche l'archiviazione necessaria, la rete, le interfacce di rete, gli indirizzi IP e così via, insieme alle macchine virtuali stesse. È possibile usare modelli per creare ambienti coerenti riproducibili per scopi di sviluppo e test; questo consentirà di replicare facilmente ambienti di produzione e viceversa. Altre informazioni sulla [creazione e uso dei modelli](../azure-resource-manager/resource-group-overview.md#template-deployment) utili per la creazione e la distribuzione di VM.

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]




<!--HONumber=Dec16_HO3-->


