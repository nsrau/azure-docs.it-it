---
title: Monitoraggio e metriche-database di Azure per PostgreSQL-server flessibile
description: Questo articolo descrive le funzionalità di monitoraggio e metrica nel database di Azure per PostgreSQL-server flessibile.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 1519e0b5cef6055cf8d8b0aded0d8ad323d548a2
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91707847"
---
# <a name="monitor-metrics-on-azure-database-for-postgresql---flexible-server"></a>Monitorare le metriche nel database di Azure per PostgreSQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Il monitoraggio dei dati relativi ai server facilita la risoluzione dei problemi e l'ottimizzazione in relazione al carico di lavoro. Database di Azure per PostgreSQL offre varie opzioni di monitoraggio che consentono di ottenere informazioni dettagliate sul comportamento del server.

## <a name="metrics"></a>Metriche
Database di Azure per PostgreSQL offre varie metriche che consentono di ottenere informazioni approfondite sul comportamento delle risorse che supportano il server PostgreSQL. Ogni metrica viene emessa a una frequenza di un minuto e presenta fino a [93 giorni di cronologia](../../azure-monitor/platform/data-platform-metrics.md#retention-of-metrics). È possibile configurare avvisi in base alle metriche. Altre opzioni includono la configurazione di azioni automatiche, l'esecuzione di analisi avanzate e la cronologia di archiviazione. Per altre informazioni, vedere [Panoramica delle metriche in Microsoft Azure](../../azure-monitor/platform/data-platform-metrics.md).

### <a name="list-of-metrics"></a>Elenco delle metriche
Per i server flessibili PostgreSQL sono disponibili le metriche seguenti:


|Metrica|Nome visualizzato per la metrica|Unità|Descrizione|
|---|---|---|---|
| active_connections | Connessioni attive | Conteggio | Il numero di connessioni al server. | 
| backup_storage_used | Risorse di backup in uso | Byte | Quantità di spazio di archiviazione di backup utilizzato. Questa metrica rappresenta la somma dello spazio di archiviazione utilizzato da tutti i backup completi del database, backup differenziali e backup del log mantenuti in base al periodo di conservazione dei backup impostato per il server. La frequenza dei backup è gestita dal servizio. Per l'archiviazione con ridondanza geografica, l'utilizzo dell'archiviazione di backup è due volte quello dell'archiviazione con ridondanza locale. |
| connections_failed | Connessioni non riuscite | Conteggio | Connessioni non riuscite. |
| connections_succeeded | Connessioni riuscite | Conteggio | Connessioni riuscite. |
| cpu_credits_consumed | Crediti CPU usati | Conteggio | Numero di crediti utilizzati dal server flessibile. Applicabile a livello di espansione. |
| cpu_credits_remaining | Crediti CPU rimanenti | Conteggio | Numero di crediti disponibili per l'espansione. Applicabile a livello di espansione. |
| cpu_percent | Percentuale CPU | Percentuale | Percentuale di utilizzo della CPU. | 
| disk_queue_depth | Profondità coda del disco | Conteggio | Numero di operazioni di I/O in attesa nel disco dati. |
| iops | IOPS | Conteggio | Numero di operazioni di I/O su disco al secondo. |
| maximum_used_transactionIDs | Numero massimo di ID transazione utilizzati | Conteggio | ID transazione massimo in uso. |
| memory_percent | Percentuale memoria | Percentuale | Percentuale di memoria in uso. |
| network_bytes_egress | Rete in uscita | Byte | Quantità di traffico di rete in uscita. |
| network_bytes_ingress | Rete in ingresso | Byte | Quantità di traffico di rete in ingresso. |
| read_iops | IOPS di lettura | Conteggio | Numero di operazioni di lettura I/O del disco dati al secondo. |
| read_throughput | Velocità effettiva di lettura | Byte | Byte letti al secondo dal disco. |
| storage_free | Archiviazione gratuita | Byte | Quantità di spazio di archiviazione disponibile. |
| storage_percent | Percentuale di archiviazione | Percentuale | Percentuale dello spazio di archiviazione utilizzato. Lo spazio di archiviazione usato dal servizio può includere file di database, log delle transazioni e log del server.|
| storage_used | Spazio di archiviazione utilizzato | Byte | Percentuale dello spazio di archiviazione utilizzato. Lo spazio di archiviazione usato dal servizio può includere file di database, log delle transazioni e log del server. |
| txlogs_storage_used | Archiviazione log delle transazioni utilizzata | Byte | Quantità di spazio di archiviazione utilizzata dai log delle transazioni. | 
| write_throughput | Velocità effettiva scrittura | Byte | Byte scritti al secondo su disco. |
| write_iops | IOPS di scrittura | Conteggio | Numero di operazioni di scrittura I/O su disco dati al secondo. |

## <a name="server-logs"></a>Log del server
Database di Azure per PostgreSQL consente di configurare e accedere ai log standard di postgres. Per altre informazioni sui log, vedere la [documentazione](concepts-logging.md)relativa ai concetti di registrazione.
