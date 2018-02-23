---
title: "Considerazioni sulla progettazione per i set di scalabilità di macchine virtuali di Azure | Microsoft Docs"
description: "Considerazioni sulla progettazione per i set di scalabilità di macchine virtuali di Azure"
keywords: "macchina virtuale linux,set di scalabilità macchine virtuali"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: negat
ms.openlocfilehash: efb9f7f7daa5dbb8cd3120b21ef812106fdc7fb9
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="design-considerations-for-scale-sets"></a>Considerazioni sulla progettazione per i set di scalabilità
Questo articolo contiene una serie di considerazioni sulla progettazione per i set di scalabilità di macchine virtuali. Per sapere che cosa sono i set di scalabilità di macchine virtuali, vedere [Panoramica dei set di scalabilità di macchine virtuali](virtual-machine-scale-sets-overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Quando usare i set di scalabilità invece delle macchine virtuali?
I set di scalabilità sono in genere utili per distribuire un'infrastruttura a disponibilità elevata che include un set di computer con una configurazione simile. Tuttavia, alcune funzionalità sono disponibili soltanto nei set di scalabilità, mentre altre sono disponibili solo nelle macchine virtuali. Per prendere una decisione consapevole su quando usare una tecnologia, è consigliabile esaminare prima alcune delle funzionalità di uso comune disponibili nei set di scalabilità ma non nelle macchine virtuali:

### <a name="scale-set-specific-features"></a>Funzionalità specifiche dei set di scalabilità

- Dopo aver specificato la configurazione del set di scalabilità, è possibile aggiornare la proprietà "capacity" per distribuire in parallelo altre macchine virtuali. Si tratta di una procedura molto più semplice rispetto alla scrittura di uno script per orchestrare la distribuzione in parallelo di molte macchine virtuali singole.
- È possibile [usare il ridimensionamento automatico di Azure per la scalabilità automatica di un set di scalabilità](./virtual-machine-scale-sets-autoscale-overview.md) ma non di singole macchine virtuali.
- È possibile [ricreare l'immagine delle macchine virtuali del set di scalabilità](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-a-vm) ma [non delle singole macchine virtuali](https://docs.microsoft.com/rest/api/compute/virtualmachines).
- È possibile [eseguire il provisioning eccessivo](./virtual-machine-scale-sets-design-overview.md) delle macchine virtuali del set di scalabilità per una maggiore affidabilità e tempi di distribuzione più rapidi. Ciò non è possibile con le singole macchine virtuali, a meno che non si scriva un apposito codice personalizzato.
- È possibile specificare [criteri di aggiornamento](./virtual-machine-scale-sets-upgrade-scale-set.md) per semplificare l'implementazione degli aggiornamenti nelle macchine virtuali del set di scalabilità. Con le singole macchine virtuali, è necessario orchestrare gli aggiornamenti manualmente.

### <a name="vm-specific-features"></a>Funzionalità specifiche delle macchine virtuali

Alcune funzionalità sono attualmente disponibili solo nelle macchine virtuali:

- È possibile collegare dischi dati a singole macchine virtuali specifiche, mentre in un set di scalabilità i dischi dati collegati vengono configurati per tutte le macchine virtuali.
- È possibile collegare dischi dati non vuoti alle singole macchine virtuali, ma non alle macchine virtuali in un set di scalabilità.
- È possibile acquisire uno snapshot di una singola macchina virtuale, ma non di una macchina virtuale in un set di scalabilità.
- È possibile acquisire un'immagine da una singola macchina virtuale, ma non da una macchina virtuale in un set di scalabilità.
- È possibile eseguire la migrazione di una singola macchina virtuale da dischi nativi a dischi gestiti, ma la stessa operazione non può essere eseguita per le macchine virtuali in un set di scalabilità.
- È possibile assegnare indirizzi IP pubblici IPv6 alle schede di interfaccia di rete di singole macchine virtuali, ma la stessa operazione non può essere eseguita per le macchine virtuali in un set di scalabilità. È possibile assegnare indirizzi IP pubblici IPv6 ai servizi di bilanciamento del carico che si trovano davanti a singole macchine virtuali o a macchine virtuali di un set di scalabilità.

## <a name="storage"></a>Archiviazione

### <a name="scale-sets-with-azure-managed-disks"></a>Set di scalabilità con Azure Managed Disks
Invece che con i tradizionali account di archiviazione di Azure, i set di scalabilità possono essere creati con [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md). Managed Disks offre i seguenti vantaggi:
- Non è necessario creare in precedenza un set di account di archiviazione di Azure per le macchine virtuali con set di scalabilità.
- È possibile definire [i dischi dati collegati](virtual-machine-scale-sets-attached-disks.md) per le macchine virtuali nel set di scalabilità.
- È possibile configurare i set di scalabilità per un [supporto di massimo 1.000 macchine virtuali per set](virtual-machine-scale-sets-placement-groups.md). 

Se si ha un modello esistente, è anche possibile [aggiornare il modello per l'uso di Managed Disks](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Archiviazione gestita dall'utente
Un set di scalabilità non definito con Azure Managed Disks si basa sugli account di archiviazione creati dall'utente per archiviare i dischi del sistema operativo delle macchine virtuali nel set. È consigliabile un rapporto di 20 o meno macchine virtuali per ogni account di archiviazione per ottenere il massimo IO e per sfruttare i vantaggi del _provisioning in eccesso_ (vedere di seguito). È anche consigliabile usare le diverse lettere dell'alfabeto come caratteri iniziali dei nomi degli account di archiviazione. Questo consente di suddividere il carico tra diversi sistemi interni. 


## <a name="overprovisioning"></a>Provisioning eccessivo
L'impostazione predefinita per i set di scalabilità è attualmente il "provisioning eccessivo" di macchine virtuali. Con il provisioning eccessivo attivato, il set di scalabilità di fatto avvia un maggior numero di macchine virtuali rispetto a quanto richiesto, dopodiché elimina le macchine virtuali aggiuntive una volta eseguito correttamente il provisioning del numero di macchine virtuali richiesto. Il provisioning eccessivo migliora le percentuali di riuscita del provisioning e riduce i tempi di distribuzione. Le macchine virtuali aggiuntive non vengono addebitate all'utente e non se ne tiene conto per il raggiungimento dei limiti di quota.

Il provisioning eccessivo consente di migliorare la percentuale di riuscita del provisioning, ma può causare il comportamento confuso delle applicazioni non progettate per gestire VM aggiuntive che compaiono e scompaiono senza preavviso. Per disattivare il provisioning eccessivo, assicurarsi che il modello includa la stringa seguente: `"overprovision": "false"`. Per altre informazioni, consultare la [documentazione dell'API REST relativa ai set di scalabilità](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Se il set di scalabilità usa l'archiviazione gestita dall'utente e se si disattiva il provisioning in eccesso, è possibile disporre di più di 20 macchine virtuali per account di archiviazione. Tuttavia, non è consigliabile superare le 40 macchine virtuali per ragioni di prestazioni I/O. 

## <a name="limits"></a>Limiti
Un set di scalabilità basato su un'immagine Marketplace (chiamata anche immagine di piattaforma) e configurato per usare Azure Managed Disks supporta una capacità massima di 1.000 macchine virtuali. Se si configura il set di scalabilità per supportare più di 100 macchine virtuali, non tutti gli scenari funzioneranno allo stesso modo, ad esempio il bilanciamento del carico. Per altre informazioni, vedere [Uso di set di scalabilità di macchine virtuali di grandi dimensioni](virtual-machine-scale-sets-placement-groups.md). 

Un set di scalabilità configurato con account di archiviazione gestita dall'utente ha un limite corrente di 100 macchine virtuali. Per questa scalabilità sono consigliati 5 account di archiviazione.

Un set di scalabilità basato su un'immagine personalizzata (creata dall'utente) può avere una capacità massima di 300 macchine virtuali quando configurato con Azure Managed Disks. Se il set di scalabilità è configurato con gli account di archiviazione gestita dall'utente, è necessario creare tutti i dischi rigidi virtuali del disco del sistema operativo all'interno di un unico account di archiviazione. Di conseguenza, il numero massimo consigliato di VM in un set di scalabilità basato su un'immagine personalizzata e su un'archiviazione gestita dall'utente è 20. Se si disattiva il provisioning eccessivo, è possibile arrivare a 40.

Per un numero di macchine virtuali superiore a tali limiti, è necessario distribuire più set di scalabilità, come indicato in [questo modello](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

