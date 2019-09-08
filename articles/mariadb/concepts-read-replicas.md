---
title: Leggere le repliche nel database di Azure per MariaDB
description: Questo articolo descrive le repliche di lettura per il database di Azure per MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 6c1104ba340f23a76d5b9b4167f895f5a115152c
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773277"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Leggere le repliche nel database di Azure per MariaDB

La funzionalità di lettura della replica consente di replicare i dati da un database di Azure per il server MariaDB a un server di sola lettura. È possibile creare fino a un massimo di cinque repliche da un server master. Le repliche vengono aggiornate in modo asincrono utilizzando la tecnologia di replica basata su posizione del file binlog (binary log) del motore MariaDB con l'ID di transazione globale (GTID). Per altre informazioni su questo tipo di replica, vedere [binlog replication overview](https://mariadb.com/kb/en/library/replication-overview/) (Panoramica della replica basata su binlog).

Le repliche sono nuovi server gestiti in modo analogo al normale database di Azure per i server MariaDB. Per ogni replica in lettura, viene addebitato il costo delle risorse di calcolo e di archiviazione sottoposte a provisioning, espresse rispettivamente in vCore e GB/mese.

Per ulteriori informazioni sulla replica GTID, vedere la [documentazione relativa alla replica di MariaDB](https://mariadb.com/kb/en/library/gtid/).

## <a name="when-to-use-a-read-replica"></a>Quando usare una replica in lettura

La funzionalità di replica in lettura aiuta a migliorare prestazioni e scalabilità dei carichi di lavoro con utilizzo elevato della lettura. I carichi di lavoro di lettura possono essere isolati alle repliche, mentre i carichi di lavoro di scrittura possono essere indirizzati al master.

Uno scenario comune consiste nel fare in modo che i carichi di lavoro BI e analitici usino le repliche in lettura come origine dati per la creazione di report.

Poiché le repliche sono di sola lettura, non riducono direttamente gli oneri per la capacità di scrittura sul master. Questa funzionalità non è destinata a carichi di lavoro con utilizzo elevato di scrittura.

La funzionalità di lettura della replica utilizza la replica asincrona. La funzionalità non è concepita per scenari di replica sincrona. Esisterà un ritardo misurabile significativo tra il master e la replica. I dati nella replica diventano alla fine coerenti con i dati nel master. Usare questa funzionalità per i carichi di lavoro in grado di sostenere questo ritardo.


## <a name="cross-region-replication"></a>Replica tra aree
È possibile creare una replica di lettura in un'area diversa dal server master. La replica tra aree può essere utile per scenari come la pianificazione del ripristino di emergenza o per avvicinare i dati agli utenti.

È possibile avere un server master in qualsiasi [database di Azure per l'area MariaDB](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb).  Un server master può avere una replica nell'area abbinata o nelle aree di replica universale. L'immagine seguente mostra le aree di replica disponibili a seconda dell'area master.

[![Leggere le aree di replica](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Aree di replica universale
È sempre possibile creare una replica di lettura in una delle aree seguenti, indipendentemente dalla posizione in cui si trova il server master. Queste sono le aree di replica universale:

Australia orientale, Australia sudorientale, Stati Uniti centrali, Asia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, Regno Unito meridionale, Regno Unito occidentale, Europa occidentale, Stati Uniti occidentali, Stati Uniti occidentali 2.


### <a name="paired-regions"></a>Aree abbinate
Oltre alle aree di replica universale, è possibile creare una replica di lettura nell'area abbinata di Azure del server master. Se non si conosce la coppia dell'area, è possibile ottenere altre informazioni nell' [articolo sulle aree abbinate di Azure](../best-practices-availability-paired-regions.md).

Se si usano repliche tra aree per la pianificazione del ripristino di emergenza, è consigliabile creare la replica nell'area abbinata anziché in una delle altre aree. Le aree abbinate evitano gli aggiornamenti simultanei e assegnano priorità all'isolamento fisico e alla residenza dei dati.  

Esistono tuttavia alcune limitazioni da considerare: 

* Disponibilità a livello di area: Il database di Azure per MariaDB è disponibile negli Stati Uniti occidentali 2, Francia centrale, Emirati Arabi Uniti settentrionali e Germania centrale. Tuttavia, le aree abbinate non sono disponibili.
    
* Coppie uni-direzionali: Alcune aree di Azure sono abbinate solo in una direzione. Queste aree includono l'India occidentale, il Brasile meridionale e US Gov Virginia. 
   Ciò significa che un server master nell'India occidentale può creare una replica nell'India meridionale. Tuttavia, un server master nell'India meridionale non è in grado di creare una replica nell'India occidentale. Questo è dovuto al fatto che l'area secondaria dell'India occidentale è India meridionale, ma l'area secondaria dell'India meridionale non è India occidentale.


## <a name="create-a-replica"></a>Creare una replica

Se in un server master non sono presenti server di replica, il database master viene innanzitutto riavviato per prepararsi per la replica.

Quando si avvia il flusso di lavoro di creazione della replica, viene creato un database di Azure vuoto per il server MariaDB. Il nuovo server viene riempito con i dati presenti nel server master. Il tempo necessario per la creazione dipende dalla quantità di dati nel master e dal tempo trascorso dall'ultimo backup completo settimanale. Il tempo può variare da pochi minuti a diverse ore.

> [!NOTE]
> Se non si ha un avviso di archiviazione impostato nei server, si consiglia di impostarlo. L'avviso informa l'utente quando un server sta per raggiungere il limite di archiviazione, che influisce sulla replica.

Informazioni su come [creare una replica di lettura nel portale di Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Connessione a una replica

Quando si crea una replica, questa non eredita le regole del firewall o l'endpoint del servizio rete virtuale del server master. Queste regole devono essere configurate in modo indipendente per la replica.

La replica eredita l'account amministratore dal server master. Tutti gli account utente nel server master vengono replicati nelle repliche in lettura. È possibile connettersi a una replica in lettura solo tramite gli account utente che sono disponibili nel server master.

È possibile connettersi alla replica usando il nome host e un account utente valido, come per un normale database di Azure per il server MariaDB. Per un server denominato la **replica** con il **nome utente amministratore amministratore,** è possibile connettersi alla replica usando l'interfaccia della riga di comando di MySQL:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

Quando richiesto, immettere la password per l'account dell'utente.

## <a name="monitor-replication"></a>Monitorare la replica

Il database di Azure per MariaDB fornisce la metrica di **ritardo della replica in secondi** in monitoraggio di Azure. Questa metrica è disponibile per solo le repliche.

Questa metrica viene calcolata usando `seconds_behind_master` la metrica disponibile nel comando `SHOW SLAVE STATUS` di MariaDB.

Impostare un avviso per informare l'utente quando il ritardo di replica raggiunge un valore che non è accettabile per il carico di lavoro.

## <a name="stop-replication"></a>Arresta replica

È possibile scegliere di arrestare la replica tra un master e una replica. Dopo l'arresto della replica tra un server master e una replica in lettura, la replica diventa un server autonomo. I dati nel server autonomo sono i dati che erano disponibili nella replica al momento dell'esecuzione del comando di arresto della replica. Il server autonomo non è aggiornato con il server master.

Quando si sceglie di arrestare la replica in una replica, vengono persi tutti i collegamenti al master precedente e ad altre repliche. Non esiste un failover automatico tra un master e la relativa replica.

> [!IMPORTANT]
> Il server autonomo non può essere di nuovo impostato come replica.
> Prima di arrestare la replica in una replica in lettura, assicurarsi che la replica abbia tutti i dati necessari.

Informazioni su come [arrestare la replica in una replica](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Considerazioni e limiti

### <a name="pricing-tiers"></a>Piani tariffari

Le repliche in lettura sono attualmente disponibili solo nei livelli di prezzo per utilizzo generico e ottimizzato per la memoria.

### <a name="master-server-restart"></a>Riavvio del server master

Quando si crea una replica per un master senza repliche, il master viene innanzitutto riavviato per prepararsi per la replica. Tenere in considerazione questo aspetto ed eseguire queste operazioni durante un periodo di scarso traffico.

### <a name="new-replicas"></a>Nuove repliche

Una replica di lettura viene creata come un nuovo database di Azure per il server MariaDB. Un server esistente non può essere impostato come replica. Non è possibile creare una replica di un'altra replica in lettura.

### <a name="replica-configuration"></a>Configurazione della replica

Una replica viene creata usando la stessa configurazione server del master. Dopo la creazione di una replica, è possibile modificare diverse impostazioni indipendentemente dal server master: generazione di calcolo, Vcore, archiviazione, periodo di conservazione dei backup e versione del motore MariaDB. È anche possibile modificare in modo indipendente il piano tariffario, tranne da o verso il livello Basic.

> [!IMPORTANT]
> Prima che la configurazione del server master venga aggiornata con nuovi valori, la configurazione delle repliche deve essere aggiornata impostandola su valori uguali o superiori. Questa azione garantisce che le repliche siano sempre aggiornate con le modifiche apportate al master.

### <a name="stopped-replicas"></a>Repliche arrestate

Se si interrompe la replica tra un server master e una replica di lettura, la replica arrestata diventa un server autonomo che accetta letture e scritture. Il server autonomo non può essere di nuovo impostato come replica.

### <a name="deleted-master-and-standalone-servers"></a>Master eliminato e server autonomi

Quando un server master viene eliminato, la replica viene arrestata per tutte le repliche in lettura. Queste repliche diventano server autonomi. Il server master viene eliminato.

### <a name="user-accounts"></a>Account utente

Gli utenti del server master vengono replicati nelle repliche in lettura. È possibile connettersi a una replica in lettura solo tramite gli account utente disponibili nel server master.

### <a name="server-parameters"></a>Parametri del server

Per evitare che i dati risultino fuori dalla sincronizzazione e per evitare potenziali perdite o danneggiamenti dei dati, alcuni parametri del server vengono bloccati dall'aggiornamento quando si usano le repliche di lettura.

I parametri del server seguenti sono bloccati nei server master e di replica:
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

Il [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) parametro è bloccato nei server di replica.

### <a name="other"></a>Altro

- La creazione di una replica di replica non è supportata.
- Le tabelle in memoria potrebbero causare la perdita di sincronia delle repliche. Si tratta di una limitazione della tecnologia di replica di MariaDB.
- Verificare che le tabelle di server master dispongano di chiavi primarie. La mancanza di chiavi primarie può comportare una latenza di replica tra il server master e le repliche.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare e gestire le repliche in lettura tramite il portale di Azure](howto-read-replicas-portal.md)
- Leggere le informazioni su come [creare e gestire repliche in lettura tramite l'interfaccia della riga di comando di Azure](howto-read-replicas-cli.md)