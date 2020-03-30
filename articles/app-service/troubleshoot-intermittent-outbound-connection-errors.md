---
title: Risoluzione di errori di connessione in uscita intermittenti nel servizio app di AzureTroubleshooting intermittent outbound connection errors in Azure App Service
description: Risolvere gli errori di connessione intermittenti e i problemi di prestazioni correlati nel servizio app di AzureTroubleshoot intermittent connection errors and related performance issues in Azure App Service
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 03/24/2020
ms.author: ramakoni
ms.custom: security-recommendations
ms.openlocfilehash: 028ddccdb989d35710e387081b08a3b973d75bdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367551"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Risoluzione di errori di connessione in uscita intermittenti nel servizio app di AzureTroubleshooting intermittent outbound connection errors in Azure App Service

Questo articolo consente di risolvere errori di connessione intermittenti e problemi di prestazioni correlati nel servizio app di [Azure.](https://docs.microsoft.com/azure/app-service/overview) In questo argomento verranno fornite ulteriori informazioni e sulla risoluzione dei problemi relativi alle metodologie per l'esaurimento delle porte SNAT (Source Address Network Translation). Se è necessaria ulteriore assistenza in qualsiasi momento di questo articolo, contattare gli esperti di Azure nei [forum MSDN Azure e Overflow dello stack](https://azure.microsoft.com/support/forums/). In alternativa, archiviare un incidente di supporto di Azure.Alternatively, file an Azure support incident. Passare al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare Ottieni **supporto**.

## <a name="symptoms"></a>Sintomi

Le applicazioni e le funzioni ospitate nel servizio App di Azure possono presentare uno o più dei sintomi seguenti:Applications and Functions hosted on Azure App service may exhibit one or more of the following symptoms:

* Tempi di risposta lenti in tutte o alcune delle istanze in un piano di servizio.
* Errori intermittenti 5xx o **Gateway non valido**
* Messaggi di errore di timeout
* Impossibile connettersi a endpoint esterni (ad esempio SQLDB, Service Fabric, altri servizi app e così via)

## <a name="cause"></a>Causa

Una delle cause principali di questi sintomi è che l'istanza dell'applicazione non è in grado di aprire una nuova connessione all'endpoint esterno perché ha raggiunto uno dei limiti seguenti:

* Connessioni TCP: esiste un limite al numero di connessioni in uscita che è possibile effettuare. Ciò è associato alla dimensione del lavoratore utilizzato.
* Porte SNAT: come descritto in [Connessioni in uscita in Azure,](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)Azure usa la conversione degli indirizzi di rete di origine (SNAT) e un servizio di bilanciamento del carico (non esposto ai clienti) per comunicare con gli endpoint esterni ad Azure nello spazio di indirizzi IP pubblici. A ogni istanza nel servizio app di Azure viene inizialmente assegnato un numero preallocato di **128** porte SNAT. Tale limite influisce sull'apertura delle connessioni alla stessa combinazione host e porta. Se l'app crea connessioni a una combinazione di combinazioni di indirizzi e porte, non si utilizzeranno le porte SNAT. Le porte SNAT vengono esaurite quando si verificano chiamate ripetute alla stessa combinazione di indirizzo e porta. Una volta rilasciata una porta, la porta è disponibile per il riutilizzo in base alle esigenze. Il servizio di bilanciamento del carico di rete di Azure recupera la porta SNAT dalle connessioni chiuse solo dopo aver atteso 4 minuti.

Quando le applicazioni o le funzioni aprono rapidamente una nuova connessione, possono esaurire rapidamente la quota preallocata delle 128 porte. Vengono quindi bloccati fino a quando non diventa disponibile una nuova porta SNAT, tramite l'allocazione dinamica di porte SNAT aggiuntive o tramite il riutilizzo di una porta SNAT recuperata. Le applicazioni o le funzioni bloccate a causa di questa incapacità di creare nuove connessioni inizieranno a verificarsi uno o più dei problemi descritti nella sezione **"Sintomi"** di questo articolo.

## <a name="avoiding-the-problem"></a>Evitare il problema

Evitare il problema della porta SNAT significa evitare la creazione di nuove connessioni ripetutamente allo stesso host e alla stessa porta.

Le strategie generali per attenuare l'esaurimento delle porte SNAT sono descritte nella sezione Risoluzione dei problemi della documentazione relativa alle **connessioni in uscita di Azure.General** strategies for mitigating SNAT port exhaustion are discussed in the [Problem-solving section](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#problemsolving) of the Outbound connections of Azure documentation. Di queste strategie, le seguenti sono applicabili alle app e alle funzioni ospitate nel servizio app di Azure.Of these strategies, the following are applicable to apps and functions hosted on Azure App service.

### <a name="modify-the-application-to-use-connection-pooling"></a>Modificare l'applicazione per usare pool di connessioni

* Per il pooling delle connessioni HTTP, esaminare [le connessioni HTTP del pool con HttpClientFactory](https://docs.microsoft.com/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* Per informazioni sul pool di connessioni di SQL Server, vedere Pool di connessioni di [SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).
* Per implementare il pooling con le applicazioni del framework di entità, vedere [DbContext pooling](https://docs.microsoft.com/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling).

Di seguito è riportata una raccolta di collegamenti per l'implementazione del pool di connessioni in base a uno stack di soluzioni diverso.

#### <a name="node"></a>Nodo

Per impostazione predefinita, le connessioni per NodeJS non vengono mantenute attive. Di seguito sono riportati i database e i pacchetti comuni per il pool di connessioni che contengono esempi su come implementarli.

* [Mysql](https://github.com/mysqljs/mysql#pooling-connections)
* [Mongodb](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP Keep-alive

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Documentazione di Node.js v13.9.0](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Di seguito sono riportate le librerie comuni utilizzate per il pool di connessioni JDBC che contengono esempi su come implementarle: Pool di connessioni JDBC.

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

Pool di connessioni HTTP

* [Gestione delle connessioni Apache](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [Pool di classiHttpClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Sebbene PHP non supporti il pool di connessioni, è possibile provare a utilizzare connessioni di database permanenti al server back-end.
 
* Server MySQL

   * [Connessioni MySQLi](https://www.php.net/manual/mysqli.quickstart.connections.php) per le versioni più recenti
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) per le versioni precedenti di PHP

* Altre origini dati

   * [Gestione delle connessioni PHP](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [Mysql](https://github.com/mysqljs/mysql#pooling-connections)
* [Mongodb](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools) (NOTA: SQLAlchemy può essere utilizzato con altri database oltre a MicrosoftSQL Server)
* [Keep-alive HTTP](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)(Keep-Alive è automatico quando si utilizzano [session-objects](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)).

Per altri ambienti, esaminare i documenti specifici del provider o del driver per l'implementazione del pool di connessioni nelle applicazioni.

### <a name="modify-the-application-to-reuse-connections"></a>Modificare l'applicazione per riutilizzare le connessioni

*  Per altri puntatori ed esempi sulla gestione delle connessioni nelle funzioni di Azure, vedere Gestire le connessioni in Funzioni di Azure.For additional [pointers](https://docs.microsoft.com/azure/azure-functions/manage-connections)and examples on managing connections in Azure functions, review Manage connections in Azure Functions .

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Modificare l'applicazione per usare una logica di ripetizione meno aggressiva

* Per ulteriori indicazioni ed esempi, vedere [Retry pattern](https://docs.microsoft.com/azure/architecture/patterns/retry).

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Usare keep-alive per reimpostare il timeout di inattività per le connessioni uscita

* Per implementare keepalive per le app Node.js, vedere [L'applicazione My node sta effettuando chiamate in uscita eccessive.](https://docs.microsoft.com/azure/app-service/app-service-web-nodejs-best-practices-and-troubleshoot-guide#my-node-application-is-making-excessive-outbound-calls)

### <a name="additional-guidance-specific-to-app-service"></a>Ulteriori indicazioni specifiche per il servizio app:Additional guidance specific to App Service:

* Un [test di carico](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test) dovrebbe simulare i dati del mondo reale in una velocità di alimentazione costante. Il test di app e funzioni in condizioni di stress reali può identificare e risolvere i problemi di esaurimento della porta SNAT in anticipo.
* Assicurarsi che i servizi back-end siano in grado di restituire rapidamente le risposte. Per la risoluzione dei problemi di prestazioni con il database SQL di Azure, vedere [Risolvere i problemi di prestazioni](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance#recommended-troubleshooting-flow)del database SQL di Azure con Intelligent Insights .
* Scalare il piano di servizio app a più istanze. Per altre informazioni sul ridimensionamento, vedere [Ridimensionare un'app nel Servizio app di Azure](https://docs.microsoft.com/azure/app-service/manage-scale-up). A ogni istanza di lavoro in un piano di servizio app viene allocato un numero di porte SNAT. Se si distribuisce l'utilizzo in più istanze, è possibile ottenere l'utilizzo delle porte SNAT per ogni istanza al di sotto del limite consigliato di 100 connessioni in uscita, per endpoint remoto univoco.
* Valutare la possibilità di passare a Ambiente del [servizio app (AMBIENTE del servizio app),](https://docs.microsoft.com/azure/app-service/environment/using-an-ase)in cui è stato assegnato un singolo indirizzo IP in uscita e i limiti per le connessioni e le porte SNAT sono molto più elevati.

Evitare i limiti TCP in uscita è più facile da risolvere, in quanto i limiti sono impostati dalle dimensioni del lavoratore. È possibile visualizzare i limiti nei [limiti numerici tra macchine virtuali in sandbox - Connessioni TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nome limite|Descrizione|Piccolo (A1)|Medio (A2)|Grande (A3)|Livello isolato (ASE)|
|---|---|---|---|---|---|
|Connessioni|Numero di connessioni nell'intera macchina virtuale|1920|3968|8064|16.000|

Per evitare limiti TCP in uscita, è possibile aumentare le dimensioni dei lavoratori o scalare orizzontalmente.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Conoscere i due tipi di limiti di connessione in uscita e le operazioni eseguite dall'app dovrebbe semplificare la risoluzione dei problemi. Se si sa che l'app effettua molte chiamate allo stesso account di archiviazione, è possibile sospettare un limite SNAT. Se l'app crea un numero elevato di chiamate agli endpoint in Internet, si sospetterà di raggiungere il limite della macchina virtuale.

Se non si conosce il comportamento dell'applicazione sufficiente per determinare rapidamente la causa, in Servizio app sono disponibili alcuni strumenti e tecniche per facilitare tale determinazione.

### <a name="find-snat-port-allocation-information"></a>Trovare le informazioni sull'allocazione delle porte SNATFind SNAT port allocation information

È possibile usare [Diagnostica servizio app](https://docs.microsoft.com/azure/app-service/overview-diagnostics) per trovare informazioni sull'allocazione delle porte SNAT e osservare la metrica di allocazione delle porte SNAT di un sito del servizio app. Per trovare le informazioni sull'allocazione delle porte SNAT, attenersi alla seguente procedura:

1. Per accedere alla diagnostica del servizio app, passare all'app Web del servizio app o all'ambiente del servizio app nel portale di [Azure.](https://portal.azure.com/) Nel riquadro di spostamento sinistro selezionare **Diagnostica e risolvi problemi**.
2. Selezionare la categoria Disponibilità e prestazioni
3. Selezionare il riquadro Esaurimento porta SNAT nell'elenco dei riquadri disponibili nella categoria. La pratica è quella di tenerlo al di sotto di 128.
Se è necessario, è comunque possibile aprire un ticket di supporto e il tecnico del supporto otterrà la metrica dal back-end per te.

Si noti che poiché l'utilizzo delle porte SNAT non è disponibile come metrica, non è possibile eseguire la scalabilità automatica in base all'utilizzo delle porte SNAT o configurare la scalabilità automatica in base alla metrica di allocazione delle porte SNAT.

### <a name="tcp-connections-and-snat-ports"></a>Connessioni TCP e porte SNAT

Le connessioni TCP e le porte SNAT non sono direttamente correlate. Un rilevatore di utilizzo delle connessioni TCP è incluso nel pannello Diagnostica e risolvi problemi di qualsiasi sito del servizio app. Cercare la frase "Connessioni TCP" per trovarla.

* Le porte SNAT vengono utilizzate solo per i flussi di rete esterni, mentre le connessioni TCP totali includono connessioni di loopback locali.
* Una porta SNAT può essere condivisa da flussi diversi, se i flussi sono diversi in protocollo, indirizzo IP o porta. La metrica Connessioni TCP conta tutte le connessioni TCP.
* Il limite delle connessioni TCP si verifica a livello di istanza di lavoro. Il bilanciamento del carico in uscita di rete di Azure non usa la metrica Connessioni TCP per la limitazione delle porte SNAT.
* I limiti delle connessioni TCP sono descritti in [Sandbox Cross VM Numerical Limits - TCP Connections](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nome limite|Descrizione|Piccolo (A1)|Medio (A2)|Grande (A3)|Livello isolato (ASE)|
|---|---|---|---|---|---|
|Connessioni|Numero di connessioni nell'intera macchina virtuale|1920|3968|8064|16.000|

### <a name="webjobs-and-database-connections"></a>Processi Web e connessioni di database
 
Se le porte SNAT sono esaurite, in cui WebJobs non è in grado di connettersi al database SQL di Azure, non esiste alcuna metrica per mostrare quante connessioni vengono aperte da ogni singolo processo dell'applicazione Web. Per trovare il processo Web problematico, spostare diversi WebJobs in un altro piano di servizio app per verificare se la situazione migliora o se un problema persiste in uno dei piani. Ripetere il processo fino a trovare il processo Web problematico.

### <a name="using-snat-ports-sooner"></a>Utilizzo delle porte SNAT prima

Non è possibile modificare le impostazioni di Azure per rilasciare le porte SNAT usate prima, poiché tutte le porte SNAT verranno rilasciate in base alle condizioni seguenti e il comportamento è in base alla progettazione.
 
* Se il server o il client invia FINACK, la [porta SNAT verrà rilasciata](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#tcp-snat-port-release) dopo 240 secondi.
* Se viene visualizzato un RST, la porta SNAT verrà rilasciata dopo 15 secondi.
* Se è stato raggiunto un timeout di inattività, la porta viene rilasciata.
 
## <a name="additional-information"></a>Informazioni aggiuntive

* [SNAT con il servizio app](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Risoluzione dei problemi di rallentamento delle prestazioni delle app nel Servizio app di Azure](https://docs.microsoft.com/azure/app-service/troubleshoot-performance-degradation)
