---
title: Usare Analisi archiviazione di Azure per raccogliere dati di metriche e log | Microsoft Docs
description: Analisi archiviazione consente di tenere traccia dei dati delle metriche per tutti i servizi di archiviazione e di raccogliere i log per l'archiviazione di BLOB, code e tabelle.
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: eb85f8c756e7373a8dedabbce362cfa534e56fd8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849177"
---
# <a name="storage-analytics"></a>di Analisi archiviazione

Analisi archiviazione di Azure esegue la registrazione e fornisce le metriche dei dati per un account di archiviazione. È possibile utilizzare questi dati per tenere traccia delle richieste, analizzare le tendenze d'uso e diagnosticare i problemi relativi al proprio account di archiviazione.

Per utilizzare Analisi archiviazione, è necessario abilitarla singolarmente per ciascun servizio che si desidera monitorare. È possibile abilitarlo dal [portale di Azure](https://portal.azure.com). Per informazioni dettagliate, vedere [monitorare un account di archiviazione nel portale di Azure](storage-monitor-storage-account.md). È inoltre possibile abilitare Analisi archiviazione a livello di codice tramite l'API REST o la libreria client. Usare la [Set Blob Service Properties](/rest/api/storageservices/set-blob-service-properties), [Set Queue Service Properties](/rest/api/storageservices/set-queue-service-properties), [Set Table Service Properties](/rest/api/storageservices/set-table-service-properties), e [Set File Service Properties](/rest/api/storageservices/Get-File-Service-Properties)operazioni per abilitare archiviazione Analitica per ogni servizio.

I dati aggregati vengono archiviati in un BLOB noto (per la registrazione) e in tabelle note (per le metriche), a cui è possibile accedere tramite le API del servizio BLOB e del servizio tabelle.

Analisi archiviazione può archiviare un massimo di 20 TB di dati. Tale limite è indipendente dal limite totale dell'account di archiviazione. Per informazioni sui limiti dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md).

Per una guida dettagliata sull'utilizzo di Analisi archiviazione e di altri strumenti per identificare, diagnosticare e risolvere i problemi relativi ad Archiviazione di Azure, vedere [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="billing-for-storage-analytics"></a>Fatturazione per Analisi archiviazione

Tutti i dati delle metriche vengono scritti dai servizi di un account di archiviazione. Di conseguenza, ogni operazione di scrittura eseguita da Analisi archiviazione è fatturabile. Inoltre, anche la quantità di archiviazione utilizzata dai dati delle metriche è fatturabile.

Le seguenti azioni eseguite da Analisi archiviazione sono fatturabili:

* Richieste di creazione di BLOB per la registrazione
* Richieste di creazione di entità di tabella per le metriche

Se è stato configurato un criterio di conservazione dei dati, non verranno addebitati costi per le transazioni eliminate quando Analisi archiviazione elimina i vecchi dati di registrazione e delle metriche. Tuttavia, le transazioni eliminate da un client sono fatturabili. Per ulteriori informazioni sui criteri di conservazione, vedere [Impostazione di un criterio di conservazione dei dati di Analisi archiviazione](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Informazioni sulle richieste fatturabili

Ogni richiesta effettuata al servizio di archiviazione di un account è fatturabile oppure non fatturabile. Analisi archiviazione registra ogni singola richiesta eseguita a un servizio, incluso un messaggio di stato che indica in che modo è stata gestita la richiesta. Allo stesso modo, Analisi archiviazione archivia le metriche per un servizio e per le operazioni API di tale servizio, incluse le percentuali e il numero di determinati messaggi di stato. Insieme, queste funzionalità possono aiutare l'utente ad analizzare le richieste fatturabili, apportare miglioramenti all'applicazione, e diagnosticare i problemi relativi alle richieste ai servizi. Per altre informazioni sulla fatturazione, vedere [Informazioni sulla fatturazione di Archiviazione di Azure - Larghezza di banda, transazioni e capacità](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Quando si osservano i dati di Analisi archiviazione, è possibile utilizzare le tabelle dell'argomento [Operazioni registrate di Analisi archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) per determinare quali sono le richieste fatturabili. Quindi è possibile confrontare i log e i dati delle metriche ai messaggi di stato per vedere se è stata addebitata una particolare richiesta. Le tabelle dell'argomento citato possono anche essere utilizzate per investigare la disponibilità di un servizio di archiviazione o di una singola operazione API.

## <a name="next-steps"></a>Passaggi successivi
* [Monitorare un account di archiviazione nel portale di Azure](storage-monitor-storage-account.md)
* [Metriche di Analisi archiviazione](storage-analytics-metrics.md)
* [Registrazione di Analisi archiviazione](storage-analytics-logging.md)
