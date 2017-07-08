---
title: "Creare più macchine virtuali | Microsoft Docs"
description: "Opzioni per la creazione di più macchine virtuali in Windows"
services: virtual-machines-windows
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: dfc1d1bb-a47d-4d7c-9fd2-f12050baacab
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: guybo
ms.translationtype: Human Translation
ms.sourcegitcommit: 51de0e9aa1d29d0b3f3ffc4f126b8ca688be3504
ms.openlocfilehash: 5e96805f8880a30a5fc8779d8f07addb6d068c09
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
# <a name="create-multiple-azure-virtual-machines"></a>Creazione di più macchine virtuali di Azure
Esistono molti scenari in cui è necessario creare un numero elevato di macchine virtuali simili (VM). Alcuni esempi comprendono scenari di high-performance computing (HPC), di analisi dei dati su larga scala, di server di livello intermedio o back-end scalabili e spesso senza stato (come i server Web) e di database distribuiti.

In questo articolo vengono illustrate le opzioni disponibili per creare più VM in Azure. Queste opzioni vanno oltre i semplici casi in cui si crea manualmente una serie di VM. Per creare più VM, i processi che si utilizzano in genere non vengono scalati bene se è necessario creare un numero maggiore di macchine virtuali.

Un modo per creare molte VM simili consiste nell'usare il costrutto dei *cicli di risorse*di Gestione risorse di Azure.

## <a name="resource-loops"></a>cicli di risorse
I cicli di risorse sono una sintassi abbreviata nei modelli di Gestione risorse di Azure. I cicli di risorse possono creare un set di risorse configurate in modo simile in un ciclo. È possibile usare cicli di risorse per creare più account di archiviazione, interfacce di rete, macchine virtuali. Per altre informazioni sui cicli di risorse, fare riferimento a [Create VMs in availability sets using resource loops](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/)(Creare VM in set di disponibilità usando cicli di risorse).

## <a name="challenges-of-scale"></a>Problemi di scalabilità
Anche se i cicli di risorse rendono più semplice la creazione di un'infrastruttura cloud su larga scala e la produzione di modelli più concisi, alcuni problemi permangono. Ad esempio, se si usa un ciclo di risorse per creare 100 macchine virtuali, è necessario correlare i controller di interfaccia di rete (NIC) con le VM corrispondenti e gli account di archiviazione. Poiché il numero di VM è probabilmente diverso dal numero di account di archiviazione, è necessario gestire anche dimensioni di ciclo delle risorse diverse. Si tratta di problemi risolvibili ma la loro complessità aumenta in modo significativo con il ridimensionamento.

Un altro problema si verifica quando è necessaria un'infrastruttura che viene ridimensionata in modo elastico. Ad esempio, è possibile che sia necessaria un'infrastruttura di scalabilità automatica che aumenti o riduca in modo automatico il numero di VM in risposta al carico di lavoro. Le VM non sono dotate di un meccanismo integrato per variare di numero (aumento e riduzione). Se si esegue una riduzione rimuovendo VM, è difficile garantire che le VM siano distribuite in modo equilibrato nei domini di aggiornamento e di errore.

Infine, quando si usa un ciclo di risorse, più chiamate per creare risorse passano all'infrastruttura sottostante. Se più chiamate creano risorse simili, Azure ha un'opportunità implicita di migliorare la progettazione e di ottimizzare l'affidabilità e le prestazioni della distribuzione. A questo punto entrano in gioco i *set di scalabilità di macchine virtuali* .

## <a name="virtual-machine-scale-sets"></a>set di scalabilità di macchine virtuali
I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure per distribuire e gestire un set di VM identiche. Con tutte le VM configurate allo stesso modo, è facile ridurre e aumentare le impostazioni di scalabilità della VM. È sufficiente modificare il numero di VM nel set. È anche possibile configurare set di scalabilità automatica della VM in base alle esigenze del carico di lavoro.

Per le applicazioni che richiedono l'aumento e la riduzione delle risorse di calcolo, le operazioni di ridimensionamento vengono bilanciate in modo implicito tra domini di errore e domini di aggiornamento.

Invece di correlare più risorse, ad esempio schede NIC e VM, un set di scalabilità della VM è dotato delle proprietà relative alla rete, all'archiviazione, alle macchine virtuali e all'estensione, che è possibile configurare in modo centralizzato.

Per un'introduzione ai set di scalabilità della VM, consultare la [pagina del prodotto dei set di scalabilità di macchine virtuali](https://azure.microsoft.com/services/virtual-machine-scale-sets/). Per altre informazioni, accedere alla [Documentazione su Set di scalabilità di macchine virtuali](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/).


