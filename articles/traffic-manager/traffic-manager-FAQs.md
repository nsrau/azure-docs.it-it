---
title: Gestione traffico di Azure - Domande frequenti | Documentazione Microsoft
description: Questo articolo risponde ad alcune domande frequenti su Gestione traffico
services: traffic-manager
documentationcenter: 
author: KumudD
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2017
ms.author: kumud
ms.openlocfilehash: d6681a5b46aa352b1aa0dadedad8a51c9d1e5eaf
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2017
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Domande frequenti (FAQ) su Gestione traffico

## <a name="traffic-manager-basics"></a>Fondamenti di Gestione traffico

### <a name="what-ip-address-does-traffic-manager-use"></a>Quale indirizzo IP viene usato da Gestione traffico?

Come spiegato nella sezione [Modalità di funzionamento di Gestione traffico](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Gestione traffico funziona a livello di DNS. Invia le risposte DNS per indirizzare i client all'endpoint di servizio appropriato. I client si connettono quindi all'endpoint di servizio in modo diretto, senza passare per Gestione traffico.

Gestione traffico non prevede quindi un endpoint o indirizzo IP per la connessione dei client. Se si intende avere un indirizzo IP statico per un servizio, è necessario configurarlo nel servizio, non in Gestione traffico.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Gestione traffico supporta sessioni "permanenti"?

Come spiegato nella sezione [Modalità di funzionamento di Gestione traffico](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Gestione traffico funziona a livello di DNS. Usa le risposte DNS per indirizzare i client all'endpoint di servizio appropriato. I client si connettono all'endpoint di servizio in modo diretto, senza passare per Gestione traffico. Gestione traffico non visualizza quindi il traffico HTTP tra il client e il server.

Inoltre l'indirizzo IP di origine della query DNS ricevuta da Gestione traffico appartiene al servizio DNS ricorsivo e non al client. Il servizio Gestione traffico non può quindi tracciare client singoli e non può implementare sessioni "permanenti". Questa limitazione è comune a tutti i sistemi di gestione del traffico basati su DNS e non è specifica di Gestione traffico.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Quando si usa Gestione traffico, viene visualizzato un errore HTTP. Perché?

Come spiegato nella sezione [Modalità di funzionamento di Gestione traffico](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Gestione traffico funziona a livello di DNS. Usa le risposte DNS per indirizzare i client all'endpoint di servizio appropriato. I client si connettono quindi all'endpoint di servizio in modo diretto, senza passare per Gestione traffico. Gestione traffico non vede il traffico HTTP tra client e server. Ogni errore HTTP visualizzato proviene quindi dall'applicazione. Per la connessione del client all'applicazione, è necessario che siano stati completati tutti i passaggi di risoluzione DNS. È inclusa qualsiasi interazione di Gestione traffico con il flusso del traffico dell'applicazione.

Eventuali verifiche più approfondite dovranno quindi concentrarsi sull'applicazione.

L'intestazione host HTTP inviata dal browser del client è l'origine dei problemi più comune. Assicurarsi che l'applicazione sia configurata per accettare l'intestazione host corretta per il nome di dominio in uso. Per gli endpoint che usano il Servizio app di Azure, vedere [Configurazione di un nome di dominio personalizzato per un'app Web nel servizio app di Azure con Gestione traffico](../app-service/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Qual è l'impatto sulle prestazioni dell'uso di Gestione traffico?

Come spiegato nella sezione [Modalità di funzionamento di Gestione traffico](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Gestione traffico funziona a livello di DNS. Dal momento che i client si connettono direttamente agli endpoint di servizio, dopo che è stata stabilita la connessione non si verifica alcun impatto sulle prestazioni.

Poiché Gestione traffico si integra con le applicazioni a livello di DNS, richiede l'inserimento di una ricerca DNS aggiuntiva nella catena di risoluzione DN. L'impatto di Gestione traffico sul tempo di risoluzione DNS è minimo. Gestione traffico usa una rete globale di server dei nomi e reti [Anycast](https://en.wikipedia.org/wiki/Anycast) per garantire che le query DNS vengano sempre indirizzate al server dei nomi più vicino disponibile. Inoltre, la memorizzazione nella cache delle risposte DNS significa che la latenza DNS aggiuntiva associata all'uso di Gestione traffico si applica solo a una frazione di sessioni.

Il metodo Prestazioni instrada il traffico all'endpoint disponibile più vicino. Il risultato della rete è che l'impatto sulle prestazioni complessive associate a questo metodo dovrebbe essere minimo. Un aumento della latenza DNS deve essere compensato da minore latenza di rete all'endpoint.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Quali protocolli di applicazione possono essere usati con Gestione traffico?

Come spiegato nella sezione [Modalità di funzionamento di Gestione traffico](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Gestione traffico funziona a livello di DNS. Dopo il completamento della ricerca DNS, i client si connettono all'endpoint dell'applicazione direttamente, non tramite Gestione traffico. La connessione può pertanto usare qualsiasi protocollo dell'applicazione. Se come protocollo di monitoraggio si seleziona TCP, i controlli dell'integrità dell'endpoint eseguiti da Gestione traffico non richiedono l'uso dei protocolli dell'applicazione. Se si sceglie di verificare l'integrità usando un protocollo dell'applicazione, l'endpoint deve poter rispondere alle richieste HTTP o HTTPS GET.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>È possibile usare Gestione traffico con un nome di dominio di tipo "naked" (senza www)?

di serie Gli standard DNS non consentono la coesistenza tra record CNAME e altri record DNS dello stesso nome. Il vertice o la radice di una zona DNS contiene sempre due record DNS preesistenti: il record SOA e quello del server dei nomi autorevole. Non è quindi possibile creare un record CNAME al vertice della zona senza violare gli standard DNS.

Gestione traffico richiede un record CNAME DNS per eseguire il mapping del nome DNS personalizzato. Ad esempio, eseguire il mapping di www.contoso.com al nome DNS del profilo di Gestione traffico contoso.trafficmanager.net. Inoltre, il profilo di Gestione traffico restituisce un secondo record DNS CNAME per indicare l'endpoint a cui il client dovrebbe collegarsi.

Per risolvere questo problema, è consigliabile usare un reindirizzamento HTTP per indirizzare il traffico dal nome di dominio naked a un URL differente, che può quindi usare Gestione traffico. Ad esempio, il dominio naked "contoso.com" può reindirizzare gli utenti al dominio "www.contoso.com" CNAME, che punta al nome DNS di Gestione traffico.

Il supporto completo per i domini naked in Gestione traffico è riportato nel backlog delle funzionalità. È possibile registrare il supporto per questa funzionalità [votandolo sul sito dei commenti della community](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Gestione traffico tiene conto dell'indirizzo della subnet client quando si gestiscono query DNS? 
Sì, oltre all'indirizzo IP di origine della query DNS che riceve (che è in genere l'indirizzo IP del sistema di risoluzione DNS), quando si eseguono ricerche per i metodi di routing geografico e le prestazioni, Gestione traffico considera anche l'indirizzo di subnet del client se viene incluso nella query dal resolver che effettua la richiesta per conto dell'utente finale.  
Per altri dettagli, vedere la [RFC 7871 – Client Subnet in DNS Queries](https://tools.ietf.org/html/rfc7871) (RFC 7871 - Subnet client nelle query DNS), che indica un [meccanismo di estensione per DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) che può trasferire l'indirizzo della subnet client dai resolver che lo supportano.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Cos'è la durata TTL del DNS e che impatto ha sugli utenti?

Quando una query DNS viene ricevuta da Gestione traffico, nella risposta viene impostato un valore chiamato Durata (TTL). Questo valore, espresso in secondi, indica ai resolver DNS a valle il tempo di memorizzazione della risposta nella cache. Sebbene ciò non garantisca che i resolver memorizzino il risultato nella cache, la memorizzazione consente loro di rispondere a ogni successiva query usando la cache, invece di inoltrare la query ai server DNS di Gestione traffico. Di seguito è illustrato l'impatto sulle risposte:
- Una durata TTL maggiore riduce il numero di query che raggiunge i server DNS di Gestione traffico; ciò contribuisce a ridurre i costi per il cliente, poiché il numero di query gestite incide sulla fatturazione.
- Una durata TTL maggiore può potenzialmente ridurre il tempo necessario per eseguire una ricerca DNS.
- Una durata TTL maggiore implica anche che i dati possono non riflettere le informazioni di integrità più recenti ottenute da Gestione traffico tramite gli agenti che eseguono il sondaggio.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Come impostare una durata TTL maggiore o minore per le risposte di Gestione traffico?

A livello di singolo profilo, è possibile impostare una durata TTL del DNS minima di 0 secondi e massima di 2.147.483.647 secondi, ovvero l'intervallo massimo conforme a [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt ). Una durata TTL pari a 0 indica che i resolver DNS a valle non memorizzano nella cache le risposte alle query; tutte le query vengono ricevute dai server DNS di Gestione traffico per essere risolte.

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Metodi geografico di routing del traffico di Gestione traffico

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Quali sono alcuni casi di uso in cui il routing geografico è utile? 
Il tipo di routing Geografico può essere usato in qualsiasi scenario in cui un cliente di Azure abbia bisogno di distinguere gli utenti in base alle aree geografiche. Ad esempio, tramite il metodo di routing del traffico Geografico è possibile offrire agli utenti di una determinata area un'esperienza utente diversa rispetto a quelli di altre aree. Un altro esempio è la necessità di garantire la conformità con requisiti dei dati locali che richiedono di servire gli utenti di una determinata area solo con gli endpoint di tale area.

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


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Perché è decisamente consigliato che i clienti creino profili nidificati anziché endpoint in un profilo con il routing geografico abilitato? 

Un'area può essere assegnata a un solo endpoint all'interno di un profilo se usa il tipo di routing geografico. Se tale endpoint non è un tipo annidato con un profilo figlio collegato, nel caso l'endpoint perdesse l'integrità, Gestione traffico continuerà a inviare traffico a esso in quanto l'alternativa di non inviare alcun traffico non è migliore. Gestione traffico non esegue il failover su un altro endpoint, anche quando l'area assegnata è "padre" dell'area assegnata all'endpoint danneggiato. Se, ad esempio, un endpoint che include l'area Spagna viene danneggiato, non si eseguirà il failover su un altro endpoint a cui è assegnata l'area Europa. Lo scopo di tutto questo è garantire che Gestione traffico rispetti i confini geografici che un cliente ha stabilito nel proprio profilo. Per ottenere il vantaggio del failover su un altro endpoint quando un endpoint perde la sua integrità, è consigliabile assegnare le aree geografiche a profili annidati con più endpoint all'interno invece di singoli endpoint. In questo modo se un endpoint nel profilo figlio annidato non funziona, il traffico può eseguire il failover su un altro endpoint all'interno dello stesso profilo figlio annidato.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Ci sono restrizioni sulla versione API che supporta questo tipo di routing?

Sì, solo l'API versione 2017-03-01 e versioni successive supportano il routing di tipo geografico. Le versioni precedenti dell'API non possono essere usate per creare profili con routing di tipo Geografico o assegnare aree geografiche agli endpoint. Se viene usata una versione precedente dell'API per recuperare i profili da una sottoscrizione di Azure, non vengono restituiti profili con routing di tipo Geografico. Inoltre, quando si usano versioni precedenti dell'API, tutti i profili restituiti che hanno endpoint con un'area geografica assegnata non mostreranno l'area geografica assegnata.

## <a name="real-user-measurements"></a>Misurazioni utente reale

>[!NOTE]
>La funzionalità Misurazioni utente reale in Gestione traffico è attualmente in versione di anteprima pubblica e potrebbe non offrire lo stesso livello di disponibilità e affidabilità delle funzionalità presenti nella versione con disponibilità generale. La funzionalità non è supportata, potrebbe avere funzioni vincolate e potrebbe non essere disponibile in tutte le località di Azure. Per ricevere le notifiche più aggiornate su disponibilità e stato di questa funzionalità, vedere la pagina [Aggiornamenti di Gestione traffico di Azure](https://azure.microsoft.com/updates/?product=traffic-manager).

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Quali sono i vantaggi offerti dall'utilizzo di Misurazioni utente reale?
Quando si usa il metodo di routing basato sulle prestazioni, Gestione traffico seleziona l'area di Azure migliore a cui l'utente finale può connettersi, controllando l'IP di origine e la subnet client EDNS (se passata) e confrontandoli con l'intelligence di latenza di rete gestita dal servizio. La funzionalità Misurazioni utente reale migliora questo scenario facendo in modo che le esperienze della base di utenti finali contribuiscano a questa tabella di latenza e assicurando che la tabella includa adeguatamente le reti degli utenti finali da cui gli utenti finali si connettono ad Azure. In questo modo si ottiene una maggiore precisione nel routing degli utenti finali.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>È possibile usare Misurazioni utente reale con aree non di Azure?
La funzionalità Misurazioni utente finale misura e riporta solo la latenza per raggiungere le aree di Azure. Se si usa il routing basato sulle prestazioni con endpoint ospitati in aree non di Azure, è comunque possibile beneficiare di questa funzionalità facendo in modo che le informazioni di latenza aumentata sull'area di Azure rappresentativa precedentemente selezionata vengano associate a questi endpoint.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Quale metodo di routing trae vantaggio dalla funzionalità Misurazioni utente reale?
Le informazioni aggiuntive ottenute tramite Misurazioni utente reale sono applicabili solo ai profili che usano il metodo di routing basato sulle prestazioni. Si noti che il collegamento Misurazioni utente reale è disponibile da tutti i profili quando lo si visualizza tramite il portale di Azure.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>È necessario abilitare Misurazioni utente reale per ogni profilo separatamente?
No, basta abilitare la funzionalità una sola volta per ogni sottoscrizione e tutte le informazioni di latenza misurate e riportate diventano disponibili per tutti i profili.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Come si disattiva Misurazioni utente reale in una sottoscrizione?
È possibile interrompere gli addebiti dei costi relativi a Misurazioni utente reale quando si smette di raccogliere e restituire le misure di latenza dall'applicazione client. Quando il codice JavaScript di misurazione è integrato in pagine Web, ad esempio, è possibile arrestare la funzionalità rimuovendo il codice JavaScript o disattivando la chiamata durante il rendering della pagina.
Un altro modo per disattivare Misurazioni utente reale consiste nell'eliminare la chiave. Dopo che la funzionalità è stata disabilitata, tutte le misurazioni inviate a Gestione traffico con tale chiave vengono rimosse.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>È possibile usare Misurazioni utente reale con applicazioni client diverse dalle pagine Web?
Sì, Misurazioni utente reale è progettata per inserire i dati raccolti tramite tipi diversi di client utente finale. Queste domande frequenti verranno aggiornate man mano che verranno supportati nuovi tipi di applicazioni client.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Quante misurazioni vengono effettuate ogni volta che viene eseguito il rendering di una pagina Web con Misurazioni utente reale abilitata?
Quando Misurazioni utente reale viene usata con il codice JavaScript di misurazione specificato, ogni rendering di pagina restituisce sei misurazioni. Queste misurazioni vengono quindi riportate al servizio Gestione traffico. Si noti che l'addebito per questa funzionalità avviene in base al numero di misurazioni riportate al Gestione traffico. Se, ad esempio, l'utente esce dalla pagina Web durante le misurazioni ma prima che queste siano riportate al servizio,tali misurazioni non vengono considerate per la fatturazione.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>È presente un ritardo prima dell'esecuzione dello script di Misurazioni utente reale nella pagina Web?
No, non vi sono ritardi programmati prima che lo script venga richiamato.

### <a name="can-i-use-configure-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>È possibile configurare Misurazioni utente reale solo con le aree di Azure che si intende misurare?
No, ogni volta che viene chiamato, lo script di Misurazioni utente reale misura un set di sei aree di Azure determinato dal servizio. Questo set cambia tra una chiamata e l'altra e, quando si verificano tante chiamate, la copertura delle misurazioni spazia tra aree di Azure diverse.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>È possibile limitare il numero di misurazioni effettuate a un numero specifico?
Il codice JavaScript di misurazione è integrato nella pagina Web, pertanto si ha il controllo completo su quando iniziare e arrestare l'uso del servizio. Il set di aree viene restituito fino a quando il servizio Gestione traffico riceve una richiesta di misurazione di un elenco di aree di Azure. Occorre inoltre sapere che, durante il periodo di anteprima, non verrà addebitato alcuno costo per le misurazioni riportate a Gestione traffico.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>È possibile visualizzare le misurazioni effettuate dall'applicazione client nell'ambito di Misurazioni utente reale?
Poiché la logica di misurazione viene eseguita dall'applicazione client, si ha il controllo completo di ciò che accade, inclusa la visualizzazione delle misurazioni di latenza. Gestione traffico non mostra una vista aggregata delle misurazioni ricevute nella chiave collegata alla sottoscrizione in uso.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>È possibile modificare lo script di misurazione di Gestione traffico?
È possibile controllare ciò che è integrato nella pagina Web, ma è fortemente sconsigliato modificare lo script di misurazione per garantire che misuri e riporti le latenze correttamente.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Gli altri utenti sono in grado di vedere la chiave di un utente che viene usata con Misurazioni utente reale?
Quando si incorpora lo script di misurazione in una pagina Web, gli altri utenti possono visualizzare lo script e la chiave di Misurazioni utente reale. È tuttavia importante sapere che questa chiave è diversa dall'ID sottoscrizione e viene generata da Gestione traffico per essere usata solo a questo scopo. Il fatto che altri utenti conoscano la chiave di Misurazioni utente reale di un utente non compromette la sicurezza dell'account di Azure che la usa.

### <a name="can-others-abuse-my-rum-key"></a>È possibile che altri utenti usino la chiave di Misurazioni utente reale di un utente senza autorizzazione?
Sebbene sia possibile che altri utenti usino la chiave di un utente per inviare informazioni errate ad Azure, la presenza di alcune misurazioni errate non cambia il routing, in quanto viene preso in considerazione insieme a tutte le altre misurazioni ricevute. Se è necessario modificare le chiavi, è possibile generare la chiave nuovamente e a quel punto la chiave precedente viene eliminata.

###  <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>È necessario inserire il codice JavaScript di misurazione in tutte le pagine Web?
Il servizio Misurazioni utente reale diventa più prezioso man mano che il numero delle misurazioni aumenta. Spetta comunque all'utente valutare se è necessario inserirlo in tutte le pagine Web o solo in alcune. Si consiglia di iniziare inserendolo nella pagina più visitata, dove si presume che gli utenti vi rimangano per almeno cinque secondi.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Gestione traffico è in grado di identificare le informazioni sugli utenti finali se si usa Misurazioni utente reale?
Quando si usa il codice JavaScript di misurazione, Gestione traffico può vedere l'indirizzo IP del client dell'utente finale e l'indirizzo IP di origine del resolver DNS locale usati. Gestione traffico usa l'indirizzo IP del client solo dopo averlo troncato per non consentire l'identificazione dell'utente finale specifico che ha inviato le misurazioni. 

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>La pagina Web che misura con Misurazioni utente reale deve usare Gestione traffico per il routing?
No, non deve necessariamente usare Gestione traffico. Il lato routing di Gestione traffico opera separatamente dal lato Misurazioni utente reale e, sebbene sia molto utile usarli entrambi nella stessa proprietà Web, non è obbligatorio.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>È necessario ospitare un qualsiasi servizio nelle aree di Azure da usare con Misurazioni utente reale?
No, non è necessario ospitare alcun componente lato server in Azure perché Misurazioni utente reale funzioni. L'immagine a pixel singolo scaricata dal codice JavaScript di misurazione e il servizio che lo esegue in aree di Azure diverse sono ospitati e gestiti da Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>L'utilizzo della larghezza di banda di Azure aumenta quando si usa Misurazioni utente reale?
Come indicato nella risposta precedente, i componenti lato server di Misurazioni utente reale sono di proprietà e gestiti da Azure. Questo significa che l'uso di Misurazioni utente reale non determina un aumento dell'utilizzo della larghezza di banda. Si noti che questo non include qualsiasi utilizzo della larghezza di banda al di fuori di quanto viene addebitato da Azure. La larghezza di banda usata per scaricare solo un'immagine a pixel singolo viene limitata alla misurazione della latenza a un'area di Azure. 

## <a name="traffic-view"></a>Visualizzazione traffico

>[!NOTE]
>La funzionalità Visualizzazione traffico di Gestione traffico è attualmente in versione di anteprima pubblica e potrebbe non offrire lo stesso livello di disponibilità e affidabilità delle funzionalità presenti nella versione con disponibilità generale. La funzionalità non è supportata, potrebbe avere funzioni vincolate e potrebbe non essere disponibile in tutte le località di Azure. Per ricevere le notifiche più aggiornate su disponibilità e stato di questa funzionalità, vedere la pagina [Aggiornamenti di Gestione traffico di Azure](https://azure.microsoft.com/updates/?product=traffic-manager).

### <a name="what-does-traffic-view-do"></a>A cosa serve Visualizzazione traffico?
Visualizzazione traffico è una funzionalità di Gestione traffico che consente di comprendere a fondo gli utenti e le relative esperienze. Usa le query che riceve da Gestione traffico e le tabelle di intelligence di latenza di rete che il servizio gestisce per offrire le informazioni seguenti:
- Le aree da cui gli utenti si connettono agli endpoint in Azure.
- Il volume di utenti che si connettono da tali aree.
- Le aree di Azure a cui gli utenti vengono instradati.
- L'esperienza di latenza per queste aree di Azure.

Queste informazioni sono disponibili all'utente tramite una vista tabulare nel portale. Sono inoltre disponibili come dati non elaborati da scaricare.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Quali sono i vantaggi dell'utilizzo di Visualizzazione traffico?

Visualizzazione traffico offre una visione complessiva del traffico ricevuto dai profili di Gestione traffico. Può essere usata, in particolare, per comprendere da dove si connette la base utente e, non meno importante, qual è l'esperienza di latenza media. È quindi possibile usare queste informazioni per individuare le aree su cui è necessario concentrarsi, ad esempio, espandendo il footprint di Azure a un'area che può gestire gli utenti con una latenza più bassa. Un'altra informazione che è possibile derivare da Visualizzazione traffico riguarda i modelli di traffico verso aree diverse. Questa informazione può aiutare a prendere decisioni in merito all'aumento o alla diminuzione dell'inventario in quelle aree.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>In che modo Visualizzazione traffico differisce dalle metriche di Gestione traffico disponibili tramite Monitoraggio di Azure?

Monitoraggio di Azure aiuta a comprendere, a livello di aggregazione, il traffico ricevuto dal proprio profilo e dai relativi endpoint. Il servizio consente di tenere traccia dello stato di integrità degli endpoint tramite l'esposizione dei risultati del controllo di integrità. Quando occorre andare oltre questi dati e studiare l'esperienza dei propri utenti finali che si connettono ad Azure a livello di area, Visualizzazione traffico è lo strumento giusto.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Visualizzazione traffico usa le informazioni della subnet client EDNS?

Visualizzazione traffico non considera le informazioni della subnet client EDNS quando genera il proprio output. Usa l'indirizzo IP del resolver DNS locale degli utenti per raggrupparli.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Quanti giorni di dati usa Visualizzazione traffico?

Visualizzazione traffico genera il proprio output elaborando i dati dei sette giorni che precedono quello prima del giorno in cui l'utente visualizza l'output. Si tratta di una finestra mobile e a ogni visita vengono usati i dati più recenti.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>In che modo Visualizzazione traffico gestisce gli endpoint esterni?

Quando si usano endpoint esterni ospitati all'esterno di aree di Azure in un profilo di Gestione traffico, è possibile scegliere che venga eseguito il mapping a un'area di Azure che è un proxy per le relative caratteristiche di latenza (che è in effetti necessario se si usa il metodo di routing basato sulle prestazioni). Con questo mapping dell'area di Azure verranno usate le metriche di latenza di quell'area di Azure per generare l'output di Visualizzazione traffico. Se non viene specificata alcuna area di Azure, le informazioni sulla latenza saranno vuote nei dati per quegli endpoint esterni.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>È necessario abilitare Visualizzazione traffico per ogni profilo della sottoscrizione?
Durante il periodo di anteprima, la funzionalità Visualizzazione traffico è abilitata a livello di sottoscrizione ed è disponibile per tutti i profili di Gestione traffico in tale sottoscrizione.

### <a name="how-can-i-turn-off-traffic-view"></a>Come si disabilita Visualizzazione traffico?
Durante il periodo di anteprima, è necessario creare un ticket di supporto per richiederne la disabilitazione nella propria sottoscrizione.

### <a name="how-does-traffic-view-billing-work"></a>Come funziona la fatturazione di Visualizzazione traffico?

La determinazione dei prezzi di Visualizzazione traffico è basata sul numero di punti dati usati per creare l'output. L'unico tipo di dati supportato al momento sono le query che vengono ricevute dal proprio profilo. Viene addebitata inoltre solo l'elaborazione eseguita quando Visualizzazione traffico è abilitata. Ciò significa che, se si abilita Visualizzazione traffico per un periodo di tempo specifico in un mese e la si disabilita in altri periodi, vengono considerati per la fatturazione solo i punti dati elaborati mentre la funzionalità era abilitata.
Durante il periodo di anteprima, l'uso di Visualizzazione traffico non viene fatturato.

## <a name="traffic-manager-endpoints"></a>Endpoint di Gestione traffico

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>È possibile usare Gestione traffico con endpoint di più sottoscrizioni?

L'uso di endpoint di più sottoscrizioni non è possibile con app Web di Azure. Per le app Web di Azure è necessario che ogni nome di dominio personalizzato usato con app Web venga usato solo all'interno di una singola sottoscrizione. Non è possibile usare app Web da più sottoscrizioni con lo stesso nome di dominio.

Per altri tipi di endpoint, è possibile utilizzare Gestione traffico con gli endpoint da più di una sottoscrizione. In Resource Manager è possibile aggiungere endpoint di qualsiasi sottoscrizione a Gestione traffico, purché la persona che configura il profilo di Gestione traffico abbia accesso in lettura all'endpoint. Queste autorizzazioni possono essere concesse tramite il [controllo di accesso in base al ruolo di Azure Resource Manager](../active-directory/role-based-access-control-configure.md).


### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>È possibile usare Gestione traffico con slot di "staging" del servizio cloud?

Sì. Gli slot di "staging" del servizio cloud possono essere configurati come endpoint esterni in Gestione traffico. I controlli di integrità vengono fatturati in base alla tariffa degli endpoint di Azure. Dato che viene usato il tipo di endpoint esterno, le modifiche al servizio sottostante non vengono rilevate automaticamente. Se si usano endpoint esterni, Gestione traffico non è in grado di rilevare l'eventuale arresto o eliminazione del servizio cloud. Di conseguenza, Gestione traffico continua a fatturare i controlli di integrità fino a quando l'endpoint non viene disabilitato o eliminato.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Gestione traffico supporta endpoint IPv6?

Attualmente Gestione traffico non fornisce server dei nomi indirizzabili tramite IPv6. Può comunque essere usato da client IPv6 che si connettono a endpoint IPv6. Un client non invia richieste DNS direttamente a Gestione traffico, ma usa un servizio DNS ricorsivo. Un client solo IPv6 invia richieste al servizio DNS ricorsivo tramite IPv6. Il servizio ricorsivo può quindi contattare i server dei nomi di Gestione traffico tramite IPv4.

Gestione traffico risponde con il nome DNS dell'endpoint. Per supportare un endpoint IPv6, è necessaria la presenza di un record AAAA DNS che punti il nome DNS dell'endpoint all'indirizzo IPv6. I controlli di integrità di Gestione traffico supportano soltanto gli indirizzi IPv4. Il servizio deve esporre un endpoint IPv4 sullo stesso nome DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>È possibile usare Gestione traffico con più app Web nella stessa area?

In genere, Gestione traffico viene usato per indirizzare il traffico ad applicazioni distribuite in aree diverse. Tuttavia, può anche essere usato in un'applicazione che abbia più distribuzioni nella stessa area. Gli endpoint di Azure di Gestione traffico non permettono l'aggiunta di più endpoint di app Web della stessa area di Azure allo stesso profilo di Gestione traffico.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group"></a>Qual è la procedura per spostare gli endpoint di Azure del profilo di Gestione traffico in un gruppo di risorse diverso?

Per tenere traccia degli endpoint di Azure associati a un profilo di Gestione traffico vengono usati i relativi ID di risorsa. Quando una risorsa di Azure usata come endpoint (ad esempio, indirizzo IP pubblico, servizio cloud classico, app Web o un altro profilo di Gestione traffico usato con annidamento) viene spostata in un gruppo di risorse diverso, il relativo ID di risorsa cambia. In questo scenario, è attualmente necessario aggiornare il profilo di Gestione traffico eliminando e riaggiungendo gli endpoint al profilo. 

##  <a name="traffic-manager-endpoint-monitoring"></a>Monitoraggio degli endpoint di Gestione traffico

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Gestione traffico è resiliente rispetto agli errori di area di Azure?

Gestione traffico è un componente fondamentale del recapito di applicazioni a disponibilità elevata in Azure.
Per assicurare una disponibilità elevata, Gestione traffico deve garantire un livello estremamente elevato di disponibilità, nonché la resilienza rispetto agli errori di area.

Per impostazione predefinita, i componenti di Gestione traffico resistono sono resilienti agli errori di qualsiasi area di Azure a livello globale. Questa resilienza si applica a tutti i componenti di Gestione traffico: server dei nomi DNS, API, livello di archiviazione e servizio di monitoraggio degli endpoint.

Nel caso improbabile in cui si verifichi l'interruzione di un'intera area di Azure, Gestione traffico deve continuare a funzionare normalmente. Per le applicazioni distribuite in più aree di Azure Gestione traffico consente di indirizzare il traffico alle istanze disponibili delle applicazioni.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>In che modo la scelta della posizione del gruppo di risorse si ripercuote su Gestione traffico?

Gestione traffico è un singolo servizio globale. Non è a livello di area. La scelta della posizione del gruppo di risorse non è rilevante per i profili di Gestione traffico distribuiti in quel gruppo di risorse.

Azure Resource Manager richiede che tutti i gruppi di risorse specifichino una posizione che determina il percorso predefinito delle risorse distribuite nel gruppo di risorse in questione. Quando si crea un profilo di Gestione traffico, viene creato in un gruppo di risorse. Tutti i profili di Gestione traffico usano **globale** come posizione, ignorando l'impostazione predefinita del gruppo di risorse.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Come si determina lo stato di integrità corrente di ogni endpoint?

Lo stato di monitoraggio corrente di ogni endpoint viene visualizzato nel portale di Azure, insieme al profilo complessivo. Queste informazioni sono anche disponibili con l'[API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) di Gestione traffico, i [cmdlet PowerShell](https://msdn.microsoft.com/library/mt125941.aspx) e l'[interfaccia della riga di comando multipiattaforma di Azure](../cli-install-nodejs.md).

È anche possibile usare Monitoraggio di Azure per monitorare l'integrità degli endpoint e vedere una rappresentazione visiva dei risultati. Per altre informazioni su Monitoraggio di Azure, vedere la [documentazione del Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

### <a name="can-i-monitor-https-endpoints"></a>È possibile monitorare gli endpoint HTTPS?

Sì. Gestione traffico supporta il probing su HTTPS. Definire **HTTPS** come protocollo nella configurazione del monitoraggio.

Gestione traffico non prevede alcuna convalida di certificati, tra cui:

* I certificati sul lato server non vengono convalidati
* I certificati SNI sul lato server non sono supportati
* I certificati client non sono supportati

### <a name="i-stopped-an-azure-cloud-service--web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>L'endpoint del servizio cloud e dell'applicazione Web Azure è stato interrotto nel profilo di Gestione traffico, ma non si riceve traffico neanche dopo il riavvio. Come si risolve questo problema?

Quando un endpoint del servizio cloud e dell'applicazione Web Azure viene interrotto, Gestione traffico non ne verifica più l'integrità e riavvia i controlli di integrità solo quando rileva che l'endpoint è stato riavviato. Per evitare questo ritardo, disabilitare e riabilitare l'endpoint nel profilo di Gestione traffico dopo aver riavviato l'endpoint.   

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

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Quali intestazione host viene usata per i controlli di integrità degli endpoint?

Gestione traffico usa le intestazioni host nei controlli di integrità HTTP e HTTPS. L'intestazione host usata da Gestione traffico è il nome dell'endpoint di destinazione configurato nel profilo. Il valore usato nell'intestazione host non può essere specificato separatamente dalla proprietà target.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Quali sono gli indirizzi IP da cui si originano i controlli di integrità?

L'elenco seguente contiene gli indirizzi IP da cui possono provenire i controlli di integrità di Gestione traffico. È possibile usare questo elenco per assicurarsi che le connessioni in ingresso da questi indirizzi IP siano consentite agli endpoint per controllarne lo stato di integrità.

* 40.68.30.66
* 40.68.31.178
* 137.135.80.149
* 137.135.82.249
* 23.96.236.252
* 65.52.217.19
* 40.87.147.10
* 40.87.151.34
* 13.75.124.254
* 13.75.127.63
* 52.172.155.168
* 52.172.158.37
* 104.215.91.84
* 13.75.153.124
* 13.84.222.37
* 23.101.191.199
* 23.96.213.12
* 137.135.46.163
* 137.135.47.215
* 191.232.208.52
* 191.232.214.62
* 13.75.152.253
* 104.41.187.209
* 104.41.190.203
* 52.173.90.107
* 52.173.250.232
* 104.45.149.110
* 40.114.5.197
* 52.240.151.125
* 52.240.144.45
* 13.65.95.152
* 13.65.92.252
* 40.78.67.110
* 104.42.192.195

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Qual è il numero di controlli di integrità previsto per un endpoint da parte di Gestione traffico?

Il numero di controlli di integrità eseguiti da Gestione traffico sull'endpoint dipende dagli elementi seguenti:
- Il valore impostato per l'intervallo di monitoraggio; un intervallo inferiore indica un maggior numero di richieste che raggiungono l'endpoint in un determinato periodo di tempo.
- Il numero di posizioni da cui hanno origine i controlli di integrità; gli indirizzi IP da cui possono avere origine tali controlli sono elencati nella domanda precedente.

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

di serie Quando si usano i profili annidati non si verifica alcun impatto sulle prestazioni.

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
