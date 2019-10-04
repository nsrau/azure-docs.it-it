---
title: Disponibilità elevata e ripristino di emergenza - Azure Batch | Microsoft Docs
description: Informazioni su come progettare un'applicazione Batch per un'interruzione a livello di area
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2019
ms.author: lahugh
ms.openlocfilehash: 11446f5f23d470234b5dba641dc16e0bf71d9b72
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094041"
---
# <a name="design-your-application-for-high-availability"></a>Progettare l'applicazione per la disponibilità elevata

Azure Batch è un servizio a livello di area, disponibile in tutte le aree di Azure. Quando si crea un account Batch, è necessario associarlo a un'area, a cui faranno riferimento tutte le operazioni relative all'account Batch. I pool e le macchine virtuali associate, ad esempio, dovranno essere creati nella stessa area dell'account Batch.

Quando si progetta un'applicazione che usa Batch, è necessario considerare la possibilità che Batch non sia disponibile in una determinata area. Può accadere, ad esempio, che un problema interessi un'area nel suo complesso o l'intero servizio Batch creato nell'area o che un problema interessi uno specifico account Batch.

Se l'applicazione o la soluzione che usa Batch deve essere sempre disponibile e quindi deve essere progettata in modo che ne venga eseguito il failover in un'altra area o che il carico di lavoro venga suddiviso tra due o più aree. Entrambi gli approcci richiedono almeno due account Batch, che devono essere creati in aree diverse.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Più account Batch in più aree

Se si usano più account Batch in aree diverse, l'applicazione rimane in esecuzione anche se l'account Batch smette di essere disponibile in una delle aree. L'uso di più account è particolarmente importante se l'applicazione deve essere a disponibilità elevata.

In alcuni casi, un'applicazione può essere progettata in modo da usare sempre due o più aree. Quando ad esempio è richiesta una notevole capacità, è possibile che sia necessario usare più aree per gestire un'applicazione su larga scala o far fronte a un'espansione futura.

## <a name="design-considerations-for-providing-failover"></a>Considerazioni di progettazione per consentire il failover

Quando si prevede la possibilità di eseguire il failover in un'altra area, è necessario prendere in considerazione tutti i componenti di una soluzione. Non è sufficiente avere semplicemente un secondo account Batch. Nella maggior parte delle applicazioni Batch, ad esempio, è necessario un account di archiviazione di Azure, che deve trovarsi nella stessa area dell'account Batch per poter ottenere prestazioni accettabili.

Quando si progetta una soluzione di cui poter eseguire il failover, tenere presente quanto segue:

- Creare in anticipo tutti gli account necessari in ogni area, ad esempio l'account Batch e l'account di archiviazione. Nella maggior parte dei casi, non è previsto alcun addebito per la creazione di un account, ma solo nel momento in cui si usa l'account o si archiviano dati al suo interno.
- Assicurarsi che le quote vengono impostate in anticipo sugli account, in modo da poter allocare il numero necessario di core tramite l'account Batch.
- Usare modelli e/o script per automatizzare la distribuzione dell'applicazione in un'area.
- Mantenere costantemente aggiornati i file binari dell'applicazione e i dati di riferimento in tutte le aree. In questo modo, infatti, l'area può essere portata online rapidamente senza dover attendere il caricamento e la distribuzione dei file. Se, ad esempio, si installa e si fa riferimento a un'applicazione personalizzata da installare nei nodi del pool tramite i pacchetti dell'applicazione Batch, quando viene generata una nuova versione dell'applicazione, è necessario che venga caricata in ogni account Batch e che ad essa faccia riferimento la configurazione del pool. In alternativa, è possibile impostarla come versione predefinita.
- Nell'applicazione che chiama il servizio Batch, il servizio di archiviazione o qualsiasi altro servizio, è necessario poter passare facilmente i client o il carico in un'altra area.
- Per garantire che il failover abbia esito positivo, è consigliabile adottare la procedura consigliata di passare frequentemente a un'area diversa. Se si hanno due distribuzioni in aree separate, ad esempio, passare all'area alternativa a intervalli mensili.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla creazione di account Batch con il [portale di Azure](batch-account-create-portal.md), l'[interfaccia della riga di comando di Azure](cli-samples.md), [Powershell](batch-powershell-cmdlets-get-started.md) o l'[API di gestione Batch](batch-management-dotnet.md).
- A un account Batch sono associate quote predefinite. [Questo articolo](batch-quota-limit.md) illustra i valori delle quote predefinite e descrive come poter aumentare le quote.
