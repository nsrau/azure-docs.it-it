---
title: Supporto per le applicazioni di gestione del rendering - Azure Batch
description: Utilizzo dell'integrazione di Azure Batch render manager.Using Azure Batch render manager integration. Scopri di più sul supporto integrato o sui componenti aggiuntivi per i gestori di rendering più diffusi.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 246907b16534d1a91833cab633a1973c97429f47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75449690"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Uso di Azure Batch con le applicazioni di gestione delle farm di rendering

Se si usa una farm di rendering locale esistente, è molto probabile che un'applicazione di gestione del rendering controlli la capacità della farm e i processi di rendering.

Azure offre supporto integrato o componenti aggiuntivi per le applicazioni di gestione del rendering più diffuse. È quindi possibile aggiungere e rimuovere macchine virtuali (VM) di Azure, tra cui VM con licenze per le applicazioni con pagamento in base al consumo e VM con bassa priorità.

Sono supportate le applicazioni di gestione del rendering seguenti:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](https://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Hub di rendering di AzureAzure Render Hub

Azure Render Hub semplifica la creazione e la gestione delle render farm di Azure.Azure Render Hub simplies the creation and management of Azure render farms.  Render Hub ha il supporto nativo per PipelineFx Qube e Deadline 10.  Per ulteriori informazioni e istruzioni dettagliate, vedere [il repository GitHub](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Uso di Azure con PipelineFX Qube

Azure Render Hub supports popular render managers including Deadline.  Per istruzioni sulla distribuzione e l'utilizzo di Render Hub, consultate [il repository GitHub.](https://github.com/Azure/azure-render-hub)

Script e istruzioni per abilitare le macchine virtuali del pool Batch di Azure da usare poiché i ruoli di lavoro Qube sono disponibili anche nel [repository GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Uso di Azure con Royal Render

In Royal Render è già inclusa l'integrazione di Azure e Azure Batch. È quindi possibile estendere una farm di rendering con le VM basate su Azure. Per un riepilogo, vedere [i file della guida](https://www.royalrender.de/help8/index.html?Cloudrendering.html).

Per un esempio di un cliente di Royal Render che usa l'integrazione di Azure, vedere il [caso di successo del cliente Jellyfish Pictures](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Uso di Azure con Thinkbox Deadline

Azure Render Hub supports popular render managers including Deadline.  Per istruzioni sulla distribuzione e l'utilizzo di Render Hub, consultate [il repository GitHub.](https://github.com/Azure/azure-render-hub)

## <a name="next-steps"></a>Passaggi successivi

Provare l'integrazione di Azure Batch con l'applicazione di gestione del rendering in uso, sfruttando, dove applicabile, il plug-in appropriato e le istruzioni in GitHub.
