---
title: Leggere le repliche di Database di Azure per PostgreSQL - Server singolo
description: Questo articolo descrive la funzionalità di lettura della replica nel Database di Azure per PostgreSQL - singolo Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/14/2019
ms.openlocfilehash: c98247b0ba8b670a59dec9aa3ec87e949f1dda78
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147935"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Leggere le repliche di Database di Azure per PostgreSQL - Server singolo

La funzionalità relativa alle repliche in lettura consente di replicare i dati dal server del Database di Azure per PostgreSQL ad un server di sola lettura. È possibile eseguire la replica dal server master per fino a cinque repliche. Le repliche in lettura vengono aggiornate in modo asincrono tramite la tecnologia di replica nativa del motore PostgreSQL.

> [!IMPORTANT]
> È possibile creare una replica di lettura nella stessa area del server master o in altre aree di Azure di propria scelta. La replica tra aree è attualmente in anteprima pubblica.

Le repliche sono nuovi server da gestire in modo simile ai normali server del Database di Azure per PostgreSQL. Per ogni replica in lettura, viene addebitato il costo delle risorse di calcolo e di archiviazione sottoposte a provisioning, espresse rispettivamente in vCore e GB/mese.

Informazioni su come [creare e gestire le repliche](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Quando usare una replica in lettura
La funzionalità di replica in lettura aiuta a migliorare prestazioni e scalabilità dei carichi di lavoro con utilizzo elevato della lettura. I carichi di lavoro di lettura possono essere isolati alle repliche, mentre i carichi di lavoro di scrittura possono essere indirizzati al master.

Uno scenario comune consiste nel fare in modo che i carichi di lavoro BI e analitici usino le repliche in lettura come origine dati per la creazione di report.

Poiché le repliche sono di sola lettura, non riducono direttamente gli oneri per la capacità di scrittura sul master. Questa funzionalità non è destinata a carichi di lavoro con utilizzo elevato di scrittura.

Questa funzionalità di replica in lettura si avvale della replica asincrona di PostgreSQL. La funzionalità non è concepita per scenari di replica sincrona. Esisterà un ritardo misurabile significativo tra il master e la replica. I dati nella replica diventano alla fine coerenti con i dati nel master. Usare questa funzionalità per i carichi di lavoro in grado di sostenere questo ritardo.

Lettura delle repliche possono migliorare il piano di ripristino di emergenza. È necessario disporre di una replica in un'area di Azure diversa dal server master. Se si verifica un'emergenza dell'area, è possibile arrestare la replica per la replica e reindirizzare il carico di lavoro a essa. L'arresto della replica consente alla replica iniziare ad accettare operazioni di scrittura, nonché legge. Altre informazioni, vedere la [arrestare la replica](#stop-replication) sezione. 

## <a name="create-a-replica"></a>Creare una replica
Il server master deve avere il`azure.replication_support`parametro impostato su **REPLICA**. Per rendere effettive eventuali modifiche di questo parametro è necessario riavviare il server. (Il parametro `azure.replication_support` si applica solo ai livelli Utilizzo generico e Con ottimizzazione per la memoria).

Quando si avvia il flusso di lavoro per la creazione della replica, viene creato un server di Database di Azure per PostgreSQL vuoto. Il nuovo server viene riempito con i dati presenti nel server master. Il tempo necessario per la creazione dipende dalla quantità di dati nel master e dal tempo trascorso dall'ultimo backup completo settimanale. Il tempo può variare da pochi minuti a diverse ore.

Ogni replica è abilitata per l'archiviazione [aumento automatico delle dimensioni](concepts-pricing-tiers.md#storage-auto-grow). La funzionalità aumento consente alla replica di allinearsi con i dati replicati ad esso ed evitare un'interruzione della replica causata da errori di memoria insufficiente.

La funzionalità di replica in lettura usa la replica fisica di PostgreSQL e non la replica logica. Lo streaming della replica usando gli slot di replica è la modalità operativa predefinita. Se necessario, viene usato il log shipping per mettersi in pari.

Informazioni su come [creare una replica di lettura nel portale di Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Connessione a una replica
Quando si crea una replica, questa non eredita le regole del firewall o l'endpoint del servizio rete virtuale del server master. Queste regole devono essere configurate in modo indipendente per la replica.

La replica eredita l'account amministratore dal server master. Tutti gli account utente nel server master vengono replicati nelle repliche in lettura. È possibile connettersi a una replica in lettura solo tramite gli account utente che sono disponibili nel server master.

È possibile connettersi alla replica usando il relativo nome host e un account utente valido, come si farebbe per un normale server di Database di Azure per PostgreSQL. Per un server denominato **repliche mia** con il nome utente amministratore **myadmin**, è possibile connettersi alla replica usando psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Quando richiesto, immettere la password per l'account dell'utente.

## <a name="monitor-replication"></a>Monitorare la replica
Database di Azure per PostgreSQL offre due metriche di monitoraggio della replica. Le due metriche sono **ritardo massimo tra repliche** e **Replica Lag**. Per informazioni su come visualizzare queste metriche, vedere la **monitorare una replica** sezione il [leggere l'articolo sulle procedure di replica](howto-read-replicas-portal.md).

Il **ritardo massimo tra repliche** metrica indica il ritardo in byte tra il master e la replica di massimo ritardo. Questa metrica è disponibile solo nel server master.

Il **Lag Replica** metrica Mostra il tempo dal momento dell'ultima riproduzione delle transazioni. Se non sono presenti transazioni sul server master, la metrica riflette questo intervallo di tempo. Questa metrica è disponibile per i server di replica solo. Ritardo di replica viene calcolato dal `pg_stat_wal_receiver` Vista:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Impostare un avviso per essere informati quando il ritardo di replica raggiunge un valore che non è accettabile per il carico di lavoro. 

Per altre informazioni dettagliate, eseguire una query direttamente sul server master per ottenere il ritardo di replica in byte per tutte le repliche.

In PostgreSQL versione 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

In PostgreSQL versione 9.6 e precedenti:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> In caso di riavvio di un server master o di una replica in lettura, il tempo necessario per il riavvio e per mettersi in pari sarà indicato nella metrica Replica Lag (Ritardo della replica).

## <a name="stop-replication"></a>Arrestare la replica
È possibile scegliere di arrestare la replica tra un master e una replica. L'interruzione dell'operazione causa il riavvio della replica e la rimozione delle impostazioni di replica. Dopo l'arresto della replica tra un server master e una replica in lettura, la replica diventa un server autonomo. I dati nel server autonomo sono i dati che erano disponibili nella replica al momento dell'esecuzione del comando di arresto della replica. Il server autonomo non è aggiornato con il server master.

> [!IMPORTANT]
> Il server autonomo non può essere di nuovo impostato come replica.
> Prima di arrestare la replica in una replica in lettura, assicurarsi che la replica abbia tutti i dati necessari.

Quando si arresta la replica, la replica perde tutti i collegamenti al relativo schema precedente e le altre repliche. Non vi è alcun failover automatico tra un master e la replica. 

Informazioni su come [arrestare la replica in una replica](howto-read-replicas-portal.md).


## <a name="considerations"></a>Considerazioni

Questa sezione riepiloga le considerazioni sulla funzionalità di replica in lettura.

### <a name="prerequisites"></a>Prerequisiti
Prima di creare una replica in lettura, il `azure.replication_support`parametro deve essere impostato su **REPLICA** nel server master. Per rendere effettive eventuali modifiche di questo parametro è necessario riavviare il server. Il parametro `azure.replication_support` si applica solo ai livelli Utilizzo generico e Con ottimizzazione per la memoria.

### <a name="new-replicas"></a>Nuove repliche
Una replica in lettura viene creata come nuovo server di Database di Azure per PostgreSQL. Un server esistente non può essere impostato come replica. Non è possibile creare una replica di un'altra replica in lettura.

### <a name="replica-configuration"></a>Configurazione della replica
Una replica viene creata usando la stessa configurazione server del master. Dopo aver creato una replica, è possibile modificare diverse impostazioni in modo indipendente dal server master: la generazione di calcolo, i vCore, l'archiviazione e il periodo di conservazione dei backup. È anche possibile modificare in modo indipendente il piano tariffario, tranne da o verso il livello Basic.

> [!IMPORTANT]
> Prima che la configurazione del server master venga aggiornata con nuovi valori, la configurazione delle repliche deve essere aggiornata impostandola su valori uguali o superiori. Questa azione garantisce che le repliche siano sempre aggiornate con le modifiche apportate al master.

PostgreSQL richiede che il valore del parametro`max_connections` sulla replica in lettura sia maggiore o uguale a quello del master; in caso contrario, la replica non si avvia. Nel Database di Azure per PostgreSQL, il valore del parametro `max_connections` è basato sullo SKU. Per altre informazioni, vedere [Limiti in Database di Azure per PostgreSQL](concepts-limits.md). 

Se si tenta di aggiornare i valori del server, ma non si rispettano i limiti, viene visualizzato un errore.

### <a name="maxpreparedtransactions"></a>max_prepared_transactions
[Richiede di PostgreSQL](https://www.postgresql.org/docs/10/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) il valore della `max_prepared_transactions` parametro sulla replica di lettura sia maggiore o uguale a quello master; in caso contrario, non si avvia la replica. Se si desidera modificare `max_prepared_transactions` sul master, prima di tutto modificarla nelle repliche.

### <a name="stopped-replicas"></a>Repliche arrestate
Se si arresta la replica tra un server master e una replica in lettura, la replica verrà riavviata per poter applicare tale modifica. La replica arrestata diventa un server autonomo che supporta sia la lettura che la scrittura. Il server autonomo non può essere di nuovo impostato come replica.

### <a name="deleted-master-and-standalone-servers"></a>Master eliminato e server autonomi
Quando viene eliminato un server master, tutte le relative repliche in lettura diventano server autonomi. Le repliche vengono riavviate in modo da riflettere questa modifica.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [creare e gestire le repliche in lettura nel portale di Azure](howto-read-replicas-portal.md).
