---
title: Supporto per le applicazioni di gestione del rendering di Azure Batch
description: Uso di Azure per il rendering tramite l'integrazione di applicazioni di gestione del rendering di Azure Batch
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 798b2b457016856662f392af25d987788f73c242
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2018
ms.locfileid: "40036752"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Uso di Azure Batch con le applicazioni di gestione delle farm di rendering

Se si usa una farm di rendering locale esistente, è molto probabile che un'applicazione di gestione del rendering controlli la capacità della farm e i processi di rendering.

Azure offre supporto integrato o componenti aggiuntivi per le applicazioni di gestione del rendering più diffuse. È quindi possibile aggiungere e rimuovere macchine virtuali (VM) di Azure, tra cui VM con licenze per le applicazioni con pagamento in base al consumo e VM con bassa priorità.

Sono supportate le applicazioni di gestione del rendering seguenti:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](http://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="using-azure-with-pipelinefx-qube"></a>Uso di Azure con PipelineFX Qube

Gli script e le istruzioni per abilitare l'uso delle VM del pool di Azure Batch come ruoli di lavoro di Qube sono disponibili nel [repository GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Uso di Azure con Royal Render

In Royal Render è già inclusa l'integrazione di Azure e Azure Batch. È quindi possibile estendere una farm di rendering con le VM basate su Azure. Per un riepilogo, vedere [i file della guida](http://www.royalrender.de/help8/index.html?Cloudrendering.html).

Per un esempio di un cliente di Royal Render che usa l'integrazione di Azure, vedere il [caso di successo del cliente Jellyfish Pictures](https://customers.microsoft.com/en-gb/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Uso di Azure con Thinkbox Deadline

Gli script e le istruzioni per abilitare l'uso delle VM del pool di Azure Batch come slave di Deadline sono disponibili nel [repository GitHub](https://github.com/Azure/azure-deadline).

## <a name="next-steps"></a>Passaggi successivi

Provare l'integrazione di Azure Batch con l'applicazione di gestione del rendering in uso, sfruttando, dove applicabile, il plug-in appropriato e le istruzioni in GitHub.