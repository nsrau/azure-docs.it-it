---
title: Domande frequenti sul servizio di inoltro di Azure | Microsoft Docs
description: Leggere le risposte ad alcune domande frequenti sul servizio di inoltro di Azure.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2018
ms.author: sethm
ms.openlocfilehash: 07cbdd24368d66104ecdeb263983e3aaf3f219fe
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="azure-relay-faqs"></a>Domande frequenti sul servizio di inoltro di Azure

Questo articolo contiene le risposte ad alcune domande frequenti sul [servizio di inoltro di Microsoft Azure](https://azure.microsoft.com/services/service-bus/). Per informazioni generali sui prezzi e sul supporto di Azure, vedere [Domande frequenti sul supporto di Azure](http://go.microsoft.com/fwlink/?LinkID=185083).

## <a name="general-questions"></a>Domande generali
### <a name="what-is-azure-relay"></a>Che cos'è il servizio di inoltro di Azure?
Il [servizio di inoltro di Azure](relay-what-is-it.md) facilita le applicazioni ibride grazie alla possibilità di esporre in modo sicuro i servizi che risiedono all'interno di una rete aziendale nel cloud pubblico. È possibile esporre i servizi senza dover aprire una connessione firewall e senza richiedere modifiche di notevole impatto a un'infrastruttura di rete aziendale.

### <a name="what-is-a-relay-namespace"></a>Che cos'è uno spazio dei nomi di inoltro?
Uno [spazio dei nomi](relay-create-namespace-portal.md) è un contenitore di ambiti che è possibile usare per gestire le risorse di inoltro nell'applicazione. È necessario creare uno spazio dei nomi per usare l'inoltro. Questo è uno dei primi passaggi nella guida introduttiva.

### <a name="what-happened-to-service-bus-relay-service"></a>Quali novità interessano il servizio di inoltro del bus di servizio?
Il servizio in precedenza chiamato Inoltro del bus di servizio ora si chiama [Inoltro WCF](relay-wcf-dotnet-get-started.md). È possibile continuare a usarlo come un tempo. La funzionalità Connessioni ibride è una versione aggiornata di un servizio trasferito da Servizi BizTalk di Azure. Verrà fornito ancora il supporto per l'inoltro WCF e per Connessioni ibride.

## <a name="pricing"></a>Prezzi
Questa sezione contiene le risposte ad alcune domande frequenti sulla struttura dei prezzi del servizio di inoltro. Per informazioni generali sui prezzi di Azure, vedere [Domande frequenti sul supporto di Azure](http://go.microsoft.com/fwlink/?LinkID=185083). Per informazioni complete sui prezzi del servizio di inoltro, vedere la pagina contenente i [dettagli dei prezzi del bus di servizio][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Come vengono addebitati i costi di Connessioni ibride e Inoltro WCF?
Per informazioni complete sui prezzi di inoltro, vedere la tabella relativa alle [connessioni ibride e agli inoltri WCF][Pricing overview] nella pagina dei dettagli dei prezzi del bus di servizio. Oltre ai prezzi indicati in questa pagina, vengono addebitati i trasferimenti di dati associati in uscita dal data center in cui è stato effettuato il provisioning dell'applicazione.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Come viene fatturato l'uso di Connessioni ibride?
Ecco tre scenari di fatturazione di esempio per Connessioni ibride:

*   Scenario 1:
    *   Si dispone di un singolo listener, ad esempio un'istanza di gestione di Connessioni ibride installato e in esecuzione continua per l'intero mese.
    *   Si inviano 3 GB di dati attraverso la connessione durante il mese. 
    *   Il costo totale è $ 5.
*   Scenario 2:
    *   Si dispone di un singolo listener, ad esempio un'istanza di gestione di Connessioni ibride installato e in esecuzione continua per l'intero mese.
    *   Si inviano 10 GB di dati attraverso la connessione durante il mese.
    *   Il costo totale è $ 7,50, che corrisponde a $ 5 per la connessione e i primi 5 GB + $ 2,50 per i 5 GB di dati aggiuntivi.
*   Scenario 3:
    *   Sono disponibili due istanze, A e B, di un'istanza di gestione di Connessioni ibride installato e in esecuzione continua per l'intero mese.
    *   Si inviano 3 GB di dati attraverso la connessione A durante il mese.
    *   Si inviano 6 GB di dati attraverso la connessione B durante il mese.
    *   Il costo totale è $ 10,50, che corrisponde a $5 per la connessione A + $ 5 per la connessione B + $ 0,50 per il sesto gigabyte sulla connessione B.

Si noti che i prezzi usati negli esempi sono applicabili solo durante il periodo di anteprima di Connessioni ibride. I prezzi sono soggetti a modifiche al momento della disponibilità generale di Connessioni ibride.

### <a name="how-are-hours-calculated-for-relay"></a>Come vengono calcolate le ore per l'inoltro?

L'inoltro WCF è disponibile solo negli spazi dei nomi di livello Standard. Prezzi e [quote di connessione](../service-bus-messaging/service-bus-quotas.md) per i servizi di inoltro restano invariati in caso contrario. Ciò significa che gli inoltri continueranno a essere addebitati in base al numero di messaggi (non alle operazioni) e alle ore per l'inoltro. Per altre informazioni, vedere la tabella relativa alle [connessioni ibride e agli inoltri WCF](https://azure.microsoft.com/pricing/details/service-bus/) nella pagina dei dettagli dei prezzi.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Che cosa succede se più listener sono connessi a uno specifico inoltro?
In alcuni casi, a un singolo inoltro possono essere connessi più listener. Un inoltro viene considerato "aperto" quando vi è connesso almeno un listener di inoltro. L'aggiunta di listener a un inoltro aperto genera ore per l'inoltro aggiuntive. Il numero di mittenti di inoltro, ossia di client che richiamano o inviano messaggi agli inoltri, connessi a un inoltro non influisce sul calcolo delle ore per l'inoltro.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Come viene calcolata la misurazione dei messaggi per gli inoltri WCF?
**Questo vale solo per gli inoltri WCF. I messaggi non implicano un costo per Connessioni ibride.**

In generale, i messaggi fatturabili per gli inoltri vengono calcolati con lo stesso metodo usato per le entità con broker, come code, argomenti e sottoscrizioni, descritti in precedenza. Esistono tuttavia alcune differenze significative.

L'invio di un messaggio a un inoltro del bus di servizio viene considerato come invio "diretto" al listener di inoltro che riceve il messaggio. Non viene gestito come operazione di invio all'inoltro del bus di servizio, seguito da un recapito al listener di inoltro. Una chiamata al servizio di tipo richiesta-risposta di dimensioni fino a 64 KB per un listener di inoltro genera due messaggi fatturabili, uno per la richiesta e uno per la risposta, presupponendo che anche la risposta sia pari a 64 KB o a un valore inferiore. Questo comportamento è diverso dall'uso di una coda per la mediazione tra un client e un servizio. Se si usa una coda per la mediazione tra un client e un servizio, lo stesso modello di richiesta-risposta richiede l'invio di una richiesta alla coda, seguita da un'operazione di rimozione dalla coda/recapito dalla coda al servizio. Questa operazione è seguita dall'invio di una risposta a un'altra coda e da un'operazione di rimozione dalla coda o recapito da tale coda al client. Presupponendo sempre le stesse dimensioni (fino a 64 KB), il modello di accodamento mediato comporterà 4 messaggi fatturabili. Verrà addebitato per due volte il numero di messaggi per implementare lo stesso modello completato usando l'inoltro. L'uso delle code per applicare questo modello presenta ovviamente alcuni vantaggi, tra cui durabilità e livellamento del carico. Questi vantaggi possono giustificare costi aggiuntivi.

Gli inoltri aperti con il binding WCF **netTCPRelay** considerano i messaggi non come singoli messaggi, ma come flusso di dati che attraversa il sistema. Quando si usa questo binding, solo il mittente e il listener hanno visibilità sulla struttura dei singoli messaggi inviati/ricevuti. Per gli inoltri che usano il binding **netTCPRelay**, tutti i dati vengono considerati come flusso per il calcolo dei messaggi fatturabili. In questo caso, il bus di servizio calcola la quantità totale di dati inviati o ricevuti mediante ciascun inoltro ogni 5 minuti. Quindi divide la quantità totale di dati per 64 KB per determinare il numero di messaggi fatturabili per questo inoltro durante questo periodo di tempo.

## <a name="quotas"></a>Quote
| Nome della quota | Scope |  Note | Valore |
| --- | --- | --- | --- |
| Listener simultanei per un inoltro |Entità |Le richieste successive di connessioni aggiuntive vengono rifiutate e il codice chiamante riceverà un'eccezione. |25 |
| Connessioni di inoltro simultanee per tutti gli endpoint di inoltro in uno spazio dei nomi del servizio |Spazio dei nomi |- |5.000 |
| Endpoint di inoltro per ogni spazio dei nomi del servizio |Spazio dei nomi |- |10.000 |
| Dimensione dei messaggi per gli inoltri [NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) e [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx) |Spazio dei nomi |I messaggi in ingresso che superano queste quote vengono rifiutati e il codice chiamante riceve un'eccezione. |64 KB |
| Dimensione dei messaggi per gli inoltri [HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) e [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) |Spazio dei nomi |Nessun limite alla dimensione dei messaggi. |Illimitato |

### <a name="does-relay-have-any-usage-quotas"></a>Per il servizio di inoltro sono previste quote di utilizzo?
Per impostazione predefinita per qualsiasi servizio cloud, Microsoft imposta una quota di utilizzo mensile aggregata che viene calcolata su tutte le sottoscrizioni di un cliente. In alcuni casi le esigenze del cliente possono superare questi limiti. È possibile contattare il servizio clienti in qualsiasi momento per comunicare esigenze specifiche e consentire un adeguamento appropriato di tali limiti. Per il bus di servizio, le quote di utilizzo aggregate sono le seguenti:

* 5 miliardi di messaggi
* 2 milioni di ore di inoltro

Anche se si riserva il diritto di disabilitare un account che superi le quote di utilizzo previste in un mese, Microsoft invierà una notifica tramite posta elettronica ed effettuerà diversi tentativi di contattare il cliente prima di intraprendere qualsiasi azione. I clienti che superano tali quote saranno comunque responsabili degli addebiti delle eccedenze.

### <a name="naming-restrictions"></a>Limitazioni relative all'assegnazione dei nomi
Lo spazio dei nomi dell'inoltro deve avere una lunghezza compresa tra 6 e 50 caratteri.

## <a name="subscription-and-namespace-management"></a>Gestione di sottoscrizioni e spazi dei nomi
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Come si esegue la migrazione di uno spazio dei nomi a un'altra sottoscrizione di Azure?

Per spostare uno spazio dei nomi da una sottoscrizione di Azure a un'altra sottoscrizione, è possibile usare il [portale di Azure](https://portal.azure.com) o i comandi di PowerShell. Per spostare uno spazio dei nomi in un'altra sottoscrizione, lo spazio dei nomi deve essere già attivo. L'utente che esegue i comandi deve inoltre essere un amministratore per le sottoscrizioni di origine e di destinazione.

#### <a name="azure-portal"></a>Portale di Azure

Per usare il portale di Azure per migrare gli spazi dei nomi di inoltro di Azure da una sottoscrizione a un'altra sottoscrizione, vedere l'articolo relativo allo [spostamento di risorse in un nuovo gruppo di risorse o in una nuova sottoscrizione](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

Per usare PowerShell per spostare uno spazio dei nomi da una sottoscrizione di Azure a un'altra sottoscrizione, usare la sequenza di comandi seguente. Per eseguire questa operazione, lo spazio dei nomi deve essere già attivo e l'utente che esegue i comandi di PowerShell deve essere un utente amministratore nelle sottoscrizioni di origine e di destinazione.

```powershell
# Create a new resource group in the target subscription.
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>risoluzione dei problemi
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Quali sono alcune eccezioni generate dalle API del servizio di inoltro di Azure e le azioni consigliate?
Per una descrizione di eccezioni comuni e delle azioni consigliate, vedere [Eccezioni di inoltro][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Cos'è una firma di accesso condiviso e quali linguaggi è possibile usare per generare una firma?
Le firme di accesso condiviso sono un meccanismo di autenticazione basato su hash sicuri SHA-256 o URI. Per informazioni su come generare le firme in Node, PHP, Java, C e C#, vedere [Service Bus authentication with shared access signatures][Shared Access Signatures] (Autenticazione del bus di servizio con le firme di accesso condiviso).

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>È possibile aggiungere all'elenco elementi consentiti gli endpoint di inoltro?
Sì. Il client di inoltro stabilisce connessioni al servizio di inoltro di Azure usando nomi di dominio completi. I clienti possono quindi aggiungere una voce per `*.servicebus.windows.net` nei firewall che supportano l'aggiunta all'elenco elementi consentiti per DNS.

## <a name="next-steps"></a>Passaggi successivi
* [Creare uno spazio dei nomi](relay-create-namespace-portal.md)
* [Introduzione a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introduzione a Node](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared access signatures]: ../service-bus-messaging/service-bus-sas.md