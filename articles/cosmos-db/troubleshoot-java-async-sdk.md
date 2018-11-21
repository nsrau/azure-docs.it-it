---
title: Diagnosticare e risolvere i problemi dell'SDK Java Async di Azure Cosmos DB| Microsoft Docs
description: Usa funzionalità come la registrazione lato client e altri strumenti di terze parti per identificare, diagnosticare e risolvere i problemi correlati ad Azure Cosmos DB.
services: cosmos-db
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.component: cosmosdb-sql
ms.openlocfilehash: ee92a5dd474cdf4f32ed2c7327d732a2cfbbbf79
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632935"
---
# <a name="troubleshooting-issues-when-using-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Risoluzione dei problemi durante l'uso dell'SDK Java Async con gli account API SQL di Azure Cosmos DB
Questo articolo illustra problemi e soluzioni alternative comuni, passaggi di diagnostica e strumenti utili durante l'uso di [ADK Async Java](sql-api-sdk-async-java.md) con gli account API SQL di Azure Cosmos DB.
Java Async SDK offre una rappresentazione logica lato client per accedere alle API SQL di Azure Cosmos DB. Questo articolo descrive gli strumenti e approcci utili ad affrontare eventuali problemi.

Iniziamo con un elenco:
    * Diamo un'occhiata alla sezione [Problemi comuni e soluzioni alternative] in questo articolo.
    * Il nostro SDK è [open source su github](https://github.com/Azure/azure-cosmosdb-java) e abbiamo una [sezione relativa ai problemi](https://github.com/Azure/azure-cosmosdb-java/issues) monitorata attivamente. Cercare eventuali problemi simili con una soluzione alternativa già archiviata.
    * Esaminare i [suggerimenti sulle prestazioni](performance-tips-async-java.md) e seguire le procedure consigliate.
    * Leggere tutto il presente articolo: se non si trova una soluzione, segnalare un [problema di GitHub](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-workarounds"></a>Problemi e soluzioni alternative comuni

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problemi di rete, errore di timeout nella lettura di Netty, ridotta velocità effettiva, latenza elevata

#### <a name="general-suggestions"></a>Suggerimenti generici
* Assicurarsi che l'app sia in esecuzione nella stessa area dell'account Cosmos DB. 
* Controllare l'utilizzo della CPU nell'host in cui viene eseguita l'app. Se l'utilizzo della CPU è superiore al 90%, consigliamo di eseguire l'app in un host con configurazione superiore o di distribuire il carico su più computer.

#### <a name="connection-throttling"></a>Limitazione della connessione
La limitazione della connessione può verificarsi a causa di un [Limite di connessione nel computer host] o di un [esaurimento delle porte SNAT (PAT) di Azure]:

##### <a name="connection-limit-on-host"></a>Limite di connessione nel computer host
Alcuni sistemi Linux (come "Red Hat") hanno un limite superiore al numero totale di file aperti. I socket in Linux vengono implementati come file, per cui questo numero limita anche il numero totale di connessioni.
Eseguire il comando seguente:

```bash
ulimit -a
```
Il numero di file aperti ("nofile") deve essere sufficientemente grande (almeno il doppio delle dimensioni del pool di connessione). Per altri dettagli, leggere i [suggerimenti sulle prestazioni](performance-tips-async-java.md).

##### <a name="snat"></a>Esaurimento delle porte SNAT (PAT) di Azure

Se l'app viene distribuita nella macchina virtuale di Azure senza un indirizzo IP pubblico, le [porte SNAT di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) vengono usate per impostazione predefinita per stabilire le connessioni agli endpoint all'esterno della macchina virtuale. Il numero di connessioni consentite dalla macchina virtuale per l'endpoint di Cosmos DB è limitato dalla [configurazione SNAT di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

Le porte SNAT di Azure vengono usate solo quando la macchina virtuale di Azure ha un indirizzo IP privato e un processo proveniente dalla macchina virtuale tenta di stabilire una connessione a un indirizzo IP pubblico. Pertanto, esistono due soluzioni alternative per evitare la limitazione dello SNAT di Azure:
    * Aggiungere l'endpoint del servizio Azure Cosmos DB alla subnet della VNET della macchina virtuale di Azure come descritto in [Attivazione dell'endpoint di servizio della VNET](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Quando l'endpoint di servizio è attivo, le richieste non sono più inviate da un indirizzo IP pubblico a Cosmos DB, ma vengono inviate l'identità della VNET e della subnet. Questa modifica può comportare drop del firewall se sono consentiti solo gli indirizzi IP pubblici. Se si usa un firewall, aggiungere la subnet al firewall durante l'abilitazione dell'endpoint di servizio usando gli [ACL della rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
    * Assegnare un indirizzo IP pubblico alla macchina virtuale di Azure.

#### <a name="http-proxy"></a>Proxy HTTP

Se si usa un proxy HTTP, assicurarsi che possa supportare il numero di connessioni configurate nell'SDK `ConnectionPolicy`.
In caso contrario, verranno riscontrati problemi di connessione.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Modello di codifica non valido: blocco del thread I/O Netty

L'SDK usa la libreria I/O [Netty](https://netty.io/) per comunicare con il servizio Azure Cosmos DB. Abbiamo API asincrone e usiamo API di I/O non bloccante di Netty. Le operazioni di I/O dell'SDK vengono eseguite su thread I/O di Netty. Il numero di thread Netty di I/O è configurato per corrispondere al numero di core della CPU del computer dell'app. I thread I/O di Netty sono concepiti esclusivamente per le operazioni di I/O non bloccante di Netty. L'SDK restituisce al codice dell'app il risultato della chiamata dell'API in uno dei thread I/O di Netty. Se l'app, dopo la ricezione dei risultati sul thread di Netty, esegue un'operazione di lunga durata nel thread stesso, l'SDK potrebbe non avere un numero sufficiente di thread I/O per eseguire le sue operazioni di I/O interne. Questa codifica dell'app potrebbe comportare ridotta velocità effettiva, latenza elevata, ed errori di `io.netty.handler.timeout.ReadTimeoutException`. La soluzione alternativa consiste nel cambiare il thread quando si prevede che l'operazione richieda tempo.

   Ad esempio, il seguente frammento di codice dimostra che, se vengono delle operazioni di lunga durata (che richiedono più di qualche millisecondo) sul thread di Netty, è possibile ritrovarsi in uno stato in cui non ci sono thread I/O di Netty per elaborare le operazioni di I/O, ricavando come risultato ReadTimeoutException:
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
                        // time consuming work. For example:
                        // writing to a file, computationally heavy work, or just sleep
                        // basically anything which takes more than a few milliseconds
                        // doing such operation on the IO netty thread
                        // without a proper scheduler, will cause problems.
                        // The subscriber will get ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //will be io.netty.handler.timeout.ReadTimeoutException
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
   La soluzione alternativa consiste nel cambiare il thread in cui eseguire l'operazione più lunga. Definire un'istanza singleton dell'Utilità di pianificazione per l'app:
   ```java
// have a singleton instance of executor and scheduler
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Quando è necessario eseguire un'operazione di lunga durata (ad esempio calcoli impegnativi, I/O bloccante), cambiare il thread a un ruolo di lavoro fornito da `customScheduler` usando l'API `.observeOn(customScheduler)`.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // switches the thread.
        .subscribe(
            // ...
        );
```
Usando `observeOn(customScheduler)`, verrà rilasciato il thread I/O di Netty e verrà cambiato con il thread personalizzato fornito da customScheduler. Questa modifica risolverà il problema e non si verificheranno più errori di `io.netty.handler.timeout.ReadTimeoutException`.

### <a name="connection-pool-exhausted-issue"></a>Problema di esaurimento del pool di connessione

`PoolExhaustedException` è un errore lato client. Se questo errore si verifica spesso, è segnale del fatto che il carico di lavoro dell'app è superiore alle possibilità del pool di connessione dell'SDK. L'aumento delle dimensioni del pool di connessione o la distribuzione del carico su più app potrebbe essere utile.

### <a name="request-rate-too-large"></a>La frequenza delle richieste è troppo elevata
Questo errore è un errore lato server per indicare che è stata consumata la velocità effettiva del provisioning e sarebbe opportuno riprovare in un secondo momento. Se questo errore si verifica spesso, provare ad aumentare la velocità effettiva di raccolta.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Errore di connessione all'emulatore di Azure Cosmos DB

Il certificato HTTPS dell'emulatore di Cosmos DB è autofirmato. Per poter usare l'SDK con l'emulatore, importare il certificato dell'emulatore in Java TrustStore. L'operazione è illustrata [qui](local-emulator-export-ssl-certificates.md).


## <a name="enable-client-sice-logging"></a>Abilitare la registrazione dell'SDK del client

Java Async SDK usa SLF4j come interfaccia per supportare la registrazione in framework di registrazione diffusi come log4j e logback.

Ad esempio, se desideri usare log4j come framework di registrazione, aggiungere le seguenti librerie nel classpath Java:

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

Aggiungere anche una configurazione log4j:
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

Vedere il [manuale di registrazione di sfl4j](https://www.slf4j.org/manual.html) per altre informazioni.

## <a name="netstats"></a>Statistiche di rete del sistema operativo
Eseguire il comando netstat per comprendere numero di connessioni in stato `Established`, stato `CLOSE_WAIT` ecc.

Su Linux, è possibile eseguire il comando seguente:
```bash
netstat -nap
```
Filtrare i risultati per visualizzare solo le connessioni all'endpoint di Cosmos DB.

Evidentemente, il numero di connessioni all'endpoint di Cosmos DB in stato `Established` non deve essere superiore alle dimensioni del pool di connessione configurato.

Se sono presenti molte connessioni all'endpoint di Cosmos DB in stato `CLOSE_WAIT`, per esempio più di 1000, è segno che le connessioni vengono stabilite ed eliminate rapidamente, il che potrebbe causare problemi. Vedere la sezione [Problemi comuni e soluzioni alternative] per altri informazioni.

 <!--Anchors-->
[Problemi comuni e soluzioni alternative]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limite di connessione nel computer host]: #connection-limit-on-host
[Esaurimento delle porte SNAT (PAT) di Azure]: #snat


