---
title: Domande frequenti sul servizio di inoltro | Microsoft Docs
description: Risposte ad alcune domande frequenti sul servizio di inoltro di Azure.
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: jotaub,sethm
translationtype: Human Translation
ms.sourcegitcommit: 849d7995e9c74bc929c4f791ae9155ca18ddb77b
ms.openlocfilehash: 12702f585605a88ed6e753a0ff301095e8629e05


---
# <a name="relay-faq"></a>Domande frequenti sul servizio di inoltro
Questo articolo contiene le risposte ad alcune domande frequenti sul servizio di inoltro di Microsoft Azure. Per informazioni generali sui prezzi e sul supporto di Azure, vedere [Domande frequenti sul supporto di Azure](http://go.microsoft.com/fwlink/?LinkID=185083) .

## <a name="general-questions"></a>Domande generali
### <a name="what-is-azure-relay"></a>Che cos'è il servizio di inoltro di Azure?
Il [servizio di inoltro](relay-what-is-it.md) di Azure semplifica le applicazioni ibride consentendo di esporre in modo sicuro nel cloud pubblico i servizi che risiedono in una rete aziendale, senza dover aprire una connessione firewall o richiedere modifiche di notevole impatto a un'infrastruttura di rete aziendale.

### <a name="what-is-a-relay-namespace"></a>Che cos'è uno spazio dei nomi di inoltro?
Uno [spazio dei nomi](relay-create-namespace-portal.md) fornisce un contenitore di ambito per fare riferimento alle risorse di inoltro nell'applicazione. La creazione di uno spazio dei nomi è necessaria per usare il servizio di inoltro ed è uno dei primi passaggi delle attività iniziali.

### <a name="what-happened-to-the-previously-named-relay-service"></a>Cos'è successo al servizio di inoltro disponibile in precedenza?
Il servizio che un tempo si chiamava **Inoltro** ora si chiama *Inoltro WCF*. È possibile continuare a usarlo come un tempo. Connessioni ibride è una versione aggiornata di un servizio trasferito da BizTalk. Sia Inoltro WCF che Connessioni ibride continueranno a essere supportati.

## <a name="pricing"></a>Prezzi
Questa sezione contiene le risposte ad alcune domande frequenti sulla struttura dei prezzi del servizio di inoltro. Per informazioni generali sui prezzi di Microsoft Azure, vedere le [Domande frequenti su Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=185083) . Per informazioni complete sui prezzi del servizio di inoltro, vedere la pagina contenente i [dettagli dei prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Come vengono addebitati i costi di Connessioni ibride e Inoltro WCF?
Per informazioni complete sui prezzi del servizio di inoltro, vedere la pagina contenente i [dettagli dei prezzi del bus di servizio][Panoramica sui prezzi]. Oltre ai prezzi indicati, vengono addebitati i trasferimenti di dati associati in uscita dal data center in cui è stato effettuato il provisioning dell'applicazione.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Come viene fatturato l'uso di Connessioni ibride?
Ecco tre scenari di esempio:

1. Se si dispone di un singolo listener, ad esempio un'istanza della gestione delle connessioni ibride installata e continuamente in esecuzione per tutto il mese, e vengono inviati 3 GB di dati tramite la connessione durante il mese, l'addebito totale sarà pari a $ 5.
2. Se si dispone di un singolo listener, ad esempio un'istanza della gestione delle connessioni ibride installata e continuamente in esecuzione per tutto il mese, e vengono inviati 10 GB di dati tramite la connessione durante il mese, l'addebito totale sarà pari a $ 7,50: $ 5 per la connessione e i primi 5 GB + $ 2,50 per i 5 GB aggiuntivi di dati.
3. Se si dispone di due istanze, A e B, della gestione delle connessioni ibride installate e continuamente in esecuzione per tutto il mese e vengono inviati 3 GB di dati tramite la connessione A e 6 GB tramite la connessione B, l'addebito totale sarà pari a $ 10,50: $ 5 per la connessione A + $ 5 per la connessione B + $ 0,50 (per il 6° GB nella connessione B).

I prezzi usati negli esempi saranno validi solo durante il periodo di anteprima e saranno soggetti a modifiche in occasione del passaggio alla versione disponibile a livello generale delle connessioni ibride.

### <a name="how-are-wcf-relay-hours-calculated"></a>Come vengono calcolate le ore di Inoltro WCF?
Le ore di inoltro vengono fatturate per la quantità totale di tempo durante il quale ogni inoltro del bus di servizio è "aperto". Un inoltro viene istanziato e aperto in modo implicito in un determinato indirizzo del bus di servizio (URL dello spazio dei nomi del servizio) quando un servizio WCF abilitato per l'inoltro, o "listener di inoltro", si connette per la prima volta a tale indirizzo. Viene chiuso solo quando l'ultimo listener si disconnette dal relativo indirizzo. Pertanto, ai fini della fatturazione un inoltro viene considerato "aperto" dal momento della prima connessione da parte di un listener di inoltro fino al momento in cui l'ultimo listener di inoltro si disconnette dall'indirizzo del bus di servizio di tale inoltro.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-given-relay"></a>Che cosa succede se più listener sono connessi a un determinato inoltro?
In alcuni casi, a un singolo inoltro possono essere connessi più listener. Un inoltro viene considerato "aperto" quando vi è connesso almeno un listener di inoltro. L'aggiunta di altri listener a un inoltro aperto si traduce in ore inoltro aggiuntive. Il numero di mittenti di inoltro (ossia di client che richiamano o inviano messaggi agli inoltri) connessi a un inoltro non influisce sul calcolo delle ore inoltro.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Come viene calcolata la misurazione dei messaggi per gli inoltri WCF?
**È applicabile solo agli inoltri WCF e non costituisce un costo per le connessioni ibride**

In generale, i messaggi fatturabili vengono calcolati per gli inoltri usando lo stesso metodo descritto in precedenza per le entità negoziate, quali code, argomenti e sottoscrizioni. Esistono tuttavia numerose differenze significative:

L'invio di un messaggio a un inoltro del bus di servizio viene considerato come un invio "diretto" al listener di inoltro che lo riceve, anziché come un invio all'inoltro del bus di servizio seguito da un recapito al listener di inoltro. Di conseguenza, una chiamata al servizio di tipo richiesta-risposta di dimensioni fino a 64 KB per un listener di inoltro genererà due messaggi fatturabili, uno per la richiesta e uno per la risposta, presupponendo che anche la risposta sia \<= a 64 KB. Questo comportamento è diverso dall'uso di una coda per la mediazione tra un client e un servizio. In quest'ultimo caso, lo stesso modello di richiesta-risposta renderebbe necessario l'invio di una richiesta alla coda, seguito da un annullamento dell'accodamento e/o dal recapito dalla coda al servizio, seguito dall'invio di una risposta a un'altra coda, seguito da un annullamento dell'accodamento e/o da un recapito da tale coda al client. Usando sempre gli stessi presupposti per le dimensioni, ossia \<= 64 KB, il modello di accodamento mediato genererebbe quindi quattro messaggi fatturabili, ossia un numero doppio rispetto a quello fatturato per implementare lo stesso modello con l'inoltro. L'uso delle code per applicare questo modello presenta ovviamente alcuni vantaggi, tra cui durabilità e livellamento del carico. Questi vantaggi possono giustificare i costi aggiuntivi.

Gli inoltri aperti con il binding WCF netTCPRelay considerano i messaggi non come singoli messaggi, ma come un flusso di dati che attraversa il sistema. In altri termini, solo il mittente e il listener hanno visibilità sulla struttura dei singoli messaggi inviati/ricevuti con questo binding. Di conseguenza, per gli inoltri che usano il binding netTCPRelay, tutti i dati vengono considerati come un flusso ai fini del calcolo dei messaggi fatturabili. In questo caso, il bus di servizio calcolerà la quantità totale di dati inviati o ricevuti tramite ogni singolo inoltro ogni 5 minuti e dividerà il totale per 64 KB per determinare il numero dei messaggi fatturabili per l'inoltro in questione durante il periodo di tempo specificato.

## <a name="quotas"></a>Quote
| Nome della quota | Scope | Tipo | Comportamento in caso di superamento | Valore |
| --- | --- | --- | --- | --- |
| Listener simultanei per un inoltro |Entità |Static |Le successive richieste di connessioni aggiuntive verranno rifiutate e il codice chiamante riceverà un'eccezione. |25 |
| Listener di inoltro simultanei |Intero sistema |Static |Le successive richieste di connessioni aggiuntive verranno rifiutate e il codice chiamante riceverà un'eccezione. |2.000 |
| Connessioni di inoltro simultanee per tutti gli endpoint di inoltro in uno spazio dei nomi del servizio |Intero sistema |Static |- |5.000 |
| Endpoint di inoltro per ogni spazio dei nomi del servizio |Intero sistema |Static |- |10.000 |
| Dimensione dei messaggi per gli inoltri [NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) e [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx) |Intero sistema |Static |I messaggi in ingresso che superano queste quote verranno rifiutati e il codice chiamante riceverà un'eccezione. |64 KB |
| Dimensione dei messaggi per gli inoltri [HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) e [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) |Intero sistema |Static |- |Illimitato |

### <a name="does-relay-have-any-usage-quotas"></a>Per il servizio di inoltro sono previste quote di utilizzo?
Per impostazione predefinita, per qualsiasi servizio cloud, Microsoft imposta una quota di utilizzo mensile aggregata che viene calcolata su tutte le sottoscrizioni di un cliente. Dal momento che i limiti previsti potrebbero non essere sufficienti, è possibile rivolgersi in qualsiasi momento al servizio clienti, che identificherà le esigenze specifiche e modificherà di conseguenza i limiti. Per il bus di servizio, le quote di utilizzo aggregate sono le seguenti:

* 5 miliardi di messaggi
* 2 milioni di ore inoltro

Anche se Microsoft si riserva il diritto di disabilitare l'account di un cliente che abbia superato le quote di utilizzo previste in un determinato mese, invierà una notifica tramite posta elettronica ed effettuerà diversi tentativi di contattare il cliente prima di intraprendere qualsiasi azione. I clienti che superano tali quote saranno comunque responsabili per gli addebiti delle eccedenze.

### <a name="naming-restrictions"></a>Limitazioni relative all'assegnazione dei nomi
La lunghezza del nome di uno spazio dei nomi di inoltro deve essere compresa tra 6 e 50 caratteri.

## <a name="subscription-and-namespace-management"></a>Gestione di sottoscrizioni e spazi dei nomi
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Come si esegue la migrazione di uno spazio dei nomi a un'altra sottoscrizione di Azure?
È possibile usare i comandi di PowerShell descritti in [questo articolo](../service-bus-messaging/service-bus-powershell-how-to-provision.md#migrate-a-namespace-to-another-azure-subscription) per spostare uno spazio dei nomi da una sottoscrizione di Azure a un'altra. Per eseguire l'operazione, lo spazio dei nomi deve essere già attivo. L'utente che esegue i comandi deve inoltre essere un amministratore per le sottoscrizioni di origine e di destinazione.

## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-their-suggested-actions"></a>Quali sono alcune delle eccezioni generate dalle API del servizio di inoltro Azure e le azioni consigliate?
L'articolo [Eccezioni di inoltro][Eccezioni di inoltro] descrive alcune eccezioni con le azioni consigliate.

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Cos'è una firma di accesso condiviso e quali linguaggi supportano la generazione di una firma?
Le firme di accesso condiviso sono un meccanismo di autenticazione basato su hash sicuri SHA-256 o URI. Per informazioni su come generare le proprie firme in Node, PHP, Java e C\#, vedere l'articolo [Firme di accesso condiviso][Firme di accesso condiviso] (Firme di accesso condiviso).

[Panoramica sui prezzi]: https://azure.microsoft.com/pricing/details/service-bus/
[Eccezioni di inoltro]: relay-exceptions.md
[Firme di accesso condiviso]: ../service-bus-messaging/service-bus-sas-overview.md

## <a name="next-steps"></a>Passaggi successivi
* [Creare uno spazio dei nomi](relay-create-namespace-portal.md)
* [Introduzione a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introduzione a Node](relay-hybrid-connections-node-get-started.md)



<!--HONumber=Nov16_HO3-->


