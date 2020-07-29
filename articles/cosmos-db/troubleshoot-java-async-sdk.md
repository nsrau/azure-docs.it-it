---
title: Diagnosticare e risolvere i problemi di Azure Cosmos DB Async Java SDK v2
description: Usare funzionalità come la registrazione lato client e altri strumenti di terze parti per identificare, diagnosticare e risolvere i problemi relativi ad Azure Cosmos DB in Async Java SDK v2.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: 60d73f8b3eae21ab399853e8d05b67b7b431ee5f
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321055"
---
# <a name="troubleshoot-issues-when-you-use-the-azure-cosmos-db-async-java-sdk-v2-with-sql-api-accounts"></a>Risolvere i problemi relativi all'uso di Azure Cosmos DB Async Java SDK v2 con API SQL

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> *Non* corrisponde alla versione più recente di Java SDK per Azure Cosmos DB. È necessario aggiornare il progetto ad [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) e quindi leggere la [guida alla risoluzione dei problemi](troubleshoot-java-sdk-v4-sql.md) di Azure Cosmos DB Java SDK v4. Seguire le istruzioni della guida alla [migrazione a Java SDK v4 per Azure Cosmos DB](migrate-java-v4-sdk.md) e la guida relativa al [confronto tra Reactor e RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md). 
>
> Questo articolo illustra la risoluzione dei problemi solo di Azure Cosmos DB Async Java SDK v2. Per altre informazioni, vedere le [Note sulla versione](sql-api-sdk-async-java.md) di Azure Cosmos DB Async Java SDK v2, il [repository Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) e i [suggerimenti sulle prestazioni](performance-tips-async-java.md).
>

Questo articolo illustra problemi e soluzioni alternative comuni, passaggi di diagnostica e strumenti utili durante l'uso di [Java Async SDK](sql-api-sdk-async-java.md) con gli account API SQL di Azure Cosmos DB.
Java Async SDK offre una rappresentazione logica lato client per accedere all'API SQL di Azure Cosmos DB. Questo articolo descrive strumenti e approcci utili ad affrontare eventuali problemi.

Iniziamo con un elenco:

* Diamo un'occhiata alla sezione [Problemi comuni e soluzioni alternative] in questo articolo.
* Esaminare l'SDK, disponibile come [open source su GitHub](https://github.com/Azure/azure-cosmosdb-java). Include una [sezione per i problemi](https://github.com/Azure/azure-cosmosdb-java/issues) monitorata attivamente. Verificare se è già stato pubblicato un problema simile con una soluzione alternativa.
* Esaminare i [suggerimenti sulle prestazioni](performance-tips-async-java.md) e seguire le procedure consigliate.
* Leggere la parte restante di questo articolo, se non si trova una soluzione. Registrare poi un [problema in GitHub](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Problemi e soluzioni alternative comuni

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problemi di rete, errore di timeout nella lettura di Netty, ridotta velocità effettiva, latenza elevata

#### <a name="general-suggestions"></a>Suggerimenti generici
* Assicurarsi che l'app sia in esecuzione nella stessa area dell'account Azure Cosmos DB. 
* Controllare l'utilizzo della CPU nell'host in cui viene eseguita l'app. Se l'utilizzo della CPU è 90% o oltre, eseguire l'app in un host con una configurazione superiore oppure distribuire il carico su più computer.

#### <a name="connection-throttling"></a>Limitazione della connessione
La limitazione delle connessioni può verificarsi a causa di un [limite di connessioni nel computer host] o di un [esaurimento delle porte SNAT (PAT) di Azure].

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Limite di connessioni nel computer host
Alcuni sistemi Linux, come Red Hat, prevedono un limite massimo per il numero totale di file aperti. I socket in Linux vengono implementati come file, per cui questo numero limita anche il numero totale di connessioni.
Eseguire il comando seguente.

```bash
ulimit -a
```
Il numero massimo consentito di file aperti, identificati come "nofile", deve essere almeno il doppio della dimensione del pool di connessioni. Per altre informazioni, vedere [Suggerimenti sulle prestazioni](performance-tips-async-java.md).

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Esaurimento delle porte SNAT (PAT) di Azure

Se l'app viene distribuita in macchine virtuali di Azure senza un indirizzo IP pubblico, per impostazione predefinita le [porte SNAT di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) stabiliscono le connessioni a qualsiasi endpoint all'esterno della macchina virtuale. Il numero di connessioni consentite dalla macchina virtuale all'endpoint di Azure Cosmos DB è limitato dalla [configurazione SNAT di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Le porte SNAT di Azure vengono usate solo quando la macchina virtuale ha un indirizzo IP privato e un processo dalla macchina virtuale prova a connettersi a un indirizzo IP pubblico. Esistono due soluzioni alternative per evitare la limitazione per le porte SNAT di Azure:

* Aggiungere l'endpoint del servizio Azure Cosmos DB alla subnet della rete virtuale di macchine virtuali di Azure. Per altre informazioni, vedere [Endpoint servizio di rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Quando l'endpoint del servizio è abilitato, le richieste non vengono più inviate da un indirizzo IP pubblico ad Azure Cosmos DB. Vengono invece inviate le identità di rete virtuale e subnet. Questa modifica può comportare blocchi del firewall se sono consentiti solo indirizzi IP pubblici. Se si usa un firewall, quando si abilita l'endpoint del servizio, aggiungere una subnet al firewall tramite [ACL di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Assegnare un indirizzo IP pubblico alla macchina virtuale di Azure.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Non è possibile raggiungere il servizio - Firewall
``ConnectTimeoutException`` indica che l'SDK non riesce a raggiungere il servizio.
Quando si usa la modalità diretta, è possibile che si verifichi un errore simile al seguente:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Se nel computer dell'app è in esecuzione un firewall, aprire l'intervallo di porte da 10.000 a 20.000, che corrisponde a quello usato dalla modalità diretta.
Seguire anche il [Limite di connessione nel computer host](#connection-limit-on-host).

#### <a name="http-proxy"></a>Proxy HTTP

Se si usa un proxy HTTP, assicurarsi che possa supportare il numero di connessioni configurate in `ConnectionPolicy` dell'SDK.
In caso contrario, verranno riscontrati problemi di connessione.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Modello di codifica non valida: blocco del thread di I/O Netty

L'SDK usa la libreria di I/O [Netty](https://netty.io/) per comunicare con Azure Cosmos DB. L'SDK include API asincrone e usa le API di I/O non bloccante di Netty. Le operazioni di I/O dell'SDK vengono eseguite su thread di I/O di Netty. Il numero di thread di I/O di Netty viene configurato in modo da corrispondere al numero di core della CPU del computer dell'app. 

I thread di I/O di Netty sono concepiti esclusivamente per le operazioni di I/O non bloccante di Netty. L'SDK restituisce al codice dell'app il risultato della chiamata dell'API in uno dei thread di I/O di Netty. Se l'app esegue un'operazione di lunga durata dopo la ricezione dei risultati sul thread di Netty, l'SDK potrebbe non avere un numero di thread di I/O sufficiente a eseguire le operazioni di I/O interne. Questa codifica dell'app potrebbe comportare ridotta velocità effettiva, latenza elevata ed errori `io.netty.handler.timeout.ReadTimeoutException`. La soluzione alternativa consiste nel cambiare il thread quando si prevede che l'operazione richieda tempo.

Ad esempio, esaminare il frammento di codice seguente. È possibile che si eseguano operazioni di lunga durata che richiedono più di pochi millisecondi sul thread di Netty. In questo caso, si potrebbe raggiungere uno stato in cui non è presente alcun thread di I/O di Netty per elaborare le operazioni di I/O, con conseguente generazione dell'errore ReadTimeoutException.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-readtimeout"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // Time-consuming work is, for example,
                        // writing to a file, computationally heavy work, or just sleep.
                        // Basically, it's anything that takes more than a few milliseconds.
                        // Doing such operations on the IO Netty thread
                        // without a proper scheduler will cause problems.
                        // The subscriber will get a ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //It will be io.netty.handler.timeout.ReadTimeoutException.
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
La soluzione alternativa consiste nel cambiare il thread su cui si eseguono operazioni di lunga durata. Definire un'istanza singleton dell'utilità di pianificazione per l'app.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-scheduler"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
```
Potrebbe essere necessario eseguire operazioni che richiedono tempo, ad esempio azioni intensive a livello di calcolo oppure I/O di blocco. In questo caso, passare il thread su un ruolo di lavoro fornito da `customScheduler` tramite l'API `.observeOn(customScheduler)`.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-applycustomscheduler"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
Usando `observeOn(customScheduler)`, il thread di I/O di Netty viene rilasciato e si passa al thread personalizzato specificato dall'utilità di pianificazione personalizzata. Questa modifica risolve il problema. Non verrà più generato un errore `io.netty.handler.timeout.ReadTimeoutException`.

### <a name="connection-pool-exhausted-issue"></a>Problema di esaurimento del pool di connessione

`PoolExhaustedException` è un errore lato client. Questo errore indica che il carico di lavoro dell'app è superiore alle possibilità del pool di connessione dell'SDK. Aumentare le dimensioni del pool di connessioni o distribuire il carico su più app.

### <a name="request-rate-too-large"></a>La frequenza delle richieste è troppo elevata
Questo errore è un errore sul lato server. Indica l'esaurimento della velocità effettiva di cui è stato effettuato il provisioning. Riprovare in un secondo momento. Se questo errore si verifica spesso, provare ad aumentare la velocità effettiva della raccolta.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Errore di connessione all'emulatore di Azure Cosmos DB

Il certificato HTTPS dell'emulatore di Azure Cosmos DB è autofirmato. Per poter usare l'SDK con l'emulatore, importare il certificato dell'emulatore in un Java TrustStore. Per altre informazioni, vedere [Esportare i certificati dell'emulatore di Azure Cosmos DB](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Problemi di conflitto di dipendenze

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

L'eccezione precedente indica la presenza di una dipendenza da una versione precedente della libreria RxJava (ad esempio, 1.2.2). L'SDK si basa su RxJava 1.3.8, che include API non disponibili in versioni precedenti di RxJava. 

Per risolvere questi problemi, è possibile identificare le altre dipendenze che coinvolgono RxJava-1.2.2 ed escludere la dipendenza transitiva da RxJava-1.2.2, in modo da consentire a CosmosDB SDK di introdurre la versione più recente.

Per identificare la libreria che coinvolge RxJava-1.2.2, eseguire il comando seguente accanto al file pom.xml del progetto:
```bash
mvn dependency:tree
```
Per altre informazioni, vedere la [guida all'albero delle dipendenze di Maven](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Quando si identifica che RxJava-1.2.2 è una dipendenza transitiva di un'altra dipendenza del progetto, è possibile modificare la dipendenza da quella libreria nel file POM ed escludere la dipendenza transitiva RxJava:

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-rxjava1.2.2}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-rxjava1.2.2}</artifactId>
  <version>${version-of-lib-which-brings-in-rxjava1.2.2}</version>
  <exclusions>
    <exclusion>
      <groupId>io.reactivex</groupId>
      <artifactId>rxjava</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Per altre informazioni, vedere la [guida all'esclusione delle dipendenze transitive](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Abilitare la registrazione dell'SDK del client

Java Async SDK usa SLF4j come interfaccia per supportare la registrazione in framework di registrazione diffusi come log4j e logback.

Ad esempio, se si vuole usare log4j come framework di registrazione, aggiungere le librerie seguenti nel classpath Java.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Aggiungere anche una configurazione log4j.
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Per altre informazioni, vedere il [manuale di registrazione di sfl4j](https://www.slf4j.org/manual.html).

## <a name="os-network-statistics"></a><a name="netstats"></a>Statistiche di rete del sistema operativo
Eseguire il comando netstat per farsi un'idea di quante connessioni sono in stati come `ESTABLISHED` e `CLOSE_WAIT`.

In Linux è possibile eseguire il comando seguente.
```bash
netstat -nap
```
Filtrare i risultati per visualizzare solo le connessioni all'endpoint di Azure Cosmos DB.

Il numero di connessioni all'endpoint di Azure Cosmos DB in stato `ESTABLISHED` non deve essere superiore alle dimensioni del pool di connessione configurato.

Molte connessioni all'endpoint di Azure Cosmos DB potrebbero trovarsi nello stato `CLOSE_WAIT`. Potrebbero essercene più di 1.000. Un numero così alto indica che le connessioni vengono stabilite ed eliminate rapidamente. Questa situazione può causare potenzialmente problemi. Per altre informazioni, vedere la sezione [Problemi comuni e soluzioni alternative].

 <!--Anchors-->
[Problemi comuni e soluzioni alternative]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limite di connessioni nel computer host]: #connection-limit-on-host
[Esaurimento delle porte SNAT (PAT) di Azure]: #snat


