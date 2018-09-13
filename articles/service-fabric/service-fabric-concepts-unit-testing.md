---
title: Testing unità per i servizi con stato in Azure Service Fabric | Microsoft Docs
description: Informazioni su concetti e procedure consigliate di servizi con stato di Service Fabric di testing unità.
services: service-fabric
documentationcenter: .net
author: charleszipp
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: ryanwi
ms.openlocfilehash: c2d98316b81b3d908ebbe6147fe40f231e94c142
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703375"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Testing unità dei servizi con stato in Service Fabric

Questo articolo copre concetti e procedure consigliate di servizi con stato di Service Fabric di testing unità. Testing unità all'interno di Service Fabric merita considerazioni specifiche dovuto al fatto che il codice dell'applicazione viene eseguito attivamente in più contesti diversi. Questo articolo descrive le procedure usate per assicurare che il codice dell'applicazione è coperto in ognuno dei diversi contesti che può essere eseguito.

## <a name="unit-testing-and-mocking"></a>Testing unità e comportamento fittizio
Testing unità nel contesto di questo articolo è un testing automatizzato che può essere eseguito nel contesto di un esecutore di test, ad esempio NUnit o MSTest. Gli unit test all'interno di questo articolo non eseguono operazioni su una risorsa remota, ad esempio un database o l'API RESTFul. Queste risorse remote devono essere fittizie. Il comportamento fittizio nel contesto di questo articolo produrranno fake, record e controllo per i valori restituiti per le risorse remote.

### <a name="service-fabric-considerations"></a>Considerazioni di Service Fabric
Il testing unità di un servizio con stato di Service Fabric dispone di diverse considerazioni. In primo luogo, il codice del servizio viene eseguito in più nodi, ma in ruoli diversi. Gli unit test devono valutare il codice sottoposto a ogni ruolo per ottenere una copertura completa. I diversi ruoli sarebberp primario, secondario attivo, secondario inattivo e sconosciuto. Nessun ruolo non ha in genere bisogno di nessun numero speciale poiché Service Fabric considera questo ruolo del servizio void o null. In secondo luogo, ogni nodo modificherà il proprio ruolo in qualsiasi momento. Per ottenere una copertura completa, il percorso di esecuzione del codice deve essere testato con le modifiche di ruolo che si verificano.

## <a name="why-unit-test-stateful-services"></a>Perché i servizi con stato di unit test? 
I servizi con stato di testing unità possono aiutare a individuare alcuni errori comuni che vengono fatti che non vengono necessariamente rilevati dall'applicazione convenzionale o dal testing unità specifico di dominio. Ad esempio, se il servizio con stato ha uno stato in memoria, questo tipo di test può verificare che questo stato in memoria sia mantenuto sincronizzato tra ogni replica. Questo tipo di test può anche verificare che un servizio con stato risponda in modo appropriato ai token di annullamento passati in base all'orchestrazione di Service Fabric. Quando vengono attivati gli annullamenti, il servizio deve interrompere qualsiasi operazione in esecuzione prolungata e/o asincrone.  

## <a name="common-practices"></a>Procedure comuni

La sezione seguente indica nelle abitudini più comuni per i testing unità un servizio con stato. Viene anche indicato cosa deve avere un livello di comportamento fittizio per allineare strettamente l'orchestrazione di Service Fabric e la gestione dello stato. Per le librerie di simulazione esistono librerie che forniscono questa funzionalità. [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) al momento della stesura 3.3.0 o nella versione successiva, è una di queste librerie che forniscono la funzionalità di creazione di versioni fittizie consigliata e che segue le procedure indicate di seguito.

### <a name="arrangement"></a>Disposizione

#### <a name="use-multiple-service-instances"></a>Usare più istanze del servizio
Gli unit test devono eseguire più istanze di un servizio con stato. Viene simulato che cosa succede realmente nel cluster in cui Service Fabric esegue il provisioning di più repliche eseguendo il servizio in nodi diversi. Tuttavia, ciascuna di queste istanze verrà eseguita in un contesto diverso. Quando si esegue il test, ciascuna istanza deve essere riempita con la configurazione del ruolo prevista nel cluster. Ad esempio, se il servizio deve avere dimensioni pari a 3 repliche di destinazione, Service Fabric potrebbe effettuare il provisioning di tre repliche in nodi diversi. Una delle quali è il database primario e le altri due sono attive secondarie.

Nella maggior parte dei casi, il percorso di esecuzione del servizio varia leggermente per ciascuno di questi ruoli. Ad esempio, se il servizio non deve accettare le richieste provenienti da un secondario attivo, il servizio potrebbe avere un controllo per questo caso per generare nuovamente un'eccezione di tipo informativo che indica che una richiesta è stata tentata in un database secondario. Data la presenza di più istanze, questa situazione sarà sottoposta a test.

Inoltre, la presenza di più istanze consente ai test di invertire i ruoli di ciascuna di queste istanze per verificare che le risposte siano coerenti nonostante le modifiche dei ruoli.

#### <a name="mock-the-state-manager"></a>Simulare il gestore di stato
Il gestore di stato deve essere considerato come una risorsa remota e pertanto fittizia. Quando si simula il gestore di stato, ci deve essere una risorsa di archiviazione sottostante in memoria per tenere traccia di ciò che viene salvato nel gestore di stato, in modo che ciò possa essere letto e verificato. Un modo semplice per ottenere questo risultato consiste nel creare istanze fittizie di ciascuno dei tipi di raccolte affidabili. All'interno di tali oggetti fittizi, usare un tipo di dati che si allinea con le operazioni eseguite a fronte di tale raccolta. Ecco alcuni tipi di dati suggeriti per ogni raccolta affidabile

- IReliableDictionary<TKey, TValue> -> System.Collections.Concurrent.ConcurrentDictionary<TKey, TValue>
- IReliableQueue<T> -> System.Collections.Generic.Queue<T>
- IReliableConcurrentQueue<T> -> System.Collections.Concurrent.ConcurrentQueue<T>

#### <a name="many-state-manager-instances-single-storage"></a>Molte istanze di gestione di stato, archiviazione singola
Come accennato in precedenza, il gestore di stato e le raccolte affidabili devono essere considerate come una risorsa remota. Pertanto, queste risorse devono essere e saranno simulate all'interno degli unit test. Tuttavia, quando si eseguono più istanze di un servizio con stato sarà una sfida per mantenere sincronizzata ogni gestione fittizia dello stato tra più istanze di servizio con stato diverso. Quando il servizio con stato è in esecuzione nel cluster, Service Fabric si occupa del mantenimento della coerenza del gestore di stato di ogni replica secondaria con la replica primaria. Di conseguenza, i test devono comportarsi in modo che sia possibile simulare modifiche dei ruoli.

Un modo semplice in cui questa sincronizzazione può essere ottenuta, consiste nell'utilizzare un modello singleton per l'oggetto sottostante che archivia i dati scritti in ciascuna raccolta affidabile. Ad esempio, se un servizio con stato usa un `IReliableDictionary<string, string>`. Il gestore di stato fittizio deve restituire una simulazione di `IReliableDictionary<string, string>`. Tale simulazione può usare un `ConcurrentDictionary<string, string>` per tenere traccia delle coppie chiave/valore scritte. `ConcurrentDictionary<string, string>` deve essere un singleton usato da tutte le istanze degli strumenti di gestione dello stato passato al servizio.

#### <a name="keep-track-of-cancellation-tokens"></a>Tenere traccia dei token di annullamento
I token di annullamento sono un aspetto importante eppure comunemente trascurato dei servizi con stato. All'avvio di una replica primaria per un servizio con stato da parte di Service Fabric viene fornito un token di annullamento. Questo token di annullamento deve segnalare al servizio quando viene rimosso o abbassato di livello a un ruolo diverso. Il servizio con stato necessario deve interrompere qualsiasi operazione a esecuzione prolungata o asincrone in modo che Service Fabric possa completare il flusso di lavoro di modifica del ruolo.

Quando si eseguono gli unit test, eventuali token di annullamento forniti a RunAsync, ChangeRoleAsync e OpenAsync, CloseAsync devono essere mantenuti durante l'esecuzione del test. Il mantenimento di questi token consentirà al test di simulare un arresto o l'abbassamento di livello del servizio e di verificare che il servizio risponda in modo appropriato.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Test end-to-end con risorse remote fittizie
Gli unit test devono eseguire la maggior parte del codice dell'applicazione che può modificare lo stato del servizio con stato come possibile. È consigliabile che i test siano più end-to-end. Gli oggetti simulati esistenti servono per registrare, simulare e/o verificare le interazioni della risorsa remota. Sono incluse le interazioni con il gestore di stato e le raccolte affidabili. Il frammento seguente è un esempio di gherkin per un test che illustra test end-to-end:

```
    Given stateful service named "fabric:/MyApp/MyService" is created
    And a new replica is created as "Primary" with id "111"
    And a new replica is created as "IdleSecondary" with id "222"
    And a new replica is created as "IdleSecondary" with id "333"
    And all idle secondary replicas are promoted to active secondary
    When a request is made to add the an employee "John Smith"
    And the active secondary replica "222" is promoted to primary
    And a request is made to get all employees
    Then the request should should return the "John Smith" employee
```

Questo test indica che i dati acquisiti in una replica sono disponibili in una replica secondaria quando viene alzata di livello a primaria. Supponendo che una raccolta affidabile sia l'archivio di backup per i dati del dipendente, un errore potenziale che potrebbe essere rilevato con questo test è se il codice dell'applicazione non è stato eseguitao `CommitAsync` sulla transazione per salvare il nuovo dipendente. In tal caso, la seconda richiesta per ottenere i dipendenti non restituirà il dipendente aggiunto per la prima richiesta.

### <a name="acting"></a>Agire
#### <a name="mimic-service-fabric-replica-orchestration"></a>Simulare l'orchestrazione di replica di Service Fabric
Quando si gestiscono più istanze del servizio, i test devono inizializzare e deconfigurare questi servizi in modo analogo all'orchestrazione di Service Fabric. Ad esempio, quando viene creato un servizio in una nuova replica primaria, Service Fabric richiamerà CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync e RunAsync. Gli eventi del ciclo di vita sono documentati negli articoli seguenti:

- [Avvio di un servizio con stato](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Arresto di un servizio con stato](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Scambi della replica primaria di un servizio con stato](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Eseguire modifiche del ruolo della replica
Gli unit test devono modificare i ruoli delle istanze del servizio in modo analogo all'orchestrazione di Service Fabric. La macchina a stati del ruolo è documentata nell'articolo seguente:

[Macchina a stati del ruolo della replica](service-fabric-concepts-replica-lifecycle.md#replica-role)

La simulazione di modifiche dei ruoli è uno degli aspetti più critici del test e può rivelare problemi laddove gli stati della replica non sono coerenti tra loro. Lo stato della replica non coerente può verificarsi a causa dell'archiviazione nello stato in memoria in variabili dell'istanza statiche o a livello di classe. Esempi di questo possono essere i token di annullamento, le enumerazioni e gli oggetti/valori di configurazione. Questo garantirà inoltre che il servizio rispetti i token di annullamento forniti durante RunAsync per consentire che si verifichi la modifica del ruolo. La simulazione delle modifiche del ruolo può anche rivelare i problemi che potrebbero verificarsi se il codice non è scritto per consentire una chiamata di RunAsync più volte.

#### <a name="cancel-cancellation-tokens"></a>Annullare il token di annullamento
Devono essere presenti gli unit test in cui il token di annullamento fornito a RunAsync viene annullato. In questo modo il test verificherà che il servizio venga chiuso normalmente. Durante questo arresto qualsiasi operazione a esecuzione prolungata o asincrone deve essere arrestata. Un esempio di un processo a esecuzione prolungata che può essere presente in un servizio è uno che è in ascolto dei messaggi su un oggetto Coda affidabile. Ciò può esistere direttamente in RunAsync or in un thread in background. L'implementazione deve includere la logica per la chiusura dell'operazione se questo token di annullamento viene annullato.

Se i servizi con stato utilizzano gli stati della cache o in memoria che devono esistere solo nella replica primaria, ciò deve essere eliminato in questa fase. Così facendo si garantisce che questo stato sia coerente se il nodo diventa un database primario in un secondo momento. Il testi di annullamento consentirà al test di verificare che questo stato sia eliminato correttamente.

#### <a name="execute-requests-against-multiple-replicas"></a>Eseguire le richieste su più repliche
Dichiarare che i test devono eseguire la stessa richiesta su repliche diverse. Quando abbinati a modifiche del ruolo, i problemi di coerenza possono non essere rivelati. Un test di esempio può eseguire i passaggi seguenti:
1. Eseguire una richiesta di scrittura per il database primario corrente
2. Eseguire una richiesta di lettura che restituisce i dati scritti nel passaggio 1 in una replica primaria corrente
3. Alzare di livello un database da secondario a primario. Anche questa deve abbassare di livello il database primario corrente a quello secondario
4. Eseguire la stessa richiesta di lettura dal passaggio 2 sul nuovo database secondario.

Nell'ultimo passaggio, il test può asserire che i dati restituiti sono coerenti. Un potenziale problema che si potrebbe rilevare è che i dati restituiti dal servizio potrebbero essere in memoria ma sono supportati in definitiva da una raccolta affidabile. Tali dati in memoria potrebbero non essere mantenuti sincronizzati correttamente con ciò che è presente nella raccolta affidabile.

I dati in memoria sono in genere usati per creare indici secondari o aggregazioni di dati esistenti in una raccolta affidabile.

### <a name="asserting"></a>Asserzione
#### <a name="ensure-responses-match-across-replicas"></a>Verificare che le risposte corrispondano tra le repliche
Gli unit test devono dichiarare che una risposta per una determinata richiesta sia coerente tra più repliche dopo che eseguono la transizione alla replica primaria. Ciò può far emergere problemi potenziali in cui i dati forniti nella risposta non supportati da una raccolta affidabile, o mantenuti in memoria senza un meccanismo per sincronizzare i dati tra le repliche. Ciò garantisce che il servizio invii di nuovo le risposte coerenti dopo che Service Fabric esegua di nuovo il bilanciamento o il failover in una nuova replica primaria.

#### <a name="verify-service-respects-cancellation"></a>Verificare l'annullamento in relazione al servizio
Si deve verificare che i processi con esecuzione prolungata o asincroni che devono essere terminati quando viene annullato un token di annullamento siano effettivamente terminati dopo l'annullamento. Questa operazione assicurerà che nonostante i ruoli di modifica della replica, i processi che non sono destinati ad essere mantenuti in esecuzione nella replica non primaria vengano arrestati prima del completamento della transizione. Questo può anche rivelare problemi in cui tale processo blocca una richiesta di modifica o di arresto di un ruolo da Service Fabric da completare.

#### <a name="verify-which-replicas-should-serve-requests"></a>Verificare quali repliche devono rispondere alle richieste
I test devono dichiarare il comportamento previsto se una richiesta viene indirizzata a una replica non primaria. Service Fabric offre la possibilità di avere repliche secondarie che rispondono alle richieste. Tuttavia, le operazioni di scrittura nelle raccolte affidabili possono verificarsi solo dalla replica primaria. Se l'applicazione intende rispondere alle richieste solo per le repliche primarie o, solo un subset delle richieste può essere gestito da un database secondario, i test devono dichiarare il comportamento previsto per entrambi i casi positivi e negativi. Il caso negativo che è una richiesta viene indirizzato a una replica che non deve gestire la richiesta e il caso positivo l'opposto.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [Eseguire testing unità dei servizi con stato](service-fabric-how-to-unit-test-stateful-services.md).