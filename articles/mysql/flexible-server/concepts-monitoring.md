---
title: Monitoraggio-database di Azure per MySQL-server flessibile
description: Questo articolo descrive le metriche per il monitoraggio e l'invio di avvisi per il server flessibile di database di Azure per MySQL, incluse le statistiche relative a CPU, archiviazione e connessione.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: f3a58a6766c42385dd6611c2014ba36fd1078710
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939292"
---
# <a name="monitor-azure-database-for-mysql-flexible-servers-with-built-in-metrics"></a>Monitorare i server flessibili di database di Azure per MySQL con metriche predefinite

> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Il server flessibile database di Azure per MySQL offre il monitoraggio dei server tramite monitoraggio di Azure. Le metriche sono valori numerici che descrivono un aspetto delle risorse del server in un determinato momento. Il monitoraggio delle risorse del server consente di risolvere i problemi e ottimizzare il carico di lavoro consentendo di monitorare gli elementi più importanti per l'utente. Il monitoraggio delle metriche corrette consente di garantire prestazioni, affidabilità e disponibilità del server e delle applicazioni.

In questo articolo vengono illustrate le diverse metriche disponibili per il server flessibile che forniscono informazioni dettagliate sul comportamento del server.

## <a name="available-metrics"></a>Metriche disponibili

Il server flessibile database di Azure per MySQL offre diverse metriche per comprendere il modo in cui il carico di lavoro è in esecuzione e in base a questi dati, è possibile comprendere l'effetto sul server e sull'applicazione. In server flessibili, ad esempio, è possibile monitorare la percentuale di **CPU dell'host**, le **connessioni attive**, la **percentuale**di i/o e la percentuale di **memoria dell'host** per identificare la presenza di un effetto sulle prestazioni. A questo punto, potrebbe essere necessario ottimizzare il carico di lavoro, ridimensionare verticalmente cambiando i livelli di calcolo o ridimensionarlo orizzontalmente utilizzando Read replica.

Tutte le metriche di Azure hanno una frequenza di un minuto e offrono una cronologia di 30 giorni. È possibile configurare avvisi in base alle metriche. Per istruzioni dettagliate, vedere [How to set up Alerts](./how-to-alert-on-metric.md). Le altre attività includono la configurazione di azioni automatiche, l'esecuzione di analisi avanzate e l'archiviazione della cronologia. Per altre informazioni, vedere [Panoramica delle metriche in Microsoft Azure](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Elenco delle metriche
Per Database di Azure per MySQL sono disponibili le metriche seguenti:

|Nome visualizzato metrica|Metrica|Unità|Descrizione|
|---|---|---|---|
|Percentuale CPU host|cpu_percent|Percentuale|Percentuale di utilizzo della CPU nel server, incluso l'utilizzo della CPU da parte del carico di lavoro del cliente e dei processi di Azure MySQL|
|Rete host in |network_bytes_ingress|Byte|Traffico di rete in ingresso nel server, incluso il traffico del database dei clienti e delle funzionalità di Azure MySQL come la replica, il monitoraggio, i log e così via.|
|Rete in uscita host|network_bytes_egress|Byte|Traffico di rete in uscita sul server, incluso il traffico del database dei clienti e delle funzionalità di Azure MySQL come la replica, il monitoraggio, i log e così via.|
|Ritardo replica|replication_lag|Secondi|Ora dall'ultima transazione riprodotta. Questa metrica è disponibile solo per i server di replica.|
|Connessioni attive|active_connection|Conteggio|Numero di connessioni al server attive.|
|Risorse di backup in uso|backup_storage_used|Byte|Quantità di risorse dell'archivio di backup usate.|
|Percentuale IO|io_consumption_percent|Percentuale|Percentuale di I/O in uso.|
|Percentuale memoria host|memory_percent|Percentuale|Percentuale di memoria in uso sul server, incluso l'utilizzo della memoria da parte del carico di lavoro dei clienti e dei processi di Azure MySQL|
|Limite di archiviazione|storage_limit|Byte|Spazio di archiviazione massimo per il server.|
|Percentuale archiviazione|storage_percent|Percentuale|Percentuale di spazio di archiviazione usata rispetto al massimo del server.|
|Spazio di archiviazione utilizzato|storage_used|Byte|Quantità di spazio di archiviazione in uso. Lo spazio di archiviazione usato dal servizio può includere file di database, log delle transazioni e log del server.|
|Totale connessioni|total_connections|Conteggio|Numero totale di connessioni al server|
|Connessioni interrotte|aborted_connections|Conteggio|Il numero di tentativi non riusciti di connessione a MySQL, ad esempio la connessione non riuscita a causa di credenziali non valide.|
|Query|query|Conteggio|Numero di query al secondo|

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni sulla creazione di un avviso per una metrica, vedere [Come configurare gli avvisi](./how-to-alert-on-metric.md).
- Altre informazioni sul [ridimensionamento di IOPS](./concepts/../concepts-compute-storage.md#iops) per migliorare le prestazioni.
