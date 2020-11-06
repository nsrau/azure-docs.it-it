---
title: Leggere repliche-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive la funzionalità di lettura della replica nel database di Azure per PostgreSQL-server singolo.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: 8fabf8169270c3162604b6535a6cf2fb07cd9a9d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422145"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Leggere le repliche nel database di Azure per PostgreSQL-server singolo

La funzionalità relativa alle repliche in lettura consente di replicare i dati dal server del Database di Azure per PostgreSQL ad un server di sola lettura. Le repliche vengono aggiornate in **modo asincrono** con la tecnologia di replica fisica nativa del motore PostgreSQL. È possibile eseguire la replica dal server primario a un massimo di cinque repliche.

Le repliche sono nuovi server da gestire in modo simile ai normali server del Database di Azure per PostgreSQL. Per ogni replica in lettura, viene addebitato il costo delle risorse di calcolo e di archiviazione sottoposte a provisioning, espresse rispettivamente in vCore e GB/mese.

Informazioni su come [creare e gestire le repliche](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Quando usare una replica in lettura
La funzionalità di replica in lettura aiuta a migliorare prestazioni e scalabilità dei carichi di lavoro con utilizzo elevato della lettura. I carichi di lavoro di lettura possono essere isolati con le repliche, mentre i carichi di lavoro di scrittura possono essere indirizzati al database primario. Le repliche di lettura possono anche essere distribuite in un'area diversa e possono essere innalzate di livello come server di lettura/scrittura in caso di ripristino di emergenza.

Uno scenario comune consiste nel fare in modo che i carichi di lavoro BI e analitici usino le repliche in lettura come origine dati per la creazione di report.

Poiché le repliche sono di sola lettura, non riducono direttamente gli oneri di capacità di scrittura nel database primario.

### <a name="considerations"></a>Considerazioni
Questa funzionalità è destinata agli scenari in cui il ritardo è accettabile e destinato all'offload delle query. Non è concepito per gli scenari di replica sincrona in cui è previsto che i dati della replica siano aggiornati. Si verifica un ritardo misurabile tra il database primario e la replica. Questo può essere in minuti o addirittura in ore, a seconda del carico di lavoro e della latenza tra il database primario e la replica. I dati nella replica diventano infine coerenti con i dati nel database primario. Usare questa funzionalità per i carichi di lavoro in grado di sostenere questo ritardo. 

> [!NOTE]
> Per la maggior parte dei carichi di lavoro, le repliche offrono aggiornamenti quasi in tempo reale dal database primario. Tuttavia, con carichi di lavoro primari intensi a elevato utilizzo di scrittura, è possibile che l'intervallo di replica continui ad aumentare e non sia mai in grado di recuperare il database primario. Questo può anche aumentare l'utilizzo dell'archiviazione nel database primario perché i file WAL non vengono eliminati fino a quando non vengono ricevuti dalla replica. Se questa situazione viene mantenute, l'eliminazione e la ricreazione della replica di lettura dopo il completamento dei carichi di lavoro a elevato utilizzo di scrittura è l'opzione per riportare la replica a uno stato valido rispetto al ritardo.
> Le repliche di lettura asincrone non sono adatte per carichi di lavoro di scrittura intensivi. Quando si valutano le repliche di lettura per l'applicazione, monitorare l'intervallo di tempo per la replica per un ciclo di carico di lavoro dell'app completo attraverso il picco e le ore non di punta per accedere al ritardo possibile e alle RTO/RPO previste in diversi punti del ciclo del carico di lavoro.
> 
## <a name="cross-region-replication"></a>Replica tra più aree
È possibile creare una replica di lettura in un'area diversa dal server primario. La replica tra più aree può essere utile per scenari come la pianificazione del ripristino di emergenza o per avvicinare i dati agli utenti.

>[!NOTE]
> I server di livello Basic supportano solo la replica della stessa area.

È possibile avere un server primario in qualsiasi [area di database di Azure per PostgreSQL](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Un server primario può avere una replica nell'area abbinata o nelle aree di replica universale. L'immagine seguente mostra le aree di replica disponibili a seconda dell'area primaria.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Leggere le aree di replica":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Aree di replica universali
È sempre possibile creare una replica di lettura in una delle aree seguenti, indipendentemente dalla posizione in cui si trova il server primario. Queste sono le aree di replica universale:

Australia orientale, Australia sudorientale, Brasile meridionale, Canada centrale, Canada orientale, Stati Uniti centrali, Asia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, Regno Unito meridionale, Regno Unito occidentale, Europa occidentale, Stati Uniti occidentali, Stati Uniti occidentali

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

```bash
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Quando richiesto, immettere la password per l'account dell'utente.

## <a name="monitor-replication"></a>Monitorare la replica
Database di Azure per PostgreSQL offre due metriche per il monitoraggio della replica. Le due metriche sono il **ritardo massimo tra repliche** e **ritardo di replica**. Per informazioni su come visualizzare queste metriche, vedere la sezione **monitorare una replica** dell'articolo sulle [procedure di lettura della replica](howto-read-replicas-portal.md).

La metrica **Max lag tra repliche** indica il ritardo in byte tra la replica primaria e quella più in ritardo. Questa metrica è applicabile e disponibile solo sul server primario e sarà disponibile solo se almeno una della replica di lettura è connessa al database primario e il database primario è in modalità di replica di flusso. Le informazioni relative al ritardo non visualizzano i dettagli quando la replica è in fase di recupero con il database primario usando i log archiviati del database primario in modalità di replica per la distribuzione di file.

La metrica **ritardo di replica** indica il tempo trascorso dall'ultima transazione rieseguita. Se non sono presenti transazioni nel server primario, la metrica riflette questo intervallo di tempo. Questa metrica è applicabile e disponibile solo per i server di replica. Il ritardo di replica viene calcolato dalla `pg_stat_wal_receiver` visualizzazione:

```SQL
SELECT EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Impostare un avviso per essere informati quando il ritardo di replica raggiunge un valore che non è accettabile per il carico di lavoro. 

Per ulteriori informazioni, eseguire una query sul server primario direttamente per ottenere l'intervallo di replica in byte in tutte le repliche.

> [!NOTE]
> Se un server primario o una replica di lettura viene riavviata, il tempo necessario per il riavvio e l'aggiornamento viene riflesso nella metrica di ritardo di replica.

## <a name="stop-replication--promote-replica"></a>Interrompi replica/Promuovi replica
È possibile arrestare la replica tra una replica primaria e una replica in qualsiasi momento. L'azione di arresto causa il riavvio della replica e promuove la replica in modo che sia un server autonomo di lettura scrivibile autonomo. I dati nel server autonomo sono quelli disponibili nel server di replica nel momento in cui la replica viene arrestata. Eventuali aggiornamenti successivi nel database primario non vengono propagati alla replica. Tuttavia, è possibile che nel server di replica siano stati accumulati log non ancora applicati. Come parte del processo di riavvio, la replica applica tutti i log in sospeso prima di accettare le connessioni client.  

### <a name="considerations"></a>Considerazioni
- Prima di arrestare la replica in una replica di lettura, verificare l'intervallo di replica per assicurarsi che la replica disponga di tutti i dati necessari. 
- Poiché la replica di lettura deve applicare tutti i log in sospeso prima che sia possibile renderlo un server autonomo, RTO può essere maggiore per i carichi di lavoro di scrittura pesanti quando si verifica l'arresto della replica in quanto potrebbe verificarsi un ritardo significativo sulla replica. Prestare attenzione quando si pianifica la promozione di una replica.
- Non è possibile riportare il server di replica promosso in una replica.
- Se si alza di livello una replica come server primario, non è possibile ripristinare la replica al server primario precedente. Se si vuole tornare alla precedente area primaria, è possibile stabilire un nuovo server di replica con un nuovo nome (o) eliminare il database primario precedente e creare una replica usando il vecchio nome primario.
- Se si dispone di più repliche di lettura e si promuove una di esse come server primario, altri server di replica sono ancora connessi al database primario precedente. Potrebbe essere necessario ricreare le repliche dal nuovo server promosso.

Quando si arresta la replica, la replica perde tutti i collegamenti al database primario e alle altre repliche precedenti.

Informazioni su come [arrestare la replica in una replica](howto-read-replicas-portal.md).

## <a name="failover-to-replica"></a>Failover alla replica

In caso di errore del server primario, **non** viene eseguito automaticamente il failover alla replica di lettura. 

Poiché la replica è asincrona, potrebbe verificarsi un notevole ritardo tra il database primario e la replica. La quantità di ritardo è influenzata da diversi fattori, ad esempio il tipo di carico di lavoro in esecuzione sul server primario e la latenza tra il server primario e quello di replica. Nei casi tipici con carico di lavoro di scrittura nominale, è previsto un ritardo di replica compreso tra pochi secondi e pochi minuti. Tuttavia, nei casi in cui il database primario esegue un carico di lavoro molto elevato a elevato utilizzo di scrittura e la replica non viene rilevata abbastanza rapidamente, il ritardo può essere molto più alto. È possibile tenere traccia del ritardo di replica per ogni replica usando l' *intervallo di replica* metrica. Questa metrica indica il tempo trascorso dall'ultima transazione riprodotta nella replica. Si consiglia di identificare il ritardo medio osservando il ritardo della replica in un periodo di tempo. È possibile impostare un avviso per il ritardo di replica, in modo che se non rientra nell'intervallo previsto, si riceverà una notifica per intervenire.

> [!Tip]
> Se si esegue il failover alla replica, il ritardo nel momento in cui si scollega la replica dal database primario indicherà la quantità di dati persi.

Dopo aver deciso di voler eseguire il failover a una replica, 

1. Arrestare la replica nella replica<br/>
   Questo passaggio è necessario per fare in modo che il server di replica diventi un server autonomo ed è in grado di accettare le Scritture. Come parte di questo processo, il server di replica verrà riavviato e verrà decollegato dal database primario. Una volta avviata l'arresto della replica, il processo back-end richiede in genere pochi minuti per applicare eventuali log residui che non sono stati ancora applicati e per aprire il database come server di lettura/scrittura. Per comprendere le implicazioni di questa azione, vedere la sezione [arrestare la replica](#stop-replication--promote-replica) di questo articolo.
    
2. Puntare l'applicazione alla replica (precedente)<br/>
   Ogni server dispone di una stringa di connessione univoca. Aggiornare la stringa di connessione dell'applicazione in modo che punti alla replica (precedente) invece che al database primario.
    
Una volta che l'applicazione ha elaborato correttamente le operazioni di lettura e scrittura, il failover è stato completato. La quantità di tempo di inattività di cui l'applicazione dipenderà quando si rileva un problema e si completano i passaggi 1 e 2 precedenti.

### <a name="disaster-recovery"></a>Ripristino di emergenza

Quando si verifica un evento di emergenza grave, ad esempio un errore a livello di zona di disponibilità o di area, è possibile eseguire un'operazione di ripristino di emergenza promuovendo la replica di lettura. Dal portale dell'interfaccia utente è possibile passare al server di replica di lettura. Fare quindi clic sulla scheda replica ed è possibile arrestare la replica per innalzarla di livello come server indipendente. In alternativa, è possibile usare l' [interfaccia](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop) della riga di comando di Azure per arrestare e alzare di livello il server di replica.

## <a name="considerations"></a>Considerazioni

Questa sezione riepiloga le considerazioni sulla funzionalità di replica in lettura.

### <a name="prerequisites"></a>Prerequisiti
Le repliche e la [decodifica logica](concepts-logical.md) dipendono entrambi dal log write-ahead di Postgres (WAL) per informazioni. Queste due funzionalità richiedono diversi livelli di registrazione da postgres. Per la decodifica logica è necessario un livello di registrazione più elevato rispetto alla lettura delle repliche.

Per configurare il livello di registrazione corretto, usare il parametro di supporto della replica di Azure. Il supporto per la replica di Azure offre tre opzioni di impostazione:

* **Off** : inserisce le informazioni minime nell'oggetto Wal. Questa impostazione non è disponibile nella maggior parte dei server di database di Azure per PostgreSQL.  
* **Replica** : più dettagliata di **off**. Questo è il livello minimo di registrazione necessario per il funzionamento delle [repliche di lettura](concepts-read-replicas.md) . Questa impostazione è quella predefinita nella maggior parte dei server.
* **Logica** : più dettagliata rispetto alla **replica**. Questo è il livello minimo di registrazione per il funzionamento della decodifica logica. Le repliche di lettura funzionano anche con questa impostazione.


### <a name="new-replicas"></a>Nuove repliche
Una replica in lettura viene creata come nuovo server di Database di Azure per PostgreSQL. Un server esistente non può essere impostato come replica. Non è possibile creare una replica di un'altra replica in lettura.

### <a name="replica-configuration"></a>Configurazione della replica
Una replica viene creata usando le stesse impostazioni di calcolo e di archiviazione del database primario. Dopo la creazione di una replica, è possibile modificare diverse impostazioni, tra cui archiviazione e periodo di conservazione dei backup.

Le regole del firewall, le regole della rete virtuale e le impostazioni dei parametri non vengono ereditate dal server primario alla replica quando la replica viene creata o successivamente.

### <a name="scaling"></a>Scalabilità
Ridimensionamento di Vcore o tra per utilizzo generico e con ottimizzazione per la memoria:
* PostgreSQL richiede che l' `max_connections` impostazione in un server secondario sia [maggiore o uguale all'impostazione sul database primario](https://www.postgresql.org/docs/current/hot-standby.html). in caso contrario, il database secondario non verrà avviato.
* In database di Azure per PostgreSQL, il numero massimo di connessioni consentite per ogni server è fissato allo SKU di calcolo poiché le connessioni occupano memoria. È possibile ottenere altre informazioni sul [mapping tra max_connections e SKU di calcolo](concepts-limits.md).
* Scalabilità **verticale** : aumentare prima di tutto il calcolo di una replica, quindi aumentare le prestazioni del database primario. Questo ordine impedisce agli errori di violare il `max_connections` requisito.
* **Riduzione** delle prestazioni: prima di tutto ridurre le risorse di calcolo del database primario, quindi ridimensionare la replica. Se si prova a ridimensionare la replica in un livello inferiore rispetto al database primario, si verifica un errore perché questo viola il `max_connections` requisito.

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