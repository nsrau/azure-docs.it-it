---
title: Supporto per le applicazioni di gestione del rendering - Azure Batch
description: Uso di Azure per il rendering tramite l'integrazione di applicazioni di gestione del rendering di Azure Batch
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: eb3ce47e5ffed697392065a1faacbbfaec19f4d1
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983677"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Uso di Azure Batch con le applicazioni di gestione delle farm di rendering

Se si usa una farm di rendering locale esistente, è molto probabile che un'applicazione di gestione del rendering controlli la capacità della farm e i processi di rendering.

Azure offre supporto integrato o componenti aggiuntivi per le applicazioni di gestione del rendering più diffuse. È quindi possibile aggiungere e rimuovere macchine virtuali (VM) di Azure, tra cui VM con licenze per le applicazioni con pagamento in base al consumo e VM con bassa priorità.

Sono supportate le applicazioni di gestione del rendering seguenti:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](https://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Hub di rendering di Azure

L'hub di rendering di Azure semplifica la creazione e la gestione di farm di rendering di Azure.  L'hub di rendering dispone del supporto nativo per PipelineFx Qube e scadenza 10.  Per altre informazioni e istruzioni dettagliate, vedere [il repository GitHub](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Uso di Azure con PipelineFX Qube

Azure render Hub supporta i gestori di rendering più diffusi, inclusa la scadenza.  Per istruzioni sulla distribuzione e sull'uso dell'hub di rendering, vedere [il repository GitHub](https://github.com/Azure/azure-render-hub).

Gli script e le istruzioni per abilitare le macchine virtuali del pool di Azure Batch da usare come Qube worker sono disponibili anche nel [repository GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Uso di Azure con Royal Render

In Royal Render è già inclusa l'integrazione di Azure e Azure Batch. È quindi possibile estendere una farm di rendering con le VM basate su Azure. Per un riepilogo, vedere [i file della guida](https://www.royalrender.de/help8/index.html?Cloudrendering.html).

Per un esempio di un cliente di Royal Render che usa l'integrazione di Azure, vedere il [caso di successo del cliente Jellyfish Pictures](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Uso di Azure con Thinkbox Deadline

Azure render Hub supporta i gestori di rendering più diffusi, inclusa la scadenza.  Per istruzioni sulla distribuzione e sull'uso dell'hub di rendering, vedere [il repository GitHub](https://github.com/Azure/azure-render-hub).

## <a name="next-steps"></a>Passaggi successivi

Provare l'integrazione di Azure Batch con l'applicazione di gestione del rendering in uso, sfruttando, dove applicabile, il plug-in appropriato e le istruzioni in GitHub.
