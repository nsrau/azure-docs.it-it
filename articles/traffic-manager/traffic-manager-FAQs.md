---
title: Gestione traffico di Azure - Domande frequenti
description: Questo articolo risponde ad alcune domande frequenti su Gestione traffico
services: traffic-manager
documentationcenter: ''
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.openlocfilehash: c2bff5749ab833efcb252d3fafb5d38cfbc8691e
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310272"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Domande frequenti (FAQ) su Gestione traffico

## <a name="traffic-manager-basics"></a>Fondamenti di Gestione traffico

### <a name="what-ip-address-does-traffic-manager-use"></a>Quale indirizzo IP viene usato da Gestione traffico?

Come spiegato nella sezione [Modalità di funzionamento di Gestione traffico](../traffic-manager/traffic-manager-how-it-works.md), Gestione traffico funziona a livello di DNS. Invia le risposte DNS per indirizzare i client all'endpoint di servizio appropriato. I client si connettono quindi all'endpoint di servizio in modo diretto, senza passare per Gestione traffico.

Gestione traffico non prevede quindi un endpoint o indirizzo IP per la connessione dei client. Se si intende avere un indirizzo IP statico per un servizio, è necessario configurarlo nel servizio, non in Gestione traffico.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Quali tipi di traffico è possibile indirizzare tramite Gestione traffico?
Come spiegato in [Funzionamento di Gestione traffico](../traffic-manager/traffic-manager-how-it-works.md), un endpoint di Gestione traffico può essere qualsiasi servizio con connessione Internet all'interno o all'esterno di Azure. Di conseguenza, Gestione traffico può indirizzare il traffico che ha origine da Internet pubblico a un set di endpoint anch'esso con connessione Internet. In caso di endpoint all'interno di una rete privata, ad esempio una versione interna di [Azure Load Balancer](../load-balancer/load-balancer-overview.md#internalloadbalancer), o se gli utenti effettuano richieste DNS da queste reti interne, non è possibile usare Gestione traffico per instradare il traffico.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Gestione traffico supporta le sessioni "permanenti"?

Come spiegato nella sezione [Modalità di funzionamento di Gestione traffico](../traffic-manager/traffic-manager-how-it-works.md), Gestione traffico funziona a livello di DNS. Usa le risposte DNS per indirizzare i client all'endpoint di servizio appropriato. I client si connettono all'endpoint di servizio in modo diretto, senza passare per Gestione traffico. Gestione traffico non visualizza quindi il traffico HTTP tra il client e il server.

Inoltre l'indirizzo IP di origine della query DNS ricevuta da Gestione traffico appartiene al servizio DNS ricorsivo e non al client. Il servizio Gestione traffico non può quindi tracciare client singoli e non può implementare sessioni "permanenti". Questa limitazione è comune a tutti i sistemi di gestione del traffico basati su DNS e non è specifica di Gestione traffico.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Quando si usa Gestione traffico, viene visualizzato un errore HTTP. Perché?

Come spiegato nella sezione [Modalità di funzionamento di Gestione traffico](../traffic-manager/traffic-manager-how-it-works.md), Gestione traffico funziona a livello di DNS. Usa le risposte DNS per indirizzare i client all'endpoint di servizio appropriato. I client si connettono quindi all'endpoint di servizio in modo diretto, senza passare per Gestione traffico. Gestione traffico non vede il traffico HTTP tra client e server. Ogni errore HTTP visualizzato proviene quindi dall'applicazione. Per la connessione del client all'applicazione, è necessario che siano stati completati tutti i passaggi di risoluzione DNS. È inclusa qualsiasi interazione di Gestione traffico con il flusso del traffico dell'applicazione.

Eventuali verifiche più approfondite dovranno quindi concentrarsi sull'applicazione.

L'intestazione host HTTP inviata dal browser del client è l'origine dei problemi più comune. Assicurarsi che l'applicazione sia configurata per accettare l'intestazione host corretta per il nome di dominio in uso. Per gli endpoint che usano il Servizio app di Azure, vedere [Configurazione di un nome di dominio personalizzato per un'app Web nel servizio app di Azure con Gestione traffico](../app-service/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Qual è l'impatto sulle prestazioni dell'uso di Gestione traffico?

Come spiegato nella sezione [Modalità di funzionamento di Gestione traffico](../traffic-manager/traffic-manager-how-it-works.md), Gestione traffico funziona a livello di DNS. Dal momento che i client si connettono direttamente agli endpoint di servizio, dopo che è stata stabilita la connessione non si verifica alcun impatto sulle prestazioni.

Poiché Gestione traffico si integra con le applicazioni a livello di DNS, richiede l'inserimento di una ricerca DNS aggiuntiva nella catena di risoluzione DN. L'impatto di Gestione traffico sul tempo di risoluzione DNS è minimo. Gestione traffico usa una rete globale di server dei nomi e reti [Anycast](https://en.wikipedia.org/wiki/Anycast) per garantire che le query DNS vengano sempre indirizzate al server dei nomi più vicino disponibile. Inoltre, la memorizzazione nella cache delle risposte DNS significa che la latenza DNS aggiuntiva associata all'uso di Gestione traffico si applica solo a una frazione di sessioni.

Il metodo Prestazioni instrada il traffico all'endpoint disponibile più vicino. Il risultato della rete è che l'impatto sulle prestazioni complessive associate a questo metodo dovrebbe essere minimo. Un aumento della latenza DNS deve essere compensato da minore latenza di rete all'endpoint.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Quali protocolli di applicazione possono essere usati con Gestione traffico?

Come spiegato nella sezione [Modalità di funzionamento di Gestione traffico](../traffic-manager/traffic-manager-how-it-works.md), Gestione traffico funziona a livello di DNS. Dopo il completamento della ricerca DNS, i client si connettono all'endpoint dell'applicazione direttamente, non tramite Gestione traffico. La connessione può pertanto usare qualsiasi protocollo dell'applicazione. Se come protocollo di monitoraggio si seleziona TCP, i controlli dell'integrità dell'endpoint eseguiti da Gestione traffico non richiedono l'uso dei protocolli dell'applicazione. Se si sceglie di verificare l'integrità usando un protocollo dell'applicazione, l'endpoint deve poter rispondere alle richieste HTTP o HTTPS GET.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>È possibile usare gestione traffico con un nome di dominio "Naked"?

Sì. Per informazioni su come creare un record alias per il nome di dominio Apex per fare riferimento a un profilo di gestione traffico di Azure, vedere [configurare un record alias per supportare i nomi di dominio Apex con gestione traffico](../dns/tutorial-alias-tm.md).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Gestione traffico tiene conto dell'indirizzo della subnet client quando si gestiscono query DNS? 

Sì, oltre all'indirizzo IP di origine della query DNS che riceve (che è in genere l'indirizzo IP del sistema di risoluzione DNS), quando si eseguono ricerche per i metodi di routing Geografico, Prestazioni e Subnet, Gestione traffico considera anche l'indirizzo di subnet del client se viene incluso nella query dal resolver che effettua la richiesta per conto dell'utente finale.  
Per altri dettagli, vedere la [RFC 7871 – Client Subnet in DNS Queries](https://tools.ietf.org/html/rfc7871) (RFC 7871 - Subnet client nelle query DNS), che indica un [meccanismo di estensione per DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) che può trasferire l'indirizzo della subnet client dai resolver che lo supportano.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Cos'è la durata TTL del DNS e che impatto ha sugli utenti?

Quando una query DNS viene ricevuta da Gestione traffico, nella risposta viene impostato un valore chiamato Durata (TTL). Questo valore, espresso in secondi, indica ai resolver DNS a valle il tempo di memorizzazione della risposta nella cache. Sebbene ciò non garantisca che i resolver memorizzino il risultato nella cache, la memorizzazione consente loro di rispondere a ogni successiva query usando la cache, invece di inoltrare la query ai server DNS di Gestione traffico. Di seguito è illustrato l'impatto sulle risposte:

- Una durata TTL maggiore riduce il numero di query che raggiunge i server DNS di Gestione traffico; ciò contribuisce a ridurre i costi per il cliente, poiché il numero di query gestite incide sulla fatturazione.
- Una durata TTL maggiore può potenzialmente ridurre il tempo necessario per eseguire una ricerca DNS.
- Una durata TTL maggiore implica anche che i dati possono non riflettere le informazioni di integrità più recenti ottenute da Gestione traffico tramite gli agenti che eseguono il sondaggio.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Come impostare una durata TTL maggiore o minore per le risposte di Gestione traffico?

A livello di singolo profilo, è possibile impostare una durata TTL del DNS minima di 0 secondi e massima di 2.147.483.647 secondi, ovvero l'intervallo massimo conforme a [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt ). Una durata TTL pari a 0 indica che i resolver DNS a valle non memorizzano nella cache le risposte alle query; tutte le query vengono ricevute dai server DNS di Gestione traffico per essere risolte.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Come si può verificare il volume delle query destinate al profilo personale? 

Una delle metriche fornite da Gestione traffico è costituita dal numero di query cui un profilo risponde. È possibile ottenere queste informazioni tramite un'aggregazione a livello di profilo oppure è possibile suddividerle ulteriormente per visualizzare il volume di query in cui sono stati restituiti endpoint specifici. È anche possibile configurare avvisi per l'invio di notifiche se il volume di risposte delle query soddisfa le condizioni impostate. Per altri dettagli, vedere [Traffic Manager metrics and alerts](traffic-manager-metrics-alerts.md) (Metriche e avvisi di Gestione traffico).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Metodi geografico di routing del traffico di Gestione traffico

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Quali sono alcuni casi di uso in cui il routing geografico è utile?

Il tipo di routing Geografico può essere usato in qualsiasi scenario in cui un cliente di Azure abbia bisogno di distinguere gli utenti in base alle aree geografiche. Ad esempio, tramite il metodo di routing del traffico Geografico è possibile offrire agli utenti di una determinata area un'esperienza utente diversa rispetto a quelli di altre aree. Un altro esempio è la necessità di garantire la conformità con requisiti dei dati locali che richiedono di servire gli utenti di una determinata area solo con gli endpoint di tale area.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Come si decide se è consigliabile usare il metodo di routing relativo alle prestazioni o geografico?

La differenza essenziale tra questi due metodi di routing molto diffusi consiste nel fatto che nel metodo di routing relativo alle prestazioni l'obiettivo principale consiste nell'inviare traffico all'endpoint che può fornire la latenza più bassa al chiamante, mentre nel routing geografico l'obiettivo principale consiste nell'imporre un recinto geografico per i chiamanti, in modo che sia possibile indirizzarli deliberatamente a un endpoint specifico. La sovrapposizione si verifica perché è presente una correlazione tra vicinanza geografica e latenza più bassa, benché ciò non sia sempre vero. È possibile che sia presente un endpoint in un'area geografica diversa che può offrire un'esperienza di latenza migliore per il chiamante e in tale caso il routing relativo alle prestazioni invierà l'utente a tale endpoint, ma il routing geografico lo invierà sempre all'endpoint mappato per la rispettiva area geografica. Per chiarire meglio, si prenda in considerazione l'esempio seguente. Con il routing geografico è possibile creare mapping non comuni, ad esempio inviando tutto il traffico proveniente dall'Asia a endpoint nell'area Stati Uniti e tutto il traffico proveniente dagli Stati Uniti a endpoint in Asia. In tale caso, il routing geografico eseguirà deliberatamente esattamente le operazioni configurate e l'ottimizzazione delle prestazioni non viene presa in considerazione. 
>[!NOTE]
>È possibile che in alcuni scenari siano necessarie sia prestazioni ottimali che funzionalità di routing geografico. Per questi scenari è consigliabile usare i profili annidati. È ad esempio possibile configurare un profilo padre con il routing geografico, in cui tutto il traffico proveniente dall'America del Nord viene inviato a un profilo annidato con endpoint negli Stati Uniti e viene usato il routing relativo alle prestazioni per inviare tale traffico all'endpoint migliore entro tale set. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Quali sono le aree supportate da Gestione traffico per il routing geografico?

La gerarchia di paese/area geografica utilizzata da Gestione traffico è reperibile [qui](traffic-manager-geographic-regions.md). La pagina viene aggiornata con ogni modifica apportata, ma è possibile recuperare le stesse informazioni anche a livello programmatico usando l'[API REST di Gestione traffico di Azure](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>In che modo Gestione traffico determina da dove un utente sta eseguendo una query?

Gestione traffico esamina l'indirizzo IP di origine della query (probabilmente un sistema di risoluzione DNS locale che esegue la query per conto dell'utente) e usa un indirizzo IP interno per eseguire il mapping delle aree al fine di determinare la posizione. Questa mappa viene aggiornata regolarmente per tenere conto delle modifiche di Internet. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>È garantito che in ogni caso Gestione traffico determini correttamente l'esatta posizione geografica dell'utente?

No, Gestione traffico non può garantire che l'area geografica che si deduce dall'indirizzo IP di origine di una query DNS corrisponda sempre alla posizione dell'utente, per i motivi seguenti:

- In primo luogo, come indicato nella domanda precedente, l'indirizzo IP di origine visualizzato è quello di un resolver DNS che esegue la ricerca per conto dell'utente. La posizione geografica del resolver DNS è un proxy valido per la posizione geografica dell'utente ma può anche essere diversa, a seconda della superficie del servizio resolver DNS e dello specifico servizio resolver DNS che un cliente sceglie di usare. Ad esempio, un cliente che si trova in Malaysia può specificare nelle impostazioni del dispositivo l'uso di un servizio resolver DNS il cui server DNS a Singapore potrebbe essere scelto per gestire le risoluzioni di query per l'utente o il dispositivo specifico. In questo caso, Gestione traffico può visualizzare solo l'indirizzo IP del resolver corrispondente alla località Singapore. Vedere anche le domande frequenti precedenti, disponibili in questa pagina, relative al supporto dell'indirizzo della subnet client.

- In secondo luogo, Gestione traffico usa una mappa interna per tradurre l'indirizzo IP nell'area geografica. Anche se questa mappa viene convalidata e aggiornata in modo continuativo per aumentarne la precisione e tenere conto della natura in costante evoluzione di Internet, è comunque possibile che le informazioni contenute non rappresentino esattamente la posizione geografica di tutti gli indirizzi IP.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Un endpoint deve trovarsi fisicamente nella stessa area di quello con cui è configurato per il routing geografico?

No, il percorso dell'endpoint non impone alcuna restrizione in merito alle aree a cui può essere mappato. Ad esempio è possibile che tutti gli utenti dell'India siano indirizzati a un endpoint dell'area Stati Uniti centrali di Azure.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>È possibile assegnare aree geografiche agli endpoint in un profilo che non è configurato per eseguire il routing geografico?

Sì, se il metodo di routing di un profilo non è Geografico è possibile usare l'[API REST di Gestione traffico di Azure](https://docs.microsoft.com/rest/api/trafficmanager/) per assegnare aree geografiche agli endpoint del profilo. Nel caso di profili con un tipo di routing non geografico, questa configurazione viene ignorata. Se si cambia un profilo di questo tipo nel tipo a routing geografico in un secondo momento, Gestione traffico userà quei mapping.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Perché si riceve un errore quando si tenta di cambiare il metodo di routing di un profilo esistente in geografico?

Deve esserci almeno un'area mappata per tutti gli endpoint in un profilo con routing geografico. Per convertire un profilo esistente al tipo di routing geografico è innanzitutto necessario associare aree geografiche a tutti gli endpoint tramite l'[API REST di Gestione traffico di Azure](https://docs.microsoft.com/rest/api/trafficmanager/) prima di cambiare il tipo di routing in geografico. Se si usa il portale, eliminare innanzitutto gli endpoint, cambiare il metodo di routing del profilo in geografico e quindi aggiungere gli endpoint con i relativi mapping di area geografica.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Perché è decisamente consigliato che i clienti creino profili nidificati anziché endpoint in un profilo con il routing geografico abilitato?

Un'area può essere assegnata a un solo endpoint all'interno di un profilo se usa il tipo di routing geografico. Se tale endpoint non è un tipo annidato con un profilo figlio collegato, nel caso l'endpoint perdesse l'integrità, Gestione traffico continuerà a inviare traffico a esso in quanto l'alternativa di non inviare alcun traffico non è migliore. Gestione traffico non esegue il failover su un altro endpoint, anche quando l'area assegnata è "padre" dell'area assegnata all'endpoint danneggiato. Se, ad esempio, un endpoint che include l'area Spagna viene danneggiato, non si eseguirà il failover su un altro endpoint a cui è assegnata l'area Europa. Lo scopo di tutto questo è garantire che Gestione traffico rispetti i confini geografici che un cliente ha stabilito nel proprio profilo. Per ottenere il vantaggio del failover su un altro endpoint quando un endpoint perde la sua integrità, è consigliabile assegnare le aree geografiche a profili annidati con più endpoint all'interno invece di singoli endpoint. In questo modo se un endpoint nel profilo figlio annidato non funziona, il traffico può eseguire il failover su un altro endpoint all'interno dello stesso profilo figlio annidato.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Ci sono restrizioni sulla versione API che supporta questo tipo di routing?

Sì, solo l'API versione 2017-03-01 e versioni successive supportano il routing di tipo geografico. Le versioni precedenti dell'API non possono essere usate per creare profili con routing di tipo Geografico o assegnare aree geografiche agli endpoint. Se viene usata una versione precedente dell'API per recuperare i profili da una sottoscrizione di Azure, non vengono restituiti profili con routing di tipo Geografico. Inoltre, quando si usano versioni precedenti dell'API, tutti i profili restituiti che hanno endpoint con un'area geografica assegnata non mostreranno l'area geografica assegnata.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Metodo di routing del traffico Subnet di Gestione traffico

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Quali sono alcuni casi di uso in cui il routing della subnet è utile?

Il routing della subnet consente di differenziare l'esperienza per gruppi specifici di utenti identificati dall'IP di origine del relativo indirizzo IP delle richieste DNS. Un esempio è la visualizzazione di un contenuto diverso se gli utenti si connettono a un sito Web dalla sede centrale dell'azienda. Un altro esempio può essere limitare gli utenti di alcuni ISP in modo che accedano solo agli endpoint che supportano solo le connessioni IPv4 se la qualità delle prestazioni di tali ISP si riduce quando si usa IPv6.
Un altro motivo per usare il metodo di routing Subnet è la combinazione con altri profili in un set di profili annidati. Ad esempio, se si vuole usare il metodo di routing Geografico per creare un recinto virtuale per gli utenti, ma per un ISP specifico si vuole usare un metodo di routing diverso, è possibile avere un profilo con metodo di routing Subnet come profilo padre ed eseguire l'override di tale ISP in modo da usare un profilo figlio specifico e usare il profilo Geografico standard per tutti gli altri.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>In che modo Gestione traffico riconosce l'indirizzo IP dell'utente finale?

I dispositivi degli utenti finali usano in genere un sistema di risoluzione DNS per eseguire la ricerca DNS per loro conto. L'indirizzo IP in uscita di questi resolver è quello visualizzato da Gestione traffico come indirizzo IP di origine. Inoltre, il metodo di routing Subnet verifica anche se esistono informazioni ECS (Extended Client Subnet) EDNS0 passate con la richiesta. Se le informazioni ECS sono presenti, quello è l'indirizzo usato per determinare il routing. In assenza di informazioni ECS, l'indirizzo IP di origine della query viene usato a scopo di routing.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Come si possono specificare gli indirizzi IP quando si usa il routing Subnet?

Gli indirizzi IP da associare a un endpoint possono essere specificati in due modi. Prima di tutto, è possibile usare la notazione decimale puntata in ottetti con indirizzi di inizio e fine per specificare l'intervallo (ad esempio, 1.2.3.4-5.6.7.8 o 3.4.5.6-3.4.5.6). In secondo luogo, è possibile usare la notazione CIDR per specificare l'intervallo (ad esempio, 1.2.3.0/24). È possibile specificare più intervalli e usare entrambi i tipi di notazione in un intervallo impostato. Si applicano alcune restrizioni.

-   Poiché ogni indirizzo IP deve essere mappato solo a un singolo endpoint non è possibile sovrapporre gli intervalli di indirizzi
-   L'indirizzo iniziale non può superare l'indirizzo finale
-   Nel caso della notazione CIDR, l'indirizzo IP prima di "/" deve essere l'indirizzo iniziale dell'intervallo. Ad esempio, 1.2.3.0/24 è valido, ma 1.2.3.4.4/24 NON lo è

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Come si può specificare un endpoint di fallback quando si usa il routing Subnet?

In un profilo con il routing di tipo Subnet, se si usa un endpoint senza subnet mappate, qualsiasi richiesta che non corrisponde ad altri endpoint verrà indirizzata qui. Si consiglia vivamente di avere tale endpoint di fallback nel proprio profilo poiché Gestione traffico restituirà una risposta NXDOMAIN se arriva una richiesta e non viene mappata ad alcun endpoint o se viene mappata a un endpoint ma tale endpoint non è integro.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Cosa accade se un endpoint è disabilitato in un profilo con routing Subnet?

In un profilo con routing Subnet, se un endpoint è presente ma disabilitato, Gestione traffico si comporta come se tale endpoint e i relativi mapping della subnet non esistessero. Se si riceve una query corrispondente al mapping dell'indirizzo IP e l'endpoint è disabilitato, Gestione traffico restituisce un endpoint di fallback (uno con nessun mapping). In alternativa, se questo endpoint non è presente, viene restituita una risposta NXDOMAIN.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Metodo di routing del traffico Multivalore di Gestione traffico

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Quali sono alcuni casi di uso in cui il routing multivalore è utile?

Il routing multivalore restituisce più endpoint integri in risposta a una singola query. Il vantaggio principale di questo approccio è che, se un endpoint non è integro, il client ha più possibilità di ripetere il tentativo senza effettuare un'altra chiamata DNS, che potrebbe restituire lo stesso valore da una cache upstream. Questo vale per le applicazioni sensibili in termini di disponibilità in cui è opportuno ridurre al minimo il tempo di inattività.
Un altro uso del metodo di routing multivalore è se un endpoint è "dual-homed" in entrambi gli indirizzi IPv4 e IPv6 e si vuole assegnare al chiamante le due opzioni tra cui scegliere quando avvia una connessione all'endpoint.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Quanti endpoint vengono restituiti quando si usa il routing multivalore?

È possibile specificare il numero massimo di endpoint da restituire perché il routing multivalore restituisca un numero di endpoint non superiore a quelli integri quando viene ricevuta una query. Il valore massimo possibile per questa configurazione è 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Si riceve lo stesso set di endpoint quando si usa il routing multivalore?

Non è possibile garantire che in ogni query venga restituito lo stesso set di endpoint. Questo dipende anche dal fatto che alcuni degli endpoint potrebbero non essere più integri e per questo non verranno inclusi nella risposta

## <a name="real-user-measurements"></a>Misurazioni utente reale

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Quali sono i vantaggi offerti dall'utilizzo di Misurazioni utente reale?

Quando si usa il metodo di routing basato sulle prestazioni, Gestione traffico seleziona l'area di Azure migliore a cui l'utente finale può connettersi, controllando l'IP di origine e la subnet client EDNS (se passata) e confrontandoli con l'intelligence di latenza di rete gestita dal servizio. La funzionalità Misurazioni utente reale migliora questo scenario facendo in modo che le esperienze della base di utenti finali contribuiscano a questa tabella di latenza e assicurando che la tabella includa adeguatamente le reti degli utenti finali da cui gli utenti finali si connettono ad Azure. In questo modo, si ottiene una maggiore precisione nel routing degli utenti finali.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>È possibile usare Misurazioni utente reale con aree non di Azure?

La funzionalità Misurazioni utente finale misura e riporta solo la latenza per raggiungere le aree di Azure. Se si usa il routing basato sulle prestazioni con endpoint ospitati in aree non di Azure, è comunque possibile beneficiare di questa funzionalità facendo in modo che le informazioni di latenza aumentata sull'area di Azure rappresentativa precedentemente selezionata vengano associate a questi endpoint.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Quale metodo di routing trae vantaggio dalla funzionalità Misurazioni utente reale?

Le informazioni aggiuntive ottenute tramite Misurazioni utente reale sono applicabili solo ai profili che usano il metodo di routing basato sulle prestazioni. Il collegamento delle misurazioni utente reale è disponibile da tutti i profili quando viene visualizzato tramite il portale di Azure.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>È necessario abilitare Misurazioni utente reale per ogni profilo separatamente?

No, basta abilitare la funzionalità una sola volta per ogni sottoscrizione e tutte le informazioni di latenza misurate e riportate diventano disponibili per tutti i profili.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Come si disattiva Misurazioni utente reale in una sottoscrizione?

È possibile interrompere gli addebiti dei costi relativi a Misurazioni utente reale quando si smette di raccogliere e restituire le misure di latenza dall'applicazione client. Quando il codice JavaScript di misurazione è integrato in pagine Web, ad esempio, è possibile arrestare la funzionalità rimuovendo il codice JavaScript o disattivando la chiamata durante il rendering della pagina.

È inoltre possibile disattivare Misurazioni utente reale eliminando la chiave. Dopo aver eliminato la chiave, tutte le misurazioni inviate a Gestione traffico con tale chiave vengono rimosse.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>È possibile usare Misurazioni utente reale con applicazioni client diverse dalle pagine Web?

Sì, Misurazioni utente reale è progettata per inserire i dati raccolti tramite tipi diversi di client utente finale. Queste domande frequenti verranno aggiornate man mano che verranno supportati nuovi tipi di applicazioni client.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Quante misurazioni vengono effettuate ogni volta che viene eseguito il rendering di una pagina Web con Misurazioni utente reale abilitata?

Quando Misurazioni utente reale viene usata con il codice JavaScript di misurazione specificato, ogni rendering di pagina restituisce sei misurazioni. Queste misurazioni vengono quindi riportate al servizio Gestione traffico. L'addebito per questa funzionalità avviene in base al numero di misurazioni indicate al servizio Gestione traffico. Se, ad esempio, l'utente esce dalla pagina Web durante le misurazioni ma prima che queste siano riportate al servizio,tali misurazioni non vengono considerate per la fatturazione.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>È presente un ritardo prima dell'esecuzione dello script di Misurazioni utente reale nella pagina Web?

No, non vi sono ritardi programmati prima che lo script venga richiamato.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>È possibile usare misurazioni utente reale solo con le aree di Azure che si intende misurare?

No, ogni volta che viene chiamato, lo script di Misurazioni utente reale misura un set di sei aree di Azure determinato dal servizio. Questo set cambia tra una chiamata e l'altra e, quando si verificano tante chiamate, la copertura delle misurazioni spazia tra aree di Azure diverse.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>È possibile limitare il numero di misurazioni effettuate a un numero specifico?

Il codice JavaScript di misurazione è integrato nella pagina Web, pertanto si ha il controllo completo su quando iniziare e arrestare l'uso del servizio. Il set di aree viene restituito fino a quando il servizio Gestione traffico riceve una richiesta di misurazione di un elenco di aree di Azure.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>È possibile visualizzare le misurazioni effettuate dall'applicazione client nell'ambito di Misurazioni utente reale?

Poiché la logica di misurazione viene eseguita dall'applicazione client, si ha il controllo completo di ciò che accade, inclusa la visualizzazione delle misurazioni di latenza. Gestione traffico non mostra una vista aggregata delle misurazioni ricevute nella chiave collegata alla sottoscrizione in uso.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>È possibile modificare lo script di misurazione di Gestione traffico?

È possibile controllare ciò che è integrato nella pagina Web, ma è fortemente sconsigliato modificare lo script di misurazione per garantire che misuri e riporti le latenze correttamente.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Gli altri utenti sono in grado di vedere la chiave di un utente che viene usata con Misurazioni utente reale?

Quando si incorpora lo script di misurazione in una pagina Web, gli altri utenti possono visualizzare lo script e la chiave di Misurazioni utente reale. È tuttavia importante sapere che questa chiave è diversa dall'ID sottoscrizione e viene generata da Gestione traffico per essere usata solo a questo scopo. Il fatto che altri utenti conoscano la chiave di Misurazioni utente reale di un utente non compromette la sicurezza dell'account di Azure che la usa.

### <a name="can-others-abuse-my-rum-key"></a>È possibile che altri utenti usino la chiave di Misurazioni utente reale di un utente senza autorizzazione?

Benché altri utenti possano usare la chiave dell'utente per inviare informazioni errate ad Azure, la presenza di alcune misurazioni errate non influisce sul routing, in quanto viene preso in considerazione insieme a tutte le altre misurazioni ricevute. Se è necessario modificare le chiavi, è possibile generare la chiave nuovamente e a quel punto la chiave precedente viene eliminata.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>È necessario inserire il codice JavaScript di misurazione in tutte le pagine Web?

Il servizio Misurazioni utente reale diventa più prezioso man mano che il numero delle misurazioni aumenta. Spetta comunque all'utente valutare se è necessario inserirlo in tutte le pagine Web o solo in alcune. Si consiglia di iniziare inserendolo nella pagina più visitata, dove si presume che gli utenti vi rimangano per almeno cinque secondi.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Gestione traffico è in grado di identificare le informazioni sugli utenti finali se si usa Misurazioni utente reale?

Quando si usa il codice JavaScript di misurazione, Gestione traffico può vedere l'indirizzo IP del client dell'utente finale e l'indirizzo IP di origine del resolver DNS locale usati. Gestione traffico usa l'indirizzo IP del client solo dopo averlo troncato per non consentire l'identificazione dell'utente finale specifico che ha inviato le misurazioni.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>La pagina Web che misura con Misurazioni utente reale deve usare Gestione traffico per il routing?

No, non deve necessariamente usare Gestione traffico. Il lato routing di Gestione traffico opera separatamente dal lato Misurazioni utente reale e, sebbene sia molto utile usarli entrambi nella stessa proprietà Web, non è obbligatorio.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>È necessario ospitare un qualsiasi servizio nelle aree di Azure da usare con Misurazioni utente reale?

No, non è necessario ospitare alcun componente lato server in Azure perché le misurazioni utente reale funzionino. L'immagine a pixel singolo scaricata dal codice JavaScript di misurazione e il servizio che lo esegue in aree di Azure diverse sono ospitati e gestiti da Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>L'utilizzo della larghezza di banda di Azure aumenta quando si usa Misurazioni utente reale?

Come indicato nella risposta precedente, i componenti lato server di Misurazioni utente reale sono di proprietà e gestiti da Azure. Questo significa che l'uso di Misurazioni utente reale non determina un aumento dell'utilizzo della larghezza di banda. Non è incluso alcun utilizzo della larghezza di banda al di fuori di quanto viene addebitato da Azure. La larghezza di banda usata per scaricare solo un'immagine a pixel singolo viene limitata alla misurazione della latenza a un'area di Azure. 

## <a name="traffic-view"></a>Visualizzazione traffico

### <a name="what-does-traffic-view-do"></a>A cosa serve Visualizzazione traffico?

Visualizzazione traffico è una funzionalità di Gestione traffico che consente di comprendere a fondo gli utenti e le relative esperienze. Usa le query che riceve da Gestione traffico e le tabelle di intelligence di latenza di rete che il servizio gestisce per offrire le informazioni seguenti:

- Le aree da cui gli utenti si connettono agli endpoint in Azure.
- Il volume di utenti che si connettono da tali aree.
- Le aree di Azure a cui gli utenti vengono instradati.
- L'esperienza di latenza per queste aree di Azure.

Queste informazioni sono disponibili all'utente tramite la sovrapposizione della mappa geografica e viste tabulari nel portale. Sono inoltre disponibili come dati non elaborati da scaricare.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Quali sono i vantaggi dell'utilizzo di Visualizzazione traffico?

Visualizzazione traffico offre una visione complessiva del traffico ricevuto dai profili di Gestione traffico. Può essere usata, in particolare, per comprendere da dove si connette la base utente e, non meno importante, qual è l'esperienza di latenza media. È quindi possibile usare queste informazioni per individuare le aree su cui è necessario concentrarsi, ad esempio, espandendo il footprint di Azure a un'area che può gestire gli utenti con una latenza più bassa. Un'altra informazione che è possibile derivare da Visualizzazione traffico riguarda i modelli di traffico verso aree diverse. Questa informazione può aiutare a prendere decisioni in merito all'aumento o alla diminuzione dell'inventario in quelle aree.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>In che modo Visualizzazione traffico differisce dalle metriche di Gestione traffico disponibili tramite Monitoraggio di Azure?

Monitoraggio di Azure aiuta a comprendere, a livello di aggregazione, il traffico ricevuto dal proprio profilo e dai relativi endpoint. Il servizio consente di tenere traccia dello stato di integrità degli endpoint tramite l'esposizione dei risultati del controllo di integrità. Quando occorre andare oltre questi dati e studiare l'esperienza dei propri utenti finali che si connettono ad Azure a livello di area, Visualizzazione traffico è lo strumento giusto.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Visualizzazione traffico usa le informazioni della subnet client EDNS?

Le query DNS gestite da Gestione traffico di Azure considerano le informazioni ECS per aumentare la precisione del routing. Ma durante la creazione del set di dati che mostra da dove gli utenti eseguono la connessione, Visualizzazione traffico usa solo l'indirizzo IP del resolver DNS.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Quanti giorni di dati usa Visualizzazione traffico?

Visualizzazione traffico genera il proprio output elaborando i dati dei sette giorni che precedono quello prima del giorno in cui l'utente visualizza l'output. Si tratta di una finestra mobile e a ogni visita vengono usati i dati più recenti.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>In che modo Visualizzazione traffico gestisce gli endpoint esterni?

Quando si usano endpoint esterni ospitati all'esterno di aree di Azure in un profilo di Gestione traffico, è possibile scegliere che venga eseguito il mapping a un'area di Azure che è un proxy per le relative caratteristiche di latenza (che è in effetti necessario se si usa il metodo di routing basato sulle prestazioni). Con questo mapping dell'area di Azure verranno usate le metriche di latenza di quell'area di Azure per generare l'output di Visualizzazione traffico. Se non viene specificata alcuna area di Azure, le informazioni sulla latenza saranno vuote nei dati per quegli endpoint esterni.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>È necessario abilitare Visualizzazione traffico per ogni profilo della sottoscrizione?

Durante il periodo di anteprima, Visualizzazione traffico è stato abilitato a livello di sottoscrizione. Tra i miglioramenti che sono stati apportati prima della disponibilità generale, è ora possibile abilitare Visualizzazione traffico a livello di profilo, consentendo un'abilitazione più granulare di questa funzionalità. Per impostazione predefinita, Visualizzazione traffico verrà disabilitato per un profilo.

>[!NOTE]
>Se Visualizzazione traffico è stato abilitato a livello di sottoscrizione durante il periodo di anteprima, è ora necessario abilitarlo nuovamente per ogni profilo in tale sottoscrizione.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Come si disabilita Visualizzazione traffico?

È possibile disabilitare Visualizzazione traffico per qualsiasi profilo con il portale o le API REST. 

### <a name="how-does-traffic-view-billing-work"></a>Come funziona la fatturazione di Visualizzazione traffico?

La determinazione dei prezzi di Visualizzazione traffico è basata sul numero di punti dati usati per creare l'output. L'unico tipo di dati supportato al momento sono le query che vengono ricevute dal proprio profilo. Viene addebitata inoltre solo l'elaborazione eseguita quando Visualizzazione traffico è abilitata. Ciò significa che, se si abilita Visualizzazione traffico per un periodo di tempo specifico in un mese e la si disabilita in altri periodi, vengono considerati per la fatturazione solo i punti dati elaborati mentre la funzionalità era abilitata.

## <a name="traffic-manager-endpoints"></a>Endpoint di Gestione traffico

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>È possibile usare Gestione traffico con endpoint di più sottoscrizioni?

L'uso di endpoint di più sottoscrizioni non è possibile con app Web di Azure. Per le app Web di Azure è necessario che ogni nome di dominio personalizzato usato con app Web venga usato solo all'interno di una singola sottoscrizione. Non è possibile usare app Web da più sottoscrizioni con lo stesso nome di dominio.

Per altri tipi di endpoint, è possibile utilizzare Gestione traffico con gli endpoint da più di una sottoscrizione. In Resource Manager è possibile aggiungere endpoint di qualsiasi sottoscrizione a Gestione traffico, purché la persona che configura il profilo di Gestione traffico abbia accesso in lettura all'endpoint. Queste autorizzazioni possono essere concesse tramite il [controllo di accesso in base al ruolo di Azure Resource Manager](../role-based-access-control/role-assignments-portal.md).

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>È possibile usare Gestione traffico con slot di "staging" del servizio cloud?

Sì. Gli slot di "staging" del servizio cloud possono essere configurati come endpoint esterni in Gestione traffico. I controlli di integrità vengono fatturati in base alla tariffa degli endpoint di Azure.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Gestione traffico supporta endpoint IPv6?

Attualmente Gestione traffico non fornisce server dei nomi indirizzabili tramite IPv6. Può comunque essere usato da client IPv6 che si connettono a endpoint IPv6. Un client non invia richieste DNS direttamente a Gestione traffico, ma usa un servizio DNS ricorsivo. Un client solo IPv6 invia richieste al servizio DNS ricorsivo tramite IPv6. Il servizio ricorsivo può quindi contattare i server dei nomi di Gestione traffico tramite IPv4.

Gestione traffico risponde con il nome DNS o l'indirizzo IP dell'endpoint. Per supportare un endpoint IPv6, sono disponibili due opzioni. È possibile aggiungere l'endpoint come nome DNS cui è associato un record AAAA perché Gestione traffico verifichi l'integrità dell'endpoint e lo restituisca come tipo di record CNAME nella risposta alla query. È anche possibile aggiungere l'endpoint direttamente usando l'indirizzo IPv6 e Gestione traffico restituisce un record di tipo AAAA nella risposta alla query.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>È possibile usare Gestione traffico con più app Web nella stessa area?

In genere, Gestione traffico viene usato per indirizzare il traffico ad applicazioni distribuite in aree diverse. Tuttavia, può anche essere usato in un'applicazione che abbia più distribuzioni nella stessa area. Gli endpoint di Azure di Gestione traffico non permettono l'aggiunta di più endpoint di app Web della stessa area di Azure allo stesso profilo di Gestione traffico.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group"></a>Qual è la procedura per spostare gli endpoint di Azure del profilo di Gestione traffico in un gruppo di risorse diverso?

Per tenere traccia degli endpoint di Azure associati a un profilo di Gestione traffico vengono usati i relativi ID di risorsa. Quando una risorsa di Azure usata come endpoint (ad esempio, indirizzo IP pubblico, servizio cloud classico, app Web o un altro profilo di Gestione traffico usato con annidamento) viene spostata in un gruppo di risorse diverso, il relativo ID di risorsa cambia. In questo scenario, è attualmente necessario aggiornare il profilo di Gestione traffico eliminando e riaggiungendo gli endpoint al profilo.

## <a name="traffic-manager-endpoint-monitoring"></a>Monitoraggio degli endpoint di Gestione traffico

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Gestione traffico è resiliente rispetto agli errori di area di Azure?

Gestione traffico è un componente fondamentale del recapito di applicazioni a disponibilità elevata in Azure.
Per assicurare una disponibilità elevata, Gestione traffico deve garantire un livello estremamente elevato di disponibilità, nonché la resilienza rispetto agli errori di area.

Per impostazione predefinita, i componenti di Gestione traffico resistono sono resilienti agli errori di qualsiasi area di Azure a livello globale. Questa resilienza si applica a tutti i componenti di Gestione traffico: server dei nomi DNS, API, livello di archiviazione e servizio di monitoraggio degli endpoint.

Nel caso improbabile in cui si verifichi l'interruzione di un'intera area di Azure, Gestione traffico deve continuare a funzionare normalmente. Per le applicazioni distribuite in più aree di Azure Gestione traffico consente di indirizzare il traffico alle istanze disponibili delle applicazioni.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>In che modo la scelta della posizione del gruppo di risorse si ripercuote su Gestione traffico?

Gestione traffico è un singolo servizio globale. Non è a livello di area. La scelta della posizione del gruppo di risorse non è rilevante per i profili di Gestione traffico distribuiti in quel gruppo di risorse.

Azure Resource Manager richiede che tutti i gruppi di risorse specifichino una posizione che determina il percorso predefinito delle risorse distribuite nel gruppo di risorse in questione. Quando si crea un profilo di Gestione traffico, viene creato in un gruppo di risorse. Tutti i profili di Gestione traffico usano **globale** come posizione, ignorando l'impostazione predefinita del gruppo di risorse.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Come si determina lo stato di integrità corrente di ogni endpoint?

Lo stato di monitoraggio corrente di ogni endpoint viene visualizzato nel portale di Azure, insieme al profilo complessivo. Queste informazioni sono anche disponibili con l'[API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) di Gestione traffico, i [cmdlet PowerShell](https://docs.microsoft.com/powershell/module/az.trafficmanager) e l'[interfaccia della riga di comando multipiattaforma di Azure](../cli-install-nodejs.md).

È anche possibile usare Monitoraggio di Azure per monitorare l'integrità degli endpoint e vedere una rappresentazione visiva dei risultati. Per altre informazioni su Monitoraggio di Azure, vedere la [documentazione del Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

### <a name="can-i-monitor-https-endpoints"></a>È possibile monitorare gli endpoint HTTPS?

Sì. Gestione traffico supporta il probing su HTTPS. Definire **HTTPS** come protocollo nella configurazione del monitoraggio.

Gestione traffico non prevede alcuna convalida di certificati, tra cui:

* I certificati sul lato server non vengono convalidati
* I certificati lato server di SNI non vengono convalidati
* I certificati client non sono supportati

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Si usa un indirizzo IP o un nome DNS quando si aggiunge un endpoint?

Gestione traffico supporta l'aggiunta degli endpoint usando tre modi per farvi riferimento: come nome DNS, come indirizzo IPv4 e come indirizzo IPv6. Se l'endpoint viene aggiunto come indirizzo IPv4 o IPv6, la risposta alla query sarà rispettivamente il tipo di record A o AAAA. Se l'endpoint è stato aggiunto come nome DNS, la risposta alla query sarà il tipo di record CNAME. L'aggiunta di endpoint come indirizzo IPv4 o IPv6 è consentita solo se l'endpoint è di tipo **Esterno**.
Tutti i metodi di routing e le impostazioni di monitoraggio sono supportati dai tre tipi di indirizzamento endpoint.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Quali tipi di indirizzi IP è possibile usare quando si aggiunge un endpoint?

Gestione traffico consente di usare gli indirizzi IPv4 o IPv6 per specificare gli endpoint. Esistono alcune restrizioni elencate di seguito:

- Gli indirizzi che corrispondono a spazi indirizzi IP privati riservati non sono consentiti. Questi indirizzi includono quelli indicati nelle specifiche RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 e RFC 5771
- L'indirizzo non deve contenere numeri di porta (è possibile specificare le porte da usare nelle impostazioni di configurazione del profilo)
- Due endpoint nello stesso profilo non possono avere lo stesso indirizzo IP di destinazione

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>È possibile usare tipi di indirizzamento diversi per gli endpoint all'interno di un singolo profilo?

No, Gestione traffico non consente di combinare diversi tipi di indirizzamento degli endpoint all'interno di un profilo, ad eccezione del caso di un profilo con tipo di routing multivalore in cui è possibile combinare i tipi di indirizzamento IPv4 e IPv6

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Cosa accade quando il tipo di record di una query in ingresso è diverso dal tipo di record associato al tipo di indirizzamento degli endpoint?

Quando si riceve una query per un profilo, Gestione traffico per prima cosa individua l'endpoint che deve essere restituito in base al metodo di routing specificato e allo stato di integrità degli endpoint. Quindi esamina il tipo di record richiesto nella query in ingresso e il tipo di record associato all'endpoint prima di restituire una risposta in base alla tabella che segue.

Per i profili con metodo di routing diverso da Multivalore:

|Richiesta query in ingresso|    Tipo di endpoint|  Risposta specificata|
|--|--|--|
|ANY |  A / AAAA / CNAME |  Endpoint di destinazione| 
|Una |    A / CNAME | Endpoint di destinazione|
|Una |    AAAA |  NODATA |
|AAAA | AAAA / CNAME |  Endpoint di destinazione|
|AAAA | Una | NODATA |
|CNAME |    CNAME | Endpoint di destinazione|
|CNAME  |A / AAAA | NODATA |
|

Per i profili con metodo di routing impostato su Multivalore:

|Richiesta query in ingresso|    Tipo di endpoint | Risposta specificata|
|--|--|--|
|ANY |  Combinazione di A e AAAA | Endpoint di destinazione|
|Una |    Combinazione di A e AAAA | Solo endpoint di destinazione di tipo A|
|AAAA   |Combinazione di A e AAAA|     Solo endpoint di destinazione di tipo AAAA|
|CNAME |    Combinazione di A e AAAA | NODATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>È possibile usare un profilo con endpoint con indirizzi IPv4 o IPv6 in un profilo annidato?

Sì, è possibile con l'eccezione che un profilo di tipo Multivalore non può essere un profilo padre in un profilo annidato impostato.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Ho arrestato un endpoint dell'applicazione Web nel profilo di gestione traffico, ma non ricevo alcun traffico anche dopo il riavvio. Come si risolve questo problema?

Quando un endpoint dell'applicazione Web di Azure viene arrestato, gestione traffico smette di controllarne l'integrità e riavvia i controlli di integrità solo dopo aver rilevato che l'endpoint è stato riavviato. Per evitare questo ritardo, disabilitare e riabilitare l'endpoint nel profilo di Gestione traffico dopo aver riavviato l'endpoint.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>È possibile usare Gestione traffico anche se l'applicazione non supporta HTTP o HTTPS?

Sì. Specificando TCP come protocollo di monitoraggio, Gestione traffico può avviare una connessione TCP e attendere una risposta dall'endpoint. Se l'endpoint risponde alla richiesta di connessione chiedendo di stabilire la connessione entro il periodo di timeout, l'endpoint viene contrassegnato come integro.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Quali sono le risposte specifiche richieste dall'endpoint quando si usa il monitoraggio TCP?

Se si usa il monitoraggio TCP, Gestione traffico avvia un handshake TCP a tre vie, inviando una richiesta SYN all'endpoint sulla porta specificata. Quindi attende la risposta dall'endpoint per il periodo di tempo specificato nelle impostazioni di timeout. Se l'endpoint risponde alla richiesta SYN con una risposta SYN-ACK entro il periodo di timeout specificato nelle impostazioni di monitoraggio, tale endpoint viene considerato integro. Se viene ricevuta la risposta SYN-ACK, Gestione traffico reimposta la connessione inviando in risposta un RST.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Con quale velocità Gestione traffico sposta gli utenti da un endpoint considerato non integro?

Per controllare il comportamento del profilo di Gestione traffico in caso di failover sono disponibili le impostazioni seguenti:

- È possibile specificare una maggiore frequenza di sondaggio degli endpoint da parte di Gestione traffico impostando l'intervallo di sondaggio su 10 secondi. Ciò assicura che un eventuale endpoint non integro venga rilevato il prima possibile. 
- È possibile specificare il tempo di attesa prima della scadenza di una richiesta di controllo di integrità. Il valore di timeout minimo è 5 sec.
- È possibile specificare il numero di errori che può verificarsi prima che l'endpoint sia contrassegnato come non integro. Se il valore specificato è pari a 0, l'endpoint viene contrassegnato come non integro al primo controllo di integrità non riuscito. Usando questo valore minimo, tuttavia, gli endpoint possono risultare esclusi dalla rotazione in caso di problemi temporanei che si verificano al momento dell'esecuzione del sondaggio.
- È possibile impostare la durata TTL della risposta DNS su un valore pari a 0. In questo modo i resolver DNS non possono memorizzare la risposta nella cache e ogni nuova query ottiene una risposta che include le informazioni sull'integrità più aggiornate disponibili a Gestione traffico.

Con queste impostazioni Gestione traffico può segnalare i failover entro 10 secondi dal rilevamento della mancata integrità dell'endpoint ed eseguire una query DNS in base al profilo corrispondente.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Come specificare diverse impostazioni di monitoraggio per i diversi endpoint in un profilo?

Le impostazioni di monitoraggio di Gestione traffico sono configurate a livello di profilo. Se è necessario usare un'impostazione di monitoraggio diversa per un unico endpoint, è consigliabile configurare tale endpoint come [profilo annidato](traffic-manager-nested-profiles.md), con impostazioni di monitoraggio diverse da quelle del profilo padre.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Come si assegnano le intestazioni HTTP ai controlli di integrità di Gestione traffico per gli endpoint?

Gestione traffico consente di specificare intestazioni personalizzate nei controlli di integrità HTTP(S) avviati per gli endpoint. Per specificare un'intestazione personalizzata, è possibile eseguire questa operazione a livello di profilo (applicabile a tutti gli endpoint) o a livello di endpoint. Se un'intestazione viene definita su entrambi i livelli, quella specificata a livello di endpoint sostituirà quella a livello di profilo.
Un caso d'uso comune è specificare intestazioni host in modo che le richieste di Gestione traffico possano essere instradate correttamente a un endpoint ospitato in un ambiente multi-tenant. Un altro caso d'uso è identificare le richieste di Gestione traffico dai log delle richieste HTTP(S) di un endpoint.

## <a name="what-host-header-do-endpoint-health-checks-use"></a>Quali intestazione host viene usata per i controlli di integrità degli endpoint?

Se non viene specificata alcuna impostazione di intestazione host personalizzata, l'intestazione host usata da Gestione traffico è il nome DNS della destinazione dell'endpoint configurata nel profilo, se disponibile.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Quali sono gli indirizzi IP da cui si originano i controlli di integrità?

Fare clic [qui](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) per visualizzare il file JSON che elenca gli indirizzi IP da cui possono provenire i controlli di integrità di Gestione traffico. Verificare gli indirizzi IP elencati nel file JSON per assicurarsi che le connessioni in ingresso da questi indirizzi IP siano consentite agli endpoint per controllarne lo stato di integrità.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Qual è il numero di controlli di integrità previsto per un endpoint da parte di Gestione traffico?

Il numero di controlli di integrità eseguiti da Gestione traffico sull'endpoint dipende dagli elementi seguenti:

- Il valore impostato per l'intervallo di monitoraggio; un intervallo inferiore indica un maggior numero di richieste che raggiungono l'endpoint in un determinato periodo di tempo.
- Il numero di posizioni da cui hanno origine i controlli di integrità; gli indirizzi IP da cui possono avere origine tali controlli sono elencati nella domanda precedente.

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Come si possono ricevere notifiche se uno degli endpoint risulta inattivo?

Una delle metriche fornite da Gestione traffico è costituita dallo stato di integrità degli endpoint in un profilo. È possibile visualizzare questo scenario come aggregazione di tutti gli endpoint all'interno di un profilo, ad esempio il 75% degli endpoint è integro, oppure a livello di singolo endpoint. Le metriche di Gestione traffico vengono esposte tramite Monitoraggio di Azure ed è possibile usare le rispettive [funzionalità per gli avvisi](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) per ottenere notifiche in caso di modifica dello stato di integrità dell'endpoint. Per altri dettagli, vedere [Traffic Manager metrics and alerts](traffic-manager-metrics-alerts.md) (Metriche e avvisi di Gestione traffico).  

## <a name="traffic-manager-nested-profiles"></a>Profili annidati di Gestione traffico

### <a name="how-do-i-configure-nested-profiles"></a>Come si configurano i profili nidificati?

I profili annidati di Gestione traffico possono essere configurati usando Azure Resource Manager, le API REST di Azure classico, i cmdlet di Azure PowerShell e i comandi multipiattaforma dell'interfaccia della riga di comando di Azure. Sono inoltre supportati anche tramite il nuovo portale di Azure.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Quanti livelli di nidificazione supporta Gestione traffico?

È possibile nidificare i profili fino a 10 livelli. I "loop" non sono consentiti.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>È possibile combinare altri tipi di endpoint con profili figlio nidificati nello stesso profilo di Gestione traffico?

Sì. Non esistono restrizioni sulla modalità di combinazione di tipi diversi di endpoint all'interno di un profilo.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Come viene applicato il modello di fatturazione per i profili nidificati?

L'uso di profili nidificati non ha alcun impatto negativo sui prezzi.

La fatturazione di Gestione traffico include due componenti: i controlli dell'integrità degli endpoint e milioni di query DNS.

* Controlli dell'integrità degli endpoint: non è previsto alcun addebito per un profilo figlio configurato come endpoint in un profilo padre. Il monitoraggio degli endpoint nel profilo figlio viene fatturato nel modo consueto.
* Query DNS: ogni query viene conteggiata una sola volta. Una query in un profilo padre che restituisce un endpoint da un profilo figlio viene conteggiata solo per il profilo padre.

Per informazioni dettagliate, vedere la pagina [Gestione traffico Prezzi](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>I profili nidificati influiscono sulle prestazioni?

No. Quando si usano i profili annidati non si verifica alcun impatto sulle prestazioni.

I server dei nomi di Gestione traffico attraversano internamente la gerarchia dei profili durante l'elaborazione di ogni query DNS, in modo che una query DNS inviata a un profilo padre possa ricevere una risposta DNS con un endpoint da un profilo figlio. Viene usato un singolo record CNAME, indipendentemente dal fatto che si usi un profilo singolo o profili annidati. Non è necessario quindi creare un record CNAME per ogni profilo della gerarchia.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>In che modo Gestione traffico calcola l'integrità di un endpoint annidato in un profilo padre?

Il profilo padre non esegue i controlli di integrità direttamente sul profilo figlio. L'integrità degli endpoint del profilo figlio viene usata invece per calcolare l'integrità complessiva del profilo figlio. Queste informazioni vengono propagate alla gerarchia del profilo annidato per determinare l'integrità dell'endpoint annidato. Il profilo padre usa quindi questa integrità complessiva per determinare se indirizzare il traffico al profilo figlio.

La tabella seguente descrive il comportamento dei controlli dell'integrità di Gestione traffico per un endpoint annidato.

| Stato di monitoraggio del profilo figlio | Stato di monitoraggio dell'endpoint padre | Note |
| --- | --- | --- |
| Disabilitato. Il profilo figlio è stato disabilitato. |Arrestato |Lo stato dell'endpoint padre è Stopped, non Disabled. Lo stato Disabled è usato esclusivamente per indicare che l'utente ha disabilitato l'endpoint nel profilo padre. |
| Danneggiato. Almeno uno degli endpoint del profilo figlio è nello stato Danneggiato. |Online: il numero di endpoint Online nel profilo figlio è pari almeno al valore di MinChildEndpoints.<BR>CheckingEndpoint: il numero di endpoint Online e CheckingEndpoint nel profilo figlio è pari almeno al valore di MinChildEndpoints.<BR>Danneggiato: negli altri casi. |Il traffico viene indirizzato a un endpoint con stato CheckingEndpoint. Se il valore di MinChildEndpoints è troppo elevato, l'endpoint risulta sempre danneggiato. |
| Online. Almeno uno degli endpoint del profilo figlio è nello stato Online. Nessun endpoint è nello stato Danneggiato. |Vedere sopra. | |
| CheckingEndpoints. Almeno uno degli endpoint del profilo figlio è nello stato CheckingEndpoint. Nessun endpoint è nello stato Online o Danneggiato. |Come sopra. | |
| Inattivo. Tutti gli endpoint del profilo figlio sono nello stato Disabilitato o Arrestato oppure si tratta di un profilo senza endpoint. |Arrestato | |

## <a name="next-steps"></a>Passaggi successivi:

- Altre informazioni sul [monitoraggio degli endpoint e sul failover automatico](../traffic-manager/traffic-manager-monitoring.md)di Gestione traffico.
- Altre informazioni sui [metodi di routing](../traffic-manager/traffic-manager-routing-methods.md)di Gestione traffico.
