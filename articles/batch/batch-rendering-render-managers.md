---
title: Supporto per le applicazioni di gestione del rendering
description: Uso dell'integrazione del manager per il rendering di Azur Batch. Informazioni sul supporto integrato o sui componenti aggiuntivi per le applicazioni di gestione del rendering più diffuse.
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: c44cbf86d8bf2fe83a6dc91dee1c4f58eec156c0
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726452"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Uso di Azure Batch con le applicazioni di gestione delle farm di rendering

Se si usa una farm di rendering locale esistente, è molto probabile che un'applicazione di gestione del rendering controlli la capacità della farm e i processi di rendering.

Azure offre supporto integrato o componenti aggiuntivi per le applicazioni di gestione del rendering più diffuse. È quindi possibile aggiungere e rimuovere macchine virtuali (VM) di Azure, tra cui VM con licenze per le applicazioni con pagamento in base al consumo e VM con bassa priorità.

Sono supportate le applicazioni di gestione del rendering seguenti:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](https://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Hub di rendering di Azure

L'hub di rendering di Azure semplifica la creazione e la gestione di farm di rendering di Azure.  L'hub di rendering dispone del supporto nativo per PipelineFx Qube e Deadline 10.  Per ulteriori informazioni e istruzioni dettagliate, vedere [il repository GitHub](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Uso di Azure con PipelineFX Qube

L'hub di rendering di Azure supporta i gestori di rendering più diffusi, incluso Deadline.  Per istruzioni sulla distribuzione e sull'uso dell'hub di rendering, vedere [il repository GitHub](https://github.com/Azure/azure-render-hub).

Gli script e le istruzioni per abilitare l'uso delle VM del pool di Azure Batch come ruoli di lavoro di Qube sono disponibili anche nel [repository GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Uso di Azure con Royal Render

In Royal Render è già inclusa l'integrazione di Azure e Azure Batch. È quindi possibile estendere una farm di rendering con le VM basate su Azure. Per un riepilogo, vedere [i file della guida](https://www.royalrender.de/help8/index.html?Cloudrendering.html).

Per un esempio di un cliente di Royal Render che usa l'integrazione di Azure, vedere il [caso di successo del cliente Jellyfish Pictures](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Uso di Azure con Thinkbox Deadline

L'hub di rendering di Azure supporta i gestori di rendering più diffusi, incluso Deadline.  Per istruzioni sulla distribuzione e sull'uso dell'hub di rendering, vedere [il repository GitHub](https://github.com/Azure/azure-render-hub).

## <a name="next-steps"></a>Passaggi successivi

Provare l'integrazione di Azure Batch con l'applicazione di gestione del rendering in uso, sfruttando, dove applicabile, il plug-in appropriato e le istruzioni in GitHub.
