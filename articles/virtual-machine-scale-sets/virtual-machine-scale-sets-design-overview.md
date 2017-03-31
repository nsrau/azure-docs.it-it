---
title: "Progettare set di scalabilità di macchine virtuali di Azure per la scalabilità | Microsoft Docs"
description: "Informazioni su come progettare set di scalabilità di macchine virtuali di Azure per la scalabilità"
keywords: "macchina virtuale linux,set di scalabilità macchine virtuali"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: e869b06935736fae72bd3b5407ebab7c3830098d
ms.openlocfilehash: de3687a1bf36bf49db400a5660ac631f20b629d0
ms.lasthandoff: 02/14/2017


---
# <a name="designing-vm-scale-sets-for-scale"></a>Progettare set di scalabilità di VM per la scalabilità
Questo argomento descrive una serie di considerazioni di progettazione per i set di scalabilità di macchine virtuali. Per sapere che cosa sono i set di scalabilità di macchine virtuali, vedere [Panoramica dei set di scalabilità di macchine virtuali](virtual-machine-scale-sets-overview.md).

## <a name="storage"></a>Archiviazione

### <a name="scale-sets-with-azure-managed-disks"></a>Set di scalabilità con Azure Managed Disks
È ora possibile creare set di scalabilità con [Azure Managed Disks](../storage/storage-managed-disks-overview.md). Managed Disks offre i seguenti vantaggi:
- Non è necessario creare in precedenza un set di account di archiviazione di Azure per le macchine virtuali con set di scalabilità.
- È possibile definire [i dischi dati collegati](virtual-machine-scale-sets-attached-disks.md) per le macchine virtuali nel set di scalabilità.
- È possibile configurare i set di scalabilità per un [supporto di massimo 1.000 macchine virtuali per set](virtual-machine-scale-sets-placement-groups.md). 

È possibile creare set di scalabilità con Managed Disks a partire dalla versione "2016-04-30-preview" dell'API di calcolo di Azure. Per informazioni sulla conversione di un modello di set di scalabilità a Managed Disks, vedere [Convertire un modello di set di scalabilità in un modello di set di scalabilità per i dischi gestiti](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Archiviazione gestita dall'utente
Un set di scalabilità non definito con Azure Managed Disks si basa sugli account di archiviazione creati dall'utente per memorizzare nel set i dischi dei sistemi operativi delle macchine virtuali. È consigliabile un rapporto di 20 o meno macchine virtuali per ogni account di archiviazione per ottenere il massimo IO e per sfruttare i vantaggi del _provisioning in eccesso_ (vedere di seguito). È anche consigliabile usare le diverse lettere dell'alfabeto come caratteri iniziali dei nomi degli account di archiviazione. Questo consente di suddividere il carico tra diversi sistemi interni. 

>[!NOTE]
>La versione dell'API di set di scalabilità di macchine virtuali di Microsoft Azure `2016-04-30-preview` supporta l'uso di Azure Managed Disks per il disco del sistema operativo ed eventuali dischi dati aggiuntivi. Per altre informazioni, vedere [Panoramica del servizio Managed Disks](../storage/storage-managed-disks-overview.md) e [Usare dischi dati collegati](virtual-machine-scale-sets-attached-disks.md). 

## <a name="overprovisioning"></a>Provisioning eccessivo
A partire dalla versione dell'API "2016-03-30", l'impostazione predefinita dei set di scalabilità di macchine virtuali corrisponde al provisioning eccessivo di macchine virtuali. Con il provisioning eccessivo attivato, il set di scalabilità di fatto avvia un maggior numero di macchine virtuali rispetto a quanto richiesto, dopodiché elimina le macchine virtuali aggiuntive una volta eseguito correttamente il provisioning del numero di macchine virtuali richiesto. Il provisioning eccessivo migliora le percentuali di riuscita del provisioning e riduce i tempi di distribuzione. Le macchine virtuali aggiuntive non vengono addebitate all'utente e non se ne tiene conto per il raggiungimento dei limiti di quota.

Il provisioning eccessivo consente di migliorare la percentuale di riuscita del provisioning, ma può causare il comportamento confuso delle applicazioni non progettate per gestire VM aggiuntive che compaiono e scompaiono senza preavviso. Per disattivare la funzionalità di provisioning in eccesso, inserire la stringa "overprovision": "false" nel modello, se non è già presente. Per ulteriori informazioni, consultare la [documentazione dell'API REST relativo al set di scalabilità delle VM](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Se il set di scalabilità usa l'archiviazione gestita dall'utente e se si disattiva il provisioning in eccesso, è possibile disporre di più di 20 macchine virtuali per account di archiviazione. Tuttavia, non è consigliabile superare le 40 macchine virtuali per ragioni di prestazioni I/O. 

## <a name="limits"></a>Limiti
Un set di scalabilità basato su un'immagine Marketplace (chiamata anche immagine di piattaforma) e configurato per usare Azure Managed Disks supporta una capacità massima di 1.000 macchine virtuali. Se si configura il set di scalabilità per supportare più di 100 macchine virtuali, non tutti gli scenari funzioneranno allo stesso modo, ad esempio il bilanciamento del carico. Per altre informazioni, vedere [Uso di set di scalabilità di macchine virtuali di grandi dimensioni](virtual-machine-scale-sets-placement-groups.md). 

Un set di scalabilità configurato con account di archiviazione gestita dall'utente ha un limite corrente di 100 macchine virtuali. Per questa scalabilità sono consigliati 5 account di archiviazione.

Un set di scalabilità basato su un'immagine personalizzata (creata dall'utente) può avere una capacità massima di 100 macchine virtuali quando configurato con Azure Managed Disks. Se il set di scalabilità è configurato con gli account di archiviazione gestita dall'utente, è necessario creare tutti i dischi rigidi virtuali del disco del sistema operativo all'interno di un unico account di archiviazione. Di conseguenza, il numero massimo consigliato di VM in un set di scalabilità basato su un'immagine personalizzata e su un'archiviazione gestita dall'utente è 20. Se si disattiva il provisioning eccessivo, è possibile arrivare a 40.

Per un numero di macchine virtuali superiore a tali limiti, è necessario distribuire più set di scalabilità, come indicato in [questo modello](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).


