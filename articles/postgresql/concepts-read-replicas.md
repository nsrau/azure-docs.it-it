---
title: Replica di lettura - Database di Azure per PostgreSQL - Server singoloRead replicas - Azure Database for PostgreSQL - Single Server
description: Questo articolo descrive la funzionalità di replica di lettura nel database di Azure per PostgreSQL - Server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: fd6d3e24adfc22d2f6ea17f09b8dea4638a054b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76769036"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Leggere le repliche nel database di Azure per PostgreSQL - Server singoloRead replicas in Azure Database for PostgreSQL - Single Server

La funzionalità relativa alle repliche in lettura consente di replicare i dati dal server del Database di Azure per PostgreSQL ad un server di sola lettura. È possibile creare fino a un massimo di cinque repliche da un server master. Le repliche in lettura vengono aggiornate in modo asincrono tramite la tecnologia di replica nativa del motore PostgreSQL.

Le repliche sono nuovi server da gestire in modo simile ai normali server del Database di Azure per PostgreSQL. Per ogni replica in lettura, viene addebitato il costo delle risorse di calcolo e di archiviazione sottoposte a provisioning, espresse rispettivamente in vCore e GB/mese.

Informazioni su come [creare e gestire le repliche](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Quando usare una replica in lettura
La funzionalità di replica in lettura aiuta a migliorare prestazioni e scalabilità dei carichi di lavoro con utilizzo elevato della lettura. I carichi di lavoro di lettura possono essere isolati alle repliche, mentre i carichi di lavoro di scrittura possono essere indirizzati al master.

Uno scenario comune consiste nel fare in modo che i carichi di lavoro BI e analitici usino le repliche in lettura come origine dati per la creazione di report.

Poiché le repliche sono di sola lettura, non riducono direttamente gli oneri per la capacità di scrittura sul master. Questa funzionalità non è destinata a carichi di lavoro con utilizzo elevato di scrittura.

Questa funzionalità di replica in lettura si avvale della replica asincrona di PostgreSQL. La funzionalità non è concepita per scenari di replica sincrona. Esisterà un ritardo misurabile significativo tra il master e la replica. I dati nella replica diventano alla fine coerenti con i dati nel master. Usare questa funzionalità per i carichi di lavoro in grado di sostenere questo ritardo.

## <a name="cross-region-replication"></a>Replica tra aree
È possibile creare una replica di lettura in un'area diversa dal server master. La replica tra aree può essere utile per scenari come la pianificazione del ripristino di emergenza o per avvicinare i dati agli utenti.

È possibile avere un server master in qualsiasi database di [Azure per l'area PostgreSQL](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Un server master può avere una replica nella relativa area associata o nelle aree di replica universali. L'immagine seguente mostra quali aree di replica sono disponibili a seconda dell'area master.

[![Leggere le aree di replicaRead replica regions](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Aree di replica universali
È sempre possibile creare una replica di lettura in una delle aree seguenti, indipendentemente dalla posizione del server master. Queste sono le aree di replica universali:

Australia Orientale, Australia Sud-Est, Stati Uniti centrali, Asia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Giappone orientale, Giappone occidentale, Corea centrale, Corea del Sud, Stati Uniti centro-settentrionali, Nord Europa, Stati Uniti centro-meridionali, Sud-Est asiatico, Regno Unito Sud, Regno Unito occidentale, Europa occidentale, Stati Uniti occidentali.

-West US 2 è temporaneamente non disponibile come posizione di replica tra aree.


### <a name="paired-regions"></a>Aree abbinate
Oltre alle aree di replica universali, è possibile creare una replica di lettura nell'area associata di Azure del server master. Se non si conosce la coppia dell'area, è possibile ottenere altre informazioni [dall'articolo Aree associate](../best-practices-availability-paired-regions.md)di Azure.If you don't know your region's pair, you can learn more from the Azure Paired Regions article .

Se si utilizzano repliche tra aree per la pianificazione del ripristino di emergenza, è consigliabile creare la replica nell'area abbinata anziché in una delle altre aree. Le aree associate evitano gli aggiornamenti simultanei e assegnano priorità all'isolamento fisico e alla residenza dei dati.  

Ci sono limitazioni da considerare: 

* Disponibilità regionale: il database di Azure per PostgreSQL è disponibile in West US 2, France Central, UAE North e Germany Central. Tuttavia, le aree abbinate non sono disponibili.
    
* Coppie unidirezionali: alcune aree di Azure sono associate in una sola direzione. Queste regioni includono l'India occidentale, il Brasile meridionale. 
   Ciò significa che un server master nell'India occidentale può creare una replica nell'India meridionale. Tuttavia, un server master nell'India meridionale non può creare una replica nell'India occidentale. Questo perché la regione secondaria dell'India occidentale è l'India meridionale, ma la regione secondaria dell'India meridionale non è l'India occidentale.


## <a name="create-a-replica"></a>Creare una replica
Quando si avvia il flusso di lavoro per la creazione della replica, viene creato un server di Database di Azure per PostgreSQL vuoto. Il nuovo server viene riempito con i dati presenti nel server master. Il tempo necessario per la creazione dipende dalla quantità di dati nel master e dal tempo trascorso dall'ultimo backup completo settimanale. Il tempo può variare da pochi minuti a diverse ore.

Ogni replica è abilitata per l'archiviazione [automatica delle dimensioni.](concepts-pricing-tiers.md#storage-auto-grow) La funzionalità di aumento automatico delle dimensioni consente alla replica di tenere il passo con i dati replicati in essa e di evitare un'interruzione della replica causata da errori di archiviazione non insufficiente.

La funzionalità di replica in lettura usa la replica fisica di PostgreSQL e non la replica logica. Lo streaming della replica usando gli slot di replica è la modalità operativa predefinita. Se necessario, viene usato il log shipping per mettersi in pari.

Informazioni su come [creare una replica di lettura nel portale di Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Connessione a una replica
Quando si crea una replica, questa non eredita le regole del firewall o l'endpoint del servizio rete virtuale del server master. Queste regole devono essere configurate in modo indipendente per la replica.

La replica eredita l'account amministratore dal server master. Tutti gli account utente nel server master vengono replicati nelle repliche in lettura. È possibile connettersi a una replica in lettura solo tramite gli account utente che sono disponibili nel server master.

È possibile connettersi alla replica usando il relativo nome host e un account utente valido, come si farebbe per un normale server di Database di Azure per PostgreSQL. Per un server denominato **my replica** con il nome utente admin **myadmin**, è possibile connettersi alla replica utilizzando psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Quando richiesto, immettere la password per l'account dell'utente.

## <a name="monitor-replication"></a>Monitorare la replica
Database di Azure per PostgreSQL offre due metriche per il monitoraggio della replica. Le due metriche sono **Max Lag tra repliche** e Replica **Lag**. Per informazioni su come visualizzare queste metriche, vedere la sezione **Monitorare una replica** dell'articolo procedura di visualizzazione della replica di [lettura.](howto-read-replicas-portal.md)

La metrica **Max Lag Across Replicas** mostra il ritardo in byte tra il master e la replica più ritardata. Questa metrica è disponibile solo nel server master.

La **metrica Intervallo di replica** mostra il tempo trascorse dall'ultima transazione riprodotta. Se non sono presenti transazioni sul server master, la metrica riflette questo intervallo di tempo. Questa metrica è disponibile solo per i server di replica. Intervallo di replica `pg_stat_wal_receiver` viene calcolato dalla visualizzazione:

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

Quando si interrompe la replica, la replica perde tutti i collegamenti al master precedente e ad altre repliche.

Informazioni su come [arrestare la replica in una replica](howto-read-replicas-portal.md).

## <a name="failover"></a>Failover
Non esiste alcun failover automatico tra i server master e i server di replica. 

Poiché la replica è asincrona, si registra un ritardo tra il master e la replica. La quantità di ritardo può essere influenzata da una serie di fattori, ad esempio l'elevato carico di lavoro in esecuzione sul server master e la latenza tra i data center. Nella maggior parte dei casi, il ritardo di replica varia da pochi secondi a un paio di minuti. È possibile tenere traccia del ritardo di replica effettivo utilizzando la metrica *Replica lag*, disponibile per ogni replica. Questa metrica mostra il tempo dall'ultima transazione riprodotta. È consigliabile identificare il ritardo medio osservando il ritardo di replica in un periodo di tempo. È possibile impostare un avviso in caso di ritardo della replica, in modo che se non rientra nell'intervallo previsto, è possibile eseguire un'azione.

> [!Tip]
> Se si esegue il failover alla replica, il ritardo al momento dello scollegamento della replica dal master indicherà la quantità di dati persi.

Dopo aver deciso di eseguire il failover a una replica, 

1. Interrompere la replica nella replica<br/>
   Questo passaggio è necessario per rendere il server di replica in grado di accettare le scritture. Come parte di questo processo, il server di replica verrà riavviato e verrà decollegato dal database master. Una volta avviata l'interruzione della replica, il processo back-end richiede in genere circa 2 minuti. Vedere la sezione [Interrompi replica](#stop-replication) di questo articolo per comprendere le implicazioni di questa azione.
    
2. Puntare l'applicazione alla replica (precedente)<br/>
   Ogni server ha una stringa di connessione univoca. Aggiornare l'applicazione in modo che punti alla replica (precedente) anziché al master.
    
Dopo che l'applicazione sta elaborando correttamente le letture e le scritture, è stato completato il failover. La quantità di tempo di inattività delle esperienze dell'applicazione dipenderà dal momento in cui si rileva un problema e si completano i passaggi 1 e 2 precedenti.


## <a name="considerations"></a>Considerazioni

Questa sezione riepiloga le considerazioni sulla funzionalità di replica in lettura.

### <a name="prerequisites"></a>Prerequisiti
Prima di creare una replica in lettura, il `azure.replication_support`parametro deve essere impostato su **REPLICA** nel server master. Per rendere effettive eventuali modifiche di questo parametro è necessario riavviare il server. Il parametro `azure.replication_support` si applica solo ai livelli Utilizzo generico e Con ottimizzazione per la memoria.

### <a name="new-replicas"></a>Nuove repliche
Una replica in lettura viene creata come nuovo server di Database di Azure per PostgreSQL. Un server esistente non può essere impostato come replica. Non è possibile creare una replica di un'altra replica in lettura.

### <a name="replica-configuration"></a>Configurazione della replica
Una replica viene creata utilizzando le stesse impostazioni di calcolo e archiviazione del master. Dopo aver creato una replica, è possibile modificare diverse impostazioni in modo indipendente dal server master: la generazione di calcolo, i vCore, l'archiviazione e il periodo di conservazione dei backup. È anche possibile modificare in modo indipendente il piano tariffario, tranne da o verso il livello Basic.

> [!IMPORTANT]
> Prima che un'impostazione master venga aggiornata a un nuovo valore, aggiornare la configurazione della replica su un valore uguale o superiore. Questa azione garantisce che le repliche siano sempre aggiornate con le modifiche apportate al master.

PostgreSQL richiede che il valore del parametro`max_connections` sulla replica in lettura sia maggiore o uguale a quello del master; in caso contrario, la replica non si avvia. Nel Database di Azure per PostgreSQL, il valore del parametro `max_connections` è basato sullo SKU. Per altre informazioni, vedere [Limiti in Database di Azure per PostgreSQL](concepts-limits.md). 

Se si tenta di aggiornare i valori del server descritti in precedenza, ma non rispettare i limiti, viene visualizzato un errore.

Le regole del firewall, le regole di rete virtuale e le impostazioni dei parametri non vengono ereditate dal server master alla replica quando la replica viene creata o successivamente.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL richiede che](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) `max_prepared_transactions` il valore del parametro nella replica di lettura sia maggiore o uguale al valore master. in caso contrario, la replica non verrà avviata. Se si desidera `max_prepared_transactions` modificare il master, prima modificarlo nelle repliche.

### <a name="stopped-replicas"></a>Repliche arrestate
Se si arresta la replica tra un server master e una replica in lettura, la replica verrà riavviata per poter applicare tale modifica. La replica arrestata diventa un server autonomo che supporta sia la lettura che la scrittura. Il server autonomo non può essere di nuovo impostato come replica.

### <a name="deleted-master-and-standalone-servers"></a>Master eliminato e server autonomi
Quando viene eliminato un server master, tutte le relative repliche in lettura diventano server autonomi. Le repliche vengono riavviate in modo da riflettere questa modifica.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [creare e gestire le repliche in lettura nel portale di Azure](howto-read-replicas-portal.md).
* Informazioni su come creare e gestire repliche di [lettura nell'interfaccia della riga di comando di Azure e nell'API REST.](howto-read-replicas-cli.md)
