---
title: Procedure consigliate operative di MySQL server-database di Azure per MySQL
description: Questo articolo descrive le procedure consigliate per il funzionamento del database MySQL in Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1d84483a701bf7fb4e6c50b9dc4f4f89993c64d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355034"
---
# <a name="best-practices-for-server-operations-on-azure-database-for-mysql--single-server"></a>Procedure consigliate per le operazioni del server nel database di Azure per MySQL-server singolo

Informazioni sulle procedure consigliate per l'uso di database di Azure per MySQL. Quando si aggiungono nuove funzionalità alla piattaforma, si continuerà a concentrarsi sulle procedure consigliate descritte in questa sezione.

## <a name="azure-database-for-mysql-operational-guidelines"></a>Linee guida operative per database di Azure per MySQL 

Di seguito sono riportate le linee guida operative da seguire quando si usa il database di Azure per MySQL per migliorare le prestazioni del database: 

* **Condivisione percorso**: per ridurre la latenza di rete, collocare il client e il server di database si trovano nella stessa area di Azure.

* **Monitorare la memoria, la CPU e l'utilizzo dello spazio di archiviazione**: è possibile [configurare gli avvisi](howto-alert-on-metric.md) per notificare quando i modelli di utilizzo cambiano o quando si avvicina la capacità della distribuzione, in modo da poter mantenere le prestazioni e la disponibilità del sistema. 

* **Aumentare le prestazioni dell'istanza di database**: è possibile [aumentare](howto-create-manage-server-portal.md) le prestazioni quando si avvicinano i limiti di capacità di archiviazione. È necessario disporre di un buffer di archiviazione e memoria per supportare aumenti imprevisti della richiesta dalle applicazioni. È anche possibile [abilitare la funzionalità di aumento automatico delle dimensioni di archiviazione](howto-auto-grow-storage-portal.md) "on" solo per garantire che il servizio scala automaticamente lo spazio di archiviazione in prossimità dei limiti di archiviazione. 

* **Configurare i backup**: abilitare i [backup locali o con ridondanza geografica](howto-restore-server-portal.md#set-backup-configuration) in base ai requisiti dell'azienda. Inoltre, è possibile modificare il periodo di memorizzazione per quanto tempo i backup sono disponibili per la continuità aziendale. 

* **Aumentare la capacità di i/o**: se il carico di lavoro del database richiede un numero maggiore di operazioni di i/o di cui è stato effettuato il provisioning, il ripristino o altre operazioni transazionali per il database saranno lente. Per aumentare la capacità di I/O di un'istanza del server, eseguire una o più delle operazioni seguenti: 

    * Database di Azure per MySQL offre scalabilità IOPS alla velocità di tre IOPS per GB di archiviazione con provisioning. [Aumentare lo spazio di archiviazione](howto-create-manage-server-portal.md#scale-storage-up) di cui è stato effettuato il provisioning per aumentare le prestazioni. 

    * Se si usa già l'archiviazione con provisioning IOPS, effettuare il provisioning della [capacità di velocità effettiva aggiuntiva](howto-create-manage-server-portal.md#scale-storage-up) 

* **Calcolo della scalabilità**: il carico di lavoro del database può essere limitato anche a causa della CPU o della memoria e ciò può avere un notevole effetto sull'elaborazione delle transazioni. Si noti che il calcolo (piano tariffario) può essere scalato verso l'alto o verso il basso tra [per utilizzo generico o](concepts-pricing-tiers.md) solo i livelli con ottimizzazione per la memoria. 

* **Test per il failover**: eseguire manualmente il failover di test per l'istanza del server per determinare il tempo impiegato dal processo per il caso d'uso e per assicurarsi che l'applicazione che accede all'istanza del server sia in grado di connettersi automaticamente alla nuova istanza del server dopo il failover.

* **Usare la chiave primaria**: assicurarsi che le tabelle abbiano una chiave primaria o univoca mentre si opera nel database di Azure per MySQL. Questo consente di eseguire backup, replica e così via, migliorando le prestazioni.

* **Configura valore TTL**: se l'applicazione client memorizza nella cache i dati Domain Name Service (DNS) delle istanze del server, impostare un valore di durata (TTL) inferiore a 30 secondi. Poiché l'indirizzo IP sottostante di un'istanza del server può cambiare dopo un failover, la memorizzazione nella cache dei dati DNS per un periodo di tempo prolungato può causare errori di connessione se l'applicazione tenta di connettersi a un indirizzo IP che non è più disponibile nel servizio.

* Usare il pool di connessioni per evitare di raggiungere i [limiti massimi di connessione](concepts-server-parameters.md#max_connections)e usare la logica di ripetizione tentativi per evitare problemi di connessione intermittenti. 

* Se si usa la replica, usare [ProxySQL per bilanciare il carico](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847) tra il server primario e il server di replica secondaria leggibile. Vedere la procedura di installazione qui. </br> 

* Quando si esegue il provisioning della risorsa, assicurarsi che sia stata [abilitata l'aumento automatico delle dimensioni](howto-auto-grow-storage-portal.md) per il database di Azure per MySQL. Questa operazione non comporta alcun costo aggiuntivo e proteggerà il database da eventuali colli di bottiglia di archiviazione che potrebbero verificarsi. </br> 


### <a name="using-innodb-with-azure-database-for-mysql"></a>Uso di InnoDB con database di Azure per MySQL

*   Se si utilizza la `ibdata1` funzionalità, ovvero un file di dati di tablespace di sistema non può essere compattato o eliminato mediante l'eliminazione dei dati dalla tabella o lo spostando la tabella in file per tabella `tablespaces` .

* Per un database di dimensioni superiori a 1 TB, è necessario creare la tabella in **innodb_file_per_table** `tablespace` . Per una singola tabella di dimensioni superiori a 1 TB, è necessario che la tabella di [partizione](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) .

*   Per un server con un numero elevato di `tablespace` , l'avvio del motore risulteranno molto lenti a causa dell'analisi di tablespace sequenziale durante l'avvio o il failover di MySQL. 

* Impostare innodb_file_per_table = ON prima di creare una tabella, se il numero totale della tabella è inferiore a 500.

* Se si dispone di più di 500 tabelle in un database, esaminare le dimensioni della tabella per ogni singola tabella. Per una tabella di grandi dimensioni, è comunque consigliabile usare lo spazio di tabella per i file per tabella per evitare che il file di tablespace del sistema riscontri il limite massimo di archiviazione.

> [!NOTE]
> Per le tabelle con dimensioni inferiori a 5 GB, provare a usare lo spazio di tabella di sistema 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* [Partizionare](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) la tabella in fase di creazione della tabella se si dispone di una tabella di dimensioni molto grandi potrebbe potenzialmente aumentare oltre 1 TB.

* Usare più server MySQL e distribuire le tabelle tra questi server. Evitare di inserire troppe tabelle in un singolo server se si dispone di circa 10000 tabelle o più. 

## <a name="next-steps"></a>Passaggi successivi
- [Procedura consigliata per le prestazioni del database di Azure per MySQL](concept-performance-best-practices.md)
- [Procedura consigliata per il monitoraggio del database di Azure per MySQL](concept-monitoring-best-practices.md)
