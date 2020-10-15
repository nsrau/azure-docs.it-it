---
title: Procedure consigliate per lo sviluppo di app-database di Azure per MySQL
description: Informazioni sulle procedure consigliate per la compilazione di un'app con database di Azure per MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: dc9764ce68d54418578c293833c1fd38080ba0ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91538909"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-mysql"></a>Procedure consigliate per la compilazione di un'applicazione con database di Azure per MySQL 

Di seguito sono riportate alcune procedure consigliate per la creazione di un'applicazione predisposta per il cloud con database di Azure per MySQL. Queste procedure consigliate possono ridurre i tempi di sviluppo per l'app. 

## <a name="configuration-of-application-and-database-resources"></a>Configurazione delle risorse dell'applicazione e del database

### <a name="keep-the-application-and-database-in-the-same-region"></a>Mantieni l'applicazione e il database nella stessa area
Quando si distribuisce l'applicazione in Azure, verificare che tutte le dipendenze si trovino nella stessa area. La distribuzione di istanze tra aree o zone di disponibilità crea una latenza di rete che può influire sulle prestazioni complessive dell'applicazione. 

### <a name="keep-your-mysql-server-secure"></a>Mantieni sicuro il server MySQL
Configurare il server MySQL in modo che sia [protetto](https://docs.microsoft.com/azure/mysql/concepts-security) e non accessibile pubblicamente. Usare una di queste opzioni per proteggere il server: 
- [Regole del firewall](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules)
- [Reti virtuali](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet) 
- [Collegamento privato di Azure](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)

Per la sicurezza, è sempre necessario connettersi al server MySQL tramite SSL e configurare il server MySQL e l'applicazione per l'uso di TLS 1,2. Vedere [come configurare SSL/TLS](https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security). 

### <a name="tune-your-server-parameters"></a>Ottimizzare i parametri del server
Per i carichi di lavoro con utilizzo intensivo di lettura `tmp_table_size` che `max_heap_table_size` ottimizzano i parametri del server e consentono di ottimizzare le prestazioni. Per calcolare i valori richiesti per queste variabili, esaminare i valori totali della memoria per connessione e la memoria di base. Somma dei parametri di memoria per connessione, esclusi `tmp_table_size` , combinati con gli account di memoria di base per la memoria totale del server.

Per calcolare la dimensione massima possibile di `tmp_table_size` e `max_heap_table_size` , utilizzare la formula seguente:

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> Memoria totale indica la quantità totale di memoria che il server ha in vcore di cui è stato effettuato il provisioning.  Ad esempio, in un per utilizzo generico database di Azure vCore di Azure per il server MySQL, la memoria totale sarà di 5 GB * 2. Per ulteriori informazioni sulla memoria per ogni livello, vedere la documentazione del piano [tariffario](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) .
>
> Memoria di base indica le variabili di memoria `query_cache_size` , `innodb_buffer_pool_size` ad esempio e, che MySQL Inizializza e alloca all'avvio del server. La memoria per connessione, `sort_buffer_size` ad esempio e `join_buffer_size` , è memoria allocata solo quando è necessario per una query.

### <a name="create-non-admin-users"></a>Creare utenti non amministratori 
[Creare utenti non amministratori](https://docs.microsoft.com/azure/mysql/howto-create-users) per ogni database. I nomi utente vengono in genere identificati come nomi di database.

### <a name="reset-your-password"></a>Reimpostare la password
È possibile [reimpostare la password](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password) per il server MySQL usando il portale di Azure. 

La reimpostazione della password del server per un database di produzione può comportare l'arresto dell'applicazione. È consigliabile reimpostare la password per tutti i carichi di lavoro di produzione in orari di minore attività per ridurre al minimo l'effetto sugli utenti dell'applicazione.

## <a name="performance-and-resiliency"></a>Prestazioni e resilienza 
Ecco alcuni strumenti e procedure che è possibile usare per facilitare il debug dei problemi di prestazioni con l'applicazione.

### <a name="enable-slow-query-logs-to-identify-performance-issues"></a>Abilitare i log di query lente per identificare i problemi di prestazioni
È possibile abilitare i [log di query](https://docs.microsoft.com/azure/mysql/concepts-server-logs) e i log di [controllo](https://docs.microsoft.com/azure/mysql/concepts-audit-logs) lenti nel server. L'analisi di log di query lente consente di identificare i colli di bottiglia delle prestazioni per la risoluzione dei problemi. 

I log di controllo sono disponibili anche tramite i log di Diagnostica di Azure nei log di monitoraggio di Azure, negli hub eventi di Azure e negli account di archiviazione. Vedere [come risolvere i problemi relativi alle prestazioni delle query](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).

### <a name="use-connection-pooling"></a>Utilizzare un pool di connessioni
La gestione delle connessioni di database può avere un impatto significativo sulle prestazioni dell'applicazione nel suo insieme. Per ottimizzare le prestazioni, è necessario ridurre il numero di volte in cui vengono stabilite le connessioni e il tempo per stabilire le connessioni nei percorsi del codice chiave. Usare il [pool di connessioni](https://docs.microsoft.com/azure/mysql/concepts-connectivity#access-databases-by-using-connection-pooling-recommended) per connettersi a database di Azure per MySQL per migliorare la resilienza e le prestazioni. 

È possibile utilizzare il pool di connessione [ProxySQL](https://proxysql.com/) per gestire in modo efficiente le connessioni. L'uso di una connessione pool può ridurre le connessioni inattive e riutilizzare le connessioni esistenti, consentendo di evitare problemi. Per altre informazioni [, vedere come configurare ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) . 

### <a name="retry-logic-to-handle-transient-errors"></a>Logica di ripetizione dei tentativi per gestire gli errori temporanei
È possibile che l'applicazione riscontri [errori temporanei](https://docs.microsoft.com/azure/mysql/concepts-connectivity#handling-transient-errors) in cui le connessioni al database vengono eliminate o perse in modo intermittente. In tali situazioni, il server è attivo e in esecuzione dopo uno o due tentativi tra 5 e 10 secondi. 

È consigliabile attendere 5 secondi prima del primo tentativo. Quindi, seguire ogni nuovo tentativo aumentando gradualmente l'attesa, fino a 60 secondi. Limitare il numero massimo di tentativi a cui l'applicazione considera l'operazione non riuscita, quindi è possibile approfondire ulteriormente la ricerca. Per ulteriori informazioni [, vedere come risolvere gli errori di connessione](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-common-connection-issues) . 

### <a name="enable-read-replication-to-mitigate-failovers"></a>Abilitare la replica di lettura per attenuare i failover
È possibile utilizzare [replica dei dati in ingresso](https://docs.microsoft.com/azure/mysql/howto-data-in-replication) per gli scenari di failover. Quando si usano le repliche di lettura, non viene eseguito alcun failover automatico tra i server di origine e di replica. 

Si noterà un ritardo tra l'origine e la replica perché la replica è asincrona. Il ritardo di rete può essere influenzato da molti fattori, ad esempio le dimensioni del carico di lavoro in esecuzione nel server di origine e la latenza tra i Data Center. Nella maggior parte dei casi, il ritardo di replica varia da alcuni secondi a un paio di minuti.

## <a name="database-deployment"></a>Distribuzione del database 

### <a name="configure-an-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>Configurare un'attività di database di Azure per MySQL nella pipeline di distribuzione CI/CD
In alcuni casi, è necessario distribuire le modifiche nel database. In questi casi, è possibile usare l'integrazione continua (CI) e il recapito continuo (CD) tramite [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) e usare un'attività per [il server MySQL](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops) per aggiornare il database eseguendo uno script personalizzato su di esso.

### <a name="use-an-effective-process-for-manual-database-deployment"></a>Usare un processo efficace per la distribuzione manuale del database 
Durante la distribuzione manuale del database, attenersi alla seguente procedura per ridurre al minimo i tempi di inattività o ridurre il rischio di una distribuzione non riuscita: 

1. Creare una copia di un database di produzione in un nuovo database usando [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) o [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html). 
2. Aggiornare il nuovo database con le nuove modifiche dello schema o gli aggiornamenti necessari per il database. 
3. Posizionare il database di produzione in uno stato di sola lettura. Non è necessario eseguire operazioni di scrittura nel database di produzione fino al completamento della distribuzione. 
4. Testare l'applicazione con il database appena aggiornato dal passaggio 1.
5. Distribuire le modifiche apportate all'applicazione e assicurarsi che l'applicazione stia ora usando il nuovo database con gli aggiornamenti più recenti. 
6. Conservare il vecchio database di produzione in modo che sia possibile eseguire il rollback delle modifiche. È quindi possibile valutare di eliminare il database di produzione precedente o di esportarlo in archiviazione di Azure, se necessario. 

>[!NOTE]
>Se l'applicazione è simile a un'app di e-commerce e non è possibile inserirla nello stato di sola lettura, distribuire le modifiche direttamente nel database di produzione dopo avere eseguito un backup. Queste modifiche devono verificarsi durante gli orari di minore utilizzo con traffico ridotto all'app per ridurre al minimo l'effetto, perché alcuni utenti potrebbero riscontrare richieste non riuscite. 
>
>Verificare che il codice dell'applicazione gestisca anche le richieste non riuscite.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>Usare le metriche native di MySQL per verificare se il carico di lavoro supera le dimensioni delle tabelle temporanee in memoria
Con un carico di lavoro con un'intensa attività di lettura, le query in esecuzione sul server MySQL potrebbero superare le dimensioni delle tabelle temporanee in memoria. Un carico di lavoro con un'intensa attività di lettura può causare il passaggio del server alla scrittura di tabelle temporanee su disco, che influiscono sulle prestazioni dell'applicazione. Per determinare se il server sta scrivendo su disco in seguito al superamento delle dimensioni della tabella temporanea, esaminare le metriche seguenti:

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
La `created_tmp_disk_tables` metrica indica il numero di tabelle create sul disco. La `created_tmp_table` metrica indica il numero di tabelle temporanee da formare in memoria, in base al carico di lavoro. Per determinare se l'esecuzione di una query specifica utilizzerà tabelle temporanee, eseguire l'istruzione [explain](https://dev.mysql.com/doc/refman/8.0/en/explain.html) sulla query. Il dettaglio nella `extra` colonna indica `Using temporary` se la query viene eseguita utilizzando tabelle temporanee.

Per calcolare la percentuale del carico di lavoro con le query che si distribuiscono sui dischi, usare i valori delle metriche nella formula seguente:

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

Idealmente, questa percentuale dovrebbe essere inferiore al 25%. Se si nota che la percentuale è pari al 25% o superiore, è consigliabile modificare due parametri del server, tmp_table_size e max_heap_table_size.

## <a name="database-schema-and-queries"></a>Schema di database e query

Ecco alcuni suggerimenti da tenere presenti durante la compilazione dello schema del database e delle query.

### <a name="use-the-right-datatype-for-your-table-columns"></a>Usare il tipo di dati corretto per le colonne della tabella
L'utilizzo del tipo di dati appropriato in base al tipo di dati che si desidera archiviare può ottimizzare l'archiviazione e ridurre gli errori che possono verificarsi a causa di tipi di dati non corretti.

### <a name="use-indexes"></a>Usa indici
Per evitare query lente, è possibile utilizzare gli indici. Gli indici consentono di trovare rapidamente le righe con colonne specifiche. Vedere [come usare gli indici in MySQL](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html).

### <a name="use-explain-for-your-select-queries"></a>Usare EXPLAIN per le query SELECT
Usare l' `EXPLAIN` istruzione per ottenere informazioni dettagliate sulle operazioni che MySQL sta eseguendo per eseguire la query. Può aiutare a rilevare colli di bottiglia o problemi con la query. Vedere [come usare explain per profilare le prestazioni delle query](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).


