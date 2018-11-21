---
title: Monitoraggio in Database di Azure per MariaDB
description: Questo articolo illustra le metriche di monitoraggio e avviso per Database di Azure per MariaDB, che includono statistiche relative a CPU, spazio di archiviazione e connessioni.
services: mariadb
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mariadb
ms.topic: article
ms.date: 11/10/2018
ms.openlocfilehash: 3daa4eb07e626e53d9106abf978174f505e785ab
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516532"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Monitoraggio in Database di Azure per MariaDB
Il monitoraggio dei dati relativi ai server facilita la risoluzione dei problemi e l'ottimizzazione in relazione al carico di lavoro. Database di Azure per MariaDB offre varie metriche che consentono di ottenere informazioni dettagliate sul comportamento del server.

## <a name="metrics"></a>Metriche
Tutte le metriche di Azure hanno una frequenza di un minuto e offrono una cronologia di 30 giorni. È possibile configurare avvisi in base alle metriche. Le altre attività includono la configurazione di azioni automatiche, l'esecuzione di analisi avanzate e l'archiviazione della cronologia. Per altre informazioni, vedere la [Panoramica delle metriche di Azure] (../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<!--For step by step guidance, see [How to set up alerts](howto-alert-on-metric.md). -->

### <a name="list-of-metrics"></a>Elenco delle metriche
Per Database di Azure per MariaDB sono disponibili le metriche seguenti:

|Metrica|Nome visualizzato per la metrica|Unità|DESCRIZIONE|
|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Percentuale di CPU in uso.|
|memory_percent|Percentuale memoria|Percentuale|Percentuale di memoria in uso.|
|io_consumption_percent|IO percent (Percentuale IO)|Percentuale|Percentuale di I/O in uso.|
|storage_percent|Percentuale archiviazione|Percentuale|Percentuale di spazio di archiviazione usata rispetto al massimo del server.|
|storage_used|Uso archiviazione|Byte|Quantità di spazio di archiviazione in uso. Lo spazio di archiviazione usato dal servizio può includere file di database, log delle transazioni e log del server.|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Percentuale di spazio di archiviazione dei log del server usata rispetto allo spazio di archiviazione massimo dei log del server per il server.|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Quantità di spazio di archiviazione dei log del server in uso.|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Spazio di archiviazione massimo dei log del server per il server.|
|storage_limit|Limite archiviazione|Byte|Spazio di archiviazione massimo per il server.|
|active_connections|Connessioni attive|Conteggio|Numero di connessioni al server attive.|
|connections_failed|Connessioni non riuscite|Conteggio|Numero di connessioni al server non riuscite.|
|network_bytes_egress|Rete in uscita|Byte|Rete in uscita tra connessioni attive.|
|network_bytes_ingress|Rete in ingresso|Byte|Rete in ingresso tra connessioni attive.|

## <a name="server-logs"></a>Log del server
È possibile abilitare la registrazione delle query lente nel server. Per altre informazioni sull'accesso, visitare la pagina dei  [log del server](concepts-server-logs.md).

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sull'accesso alle metriche e la relativa esportazione con il portale di Azure, l'API REST o l'interfaccia della riga di comando, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<!-- - See [How to set up alerts](howto-alert-on-metric.md) for guidance on creating an alert on a metric.-->