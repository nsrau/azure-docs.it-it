<properties 
   pageTitle="Inoltro automatico di entità di messaggistica del bus di servizio | Microsoft Azure"
   description="Illustra come concatenare una coda o una sottoscrizione a una coda o un argomento differente che fa parte dello stesso spazio dei nomi del servizio."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/18/2015"
   ms.author="sethm" />

# Concatenamento di entità del bus di servizio con l'inoltro automatico

La funzionalità di *inoltro automatico* consente di concatenare una coda o una sottoscrizione a una coda o un argomento differente che fa parte dello stesso spazio dei nomi del servizio. Quando l'inoltro automatico è abilitato, il bus di servizio rimuove automaticamente i messaggi presenti nella prima coda o sottoscrizione (origine) e li inserisce nella seconda coda o nell'argomento (destinazione). Si noti che è comunque possibile inviare un messaggio direttamente all'entità di destinazione. Tenere inoltre presente che non è possibile concatenare una coda secondaria, ad esempio una coda di messaggi non recapitabili, a una coda o un argomento differente.

## Uso dell'inoltro automatico

Per abilitare l'inoltro automatico, è possibile impostare la proprietà [QueueDescription.ForwardTo][] o [SubscriptionDescription.ForwardTo][] nell'oggetto [QueueDescription][] o [SubscriptionDescription][] per l'origine, come mostrato nell'esempio seguente.

```
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

L'entità di destinazione deve essere presente al momento della creazione dell'entità di origine. In caso contrario, il bus di servizio restituisce un'eccezione quando gli viene chiesto di creare l'entità di origine.

È possibile usare l'inoltro automatico per ampliare un singolo argomento. Il bus di servizio limita il numero delle sottoscrizioni per un determinato argomento. Creando argomenti di secondo livello, è possibile aggiungere altre sottoscrizioni. Si noti che, pur non essendo vincolati dalla limitazione del bus di servizio relativa al numero delle sottoscrizioni, l'aggiunta di un secondo livello di argomenti consente complessivamente di migliorare la velocità effettiva dell'argomento.

![Scenario di inoltro automatico][0]

È possibile usare l'inoltro automatico anche per disaccoppiare i mittenti dei messaggi dai destinatari. Si consideri ad esempio un sistema ERP costituito da tre moduli: Elaborazione ordini, Gestione magazzino e Gestione rapporti con i clienti. Ognuno di questi moduli genera messaggi che vengono accodati in un argomento corrispondente. Due dei rappresentanti di vendita sono interessati a tutti i messaggi correlati ai propri clienti. Per ricevere questi messaggi, i due rappresentanti creano una coda personale e una sottoscrizione per ognuno degli argomenti ERP che inoltrano automaticamente tutti i messaggi alla rispettiva coda.

![Scenario di inoltro automatico][1]

Se uno dei rappresentanti si assenta, viene riempita la coda personale, ma non l'argomento ERP. In questo scenario, poiché un rappresentante di vendita non ha ricevuto alcun messaggio, nessuno degli argomenti ERP raggiunge la quota.

## Considerazioni sulla funzionalità di inoltro automatico

Se l'entità di destinazione ha accumulato troppi messaggi e supera la quota oppure è disabilitata, l'entità di origine aggiunge i messaggi alla relativa coda dei messaggi non recapitabili finché non si libera spazio nella destinazione o l'entità non viene abilitata di nuovo. I messaggi resteranno nella coda dei messaggi non recapitabili, pertanto sarà necessario riceverli ed elaborarli in modo esplicito da tale coda.

Se si concatenano singoli argomenti per ottenere un argomento composito con diverse sottoscrizioni, è consigliabile avere a disposizione un certo numero di sottoscrizioni sull'argomento di primo livello e un numero più elevato di sottoscrizioni sugli argomenti di secondo livello. Ad esempio, un argomento di primo livello con 20 sottoscrizioni, ciascuna delle quali concatenata a un argomento di secondo livello con 200 sottoscrizioni, consente una velocità effettiva più alta rispetto a un argomento di primo livello con 200 sottoscrizioni, ciascuna delle quali concatenata a un argomento di secondo livello con 20 sottoscrizioni.

Il bus di servizio addebita un'operazione per ogni messaggio inoltrato. Ad esempio, se viene inviato un messaggio a un argomento con 20 sottoscrizioni, ciascuna delle quali configurata per l'inoltro automatico dei messaggi a una coda o un argomento differente, vengono addebitate 21 operazioni se tutte le sottoscrizioni di primo livello ricevono una copia del messaggio.

Per creare una sottoscrizione concatenata a una coda o un argomento differente, l'autore deve disporre delle autorizzazioni di **gestione** per l'entità di origine e per quella di destinazione. Se vengono inviati messaggi solo all'argomento di origine, è sufficiente disporre delle autorizzazioni di **invio** per l'argomento di origine.

## Passaggi successivi

Per informazioni dettagliate sull'inoltro automatico, vedere gli argomenti di riferimento seguenti:

- [SubscriptionDescription.ForwardTo][]
- [QueueDescription][]
- [SubscriptionDescription][]

Per altre informazioni sull'ottimizzazione delle prestazioni del bus di servizio, vedere [Partizionamento delle entità di messaggistica][].

  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [SubscriptionDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.forwardto.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [SubscriptionDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.aspx
  [0]: ./media/service-bus-auto-forwarding/IC628631.gif
  [1]: ./media/service-bus-auto-forwarding/IC628632.gif
  [Partizionamento delle entità di messaggistica]: service-bus-partitioning.md

<!---HONumber=Sept15_HO4-->