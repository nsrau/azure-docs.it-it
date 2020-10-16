---
title: Leggere repliche-database di Azure per MariaDB
description: 'Informazioni sulle repliche di lettura nel database di Azure per MariaDB: scelta delle aree, creazione di repliche, connessione alle repliche, monitoraggio della replica e arresto della replica.'
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: fcf368c9fbbb185ac3f47faa5705e1933d085c81
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126454"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Repliche in lettura in Database di Azure per MariaDB

La funzionalità relativa alle repliche in lettura consente di replicare i dati dal server del Database di Azure per MariaDB a un server di sola lettura. È possibile creare fino a un massimo di cinque repliche da un server di origine. Le repliche vengono aggiornate in modo asincrono utilizzando la tecnologia di replica basata su posizione del file binlog (binary log) del motore MariaDB con l'ID di transazione globale (GTID). Per altre informazioni su questo tipo di replica, vedere [binlog replication overview](https://mariadb.com/kb/en/library/replication-overview/) (Panoramica della replica basata su binlog).

Le repliche sono nuovi server gestiti in modo analogo al normale database di Azure per i server MariaDB. Per ogni replica in lettura, viene addebitato il costo delle risorse di calcolo e di archiviazione sottoposte a provisioning, espresse rispettivamente in vCore e GB/mese.

Per ulteriori informazioni sulla replica GTID, vedere la [documentazione relativa alla replica di MariaDB](https://mariadb.com/kb/en/library/gtid/).

> [!NOTE]
> Comunicazione senza distorsione
>
> Microsoft supporta un ambiente eterogeneo e di inclusione. Questo articolo contiene riferimenti alla parola _slave_. La [Guida di stile Microsoft per la comunicazione senza distorsione](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) riconosce questo aspetto come una parola di esclusione. La parola viene usata in questo articolo per coerenza perché è attualmente la parola che viene visualizzata nel software. Quando il software viene aggiornato per rimuovere la parola, questo articolo verrà aggiornato in modo da essere allineato.
>

## <a name="when-to-use-a-read-replica"></a>Quando usare una replica in lettura

La funzionalità di replica in lettura aiuta a migliorare prestazioni e scalabilità dei carichi di lavoro con utilizzo elevato della lettura. I carichi di lavoro di lettura possono essere isolati alle repliche, mentre i carichi di lavoro di scrittura possono essere indirizzati al master.

Uno scenario comune consiste nel fare in modo che i carichi di lavoro BI e analitici usino le repliche in lettura come origine dati per la creazione di report.

Poiché le repliche sono di sola lettura, non riducono direttamente gli oneri per la capacità di scrittura sul master. Questa funzionalità non è destinata a carichi di lavoro con utilizzo elevato di scrittura.

La funzionalità di lettura della replica utilizza la replica asincrona. La funzionalità non è concepita per scenari di replica sincrona. Si verifica un ritardo misurabile tra l'origine e la replica. I dati nella replica diventano alla fine coerenti con i dati nel master. Usare questa funzionalità per i carichi di lavoro in grado di sostenere questo ritardo.

## <a name="cross-region-replication"></a>Replica tra più aree
È possibile creare una replica di lettura in un'area diversa dal server di origine. La replica tra più aree può essere utile per scenari come la pianificazione del ripristino di emergenza o per avvicinare i dati agli utenti.

È possibile avere un server di origine in qualsiasi [database di Azure per l'area MariaDB](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb).  Un server di origine può avere una replica nell'area abbinata o nelle aree della replica universale. L'immagine seguente mostra le aree di replica disponibili a seconda dell'area di origine.

[ ![Aree di replica in lettura](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Aree di replica universali
È possibile creare una replica di lettura in una delle aree seguenti, indipendentemente dalla posizione in cui si trova il server di origine. Le aree di replica universali supportate includono:

Australia orientale, Australia sudorientale, Brasile meridionale, Canada centrale, Canada orientale, Stati Uniti centrali, Asia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, Regno Unito meridionale, Regno Unito occidentale, Europa occidentale, Stati Uniti occidentali, Stati Uniti occidentali

### <a name="paired-regions"></a>Aree abbinate
Oltre alle aree di replica universale, è possibile creare una replica di lettura nell'area abbinata di Azure del server di origine. Se non si conosce la coppia di aree di appartenenza, vedere l'[articolo Aree associate di Azure](../best-practices-availability-paired-regions.md) per altre informazioni.

Se si usano repliche tra più aree per la pianificazione del ripristino di emergenza, è consigliabile creare la replica nell'area associata anziché in una delle altre aree. Le aree associate evitano aggiornamenti simultanei e assegnano priorità all'isolamento fisico e alla residenza dei dati.  

È tuttavia necessario considerare due limitazioni: 

* Disponibilità a livello di area: database di Azure per MariaDB è disponibile in Francia centrale, Emirati Arabi Uniti settentrionali e Germania centrale. Tuttavia, le relative aree associate non sono disponibili.
    
* Coppie unidirezionali: alcune aree di Azure sono associate in una sola direzione. Queste aree includono India occidentale, Brasile meridionale e US Gov Virginia. 
   Ciò significa che un server di origine nell'India occidentale può creare una replica nell'India meridionale. Tuttavia, un server di origine in India meridionale non è in grado di creare una replica nell'India occidentale. Questo si verifica perché l'area secondaria dell'India occidentale è l'India meridionale, mentre l'area secondaria dell'India meridionale non è l'India occidentale.

## <a name="create-a-replica"></a>Creare una replica

> [!IMPORTANT]
> La funzionalità di lettura della replica è disponibile solo per i server di database di Azure per MariaDB nei piani tariffari per utilizzo generico o con ottimizzazione per la memoria. Verificare che il server di origine si trovi in uno di questi piani tariffari.

Se un server di origine non dispone di server di replica esistenti, l'origine viene innanzitutto riavviata per prepararsi per la replica.

Quando si avvia il flusso di lavoro di creazione della replica, viene creato un database di Azure vuoto per il server MariaDB. Il nuovo server viene compilato con i dati presenti nel server di origine. Il tempo di creazione dipende dalla quantità di dati nell'origine e dall'ora dell'ultimo backup completo settimanale. Il tempo può variare da pochi minuti a diverse ore.

> [!NOTE]
> Se non si ha un avviso di archiviazione impostato nei server, si consiglia di impostarlo. L'avviso informa l'utente quando un server sta per raggiungere il limite di archiviazione, che influisce sulla replica.

Informazioni su come [creare una replica di lettura nel portale di Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Connessione a una replica

Al momento della creazione, una replica eredita le regole del firewall del server di origine. Successivamente, queste regole sono indipendenti dal server di origine.

La replica eredita l'account amministratore dal server di origine. Tutti gli account utente nel server di origine vengono replicati nelle repliche di lettura. È possibile connettersi a una replica di lettura solo utilizzando gli account utente disponibili nel server di origine.

È possibile connettersi alla replica usando il nome host e un account utente valido, come per un normale database di Azure per il server MariaDB. Per un server denominato **myreplica** con il nome utente amministratore **myadmin** è possibile connettersi alla replica usando l'interfaccia della riga di comando di mysql:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

Quando richiesto, immettere la password per l'account dell'utente.

## <a name="monitor-replication"></a>Monitorare la replica

Il database di Azure per MariaDB fornisce la metrica di **ritardo della replica in secondi** in monitoraggio di Azure. Questa metrica è disponibile per solo le repliche.

Questa metrica viene calcolata usando la `seconds_behind_master` metrica disponibile nel comando di MariaDB `SHOW SLAVE STATUS` .

Impostare un avviso per informare l'utente quando il ritardo di replica raggiunge un valore che non è accettabile per il carico di lavoro.

## <a name="stop-replication"></a>Arrestare la replica

È possibile arrestare la replica tra un'origine e una replica. Dopo che la replica è stata interrotta tra un server di origine e una replica di lettura, la replica diventa un server autonomo. I dati nel server autonomo sono i dati che erano disponibili nella replica al momento dell'esecuzione del comando di arresto della replica. Il server autonomo non si aggiorna al server di origine.

Quando si sceglie di arrestare la replica in una replica, vengono persi tutti i collegamenti all'origine precedente e ad altre repliche. Non esiste un failover automatico tra un'origine e la relativa replica.

> [!IMPORTANT]
> Il server autonomo non può essere di nuovo impostato come replica.
> Prima di arrestare la replica in una replica in lettura, assicurarsi che la replica abbia tutti i dati necessari.

Informazioni su come [arrestare la replica in una replica](howto-read-replicas-portal.md).

## <a name="failover"></a>Failover

Non esiste un failover automatico tra i server di origine e di replica. 

Poiché la replica è asincrona, esiste un ritardo tra l'origine e la replica. La quantità di ritardo può essere influenzata da diversi fattori, ad esempio la quantità di carico di lavoro in esecuzione nel server di origine e la latenza tra i Data Center. Nella maggior parte dei casi il ritardo della replica è compreso tra pochi secondi e un paio di minuti. È possibile tenere traccia dell'effettivo ritardo di replica usando l' *intervallo di replica*metrica, disponibile per ogni replica. Questa metrica indica il tempo trascorso dall'ultima transazione riprodotta. Si consiglia di identificare il ritardo medio osservando il ritardo della replica in un periodo di tempo. È possibile impostare un avviso per il ritardo di replica, in modo che se non rientra nell'intervallo previsto, è possibile intervenire.

> [!Tip]
> Se si esegue il failover alla replica, il ritardo nel momento in cui si scollega la replica dall'origine indicherà la quantità di dati persi.

Dopo aver deciso di voler eseguire il failover a una replica, 

1. Arrestare la replica nella replica<br/>
   Questo passaggio è necessario per consentire al server di replica di accettare le Scritture. Come parte di questo processo, il server di replica verrà decollegato dal master. Una volta avviata l'arresto della replica, il completamento del processo back-end richiede in genere circa 2 minuti. Per comprendere le implicazioni di questa azione, vedere la sezione [arrestare la replica](#stop-replication) di questo articolo.
    
2. Puntare l'applicazione alla replica (precedente)<br/>
   Ogni server dispone di una stringa di connessione univoca. Aggiornare l'applicazione in modo che punti alla replica (precedente) invece che al database master.
    
Una volta che l'applicazione ha elaborato correttamente le operazioni di lettura e scrittura, il failover è stato completato. La quantità di tempo di inattività di cui l'applicazione dipenderà quando si rileva un problema e si completano i passaggi 1 e 2 precedenti.

## <a name="considerations-and-limitations"></a>Considerazioni e limiti

### <a name="pricing-tiers"></a>Piani tariffari

Le repliche in lettura sono attualmente disponibili solo nei livelli di prezzo per utilizzo generico e ottimizzato per la memoria.

> [!NOTE]
> Il costo dell'esecuzione del server di replica è basato sull'area in cui è in esecuzione il server di replica.

### <a name="source-server-restart"></a>Riavvio del server di origine

Quando si crea una replica per un'origine senza repliche, l'origine viene innanzitutto riavviata per prepararsi per la replica. Tenere in considerazione questo aspetto ed eseguire queste operazioni durante un periodo di scarso traffico.

### <a name="new-replicas"></a>Nuove repliche

Una replica di lettura viene creata come un nuovo database di Azure per il server MariaDB. Un server esistente non può essere impostato come replica. Non è possibile creare una replica di un'altra replica in lettura.

### <a name="replica-configuration"></a>Configurazione della replica

Una replica viene creata usando la stessa configurazione server del master. Dopo la creazione di una replica, è possibile modificare diverse impostazioni indipendentemente dal server di origine: generazione di calcolo, Vcore, archiviazione, periodo di conservazione dei backup e versione del motore MariaDB. È anche possibile modificare in modo indipendente il piano tariffario, tranne da o verso il livello Basic.

> [!IMPORTANT]
> Prima che la configurazione del server di origine venga aggiornata con nuovi valori, la configurazione delle repliche deve essere aggiornata impostandola su valori uguali o superiori. Questa azione garantisce che le repliche siano sempre aggiornate con le modifiche apportate al master.

Le regole del firewall e le impostazioni dei parametri vengono ereditate dal server di origine alla replica quando viene creata la replica. Successivamente le regole della replica sono indipendenti.

### <a name="stopped-replicas"></a>Repliche arrestate

Se si interrompe la replica tra un server di origine e una replica di lettura, la replica arrestata diventa un server autonomo che accetta letture e scritture. Il server autonomo non può essere di nuovo impostato come replica.

### <a name="deleted-source-and-standalone-servers"></a>Server di origine e autonomi eliminati

Quando viene eliminato un server di origine, la replica viene arrestata per tutte le repliche di lettura. Queste repliche diventano automaticamente server autonomi e possono accettare operazioni di lettura e scrittura. Il server di origine viene eliminato.

### <a name="user-accounts"></a>Account utente

Gli utenti nel server di origine vengono replicati nelle repliche di lettura. È possibile connettersi a una replica di lettura solo utilizzando gli account utente disponibili nel server di origine.

### <a name="server-parameters"></a>Parametri del server

Per evitare che i dati siano esclusi dalla sincronizzazione e scongiurarne potenziali perdite o danneggiamenti, alcuni parametri del server vengono bloccati dall'aggiornamento quando si usano repliche di lettura.

I parametri del server seguenti sono bloccati nel server di origine e di replica:
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

Il parametro [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) è bloccato nei server di replica.

Per aggiornare uno dei parametri indicati in precedenza nel server di origine, eliminare i server di replica, aggiornare il valore del parametro nel database master e ricreare le repliche.

### <a name="other"></a>Altro

- La creazione di una replica di replica non è supportata.
- Le tabelle in memoria possono impedire la sincronizzazione delle repliche. Si tratta di una limitazione della tecnologia di replica di MariaDB.
- Verificare che le tabelle del server di origine dispongano di chiavi primarie. La mancanza di chiavi primarie può causare latenza di replica tra l'origine e le repliche.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare e gestire le repliche in lettura tramite il portale di Azure](howto-read-replicas-portal.md)
- Informazioni su come [creare e gestire le repliche in lettura con l'interfaccia della riga di comando di Azure e l'API REST](howto-read-replicas-cli.md)
