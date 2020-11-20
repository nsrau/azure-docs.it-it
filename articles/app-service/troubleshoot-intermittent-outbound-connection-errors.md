---
title: Risoluzione degli errori di connessione in uscita intermittenti nel servizio app Azure
description: Risolvere gli errori di connessione intermittenti e i problemi di prestazioni correlati nel servizio app Azure
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: ramakoni
ms.custom: security-recommendations,fasttrack-edit
ms.openlocfilehash: 989f47c0ff60865a8e8be15e089cdcf96ab2550c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968299"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Risoluzione degli errori di connessione in uscita intermittenti nel servizio app Azure

Questo articolo consente di risolvere gli errori di connessione intermittenti e i problemi di prestazioni correlati nel [servizio app Azure](./overview.md). In questo argomento vengono fornite ulteriori informazioni sulle metodologie per la risoluzione dei problemi relativi all'esaurimento delle porte SNAT (Source Address Network Translation). Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, contattare gli esperti di Azure nei [Forum MSDN Azure e stack overflow](https://azure.microsoft.com/support/forums/). In alternativa, archiviare un evento imprevisto del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottieni supporto**.

## <a name="symptoms"></a>Sintomi

Le applicazioni e le funzioni ospitate nel servizio app Azure possono presentare uno o più dei seguenti sintomi:

* Rallentamento dei tempi di risposta su tutte o alcune delle istanze di un piano di servizio.
* Errori intermittenti 5xx o **gateway non valido**
* Messaggi di errore di timeout
* Non è stato possibile connettersi a endpoint esterni, ad esempio SQLDB, Service Fabric, altri servizi app e così via.

## <a name="cause"></a>Causa

La maggior parte dei problemi di connessione intermittenti sta colpendo un limite durante l'esecuzione di nuove connessioni in uscita. I limiti che è possibile raggiungere includono:

* Connessioni TCP: è previsto un limite per il numero di connessioni in uscita che è possibile effettuare. Il limite per le connessioni in uscita è associato alle dimensioni del ruolo di lavoro utilizzato.
* Porte SNAT: le [connessioni in uscita in Azure](../load-balancer/load-balancer-outbound-connections.md) descrivono le restrizioni delle porte SNAT e il modo in cui influiscono sulle connessioni in uscita. Azure USA Network Address Translation di origine (SNAT) e i bilanciamenti del carico (non esposti ai clienti) per comunicare con gli indirizzi IP pubblici. A ogni istanza nel servizio app Azure viene inizialmente assegnato un numero preallocato di **128** porte SNAT. Il limite di porte SNAT influiscono sull'apertura delle connessioni alla stessa combinazione di indirizzo e porta. Se l'app crea connessioni a una combinazione di combinazioni di indirizzo e porta, le porte SNAT non vengono usate. Le porte SNAT vengono utilizzate quando si hanno ripetute chiamate alla stessa combinazione di indirizzo e porta. Una volta rilasciata una porta, la porta sarà disponibile per il riutilizzo in base alle esigenze. Il servizio di bilanciamento del carico di rete di Azure recupera la porta SNAT dalle connessioni chiuse solo dopo l'attesa di 4 minuti.

Quando le applicazioni o funzioni aprono rapidamente una nuova connessione, possono esaurire rapidamente la quota preallocata delle porte 128. Vengono quindi bloccati fino a quando non diventa disponibile una nuova porta SNAT, tramite l'allocazione dinamica di porte SNAT aggiuntive o il riutilizzo di una porta SNAT recuperata. Se l'app esaurisce le porte SNAT, si verificano problemi di connettività in uscita intermittenti. 

## <a name="avoiding-the-problem"></a>Evitare il problema

Sono disponibili alcune soluzioni che consentono di evitare limitazioni della porta SNAT. e comprendono:

* pool di connessioni: raggruppando le connessioni, è possibile evitare di aprire nuove connessioni di rete per le chiamate allo stesso indirizzo e alla stessa porta.
* endpoint di servizio: non si ha una restrizione della porta SNAT per i servizi protetti con gli endpoint di servizio.
* endpoint privati: non si ha una restrizione della porta SNAT per i servizi protetti con endpoint privati.
* Gateway NAT: con un gateway NAT sono presenti porte SNAT in uscita 64K che possono essere usate dalle risorse che inviano il traffico.

Evitare il problema della porta SNAT significa evitare la creazione ripetuta di nuove connessioni allo stesso host e alla stessa porta. I pool di connessioni sono uno dei modi più ovvi per risolvere il problema.

Se la destinazione è un servizio di Azure che supporta gli endpoint di servizio, è possibile evitare i problemi di esaurimento delle porte SNAT usando l' [integrazione VNet a livello](./web-sites-integrate-with-vnet.md) di area e gli endpoint di servizio o privati. Quando si usa l'integrazione VNet a livello di area e si inseriscono gli endpoint di servizio nella subnet di integrazione, il traffico in uscita dell'app per tali servizi non avrà restrizioni per le porte SNAT in uscita. Analogamente, se si usano l'integrazione VNet a livello di area e gli endpoint privati, non ci sono problemi di porta SNAT in uscita per tale destinazione. 

Se la destinazione è un endpoint esterno esterno ad Azure, l'uso di un gateway NAT consente di 64K porte SNAT in uscita. Fornisce anche un indirizzo in uscita dedicato che non si condivide con altri. 

Se possibile, migliorare il codice per usare i pool di connessioni ed evitare l'intera situazione. Non è sempre possibile modificare il codice in modo sufficientemente rapido per attenuare questa situazione. Per i casi in cui non è possibile modificare il codice nel tempo, sfruttare le altre soluzioni. La soluzione migliore per il problema consiste nel combinare tutte le soluzioni nel modo migliore possibile. Provare a usare gli endpoint di servizio e privati per i servizi di Azure e il gateway NAT per il resto. 

Le strategie generali per mitigare l'esaurimento delle porte SNAT sono illustrate nella [sezione risoluzione dei problemi](../load-balancer/load-balancer-outbound-connections.md) delle **connessioni in uscita della documentazione di Azure** . Di queste strategie, le seguenti sono applicabili alle app e alle funzioni ospitate nel servizio app Azure.

### <a name="modify-the-application-to-use-connection-pooling"></a>Modificare l'applicazione per usare pool di connessioni

* Per il pool di connessioni HTTP, verificare [le connessioni HTTP del pool con HttpClientFactory](/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* Per informazioni sul pool di connessioni SQL Server, vedere [SQL Server pool di connessioni (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling).
* Per l'implementazione del pool con le applicazioni Entity Framework, vedere [DbContext pooling](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling).

Di seguito è riportato un insieme di collegamenti per l'implementazione del pool di connessioni in base a uno stack di soluzioni diverso.

#### <a name="node"></a>Nodo

Per impostazione predefinita, le connessioni per NodeJS non vengono mantenute attive. Di seguito sono riportati i database e i pacchetti più diffusi per il pool di connessioni che contengono esempi su come implementarli.

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

Keep-alive HTTP

* [per agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [ Documentazione diNode.js v 13.9.0](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Di seguito sono riportate le librerie più diffuse usate per il pool di connessioni JDBC che contengono esempi su come implementarle: pool di connessioni JDBC.

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

Pool di connessioni HTTP

* [Gestione connessione Apache](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [Classe PoolingHttpClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Sebbene PHP non supporti il pool di connessioni, è possibile provare a usare connessioni di database permanenti al server back-end.
 
* Server MySQL

   * [Connessioni mysqli](https://www.php.net/manual/mysqli.quickstart.connections.php) per le versioni più recenti
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) per le versioni precedenti di php

* Altre origini dati

   * [Gestione connessione PHP](https://www.php.net/manual/en/pdo.connections.php)

### <a name="modify-the-application-to-reuse-connections"></a>Modificare l'applicazione per riutilizzare le connessioni

*  Per ulteriori puntatori ed esempi sulla gestione delle connessioni in funzioni di Azure, vedere [gestire le connessioni in funzioni di Azure](../azure-functions/manage-connections.md).

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Modificare l'applicazione per usare una logica di ripetizione meno aggressiva

* Per altre istruzioni ed esempi, vedere [modello di ripetizione dei tentativi](/azure/architecture/patterns/retry).

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Usare keep-alive per reimpostare il timeout di inattività per le connessioni uscita

* Per implementare i keep-alive per le app Node.js, esaminare [l'applicazione Node esegue](./app-service-web-nodejs-best-practices-and-troubleshoot-guide.md#my-node-application-is-making-excessive-outbound-calls)un numero eccessivo di chiamate in uscita.

### <a name="additional-guidance-specific-to-app-service"></a>Altre linee guida specifiche per il servizio app:

* Un [test di carico](/azure/devops/test/load-test/app-service-web-app-performance-test) deve simulare dati reali in una velocità di alimentazione costante. Il test delle app e delle funzioni con lo stress reale può identificare e risolvere i problemi di esaurimento delle porte SNAT in anticipo.
* Assicurarsi che i servizi back-end possano restituire rapidamente le risposte. Per la risoluzione dei problemi di prestazioni del database SQL di Azure, vedere [risolvere i problemi di prestazioni del database SQL di Azure con Intelligent Insights](../azure-sql/database/intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow).
* Aumentare il numero di istanze del piano di servizio app. Per altre informazioni sul ridimensionamento, vedere [Ridimensionare un'app nel Servizio app di Azure](./manage-scale-up.md). Ogni istanza del ruolo di lavoro in un piano di servizio app viene allocata in un numero di porte SNAT. Se si distribuisce l'utilizzo in più istanze, è possibile ottenere l'utilizzo della porta SNAT per ogni istanza al di sotto del limite consigliato di 100 connessioni in uscita, per endpoint remoto univoco.
* Prendere in considerazione il passaggio a [ambiente del servizio app (ASE)](./environment/using-an-ase.md), in cui viene assegnato un singolo indirizzo IP in uscita e i limiti per le connessioni e le porte SNAT sono molto più elevati. In un ambiente del servizio app, il numero di porte SNAT per istanza è basato sulla [tabella di preallocazione di Azure Load Balancer](../load-balancer/load-balancer-outbound-connections.md#snatporttable) . ad esempio, un ambiente del servizio app con 1-50 di istanze di lavoro ha 1024 porte preallocate per ogni istanza, mentre un ambiente del servizio app con 51-100 istanze di lavoro ha 512 porte preallocate per ogni istanza.

Evitare i limiti TCP in uscita è più semplice da risolvere, poiché i limiti vengono impostati dalle dimensioni del ruolo di lavoro. È possibile visualizzare i limiti nei [limiti numerici di sandbox tra macchine virtuali-connessioni TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nome limite|Description|Piccolo (a1)|Media (a2)|Grande (a3)|Livello isolato (ASE)|
|---|---|---|---|---|---|
|Connessioni|Numero di connessioni nell'intera VM|1920|3968|8064|16.000|

Per evitare i limiti TCP in uscita, è possibile aumentare le dimensioni dei ruoli di lavoro o scalare orizzontalmente.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Conoscere i due tipi di limiti delle connessioni in uscita e le operazioni che l'app deve eseguire per semplificare la risoluzione dei problemi. Se si è certi che l'app effettua molte chiamate allo stesso account di archiviazione, è possibile che si sospetti un limite di SNAT. Se l'app crea un numero elevato di chiamate agli endpoint in Internet, si potrebbe sospettare di raggiungere il limite di VM.

Se non si conosce abbastanza il comportamento dell'applicazione per determinare rapidamente la ragione, nel servizio app sono disponibili alcuni strumenti e tecniche per semplificare la determinazione.

### <a name="find-snat-port-allocation-information"></a>Trovare le informazioni sull'allocazione della porta SNAT

È possibile usare la [diagnostica del servizio app](./overview-diagnostics.md) per trovare le informazioni sull'allocazione delle porte SNAT e osservare la metrica di allocazione delle porte SNAT di un sito del servizio app. Per trovare le informazioni sull'allocazione delle porte SNAT, attenersi alla procedura seguente:

1. Per accedere alla diagnostica del servizio app, passare all'app Web del servizio app o ambiente del servizio app nel [portale di Azure](https://portal.azure.com/). Nel percorso di spostamento a sinistra selezionare **diagnostica e Risolvi i problemi**.
2. Selezionare la categoria disponibilità e prestazioni
3. Selezionare il riquadro dell'esaurimento delle porte SNAT nell'elenco dei riquadri disponibili sotto la categoria. La procedura consiste nel mantenerla sotto 128.
Se necessario, è comunque possibile aprire un ticket di supporto e il tecnico del supporto otterrà la metrica dal back-end.

Poiché l'utilizzo della porta SNAT non è disponibile come metrica, non è possibile eseguire la scalabilità automatica in base all'utilizzo della porta SNAT o configurare la scalabilità automatica in base alla metrica di allocazione delle porte SNAT.

### <a name="tcp-connections-and-snat-ports"></a>Connessioni TCP e porte SNAT

Le connessioni TCP e le porte SNAT non sono direttamente correlate. Un rilevatore di utilizzo delle connessioni TCP è incluso nel pannello diagnostica e Risolvi i problemi di qualsiasi sito del servizio app. Cercare la frase "connessioni TCP" per trovarla.

* Le porte SNAT vengono usate solo per i flussi di rete esterni, mentre le connessioni TCP totali includono connessioni loopback locali.
* Una porta SNAT può essere condivisa da flussi diversi, se i flussi sono diversi in entrambi i protocolli, l'indirizzo IP o la porta. Ogni connessione TCP viene conteggiata dalla metrica connessioni TCP.
* Il limite di connessioni TCP si verifica a livello di istanza di lavoro. Il bilanciamento del carico in uscita rete di Azure non usa la metrica connessioni TCP per la limitazione delle porte SNAT.
* I limiti delle connessioni TCP sono descritti in [limiti numerici di sandbox tra macchine virtuali-connessioni TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nome limite|Description|Piccolo (a1)|Media (a2)|Grande (a3)|Livello isolato (ASE)|
|---|---|---|---|---|---|
|Connessioni|Numero di connessioni nell'intera VM|1920|3968|8064|16.000|

### <a name="webjobs-and-database-connections"></a>Processi Web e connessioni di database
 
Se le porte SNAT sono esaurite, in cui i processi Web non sono in grado di connettersi al database SQL, non esiste alcuna metrica per mostrare il numero di connessioni aperte da ogni singolo processo dell'applicazione Web. Per trovare il processo Web problematico, spostare diversi processi Web in un altro piano di servizio app per verificare se la situazione è migliore o se un problema persiste in uno dei piani. Ripetere il processo fino a individuare i processo web problematici.

### <a name="using-snat-ports-sooner"></a>Uso delle porte SNAT prima

Non è possibile modificare le impostazioni di Azure per rilasciare prima le porte SNAT usate, perché tutte le porte SNAT verranno rilasciate in base alle condizioni seguenti e il comportamento è da progettazione.
 
* Se il server o il client invia FINACK, la [porta SNAT verrà rilasciata](../load-balancer/load-balancer-outbound-connections.md) dopo 240 secondi.
* Se viene individuata una RST, la porta SNAT viene rilasciata dopo 15 secondi.
* Se è stato raggiunto il timeout di inattività, la porta viene rilasciata.
 
## <a name="additional-information"></a>Informazioni aggiuntive

* [SNAT con il servizio app](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Risoluzione dei problemi di rallentamento delle prestazioni delle app nel Servizio app di Azure](./troubleshoot-performance-degradation.md)
