---
title: Gestione traffico di Azure - Domande frequenti | Documentazione Microsoft
description: Questo articolo risponde ad alcune domande frequenti su Gestione traffico
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 8c4c8db3cf57537dd77d33b3ded2dc24167f511f
ms.lasthandoff: 03/25/2017

---

# <a name="traffic-manager-frequently-asked-questions-faq"></a>Domande frequenti (FAQ) su Gestione traffico

## <a name="traffic-manager-basics"></a>Fondamenti di Gestione traffico

### <a name="what-ip-address-does-traffic-manager-use"></a>Quale indirizzo IP viene usato da Gestione traffico?

Come spiegato nella sezione [Modalità di funzionamento di Gestione traffico](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Gestione traffico funziona a livello di DNS. Invia le risposte DNS per indirizzare i client all'endpoint di servizio appropriato. I client si connettono quindi all'endpoint di servizio in modo diretto, senza passare per Gestione traffico.

Gestione traffico non prevede quindi un endpoint o indirizzo IP per la connessione dei client. Pertanto, se si vuole un indirizzo IP statico, per il servizio, questo deve essere configurato nel servizio, non in Gestione traffico.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Gestione traffico supporta sessioni "permanenti"?

Come spiegato nella sezione [Modalità di funzionamento di Gestione traffico](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Gestione traffico funziona a livello di DNS. Usa le risposte DNS per indirizzare i client all'endpoint di servizio appropriato. I client si connettono all'endpoint di servizio in modo diretto, senza passare per Gestione traffico. Gestione traffico non visualizza quindi il traffico HTTP tra il client e il server.

Inoltre l'indirizzo IP di origine della query DNS ricevuta da Gestione traffico appartiene al servizio DNS ricorsivo e non al client. Il servizio Gestione traffico non può quindi tracciare client singoli e non può implementare sessioni "permanenti". Questa limitazione è comune a tutti i sistemi di gestione del traffico basati su DNS e non è specifica di Gestione traffico.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Quando si usa Gestione traffico, viene visualizzato un errore HTTP. Perché?

Come spiegato nella sezione [Modalità di funzionamento di Gestione traffico](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Gestione traffico funziona a livello di DNS. Usa le risposte DNS per indirizzare i client all'endpoint di servizio appropriato. I client si connettono quindi all'endpoint di servizio in modo diretto, senza passare per Gestione traffico. Gestione traffico non vede il traffico HTTP tra client e server. Ogni errore HTTP visualizzato proviene quindi dall'applicazione. Per la connessione del client all'applicazione, è necessario che siano stati completati tutti i passaggi di risoluzione DNS. È inclusa qualsiasi interazione di Gestione traffico con il flusso del traffico dell'applicazione.

Eventuali verifiche più approfondite dovranno quindi concentrarsi sull'applicazione.

L'intestazione host HTTP inviata dal browser del client è l'origine dei problemi più comune. Assicurarsi che l'applicazione sia configurata per accettare l'intestazione host corretta per il nome di dominio in uso. Per gli endpoint che usano il Servizio app di Azure, vedere [Configurazione di un nome di dominio personalizzato per un'app Web nel servizio app di Azure con Gestione traffico](../app-service-web/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Qual è l'impatto sulle prestazioni dell'uso di Gestione traffico?

Come spiegato nella sezione [Modalità di funzionamento di Gestione traffico](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Gestione traffico funziona a livello di DNS. Dal momento che i client si connettono direttamente agli endpoint di servizio, dopo che è stata stabilita la connessione non si verifica alcun impatto sulle prestazioni.

Poiché Gestione traffico si integra con le applicazioni a livello di DNS, richiede l'inserimento di una ricerca DNS aggiuntiva nella catena di risoluzione DN. L'impatto di Gestione traffico sul tempo di risoluzione DNS è minimo. Gestione traffico usa una rete globale di server dei nomi e reti [Anycast](https://en.wikipedia.org/wiki/Anycast) per garantire che le query DNS vengano sempre indirizzate al server dei nomi più vicino disponibile. Inoltre, la memorizzazione nella cache delle risposte DNS significa che la latenza DNS aggiuntiva associata all'uso di Gestione traffico si applica solo a una frazione di sessioni.

Il metodo Prestazioni instrada il traffico all'endpoint disponibile più vicino. Il risultato della rete è che l'impatto sulle prestazioni complessive associate a questo metodo dovrebbe essere minimo. Un aumento della latenza DNS deve essere compensato da minore latenza di rete all'endpoint.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Quali protocolli di applicazione possono essere usati con Gestione traffico?

Come spiegato nella sezione [Modalità di funzionamento di Gestione traffico](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Gestione traffico funziona a livello di DNS. Dopo il completamento della ricerca DNS, i client si connettono all'endpoint dell'applicazione direttamente, non tramite Gestione traffico. La connessione può pertanto usare un protocollo dell'applicazione. Tuttavia, i controlli dell'integrità degli endpoint di Gestione traffico richiedono un endpoint HTTP o HTTPS. L'endpoint per un controllo di integrità può essere diverso da quello dell'endpoint dell'applicazione a cui si connettono i client.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>È possibile usare Gestione traffico con un nome di dominio di tipo "naked" (senza www)?

No. Gli standard DNS non consentono la coesistenza tra record CNAME e altri record DNS dello stesso nome. Il vertice o la radice di una zona DNS contiene sempre due record DNS preesistenti: il record SOA e quello del server dei nomi autorevole. Non è quindi possibile creare un record CNAME al vertice della zona senza violare gli standard DNS.

Gestione traffico richiede un record CNAME DNS per eseguire il mapping del nome DNS personalizzato. Ad esempio, eseguire il mapping di www.contoso.com al nome DNS del profilo di Gestione traffico contoso.trafficmanager.net. Inoltre, il profilo di Gestione traffico restituisce un secondo record DNS CNAME per indicare l'endpoint a cui il client dovrebbe collegarsi.

Per risolvere questo problema, è consigliabile usare un reindirizzamento HTTP per indirizzare il traffico dal nome di dominio naked a un URL differente, che può quindi usare Gestione traffico. Ad esempio, il dominio naked "contoso.com" può reindirizzare gli utenti al dominio "www.contoso.com" CNAME, che punta al nome DNS di Gestione traffico.

Il supporto completo per i domini naked in Gestione traffico è riportato nel backlog delle funzionalità. È possibile registrare il supporto per questa funzionalità [votandolo sul sito dei commenti della community](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Gestione traffico tiene conto dell'indirizzo della subnet client quando si gestiscono query DNS? 
No, in questa fase Gestione traffico tiene conto dell'indirizzo IP di origine della query DNS che riceve, che nella maggior parte dei casi è l'indirizzo IP del resolver DNS, solo durante l'esecuzione di ricerche dei metodi di routing Geografico e Prestazioni.  
In particolare, in [RFC7871 – Client Subnet in DNS Queries](https://tools.ietf.org/html/rfc7871) (RFC7871 – Subnet client nelle query DNS), che indica un [meccanismo di estensione per DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) che può trasferire l'indirizzo della subnet client dai resolver che lo supportano ai server DNS, non è attualmente supportata in Gestione traffico. È possibile registrare il supporto per la richiesta di questa funzionalità sul [sito di commenti e suggerimenti della community](https://feedback.azure.com/forums/217313-networking).


## <a name="traffic-manager-geographic-traffic-routing-method"></a>Metodi geografico di routing del traffico di Gestione traffico

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Quali sono alcuni casi di uso in cui il routing geografico è utile? 
Il tipo di routing Geografico può essere usato in qualsiasi scenario in cui un cliente di Azure abbia bisogno di distinguere gli utenti in base alle aree geografiche. Ad esempio, tramite il metodo di routing del traffico Geografico è possibile offrire agli utenti di una determinata area un'esperienza utente diversa rispetto a quelli di altre aree. Un altro esempio è la necessità di garantire la conformità con requisiti dei dati locali che richiedono di servire gli utenti di una determinata area solo con gli endpoint di tale area.

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Quali sono le aree supportate da Gestione traffico per il routing geografico? 
La gerarchia di paese/area geografica utilizzata da Gestione traffico è reperibile [qui](traffic-manager-geographic-regions.md). La pagina verrà aggiornata con ogni modifica, ma è possibile recuperare le stesse informazioni anche a livello programmatico usando l'[API REST di Gestione traffico di Azure](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>In che modo Gestione traffico determina da dove un utente sta eseguendo una query? 
Gestione traffico esamina l'indirizzo IP di origine della query (probabilmente un sistema di risoluzione DNS locale starà eseguendo la query per conto dell'utente) e usa un indirizzo IP interno per eseguire il mapping delle aree al fine di determinare la posizione. Questa mappa viene aggiornata regolarmente per tenere conto delle modifiche di Internet. 

### <a name="is-it-guaranteed-that-traffic-manager-will-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>È garantito che Gestione traffico determini correttamente l'esatta posizione geografica dell'utente in ogni caso?
No, Gestione traffico non può garantire che l'area geografica che si deduce dall'indirizzo IP di origine di una query DNS corrisponda sempre alla posizione dell'utente, per i motivi seguenti: 

- In primo luogo, come indicato nella domanda precedente, l'indirizzo IP di origine visualizzato è quello di un resolver DNS che esegue la ricerca per conto dell'utente. La posizione geografica del resolver DNS è un proxy valido per la posizione geografica dell'utente ma può anche essere diversa, a seconda della superficie del servizio resolver DNS e dello specifico servizio resolver DNS che un cliente sceglie di usare. Ad esempio, un cliente che si trova in Malaysia può specificare nelle impostazioni del dispositivo l'uso di un servizio resolver DNS il cui server DNS a Singapore potrebbe essere scelto per gestire le risoluzioni di query per l'utente o il dispositivo specifico. In questo caso, Gestione traffico visualizzerà solo l'indirizzo IP del resolver corrispondente alla località Singapore. Vedere anche le domande frequenti precedenti, disponibili in questa pagina, relative al supporto dell'indirizzo della subnet client.

- In secondo luogo, Gestione traffico usa una mappa interna per tradurre l'indirizzo IP nell'area geografica. Anche se questa mappa viene convalidata e aggiornata in modo continuativo per aumentarne la precisione e tenere conto della natura in costante evoluzione di Internet, è comunque possibile che le informazioni contenute non rappresentino esattamente la posizione geografica di tutti gli indirizzi IP.


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Un endpoint deve trovarsi fisicamente nella stessa area di quello con cui è configurato per il routing geografico? 
No, il percorso dell'endpoint non impone alcuna restrizione in merito alle aree a cui può essere mappato. Ad esempio è possibile che tutti gli utenti dell'India siano indirizzati a un endpoint dell'area Stati Uniti centrali di Azure.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>È possibile assegnare aree geografiche agli endpoint in un profilo che non è configurato per eseguire il routing geografico? 
Sì, se il metodo di routing di un profilo non è Geografico è possibile usare l'[API REST di Gestione traffico di Azure](https://docs.microsoft.com/rest/api/trafficmanager/) per assegnare aree geografiche agli endpoint del profilo. Nel caso di profili con un tipo di routing non geografico, questa configurazione verrà ignorata. Se si cambia un profilo di questo tipo nel tipo a routing geografico in un secondo momento, Gestione traffico userà quei mapping.


### <a name="when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic-i-am-getting-an-error"></a>Quando si tenta di cambiare il metodo di routing di un profilo esistente in geografico, si riceve un errore?
Deve esserci almeno un'area mappata per tutti gli endpoint in un profilo con routing geografico. Per convertire un profilo esistente al tipo di routing geografico è innanzitutto necessario associare aree geografiche a tutti gli endpoint tramite l'[API REST di Gestione traffico di Azure](https://docs.microsoft.com/rest/api/trafficmanager/) prima di cambiare il tipo di routing in geografico. Se si usa il portale, è necessario innanzitutto eliminare gli endpoint, cambiare il metodo di routing del profilo in geografico e quindi aggiungere gli endpoint con i relativi mapping di area geografica. 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Perché è decisamente consigliato che i clienti creino profili nidificati anziché endpoint in un profilo con il routing geografico abilitato? 
Un'area può essere assegnata a un solo endpoint all'interno di un profilo se usa il tipo di routing geografico. Se tale endpoint non è un tipo annidato con un profilo figlio collegato, nel caso l'endpoint perdesse l'integrità, Gestione traffico continuerà a inviare traffico a esso in quanto l'alternativa di non inviare alcun traffico non è migliore. Gestione traffico non esegue il failover su un altro endpoint, anche quando l'area assegnata è "padre" dell'area assegnata all'endpoint danneggiato (ad esempio, se un endpoint che include l'area Spagna perde la sua integrità, non si eseguirà il failover su un altro endpoint con assegnata l'area Europa). Lo scopo di tutto questo è garantire che Gestione traffico rispetti i confini geografici che un cliente ha stabilito nel proprio profilo. Per ottenere il vantaggio del failover su un altro endpoint quando un endpoint perde la sua integrità, è consigliabile assegnare le aree geografiche a profili annidati con più endpoint all'interno singoli endpoint. In questo modo se un endpoint nel profilo figlio annidato non funziona, il traffico può eseguire il failover su un altro endpoint all'interno dello stesso profilo figlio annidato.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Ci sono restrizioni sulla versione API che supporta questo tipo di routing?

Sì, solo l'API versione 2017-03-01 e versioni successive supportano il routing di tipo geografico. Le versioni precedenti dell'API non possono essere usate per creare profili con routing di tipo Geografico o assegnare aree geografiche agli endpoint. Se viene usata una versione precedente dell'API per recuperare i profili da una sottoscrizione di Azure, non verranno restituiti profili con routing di tipo Geografico. Inoltre, quando si usano versioni precedenti dell'API, tutti i profili restituiti che hanno endpoint con un'area geografica assegnata non mostreranno l'area geografica assegnata.



## <a name="traffic-manager-endpoints"></a>Endpoint di Gestione traffico

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>È possibile usare Gestione traffico con endpoint di più sottoscrizioni?

L'uso di endpoint di più sottoscrizioni non è possibile con app Web di Azure. Per le app Web di Azure è necessario che ogni nome di dominio personalizzato usato con app Web venga usato solo all'interno di una singola sottoscrizione. Non è possibile usare app Web da più sottoscrizioni con lo stesso nome di dominio.

Per altri tipi di endpoint, è possibile utilizzare Gestione traffico con gli endpoint da più di una sottoscrizione. La configurazione di Gestione traffico varia a seconda che si usi il modello di distribuzione classica o l'esperienza Resource Manager.

* In Resource Manager è possibile aggiungere endpoint di qualsiasi sottoscrizione a Gestione traffico, purché la persona che configura il profilo di Gestione traffico abbia accesso in lettura all'endpoint. Queste autorizzazioni possono essere concesse tramite il [controllo di accesso in base al ruolo di Azure Resource Manager](../active-directory/role-based-access-control-configure.md).
* Nell'interfaccia del modello di distribuzione classica Gestione traffico richiede che tutti i servizi cloud e le app Web configurati come endpoint di Azure si trovino nella stessa sottoscrizione del profilo di Gestione traffico. È possibile aggiungere endpoint del servizio cloud di altre sottoscrizioni a Gestione traffico come endpoint esterni. Questi endpoint esterni vengono fatturati come endpoint di Azure e non in base alla tariffa degli endpoint esterni.

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>È possibile usare Gestione traffico con slot di "staging" del servizio cloud?

Sì. Gli slot di "staging" del servizio cloud possono essere configurati come endpoint esterni in Gestione traffico. I controlli di integrità vengono fatturati in base alla tariffa degli endpoint di Azure. Dato che viene usato il tipo di endpoint esterno, le modifiche al servizio sottostante non vengono rilevate automaticamente. Se si usano endpoint esterni, Gestione traffico non è in grado di rilevare l'eventuale arresto o eliminazione del servizio cloud. Di conseguenza, Gestione traffico continua a fatturare i controlli di integrità fino a quando l'endpoint non viene disabilitato o eliminato.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Gestione traffico supporta endpoint IPv6?

Attualmente Gestione traffico non fornisce server dei nomi indirizzabili tramite IPv6. Può comunque essere usato da client IPv6 che si connettono a endpoint IPv6. Un client non invia richieste DNS direttamente a Gestione traffico, ma usa un servizio DNS ricorsivo. Un client solo IPv6 invia richieste al servizio DNS ricorsivo tramite IPv6. Il servizio ricorsivo può quindi contattare i server dei nomi di Gestione traffico tramite IPv4.

Gestione traffico risponde con il nome DNS dell'endpoint. Per supportare un endpoint IPv6, è necessaria la presenza di un record AAAA DNS che punti il nome DNS dell'endpoint all'indirizzo IPv6. I controlli di integrità di Gestione traffico supportano soltanto gli indirizzi IPv4. Il servizio deve esporre un endpoint IPv4 sullo stesso nome DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>È possibile usare Gestione traffico con più app Web nella stessa area?

In genere, Gestione traffico viene usato per indirizzare il traffico ad applicazioni distribuite in aree diverse. Tuttavia, può anche essere usato in un'applicazione che abbia più distribuzioni nella stessa area. Gli endpoint di Azure di Gestione traffico non permettono l'aggiunta di più endpoint di app Web della stessa area di Azure allo stesso profilo di Gestione traffico.

I passaggi seguenti offrono una soluzione alternativa a questo vincolo:

1. Verificare che gli endpoint si trovino in unità di scala diverse dell'app Web. Un nome di dominio deve eseguire il mapping a un unico sito in un'unità di scala specificata. Di conseguenza, due app Web nella stessa unità di scala non possono condividere un profilo di Gestione traffico.
2. Aggiungere il nome di dominio personale come nome host personalizzato a ogni app Web. Ogni App Web deve trovarsi in un'unità di scala diversa. Tutte le app Web devono appartenere alla stessa sottoscrizione.
3. Aggiungere un unico endpoint di app Web al profilo di Gestione traffico, come endpoint di Azure.
4. Aggiungere gli altri endpoint di app Web al profilo di Gestione traffico come endpoint esterni. È possibile aggiungere endpoint esterni solo usando il modello di distribuzione di Resource Manager.
5. Nel proprio dominio personale creare un record CNAME DNS che punti al nome DNS del profilo di Gestione traffico (<...>.trafficmanager.net).
6. Accedere al sito tramite il nome di dominio personalizzato anziché dal nome DNS del profilo di Gestione traffico.

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

In Azure non vengono visualizzate informazioni cronologiche sull'integrità precedente degli endpoint e non è possibile generare avvisi sulle modifiche dell'integrità degli endpoint.

### <a name="can-i-monitor-https-endpoints"></a>È possibile monitorare gli endpoint HTTPS?

Sì. Gestione traffico supporta il probing su HTTPS. Definire **HTTPS** come protocollo nella configurazione del monitoraggio.

Gestione traffico non prevede alcuna convalida di certificati, tra cui:

* I certificati sul lato server non vengono convalidati
* I certificati SNI sul lato server non sono supportati
* I certificati client non sono supportati

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

## <a name="traffic-manager-nested-profiles"></a>Profili annidati di Gestione traffico

### <a name="how-do-i-configure-nested-profiles"></a>Come si configurano i profili nidificati?

I profili annidati di Gestione traffico possono essere configurati usando Azure Resource Manager, le API REST di Azure classico, i cmdlet di Azure PowerShell e i comandi multipiattaforma dell'interfaccia della riga di comando di Azure. Sono inoltre supportati anche tramite il nuovo portale di Azure. Non sono invece supportati nel portale classico.

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
