---
title: Panoramica delle code di messaggi, argomenti e sottoscrizioni del bus di servizio di Azure | Documentazione Microsoft
description: Panoramica delle entità di messaggistica del bus di servizio.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/18/2018
ms.author: aschhab
ms.openlocfilehash: 7cacabf4f171189810e943043b5513e20113d962
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847032"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Code, argomenti e sottoscrizioni del bus di servizio

Il bus di servizio di Microsoft Azure supporta un set di tecnologie middleware orientate ai messaggi e basate sul cloud, incluso l'accodamento dei messaggi affidabile e la messaggistica di pubblicazione e sottoscrizione permanente. Queste funzionalità di messaggistica "negoziata" possono essere considerate come funzionalità di messaggistica asincrone o disaccoppiate che supportano scenari di pubblicazione e sottoscrizione, disaccoppiamento temporale e bilanciamento del carico tramite il carico di lavoro di messaggistica del bus di servizio. La comunicazione disaccoppiata presenta molti vantaggi, ad esempio client e server possono connettersi quando necessario ed eseguire le relative operazioni in modo asincrono.

Le entità di messaggistica che costituiscono le funzionalità di messaggistica di base nel bus di servizio sono code, argomenti e sottoscrizioni e regole/azioni.

## <a name="queues"></a>Queues

Le code consentono un recapito dei messaggi di tipo *FIFO (First In, First Out)* a uno o più consumer concorrenti. In base a questo metodo, in genere i messaggi vengono ricevuti ed elaborati nell'ordine temporale in cui sono stati aggiunti alla coda e ogni messaggio viene ricevuto ed elaborato da un solo consumer. Il vantaggio principale derivante dall'uso delle code è quello di ottenere un "disaccoppiamento temporale" dei componenti applicativi, ovvero non è necessario che i producer e i consumer inviino e ricevano i messaggi contemporaneamente perché i messaggi restano archiviati nella coda. Il producer inoltre non deve attendere la risposta del consumer per continuare a elaborare e inviare messaggi.

Un vantaggio correlato è quello del "livellamento del carico", che permette ai producer e ai consumer di inviare e ricevere i messaggi con frequenze diverse. In molte applicazioni, il carico del sistema varia nel tempo, tuttavia, il tempo di elaborazione necessario per ogni unità è in genere costante. L'interposizione di una coda tra producer e consumer di messaggi implica che è necessario solo eseguire il provisioning dell'applicazione consumer per gestire un carico medio invece di un carico massimo. In base alla variazione del carico in ingresso, si verificherà un incremento o una riduzione della profondità della coda, con un risparmio diretto in termini economici rispetto alle risorse infrastrutturali richieste per gestire il carico dell'applicazione. Con l'aumento del carico, è possibile aggiungere altri processi di lavoro per la lettura della coda. Ciascun messaggio viene elaborato da un solo processo di lavoro. Inoltre, il bilanciamento del carico di tipo pull permette un uso ottimale dei computer di lavoro anche quando questi presentano una potenza di elaborazione diversa. Ogni computer effettuerà infatti il pull dei messaggi in base alla propria velocità massima. Questo modello viene spesso definito modello del "consumer concorrente".

L'uso di code da interporre tra producer e consumer di messaggi fornisce un accoppiamento intrinseco di tipo regime di controllo libero tra i componenti. Poiché producer e consumer sono indipendenti gli uni dagli altri, è possibile aggiornare un consumer senza causare alcun effetto sul producer.

### <a name="create-queues"></a>Creazione di code

Creare le code usando il [portale di Azure](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), l'[interfaccia della riga di comando](service-bus-quickstart-cli.md) o i [modelli di Resource Manager](service-bus-resource-manager-namespace-queue.md). Inviare e ricevere quindi i messaggi usando un oggetto [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient).

Per imparare rapidamente a creare una coda, inviare e ricevere messaggi da e verso la coda, vedere i progetti di [avvio rapido](service-bus-quickstart-portal.md) per ogni metodo. Per un'esercitazione più dettagliata su come usare le code, vedere [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md).

Per un esempio funzionante, vedere [BasicSendReceiveUsingQueueClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) su GitHub.

### <a name="receive-modes"></a>Modalità di ricezione

È possibile specificare due diverse modalità con cui il bus di servizio riceve i messaggi: *ReceiveAndDelete* o *PeekLock*. Quando si usa la modalità [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode), la ricezione è un'operazione a un'unica fase. Quando il bus di servizio riceve la richiesta, contrassegna il messaggio come usato e lo restituisce all'applicazione. La modalità **ReceiveAndDelete** rappresenta il modello più semplice ed è adatta per scenari in cui l'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo scenario, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come consumato, quando l'applicazione viene riavviata e inizia a consumare nuovamente i messaggi, il messaggio consumato prima dell'arresto anomalo risulterà perso.

Con la modalità [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) il processo di ricezione diventa un'operazione in due fasi, che rende possibile il supporto di applicazioni che non riescono a tollerare messaggi mancanti. Quando il bus di servizio riceve la richiesta, individua il messaggio successivo da consumare, lo blocca per impedirne la ricezione da parte di altri consumer e lo restituisce quindi all'applicazione. Dopo avere elaborato il messaggio o averlo archiviato in modo affidabile per una successiva elaborazione, l'applicazione esegue la seconda fase del processo di ricezione chiamando [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) sul messaggio ricevuto. Quando il bus di servizio rileva la chiamata **CompleteAsync**, contrassegna il messaggio come usato.

Se per qualche motivo l'applicazione non è in grado di elaborare il messaggio, può chiamare il metodo [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync), invece di [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync), per il messaggio ricevuto. Questo metodo abilita il bus di servizio per sbloccare il messaggio che sarà disponibile per essere nuovamente ricevuto dallo stesso consumer o da un altro consumer concorrente. Al blocco è associato anche un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sblocca il messaggio rendendolo nuovamente disponibile per la ricezione, eseguendo essenzialmente un'operazione [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) per impostazione predefinita.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima dell'emissione della richiesta **CompleteAsync**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito di tipo *At-Least-Once*, per indicare che ogni messaggio viene elaborato almeno una volta, ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, sarà necessaria una logica aggiuntiva nell'applicazione per il rilevamento dei duplicati in base alla proprietà [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) del messaggio, che rimane costante per tutti i tentativi di recapito. Questa funzione è nota come elaborazione di tipo *Exactly Once*.

## <a name="topics-and-subscriptions"></a>Argomenti e sottoscrizioni

Diversamente dalle code, in cui ogni messaggio viene elaborato da un unico consumer, gli *argomenti* e le *sottoscrizioni* offrono una forma di comunicazione di tipo uno-a-molti, in un modello di *pubblicazione/sottoscrizione*. Particolarmente utile per la comunicazione con un numero elevato di destinatari, ogni messaggio pubblicato è reso disponibile per ogni sottoscrizione registrata con l'argomento. I messaggi vengono inviati a un argomento e recapitati a una o più sottoscrizioni associate, a seconda delle regole di filtro che possono essere impostate per ogni sottoscrizione. Per limitare i messaggi da ricevere, le sottoscrizioni possono usare filtri aggiuntivi. I messaggi vengono inviati a un argomento nello stesso modo in cui vengono inviati a una coda, con la differenza che i messaggi non vengono ricevuti direttamente dall'argomento. Vengono ricevuti dalle sottoscrizioni. La sottoscrizione di un argomento è simile a una coda virtuale che riceve copie dei messaggi inviati all'argomento. La procedura di ricezione dei messaggi da parte di una sottoscrizione è identica a quella usata per la ricezione da parte di una coda.

Ai fini di un confronto, la funzionalità di invio dei messaggi di una coda esegue il mapping direttamente a un argomento e la funzionalità di ricezione dei messaggi esegue il mapping a una sottoscrizione. Questa funzione implica anche che le sottoscrizioni supportano gli stessi modelli descritti prima in questa sezione in merito alle code: consumer concorrente, disaccoppiamento temporale, livellamento del carico e bilanciamento del carico.

### <a name="create-topics-and-subscriptions"></a>Creare argomenti e sottoscrizioni

La procedura per la creazione di un argomento è simile a quella per la creazione di una coda, descritta nella sezione precedente. È quindi possibile inviare messaggi usando la classe [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient). Per ricevere messaggi, si creano una o più sottoscrizioni all'argomento. Come accade per le code, i messaggi vengono ricevuti da una sottoscrizione usando un oggetto [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) invece di un oggetto [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient). Creare il client della sottoscrizione, passando il nome dell'argomento, il nome della sottoscrizione e (facoltativamente) la modalità di ricezione come parametri.

Per un esempio funzionante completo, vedere [BasicSendReceiveUsingTopicSubscriptionClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) su GitHub.

### <a name="rules-and-actions"></a>Regole e azioni

In molti scenari, i messaggi con caratteristiche specifiche devono essere elaborati in modi specifici. Per abilitare questa elaborazione, è possibile configurare le sottoscrizioni in modo che trovino i messaggi che presentano le proprietà desiderate e apportare quindi alcune modifiche a tali proprietà. Mentre nelle sottoscrizioni del bus di servizio tutti i messaggi vengono inviati all'argomento, l'utente può copiare solo un subset di tali messaggi nella coda virtuale delle sottoscrizioni. Questo filtraggio viene eseguito usando i filtri della sottoscrizione. Queste modifiche sono chiamate *azioni di filtro*. Quando viene creata una sottoscrizione, è possibile specificare un'espressione di filtro che agisce sulle proprietà del messaggio, sulle proprietà del sistema, ad esempio **Label**, e sulle proprietà dell'applicazione personalizzata, ad esempio **StoreName**. In questo caso l'espressione di filtro SQL è facoltativa. Senza un'espressione di filtro SQL, qualsiasi azione di filtro definita in una sottoscrizione verrà eseguita in tutti i messaggi di tale sottoscrizione.

Per un esempio funzionante completo, vedere [TopicSubscriptionWithRuleOperationsSample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) su GitHub.

Per altre informazioni sui valori di filtro possibili, vedere la documentazione relativa alle classi [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) e [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction).

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi e informazioni sull'uso della messaggistica del bus di servizio, vedere gli argomenti avanzati seguenti:

* [Panoramica della messaggistica del bus di servizio](service-bus-messaging-overview.md)
* [Avvio rapido: Inviare e ricevere messaggi usando il portale di Azure e .NET](service-bus-quickstart-portal.md)
* [Esercitazione: Aggiornare l'inventario usando il portale di Azure e argomenti/sottoscrizioni](service-bus-tutorial-topics-subscriptions-portal.md)


