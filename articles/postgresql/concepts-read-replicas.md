---
title: Repliche in lettura in Database di Azure per PostgreSQL
description: Questo articolo descrive le repliche in lettura in Database di Azure per PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: 9270c3290bd7be0bbb79d30aff8becc04dcfc603
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903996"
---
# <a name="read-replicas-in-azure-database-for-postgresql"></a>Repliche in lettura in Database di Azure per PostgreSQL

> [!IMPORTANT]
> La funzionalità di replica in lettura è in anteprima pubblica.

La funzionalità relativa alle repliche in lettura consente di replicare i dati da Database di Azure per PostgreSQL (master) in un massimo di cinque server di sola lettura (repliche in lettura) nella stessa area di Azure. Le repliche in lettura vengono aggiornate in modo asincrono tramite la tecnologia di replica nativa del motore PostgreSQL.

Le repliche sono nuovi server che possono essere gestiti in modo analogo ai server di Database di Azure per PostgreSQL. Per ogni replica in lettura, viene addebitato il costo delle risorse di calcolo e di archiviazione sottoposte a provisioning, rispettivamente in vCore e in GB/mese.

## <a name="when-to-use-read-replicas"></a>Quando usare le repliche in lettura
La funzionalità di replica in lettura è progettata per migliorare prestazioni e scalabilità dei carichi di lavoro con utilizzo elevato della lettura. Ad esempio, i carichi di lavoro di lettura possono essere confinati alle repliche, mentre i carichi di lavoro di scrittura possono essere indirizzati al master.

Uno scenario comune consiste nel fare in modo che i carichi di lavoro BI e analitici usino le repliche in lettura come origine dati per la creazione di report.

Poiché le repliche sono di sola lettura, non contribuiscono direttamente ad alleviare il carico sulle capacità di scrittura nel master, pertanto questa funzionalità non è destinata a carichi di lavoro con utilizzo elevato della scrittura.

La funzionalità di replica in lettura usa la replica asincrona di PostgreSQL e quindi non è concepita per scenari di replica sincrona. Esisterà un ritardo misurabile significativo tra il master e la replica. I dati nella replica diventano alla fine coerenti con i dati nel master. Usare questa funzionalità per i carichi di lavoro in grado di sostenere questo ritardo.

## <a name="creating-a-replica"></a>Creazione di una replica
Il parametro **azure.replication_support** deve essere impostato su REPLICA nel server master. Per rendere effettive eventuali modifiche di questo parametro è necessario riavviare il server. (Il parametro **azure.replication_support** si applica solo ai livelli Utilizzo generico e Con ottimizzazione per la memoria.)

Quando si avvia il flusso di lavoro per la creazione della replica, viene creato un server di Database di Azure per PostgreSQL vuoto. Il nuovo server viene riempito con i dati presenti nel server master. Il tempo necessario per creare la nuova replica dipende dalla quantità di dati nel master e dal tempo trascorso dall'ultimo backup completo settimanale e può variare da pochi minuti ad alcune ore.

La funzionalità di replica in lettura usa la replica fisica di PostgreSQL e non la replica logica. Lo streaming della replica usando gli slot di replica è la modalità operativa predefinita. Se necessario, viene usato il log shipping per mettersi in pari.

> [!NOTE]
> Se non è già stato configurato un avviso di archiviazione nei server, è consigliabile farlo in modo da essere informati quando un server sta per raggiungere il limite di archiviazione, perché ciò influirà sulla replica.

[Informazioni su come creare una replica di lettura nel portale di Azure](howto-read-replicas-portal.md).

## <a name="connecting-to-a-replica"></a>Connessione a una replica
Quando si crea una replica, questa non eredita le regole del firewall o l'endpoint del servizio rete virtuale del server master. Queste regole devono essere configurate in modo indipendente per la replica.

La replica eredita l'account amministratore dal server master. Tutti gli account utente nel server master vengono replicati nelle repliche in lettura. È possibile connettersi a una replica in lettura solo tramite gli account utente disponibili nel server master.

È possibile connettersi alla replica usando il relativo nome host e un account utente valido, come si farebbe per un normale server di Database di Azure per PostgreSQL. Ad esempio, se il nome del server è myreplica e il nome utente dell'amministratore è myadmin è possibile connettersi a esso da psql come indicato di seguito:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```
Quando richiesto, immettere la password per l'account dell'utente.

## <a name="monitoring-replication"></a>Monitoraggio della replica
In Monitoraggio di Azure è disponibile la metrica **Max Lag Across Replicas** (Ritardo massimo tra repliche). Questa metrica è disponibile solo nel server master. La metrica indica il tempo che intercorre tra il master e la replica più in ritardo. 

In Monitoraggio di Azure è disponibile anche la metrica **Replica Lag** (Ritardo replica). Questa metrica è disponibile per solo le repliche. 

La metrica viene calcolata dalla vista pg_stat_wal_receiver:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())
```
Ricordarsi che la metrica Replica Lag (Ritardo metrica) mostra il tempo trascorso dall'ultima transazione riprodotta. Se non sono presenti transazioni sul master, la metrica riflette questo intervallo di tempo.

È consigliabile impostare un avviso per essere informati quando il ritardo di replica raggiunge un valore che non è accettabile per il carico di lavoro. 

Per altre informazioni dettagliate, eseguire una query direttamente sul server master per ottenere il ritardo di replica in byte per tutte le repliche.
In PG 10:
```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

In PG 9.6 e versioni precedenti:
```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> In caso di riavvio di un server master o di replica, il tempo necessario per il riavvio e per mettersi in pari sarà rispecchiato nella metrica Replica Lag (Ritardo di replica).

## <a name="stopping-replication-to-a-replica"></a>Arresto della replica in una replica
È possibile scegliere di arrestare la replica tra un master e una replica. Con questa operazione la replica verrà riavviata per rimuovere le impostazioni di replica. Dopo l'arresto della replica tra un server master e un server di replica, il server di replica diventa un server autonomo. I dati nel server autonomo sono i dati che erano disponibili nella replica al momento dell'esecuzione del comando di arresto della replica. Questo server autonomo non viene aggiornato con il server master.

Questo server non può essere di nuovo impostato come replica.

Assicurarsi che la replica abbia tutti i dati necessari prima di arrestare la replica.

È possibile trovare [informazioni su come arrestare una replica nella documentazione relativa alle procedure](howto-read-replicas-portal.md).


## <a name="considerations"></a>Considerazioni

### <a name="preparing-for-replica"></a>Preparazione per la replica
Il parametro **azure.replication_support** deve essere impostato su REPLICA nel server master prima di poter creare una replica. Per rendere effettive eventuali modifiche di questo parametro è necessario riavviare il server. Questo parametro si applica solo ai livelli Utilizzo generico e Con ottimizzazione per la memoria.

### <a name="stopped-replicas"></a>Repliche arrestate
Quando si sceglie di arrestare la replica tra un master e la replica, la replica verrà riavviata per applicare tali modifiche. In seguito, questo server non può essere di nuovo impostato come replica.

### <a name="replicas-are-new-servers"></a>Le repliche sono nuovi server
Le repliche vengono create come nuovi server di Database di Azure per PostgreSQL. I server esistenti non possono essere trasformati in repliche.

### <a name="replica-server-configuration"></a>Configurazione del server di replica
I server di replica vengono creati usando le stesse configurazioni server del master, incluse le seguenti:
- Piano tariffario
- Generazione di calcolo
- vCore
- Archiviazione
- Periodo di conservazione dei backup
- Opzione di ridondanza per il backup
- Versione del motore PostgreSQL

Dopo aver creato una replica, è possibile modificare il piano tariffario (tranne da e verso Basic), la generazione di calcolo, i vCore, l'archiviazione e il periodo di conservazione dei backup in modo indipendente dal server master.

> [!IMPORTANT]
> Prima che la configurazione del server master venga aggiornata con nuovi valori, la configurazione delle repliche deve essere aggiornata impostandola su valori uguali o superiori. Ciò garantisce che le repliche siano sempre aggiornate con le modifiche apportate al master.

In particolare, Postgres richiede che il valore del server di replica per il parametro max_connections sia maggiore o uguale al valore del master. In caso contrario, la replica non verrà avviata. In Database di Azure per PostgreSQL, il valore max_connections è impostato a seconda dello SKU. Per altre informazioni, leggere il [documento sui limiti](concepts-limits.md). 

Il tentativo di eseguire un aggiornamento che viola tale regola causerà un errore.


### <a name="deleting-the-master"></a>Eliminazione del master
Quando viene eliminato un server master, tutte le relative repliche in lettura diventano server autonomi. Le repliche verranno riavviate in modo da riflettere questa modifica.

### <a name="other"></a>Altri
- Le repliche in lettura possono essere create solo nella stessa area di Azure del master.
- La creazione di una replica di replica non è supportata.

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni su come creare e gestire le repliche in lettura nel portale di Azure](howto-read-replicas-portal.md).
