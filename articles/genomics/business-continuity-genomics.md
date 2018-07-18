---
title: Continuità aziendale - Genomica di Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informazioni su come Genomica di Microsoft supporta la continuità aziendale
keywords: Ripristino di emergenza, continuità aziendale
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 04/06/2018
ms.openlocfilehash: cb3825cb89aff386c4f7c3f3b0d771d73fe637b1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
ms.locfileid: "31426574"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Panoramica della continuità aziendale con Genomica di Microsoft
Questa panoramica descrive le funzionalità offerte da Genomica di Microsoft per la continuità aziendale e il ripristino di emergenza. Vengono fornite informazioni sulle opzioni per il ripristino dopo eventi che possono causare interruzioni del funzionamento, ad esempio un'interruzione di un'area di Azure, con conseguente perdita di dati. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Funzionalità di Genomica di Microsoft che supportano la continuità aziendale 
Anche se di rado, in un data center di Azure può verificarsi un'interruzione, che può causare sospensioni nell'attività della durata di alcuni minuti o di alcune ore. Quando si verifica un'interruzione, tutti i processi attualmente in esecuzione nel data center hanno esito negativo e il servizio Genomica di Microsoft non invia di nuovo automaticamente i processi a un'area secondaria. 

* Una delle opzioni è attendere che il data center torni online al termine dell'interruzione del data center. Se l'interruzione è breve, il servizio Genomica di Microsoft rileverà automaticamente i processi non riusciti e il flusso di lavoro verrà automaticamente riavviato.

* Un'altra delle opzioni è inviare proattivamente il flusso di lavoro in un'altra area dati. Genomica di Microsoft distribuisce le istanze in diverse [aree di Azure](https://azure.microsoft.com/regions/services/) e ogni istanza specifica di un'area è indipendente. Se in una delle istanze di Genomica di Microsoft si verifica un errore locale dell'area, le altre aree che eseguono le istanze di Genomica di Microsoft continueranno a elaborare i processi. Questo trasferimento in un'area alternativa è sotto il controllo dell'utente ed è disponibile sempre.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Eseguire il failover manuale dei flussi di lavoro di Genomica di Microsoft in un'altra area
In caso di interruzione di un data center a livello di area, è possibile scegliere di inviare i flussi di lavoro di Genomica di Microsoft in un'area secondaria, in base ai singoli requisiti di continuità aziendale e sovranità dei dati. Per eseguire il failover manuale dei flussi di lavoro di Genomica di Microsoft, si userà un account di Genomica specifico di un'altra area e si invierà il processo con le credenziali appropriate dell'account di archiviazione e di Genomica specifiche dell'area.

In particolare, sarà necessario:
* Creare un account di Genomica nell'area secondaria, usando il portale di Azure. 
* Eseguire la migrazione dei dati di input a un account di archiviazione nell'area secondaria e configurare una cartella di output nell'area secondaria.
* Inviare il flusso di lavoro nell'area secondaria.

Quando l'area originale viene ripristinata, il servizio Genomica di Microsoft non esegue di nuovo la migrazione dei dati dall'area secondaria a quella originale. È possibile scegliere di spostare di nuovo i file di input e di output dall'area secondaria a quella originale.  Lo spostamento dei dati non rientra nel servizio Genomica e tutte le spese relative a tale spostamento saranno addebitate all'utente. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Preparazione per una possibile interruzione specifica dell'area
Se si vuole un ripristino più rapido in caso di interruzione di un data center, è possibile eseguire una procedura per ridurre il tempo necessario per inviare manualmente i flussi di lavoro di Genomica di Microsoft a un'area secondaria:

* Identificare un'area secondaria appropriata e creare proattivamente un account di Genomica in tale area
* Duplicare i dati nell'area primaria e secondaria in modo che siano immediatamente disponibili nell'area secondaria. È possibile eseguire questa operazione manualmente oppure usando la funzionalità di [archiviazione con ridondanza geografica](https://docs.microsoft.com/azure/storage/common/storage-redundancy) disponibile in Archiviazione di Azure. 

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono state illustrate le opzioni per la continuità aziendale e il ripristino di emergenza quando si usa il servizio Genomica di Microsoft. Per altre informazioni generali su continuità aziendale e ripristino di emergenza in Azure, vedere [Indicazioni tecniche sulla resilienza in Azure](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region). 