---
title: Leggere repliche-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive la funzionalità di lettura della replica nel database di Azure per PostgreSQL-server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: f093d9b1a67d5e6836fc7f760b0336c9923f5186
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902065"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Leggere le repliche nel database di Azure per PostgreSQL-server singolo

La funzionalità relativa alle repliche in lettura consente di replicare i dati dal server del Database di Azure per PostgreSQL ad un server di sola lettura. È possibile creare fino a un massimo di cinque repliche da un server master. Le repliche in lettura vengono aggiornate in modo asincrono tramite la tecnologia di replica nativa del motore PostgreSQL.

Le repliche sono nuovi server da gestire in modo simile ai normali server del Database di Azure per PostgreSQL. Per ogni replica in lettura, viene addebitato il costo delle risorse di calcolo e di archiviazione sottoposte a provisioning, espresse rispettivamente in vCore e GB/mese.

Informazioni su come [creare e gestire le repliche](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Quando usare una replica in lettura
La funzionalità di replica in lettura aiuta a migliorare prestazioni e scalabilità dei carichi di lavoro con utilizzo elevato della lettura. I carichi di lavoro di lettura possono essere isolati alle repliche, mentre i carichi di lavoro di scrittura possono essere indirizzati al master.

Uno scenario comune consiste nel fare in modo che i carichi di lavoro BI e analitici usino le repliche in lettura come origine dati per la creazione di report.

Poiché le repliche sono di sola lettura, non riducono direttamente gli oneri per la capacità di scrittura sul master. Questa funzionalità non è destinata a carichi di lavoro con utilizzo elevato di scrittura.

Questa funzionalità di replica in lettura si avvale della replica asincrona di PostgreSQL. La funzionalità non è concepita per scenari di replica sincrona. Esisterà un ritardo misurabile significativo tra il master e la replica. I dati nella replica diventano alla fine coerenti con i dati nel master. Usare questa funzionalità per i carichi di lavoro in grado di sostenere questo ritardo.

## <a name="cross-region-replication"></a>Replica tra più aree
È possibile creare una replica in lettura in un'area diversa dal server master. La replica tra più aree può essere utile per scenari come la pianificazione del ripristino di emergenza o per avvicinare i dati agli utenti.

>[!NOTE]
> I server di livello Basic supportano solo la replica della stessa area.

È possibile avere un server master in qualsiasi [area di database di Azure per PostgreSQL](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Per un server master può essere presente una replica nella relativa area associata o nelle aree di replica universali. L'immagine seguente illustra le aree di replica disponibili a seconda dell'area master.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Leggere le aree di replica":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Aree di replica universali
È sempre possibile creare una replica di lettura in una delle aree seguenti, indipendentemente dalla posizione in cui si trova il server master. Queste sono le aree di replica universale:

Australia orientale, Australia sudorientale, Stati Uniti centrali, Asia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, Regno Unito meridionale, Regno Unito occidentale, Europa occidentale, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti centro-occidentali.

### <a name="paired-regions"></a>Aree abbinate
Oltre alle aree di replica universali, è possibile creare una replica in lettura nell'area associata di Azure del server master. Se non si conosce la coppia di aree di appartenenza, vedere l'[articolo Aree associate di Azure](../best-practices-availability-paired-regions.md) per altre informazioni.

Se si usano repliche tra più aree per la pianificazione del ripristino di emergenza, è consigliabile creare la replica nell'area associata anziché in una delle altre aree. Le aree associate evitano aggiornamenti simultanei e assegnano priorità all'isolamento fisico e alla residenza dei dati.  

Ci sono alcune limitazioni da considerare: 

* Disponibilità a livello di area: database di Azure per PostgreSQL è disponibile in Francia centrale, Emirati Arabi Uniti settentrionali e Germania centrale. Tuttavia, le relative aree associate non sono disponibili.
    
* Coppie unidirezionali: alcune aree di Azure sono associate in una sola direzione. Queste aree includono l'India occidentale, Brasile meridionale. 
   Ciò significa che un server master in India occidentale può creare una replica in India meridionale. Al contrario, un server master in India meridionale non può creare una replica in India occidentale. Questo si verifica perché l'area secondaria dell'India occidentale è l'India meridionale, mentre l'area secondaria dell'India meridionale non è l'India occidentale.


## <a name="create-a-replica"></a>Creare una replica
Quando si avvia il flusso di lavoro per la creazione della replica, viene creato un server di Database di Azure per PostgreSQL vuoto. Il nuovo server viene riempito con i dati presenti nel server master. Il tempo necessario per la creazione dipende dalla quantità di dati nel master e dal tempo trascorso dall'ultimo backup completo settimanale. Il tempo può variare da pochi minuti a diverse ore.

Ogni replica è abilitata per l'[aumento automatico](concepts-pricing-tiers.md#storage-auto-grow) dello spazio di archiviazione. La funzionalità di aumento automatico consente alla replica di rimanere al passo con i dati replicati e impedire un'interruzioni della replica causata da errori di archiviazione indesiderati.

La funzionalità di replica in lettura usa la replica fisica di PostgreSQL e non la replica logica. Lo streaming della replica usando gli slot di replica è la modalità operativa predefinita. Se necessario, viene usato il log shipping per mettersi in pari.

Informazioni su come [creare una replica di lettura nel portale di Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Connessione a una replica
Quando si crea una replica, questa non eredita le regole del firewall o l'endpoint del servizio rete virtuale del server master. Queste regole devono essere configurate in modo indipendente per la replica.

La replica eredita l'account amministratore dal server master. Tutti gli account utente nel server master vengono replicati nelle repliche in lettura. È possibile connettersi a una replica in lettura solo tramite gli account utente che sono disponibili nel server master.

È possibile connettersi alla replica usando il relativo nome host e un account utente valido, come si farebbe per un normale server di Database di Azure per PostgreSQL. Per un server denominato **My replica** con il **nome utente amministratore amministratore,** è possibile connettersi alla replica usando PSQL:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Quando richiesto, immettere la password per l'account dell'utente.

## <a name="monitor-replication"></a>Monitorare la replica
Database di Azure per PostgreSQL offre due metriche per il monitoraggio della replica. Le due metriche sono il **ritardo massimo tra repliche** e **ritardo di replica**. Per informazioni su come visualizzare queste metriche, vedere la sezione **monitorare una replica** dell'articolo sulle [procedure di lettura della replica](howto-read-replicas-portal.md).

La metrica **Max lag tra repliche** indica il ritardo in byte tra il master e la replica più in ritardo. Questa metrica è disponibile solo nel server master.

La metrica **ritardo di replica** indica il tempo trascorso dall'ultima transazione rieseguita. Se non sono presenti transazioni sul server master, la metrica riflette questo intervallo di tempo. Questa metrica è disponibile solo per i server di replica. Il ritardo di replica viene calcolato dalla `pg_stat_wal_receiver` visualizzazione:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Impostare un avviso per essere informati quando il ritardo di replica raggiunge un valore che non è accettabile per il carico di lavoro. 

Per altre informazioni dettagliate, eseguire una query direttamente sul server master per ottenere il ritardo di replica in byte per tutte le repliche.

In PostgreSQL versione 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

In PostgreSQL versione 9.6 e precedenti:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> In caso di riavvio di un server master o di una replica in lettura, il tempo necessario per il riavvio e per mettersi in pari sarà indicato nella metrica Replica Lag (Ritardo della replica).

## <a name="stop-replication"></a>Arrestare la replica
È possibile scegliere di arrestare la replica tra un master e una replica. L'interruzione dell'operazione causa il riavvio della replica e la rimozione delle impostazioni di replica. Dopo l'arresto della replica tra un server master e una replica in lettura, la replica diventa un server autonomo. I dati nel server autonomo sono i dati che erano disponibili nella replica al momento dell'esecuzione del comando di arresto della replica. Il server autonomo non è aggiornato con il server master.

> [!IMPORTANT]
> Il server autonomo non può essere di nuovo impostato come replica.
> Prima di arrestare la replica in una replica in lettura, assicurarsi che la replica abbia tutti i dati necessari.

Quando si arresta la replica, la replica perde tutti i collegamenti al master precedente e ad altre repliche.

Informazioni su come [arrestare la replica in una replica](howto-read-replicas-portal.md).

## <a name="failover"></a>Failover
Non esiste un failover automatico tra i server master e di replica. 

Poiché la replica è asincrona, si verifica un ritardo tra il database master e la replica. La quantità di ritardo può essere influenzata da una serie di fattori quali la quantità di carico di lavoro in esecuzione nel server master e la latenza tra i Data Center. Nella maggior parte dei casi il ritardo della replica è compreso tra pochi secondi e un paio di minuti. È possibile tenere traccia dell'effettivo ritardo di replica usando l' *intervallo di replica*metrica, disponibile per ogni replica. Questa metrica indica il tempo trascorso dall'ultima transazione riprodotta. Si consiglia di identificare il ritardo medio osservando il ritardo della replica in un periodo di tempo. È possibile impostare un avviso per il ritardo di replica, in modo che se non rientra nell'intervallo previsto, è possibile intervenire.

> [!Tip]
> Se si esegue il failover alla replica, il ritardo nel momento in cui si scollega la replica dal database master indicherà la quantità di dati persi.

Dopo aver deciso di voler eseguire il failover a una replica, 

1. Arrestare la replica nella replica<br/>
   Questo passaggio è necessario per consentire al server di replica di accettare le Scritture. Come parte di questo processo, il server di replica verrà riavviato e verrà decollegato dal master. Una volta avviata l'arresto della replica, il completamento del processo back-end richiede in genere circa 2 minuti. Per comprendere le implicazioni di questa azione, vedere la sezione [arrestare la replica](#stop-replication) di questo articolo.
    
2. Puntare l'applicazione alla replica (precedente)<br/>
   Ogni server dispone di una stringa di connessione univoca. Aggiornare l'applicazione in modo che punti alla replica (precedente) invece che al database master.
    
Una volta che l'applicazione ha elaborato correttamente le operazioni di lettura e scrittura, il failover è stato completato. La quantità di tempo di inattività di cui l'applicazione dipenderà quando si rileva un problema e si completano i passaggi 1 e 2 precedenti.


## <a name="considerations"></a>Considerazioni

Questa sezione riepiloga le considerazioni sulla funzionalità di replica in lettura.

### <a name="prerequisites"></a>Prerequisiti
Le repliche e la [decodifica logica](concepts-logical.md) dipendono entrambi dal log write-ahead di Postgres (WAL) per informazioni. Queste due funzionalità richiedono diversi livelli di registrazione da postgres. Per la decodifica logica è necessario un livello di registrazione più elevato rispetto alla lettura delle repliche.

Per configurare il livello di registrazione corretto, usare il parametro di supporto della replica di Azure. Il supporto per la replica di Azure offre tre opzioni di impostazione:

* **Off** : inserisce le informazioni minime nell'oggetto Wal. Questa impostazione non è disponibile nella maggior parte dei server di database di Azure per PostgreSQL.  
* **Replica** : più dettagliata di **off**. Questo è il livello minimo di registrazione necessario per il funzionamento delle [repliche di lettura](concepts-read-replicas.md) . Questa impostazione è quella predefinita nella maggior parte dei server.
* **Logica** : più dettagliata rispetto alla **replica**. Questo è il livello minimo di registrazione per il funzionamento della decodifica logica. Le repliche di lettura funzionano anche con questa impostazione.

Il server deve essere riavviato dopo una modifica di questo parametro. Internamente, questo parametro imposta i parametri Postgres `wal_level` , `max_replication_slots` e `max_wal_senders` .

### <a name="new-replicas"></a>Nuove repliche
Una replica in lettura viene creata come nuovo server di Database di Azure per PostgreSQL. Un server esistente non può essere impostato come replica. Non è possibile creare una replica di un'altra replica in lettura.

### <a name="replica-configuration"></a>Configurazione della replica
Una replica viene creata usando le stesse impostazioni di calcolo e di archiviazione del database master. Dopo la creazione di una replica, è possibile modificare diverse impostazioni, tra cui archiviazione e periodo di conservazione dei backup.

Le regole del firewall, le regole della rete virtuale e le impostazioni dei parametri non vengono ereditate dal server master alla replica quando la replica viene creata o successivamente.

### <a name="scaling"></a>Scalabilità
Ridimensionamento di Vcore o tra per utilizzo generico e con ottimizzazione per la memoria:
* PostgreSQL richiede che l' `max_connections` impostazione in un server secondario sia [maggiore o uguale all'impostazione sul database primario](https://www.postgresql.org/docs/current/hot-standby.html). in caso contrario, il database secondario non verrà avviato.
* In database di Azure per PostgreSQL, il numero massimo di connessioni consentite per ogni server è fissato allo SKU di calcolo poiché le connessioni occupano memoria. È possibile ottenere altre informazioni sul [mapping tra max_connections e SKU di calcolo](concepts-limits.md).
* Scalabilità **verticale**: aumentare prima di tutto il calcolo di una replica, quindi aumentare le prestazioni del database primario. Questo ordine impedisce agli errori di violare il `max_connections` requisito.
* **Riduzione**delle prestazioni: prima di tutto ridurre le risorse di calcolo del database primario, quindi ridimensionare la replica. Se si prova a ridimensionare la replica in un livello inferiore rispetto al database primario, si verifica un errore perché questo viola il `max_connections` requisito.

Ridimensionamento dello spazio di archiviazione:
* Tutte le repliche hanno una crescita automatica dell'archiviazione abilitata per impedire problemi di replica da una replica di archiviazione completa. Questa impostazione non può essere disabilitata.
* È anche possibile scalare manualmente l'archiviazione, come per qualsiasi altro server


### <a name="basic-tier"></a>Livello Basic
I server di livello Basic supportano solo la replica della stessa area.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL richiede](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) che il valore del `max_prepared_transactions` parametro nella replica di lettura sia maggiore o uguale al valore master. in caso contrario, la replica non viene avviata. Se si desidera modificare `max_prepared_transactions` il database master, modificarlo prima nelle repliche.

### <a name="stopped-replicas"></a>Repliche arrestate
Se si arresta la replica tra un server master e una replica in lettura, la replica verrà riavviata per poter applicare tale modifica. La replica arrestata diventa un server autonomo che supporta sia la lettura che la scrittura. Il server autonomo non può essere di nuovo impostato come replica.

### <a name="deleted-master-and-standalone-servers"></a>Master eliminato e server autonomi
Quando viene eliminato un server master, tutte le relative repliche in lettura diventano server autonomi. Le repliche vengono riavviate in modo da riflettere questa modifica.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [creare e gestire le repliche in lettura nel portale di Azure](howto-read-replicas-portal.md).
* Informazioni su come [creare e gestire le repliche di lettura nell'interfaccia della riga di comando di Azure e nell'API REST](howto-read-replicas-cli.md).
