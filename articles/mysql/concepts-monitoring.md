---
title: Monitoraggio in Database di Azure per MySQL
description: Questo articolo illustra le metriche di monitoraggio e avviso per Database di Azure per MySQL, che includono statistiche relative a CPU, spazio di archiviazione e connessioni.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: c3cba00077fd65239382d6fdd98e73a55f926b3b
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitoraggio in Database di Azure per MySQL
Il monitoraggio dei dati relativi ai server facilita la risoluzione dei problemi e l'ottimizzazione per il carico di lavoro. Database di Azure per MySQL offre varie metriche che consentono di ottenere informazioni approfondite sul comportamento delle risorse che supportano il server MySQL. 

## <a name="metrics"></a>Metriche
Tutte le metriche di Azure hanno una frequenza di un minuto e offrono una cronologia di 30 giorni. È possibile configurare avvisi in base alle metriche. Per indicazioni dettagliate, vedere l'articolo su come [configurare gli avvisi](howto-alert-on-metric.md). Le altre attività includono la configurazione di azioni automatiche, l'esecuzione di analisi avanzate e l'archiviazione della cronologia. Per altre informazioni, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Elenco delle metriche
Per Database di Azure per MySQL sono disponibili le metriche seguenti:

|Metrica|Nome visualizzato per la metrica|Unità|DESCRIZIONE|
|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Percentuale di CPU in uso.|
|memory_percent|Percentuale memoria|Percentuale|Percentuale di memoria in uso.|
|io_consumption_percent|IO percent (Percentuale IO)|Percentuale|Percentuale di I/O in uso.|
|storage_percent|Percentuale archiviazione|Percentuale|Percentuale di spazio di archiviazione usata rispetto al massimo del server.|
|storage_used|Uso archiviazione|Byte|Quantità di spazio di archiviazione in uso. Lo spazio di archiviazione usato dal servizio include file di database, log delle transazioni e log del server.|
|storage_limit|Limite archiviazione|Byte|Spazio di archiviazione massimo per il server.|
|active_connections|Total active connections (Numero totale di connessioni attive)|Conteggio|Numero di connessioni al server attive.|
|connections_failed|Total failed connections (Numero totale di connessioni non riuscite)|Conteggio|Numero di connessioni al server non riuscite.|


## <a name="next-steps"></a>Passaggi successivi
- Per informazioni sulla creazione di un avviso per una metrica, vedere [Come configurare gli avvisi](howto-alert-on-metric.md).
- Per altre informazioni sull'accesso alle metriche e la relativa esportazione con il portale di Azure, l'API REST o l'interfaccia della riga di comando, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
