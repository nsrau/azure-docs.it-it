---
title: Leggere repliche-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive la funzionalità di lettura della replica nel database di Azure per PostgreSQL-server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: d1fa99d0954177e2804039fc71c2ba010b94bd50
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530941"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Leggere le repliche nel database di Azure per PostgreSQL-server singolo

La funzionalità relativa alle repliche in lettura consente di replicare i dati dal server del Database di Azure per PostgreSQL ad un server di sola lettura. È possibile eseguire la replica dal server primario a un massimo di cinque repliche. Le repliche in lettura vengono aggiornate in modo asincrono tramite la tecnologia di replica nativa del motore PostgreSQL.

Le repliche sono nuovi server da gestire in modo simile ai normali server del Database di Azure per PostgreSQL. Per ogni replica in lettura, viene addebitato il costo delle risorse di calcolo e di archiviazione sottoposte a provisioning, espresse rispettivamente in vCore e GB/mese.

Informazioni su come [creare e gestire le repliche](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Quando usare una replica in lettura
La funzionalità di replica in lettura aiuta a migliorare prestazioni e scalabilità dei carichi di lavoro con utilizzo elevato della lettura. I carichi di lavoro di lettura possono essere isolati con le repliche, mentre i carichi di lavoro di scrittura possono essere indirizzati al database primario.

Uno scenario comune consiste nel fare in modo che i carichi di lavoro BI e analitici usino le repliche in lettura come origine dati per la creazione di report.

Poiché le repliche sono di sola lettura, non riducono direttamente gli oneri di capacità di scrittura nel database primario. Questa funzionalità non è destinata a carichi di lavoro con utilizzo elevato di scrittura.

Questa funzionalità di replica in lettura si avvale della replica asincrona di PostgreSQL. La funzionalità non è concepita per scenari di replica sincrona. Si verifica un ritardo misurabile tra il database primario e la replica. I dati nella replica diventano infine coerenti con i dati nel database primario. Usare questa funzionalità per i carichi di lavoro in grado di sostenere questo ritardo.

## <a name="cross-region-replication"></a>Replica tra più aree
È possibile creare una replica di lettura in un'area diversa dal server primario. La replica tra più aree può essere utile per scenari come la pianificazione del ripristino di emergenza o per avvicinare i dati agli utenti.

>[!NOTE]
> I server di livello Basic supportano solo la replica della stessa area.

È possibile avere un server primario in qualsiasi [area di database di Azure per PostgreSQL](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Un server primario può avere una replica nell'area abbinata o nelle aree di replica universale. L'immagine seguente mostra le aree di replica disponibili a seconda dell'area primaria.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Leggere le aree di replica":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Aree di replica universali
È sempre possibile creare una replica di lettura in una delle aree seguenti, indipendentemente dalla posizione in cui si trova il server primario. Queste sono le aree di replica universale:

Australia orientale, Australia sudorientale, Stati Uniti centrali, Asia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, Regno Unito meridionale, Regno Unito occidentale, Europa occidentale, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti centro-occidentali.

### <a name="paired-regions"></a>Aree abbinate
Oltre alle aree di replica universale, è possibile creare una replica di lettura nell'area abbinata di Azure del server primario. Se non si conosce la coppia di aree di appartenenza, vedere l'[articolo Aree associate di Azure](../best-practices-availability-paired-regions.md) per altre informazioni.

Se si usano repliche tra più aree per la pianificazione del ripristino di emergenza, è consigliabile creare la replica nell'area associata anziché in una delle altre aree. Le aree associate evitano aggiornamenti simultanei e assegnano priorità all'isolamento fisico e alla residenza dei dati.  

Ci sono alcune limitazioni da considerare: 

* Disponibilità a livello di area: database di Azure per PostgreSQL è disponibile in Francia centrale, Emirati Arabi Uniti settentrionali e Germania centrale. Tuttavia, le relative aree associate non sono disponibili.
    
* Coppie unidirezionali: alcune aree di Azure sono associate in una sola direzione. Queste aree includono l'India occidentale, Brasile meridionale. 
   Ciò significa che un server primario nell'India occidentale può creare una replica nell'India meridionale. Tuttavia, un server primario nell'India meridionale non può creare una replica nell'India occidentale. Questo si verifica perché l'area secondaria dell'India occidentale è l'India meridionale, mentre l'area secondaria dell'India meridionale non è l'India occidentale.


## <a name="create-a-replica"></a>Creare una replica
Quando si avvia il flusso di lavoro per la creazione della replica, viene creato un server di Database di Azure per PostgreSQL vuoto. Il nuovo server viene compilato con i dati presenti sul server primario. Il tempo di creazione dipende dalla quantità di dati nel database primario e dall'ora dell'ultimo backup completo settimanale. Il tempo può variare da pochi minuti a diverse ore.

Ogni replica è abilitata per l'[aumento automatico](concepts-pricing-tiers.md#storage-auto-grow) dello spazio di archiviazione. La funzionalità di aumento automatico consente alla replica di rimanere al passo con i dati replicati e impedire un'interruzioni della replica causata da errori di archiviazione indesiderati.

La funzionalità di replica in lettura usa la replica fisica di PostgreSQL e non la replica logica. Lo streaming della replica usando gli slot di replica è la modalità operativa predefinita. Se necessario, viene usato il log shipping per mettersi in pari.

Informazioni su come [creare una replica di lettura nel portale di Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Connessione a una replica
Quando si crea una replica, non eredita le regole del firewall o l'endpoint del servizio VNet del server primario. Queste regole devono essere configurate in modo indipendente per la replica.

La replica eredita l'account amministratore dal server primario. Tutti gli account utente nel server primario vengono replicati nelle repliche di lettura. È possibile connettersi a una replica di lettura solo usando gli account utente disponibili nel server primario.

È possibile connettersi alla replica usando il relativo nome host e un account utente valido, come si farebbe per un normale server di Database di Azure per PostgreSQL. Per un server denominato **My replica** con il **nome utente amministratore amministratore,** è possibile connettersi alla replica usando PSQL:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Quando richiesto, immettere la password per l'account dell'utente.

## <a name="monitor-replication"></a>Monitorare la replica
Database di Azure per PostgreSQL offre due metriche per il monitoraggio della replica. Le due metriche sono il **ritardo massimo tra repliche** e **ritardo di replica**. Per informazioni su come visualizzare queste metriche, vedere la sezione **monitorare una replica** dell'articolo sulle [procedure di lettura della replica](howto-read-replicas-portal.md).

La metrica **Max lag tra repliche** indica il ritardo in byte tra la replica primaria e quella più in ritardo. Questa metrica è disponibile solo sul server primario.

La metrica **ritardo di replica** indica il tempo trascorso dall'ultima transazione rieseguita. Se non sono presenti transazioni nel server primario, la metrica riflette questo intervallo di tempo. Questa metrica è disponibile solo per i server di replica. Il ritardo di replica viene calcolato dalla `pg_stat_wal_receiver` visualizzazione:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Impostare un avviso per essere informati quando il ritardo di replica raggiunge un valore che non è accettabile per il carico di lavoro. 

Per ulteriori informazioni, eseguire una query sul server primario direttamente per ottenere l'intervallo di replica in byte in tutte le repliche.

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
> Se un server primario o una replica di lettura viene riavviata, il tempo necessario per il riavvio e l'aggiornamento viene riflesso nella metrica di ritardo di replica.

## <a name="stop-replication"></a>Arrestare la replica
È possibile arrestare la replica tra una replica primaria e una replica. L'interruzione dell'operazione causa il riavvio della replica e la rimozione delle impostazioni di replica. Dopo che la replica è stata interrotta tra un server primario e una replica di lettura, la replica diventa un server autonomo. I dati nel server autonomo sono i dati che erano disponibili nella replica al momento dell'esecuzione del comando di arresto della replica. Il server autonomo non si aggiorna con il server primario.

> [!IMPORTANT]
> Il server autonomo non può essere di nuovo impostato come replica.
> Prima di arrestare la replica in una replica in lettura, assicurarsi che la replica abbia tutti i dati necessari.

Quando si arresta la replica, la replica perde tutti i collegamenti al database primario e alle altre repliche precedenti.

Informazioni su come [arrestare la replica in una replica](howto-read-replicas-portal.md).

## <a name="failover"></a>Failover
Non esiste un failover automatico tra i server primario e quello di replica. 

Poiché la replica è asincrona, c'è un ritardo tra il database primario e la replica. La quantità di ritardo può essere influenzata da diversi fattori, ad esempio la quantità di carico di lavoro in esecuzione sul server primario e la latenza tra i Data Center. Nella maggior parte dei casi il ritardo della replica è compreso tra pochi secondi e un paio di minuti. È possibile tenere traccia dell'effettivo ritardo di replica usando l' *intervallo di replica*metrica, disponibile per ogni replica. Questa metrica indica il tempo trascorso dall'ultima transazione riprodotta. Si consiglia di identificare il ritardo medio osservando il ritardo della replica in un periodo di tempo. È possibile impostare un avviso per il ritardo di replica, in modo che se non rientra nell'intervallo previsto, è possibile intervenire.

> [!Tip]
> Se si esegue il failover alla replica, il ritardo nel momento in cui si scollega la replica dal database primario indicherà la quantità di dati persi.

Dopo aver deciso di voler eseguire il failover a una replica, 

1. Arrestare la replica nella replica<br/>
   Questo passaggio è necessario per consentire al server di replica di accettare le Scritture. Come parte di questo processo, il server di replica verrà riavviato e verrà decollegato dal database primario. Una volta avviata l'arresto della replica, il completamento del processo back-end richiede in genere circa 2 minuti. Per comprendere le implicazioni di questa azione, vedere la sezione [arrestare la replica](#stop-replication) di questo articolo.
    
2. Puntare l'applicazione alla replica (precedente)<br/>
   Ogni server dispone di una stringa di connessione univoca. Aggiornare l'applicazione in modo che punti alla replica (precedente) invece che al database primario.
    
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
Una replica viene creata usando le stesse impostazioni di calcolo e di archiviazione del database primario. Dopo la creazione di una replica, è possibile modificare diverse impostazioni, tra cui archiviazione e periodo di conservazione dei backup.

Le regole del firewall, le regole della rete virtuale e le impostazioni dei parametri non vengono ereditate dal server primario alla replica quando la replica viene creata o successivamente.

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
[PostgreSQL richiede](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) che il valore del `max_prepared_transactions` parametro nella replica di lettura sia maggiore o uguale al valore primario. in caso contrario, la replica non viene avviata. Se si desidera modificare il database `max_prepared_transactions` primario, modificarlo prima nelle repliche.

### <a name="stopped-replicas"></a>Repliche arrestate
Se si interrompe la replica tra un server primario e una replica di lettura, la replica viene riavviata per applicare la modifica. La replica arrestata diventa un server autonomo che supporta sia la lettura che la scrittura. Il server autonomo non può essere di nuovo impostato come replica.

### <a name="deleted-primary-and-standalone-servers"></a>Server primari e autonomi eliminati
Quando viene eliminato un server primario, tutte le relative repliche di lettura diventano server autonomi. Le repliche vengono riavviate in modo da riflettere questa modifica.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [creare e gestire le repliche in lettura nel portale di Azure](howto-read-replicas-portal.md).
* Informazioni su come [creare e gestire le repliche di lettura nell'interfaccia della riga di comando di Azure e nell'API REST](howto-read-replicas-cli.md).
