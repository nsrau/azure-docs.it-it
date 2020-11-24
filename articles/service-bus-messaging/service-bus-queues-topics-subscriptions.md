---
title: 'Messaggistica del bus di servizio di Azure: code, argomenti e sottoscrizioni'
description: Questo articolo fornisce una panoramica delle entità di messaggistica del bus di servizio di Azure (code, argomenti e sottoscrizioni).
ms.topic: article
ms.date: 11/04/2020
ms.openlocfilehash: 54b6a1fd2d4e8e5ef5bb6522374646257213e4b4
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95791598"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Code, argomenti e sottoscrizioni del bus di servizio
Il bus di servizio di Azure supporta un set di tecnologie middleware orientate ai messaggi e basate sul cloud, incluso l'accodamento dei messaggi affidabile e la messaggistica di pubblicazione e sottoscrizione permanente. Queste funzionalità di messaggistica negoziata possono essere considerate come funzionalità di messaggistica disaccoppiate che supportano scenari di pubblicazione-sottoscrizione, disaccoppiamento temporale e bilanciamento del carico tramite il carico di lavoro della messaggistica del bus di servizio. La comunicazione disaccoppiata presenta molti vantaggi. Ad esempio, i client e i server possono connettersi in base alle esigenze ed eseguire le operazioni in modo asincrono.

Le entità di messaggistica che costituiscono la base delle funzionalità di messaggistica del bus di servizio sono **Code**, **argomenti e sottoscrizioni** e regole/azioni.

## <a name="queues"></a>Code
Le code consentono un recapito dei messaggi di tipo **FIFO (First In, First Out)** a uno o più consumer concorrenti. Ovvero, i ricevitori ricevono in genere ed elaborano i messaggi nell'ordine in cui sono stati aggiunti alla coda. E, solo un consumer di messaggi riceve ed elabora ogni messaggio. Un vantaggio principale dell'uso delle code è quello di ottenere la **separazione temporale dei componenti dell'applicazione**. In altre parole, i producer (mittenti) e i consumer (ricevitori) non devono inviare e ricevere messaggi nello stesso momento. Questo perché i messaggi vengono archiviati in modo durevole nella coda. Inoltre, il producer non deve attendere che una risposta del consumer continui a elaborare e inviare messaggi.

Un vantaggio correlato è il **livellamento del carico**, che consente ai producer e ai consumer di inviare e ricevere messaggi a frequenze diverse. In molte applicazioni il carico del sistema varia nel tempo. Tuttavia, il tempo di elaborazione necessario per ogni unità di lavoro è in genere costante. L'intermediazione di Producer e consumer di messaggi con una coda significa che l'applicazione consumer deve essere in grado di gestire il carico medio anziché il carico massimo. In base alla variazione del carico in ingresso, si verificherà un incremento o una riduzione della profondità della coda, con un risparmio diretto in termini economici rispetto alle risorse infrastrutturali richieste per gestire il carico dell'applicazione. Con l'aumento del carico, è possibile aggiungere altri processi di lavoro per la lettura della coda. Ciascun messaggio viene elaborato da un solo processo di lavoro. Inoltre, il bilanciamento del carico basato su pull consente un uso ottimale dei computer di lavoro anche se i computer del ruolo di lavoro elaborano i messaggi di Power Pull alla propria velocità massima. Questo modello è spesso definito modello del **consumer concorrente**.

L'uso di code da interporre tra producer e consumer di messaggi fornisce un accoppiamento intrinseco di tipo regime di controllo libero tra i componenti. Poiché i producer e i consumer non sono consapevoli degli altri, è possibile aggiornare un consumer senza influire sul Producer.

### <a name="create-queues"></a>Creazione di code
È possibile creare code usando il [portale di Azure](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), l' [interfaccia](service-bus-quickstart-cli.md)della riga di comando o i [modelli gestione risorse](service-bus-resource-manager-namespace-queue.md). Inviare e ricevere messaggi usando i client scritti in [C#](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md), [Python](service-bus-python-how-to-use-queues.md), [JavaScript](service-bus-nodejs-how-to-use-queues.md), [php](service-bus-php-how-to-use-queues.md)e [Ruby](service-bus-ruby-how-to-use-queues.md). 

### <a name="receive-modes"></a>Modalità di ricezione
È possibile specificare due diverse modalità con cui ricevere i messaggi del bus di servizio: **ReceiveAndDelete** o **PeekLock**. In modalità [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) , quando il bus di servizio riceve la richiesta dal consumer, contrassegna il messaggio come utilizzato e lo restituisce all'applicazione consumer. Questa modalità è il modello più semplice. Funziona meglio per gli scenari in cui l'applicazione può tollerare la mancata elaborazione di un messaggio se si verifica un errore. Per comprendere meglio questo scenario, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, l'applicazione inizia a consumare messaggi al riavvio. Il messaggio utilizzato prima dell'arresto anomalo verrà perso.

In modalità [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) , l'operazione Receive diventa a due fasi, che rende possibile il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve la richiesta, esegue le operazioni seguenti:

1. Trova il messaggio successivo da utilizzare.
1. Lo blocca per impedirne la ricezione da parte di altri consumer.
1. Quindi, restituire il messaggio all'applicazione. 

Dopo aver elaborato il messaggio o averlo archiviato in modo affidabile per una successiva elaborazione, l'applicazione completa la seconda fase del processo di ricezione chiamando [`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) sul messaggio. Quando il bus di servizio riceve la richiesta **CompleteAsync** , contrassegna il messaggio come utilizzato.

Se l'applicazione non è in grado di elaborare il messaggio per qualche motivo, può chiamare il [`AbandonAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) metodo sul messaggio (anziché [`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) ). Questo metodo abilita il bus di servizio per sbloccare il messaggio che sarà disponibile per essere nuovamente ricevuto dallo stesso consumer o da un altro consumer concorrente. In secondo luogo, al blocco è associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout di blocco, il bus di servizio Sblocca il messaggio e lo rende disponibile per la ricezione.

Se l'applicazione si arresta in modo anomalo dopo l'elaborazione del messaggio, ma prima che venga chiamato [`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) , il bus di servizio riconsegnerà il messaggio all'applicazione al riavvio. Questo processo viene spesso chiamato elaborazione **at-least-once** . Ovvero ogni messaggio viene elaborato almeno una volta. ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera l'elaborazione duplicata, aggiungere logica aggiuntiva nell'applicazione per rilevare i duplicati. È possibile ottenerlo utilizzando la proprietà [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid) del messaggio, che rimane costante in tutti i tentativi di recapito. Questa funzionalità è nota come elaborazione **esatta una sola volta** .

## <a name="topics-and-subscriptions"></a>Argomenti e sottoscrizioni
Una coda consente l'elaborazione di un messaggio da un singolo consumer. Diversamente dalle code, gli argomenti e le sottoscrizioni offrono una forma di comunicazione uno-a-molti in un modello di **pubblicazione e sottoscrizione** . È utile per la scalabilità a un numero elevato di destinatari. Ogni messaggio pubblicato viene reso disponibile per ogni sottoscrizione registrata con l'argomento. Il server di pubblicazione invia un messaggio a un argomento e uno o più Sottoscrittori ricevono una copia del messaggio, a seconda delle regole di filtro impostate per queste sottoscrizioni. Per limitare i messaggi da ricevere, le sottoscrizioni possono usare filtri aggiuntivi. Gli autori inviano messaggi a un argomento nello stesso modo in cui inviano messaggi a una coda. Tuttavia, gli utenti non ricevono messaggi direttamente dall'argomento. I consumer ricevono invece messaggi dalle sottoscrizioni dell'argomento. La sottoscrizione di un argomento è simile a una coda virtuale che riceve copie dei messaggi inviati all'argomento. I consumer ricevono messaggi da una sottoscrizione in modo identico al modo in cui ricevono messaggi da una coda.

La funzionalità di invio di messaggi di una coda viene mappata direttamente a un argomento e la funzionalità di ricezione dei messaggi viene mappata a una sottoscrizione. Questa funzione implica anche che le sottoscrizioni supportano gli stessi modelli descritti prima in questa sezione in merito alle code: consumer concorrente, disaccoppiamento temporale, livellamento del carico e bilanciamento del carico.

### <a name="create-topics-and-subscriptions"></a>Creare argomenti e sottoscrizioni
La procedura per la creazione di un argomento è simile a quella per la creazione di una coda, descritta nella sezione precedente. È possibile creare argomenti e sottoscrizioni usando il [portale di Azure](service-bus-quickstart-topics-subscriptions-portal.md), [PowerShell](service-bus-quickstart-powershell.md), l' [interfaccia](service-bus-tutorial-topics-subscriptions-cli.md)della riga di comando o i [modelli gestione risorse](service-bus-resource-manager-namespace-topic.md). Inviare quindi messaggi a un argomento e ricevere messaggi dalle sottoscrizioni usando i client scritti in [C#](service-bus-dotnet-how-to-use-topics-subscriptions.md), [Java](service-bus-java-how-to-use-topics-subscriptions.md), [Python](service-bus-python-how-to-use-topics-subscriptions.md), [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md), [php](service-bus-php-how-to-use-topics-subscriptions.md)e [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md). 

### <a name="rules-and-actions"></a>Regole e azioni
In molti scenari, i messaggi con caratteristiche specifiche devono essere elaborati in modi specifici. Per abilitare questa elaborazione, è possibile configurare le sottoscrizioni in modo che trovino i messaggi che presentano le proprietà desiderate e apportare quindi alcune modifiche a tali proprietà. Mentre nelle sottoscrizioni del bus di servizio tutti i messaggi vengono inviati all'argomento, l'utente può copiare solo un subset di tali messaggi nella coda virtuale delle sottoscrizioni. Questo filtraggio viene eseguito usando i filtri della sottoscrizione. Queste modifiche sono chiamate **azioni di filtro**. Quando viene creata una sottoscrizione, è possibile specificare un'espressione di filtro che agisce sulle proprietà del messaggio. Le proprietà possono essere entrambe le proprietà di sistema (ad esempio, **Label**) e le proprietà personalizzate dell'applicazione, ad esempio **StoreName**. In questo caso, l'espressione di filtro SQL è facoltativa. Senza un'espressione di filtro SQL, qualsiasi azione di filtro definita in una sottoscrizione verrà eseguita su tutti i messaggi per tale sottoscrizione.

Per un esempio funzionante completo, vedere [TopicSubscriptionWithRuleOperationsSample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) su GitHub.

Per altre informazioni sui valori di filtro possibili, vedere la documentazione relativa alle classi [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) e [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction).

## <a name="java-message-service-jms-20-entities-preview"></a>Entità Java Message Service (JMS) 2,0 (anteprima)
Le entità seguenti sono accessibili tramite l'API di Java Message Service (JMS) 2,0.

  * Code temporanee
  * Argomenti temporanei
  * Sottoscrizioni durevoli condivise
  * Sottoscrizioni durevoli non condivise
  * Sottoscrizioni non durevoli condivise
  * Sottoscrizioni non durevoli non condivise

Altre informazioni sulle [entità JMS 2,0](java-message-service-20-entities.md) e su come [usarle](how-to-use-java-message-service-20.md).

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi e informazioni sull'uso della messaggistica del bus di servizio, vedere gli argomenti avanzati seguenti:

* [Panoramica della messaggistica del bus di servizio](service-bus-messaging-overview.md)
* [Guida introduttiva: Inviare e ricevere messaggi usando il portale di Azure e .NET](service-bus-quickstart-portal.md)
* [Esercitazione: Aggiornare l'inventario usando il portale di Azure e argomenti/sottoscrizioni](service-bus-tutorial-topics-subscriptions-portal.md)


