---
title: Come funziona Gestione traffico | Documentazione Microsoft
description: Questo articolo illustra il funzionamento di Gestione traffico di Azure
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: a6c9370d-e60d-440f-aa82-b6d3fa5416b0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 6ba1732840d094497a9e2cb7a7ea7825a7f4bc89

---

# <a name="how-traffic-manager-works"></a>Modalità di funzionamento di Gestione traffico

Gestione traffico di Azure consente di controllare in che modo il traffico viene distribuito tra gli endpoint dell'applicazione. Un endpoint è un servizio con connessione Internet ospitato all'interno o all'esterno di Azure.

Gestione traffico offre due vantaggi principali:

1. Distribuzione del traffico in base a uno dei diversi [metodi di routing del traffico](traffic-manager-routing-methods.md)
2. [Monitoraggio continuo dell'integrità degli endpoint](traffic-manager-monitoring.md) e failover automatico quando si verificano errori sugli endpoint.

Quando un client tenta di connettersi a un servizio, è necessario prima risolvere il nome DNS del servizio a un indirizzo IP. Il client si connette quindi a questo indirizzo IP per accedere al servizio.

**Il punto più importante da comprendere è che Gestione traffico lavora a livello di DNS.**  Gestione traffico usa DNS per indirizzare i client a specifici endpoint di servizio in base alle regole del metodo di routing del traffico. I client si connettono quindi **direttamente** all'endpoint selezionato. Gestione traffico non è un proxy o un gateway. Gestione traffico non visualizza il traffico tra il client e il servizio.

## <a name="traffic-manager-example"></a>Esempio di Gestione traffico

Contoso Corp ha sviluppato un nuovo portale per i partner. L'URL per questo portale è https://partners.contoso.com/login.aspx. L'applicazione è ospitata in tre aree di Azure. Per migliorare la disponibilità e ottimizzare le prestazioni globali, viene usato Gestione traffico per distribuire il traffico client all'endpoint disponibile più vicino.

Per ottenere questa configurazione:

* L'azienda distribuisce tre istanze del servizio. I nomi DNS di queste distribuzioni sono 'contoso-us.cloudapp.net', 'contoso-eu.cloudapp.net' e 'contoso-asia.cloudapp.net'.
* L'azienda crea quindi un profilo di Gestione traffico con il nome "contoso.trafficmanager.net", lo configura per usare il metodo di routing del traffico "Prestazioni" tra i 3 endpoint.
* Infine, l'azienda configura il nome di dominio personale "partners.contoso.com" in modo che punti a "contoso.trafficmanager.net" tramite il record DNS CNAME.

![Configurazione DNS di Gestione traffico][1]

> [!NOTE]
> Quando si usa un dominio personale con Gestione traffico di Azure, è necessario usare un record CNAME per scegliere il nome del dominio personale sul nome di dominio di Gestione traffico. Gli standard DNS non consentono di creare un record CNAME al "vertice" (o alla radice) di un dominio. Non è quindi possibile creare un record CNAME per "contoso.com", detto anche dominio "naked". È possibile creare solo un record CNAME per un dominio in "contoso.com", ad esempio "www.contoso.com". Per risolvere questa limitazione, è consigliabile usare un semplice reindirizzamento HTTP per indirizzare le richieste di "contoso.com" su un nome alternativo, ad esempio "www.contoso.com".

## <a name="how-clients-connect-using-traffic-manager"></a>Come si connettono i client tramite Gestione traffico

Per continuare l'esempio precedente, quando un client richiede la pagina https://partners.contoso.com/login.aspx, il client esegue i passaggi seguenti per risolvere il nome DNS e stabilire una connessione:

![Stabilire una connessione tramite Gestione traffico][2]

1. Il client invia una query DNS al relativo servizio DNS ricorsivo configurato per risolvere il nome "partners.contoso.com". Un servizio DNS ricorsivo, denominato anche servizio "DNS locale", non ospita direttamente i domini DNS, ma delega il lavoro di contattare i vari servizi DNS autorevoli su Internet necessari per risolvere un nome DNS.
2. Per risolvere il nome DNS, il servizio DNS ricorsivo cerca i server dei nomi per il dominio "contoso.com". A questo punto, il servizio contatta i server dei nomi per richiedere il record DNS di "partners.contoso.com". I server DNS contoso.com restituiscono il record CNAME che punta a contoso.trafficmanager.net.
3. Il servizio DNS ricorsivo quindi trova i server dei nomi per il dominio "trafficmanager, net", che vengono forniti dal servizio Gestione traffico di Azure. Quindi invia una richiesta per il record DNS "contoso.trafficmanager.net" a questi server DNS.
4. I server dei nomi di Gestione traffico ricevono la richiesta. Scelgono un endpoint in base a:

    - Lo stato configurato di ogni endpoint (gli endpoint disabilitati non vengono restituiti)
    - L'integrità corrente di ogni endpoint, determinata dai controlli di integrità di Gestione traffico. Per altre informazioni, vedere [Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md).
    - Il metodo di routing del traffico scelto. Per altre informazioni, vedere [Metodi di routing di Gestione traffico](traffic-manager-routing-methods.md).

5. L'endpoint scelto viene restituito come un altro record DNS CNAME. In questo caso, si supponga che venga restituito contoso-us.cloudapp.net.
6. Il servizio DNS ricorsivo cerca quindi i server dei nomi per il dominio "cloudapp.net". Il servizio contatta questi server dei nomi per richiedere il record DNS "contoso-us.cloudapp.net". Viene restituito un record DNS "A" contenente l'indirizzo IP dell'endpoint di servizio situato negli Stati Uniti.
7. Il servizio DNS ricorsivo consente di consolidare i risultati e restituisce una singola risposta DNS al client.
8. Il client riceve i risultati DNS e si connette all'indirizzo IP specificato. Il client si connette all'endpoint di servizio dell'applicazione in modo diretto, senza passare per Gestione traffico. Poiché si tratta di un endpoint HTTPS, il client esegue l'handshake SSL/TLS necessario e quindi esegue una richiesta HTTP GET per la pagina "/login.aspx".

Il servizio DNS ricorsivo memorizza nella cache le risposte DNS ricevute. Il resolver DNS nel dispositivo client memorizza nella cache anche il risultato. Il caching consente alle query DNS successive di ricevere risposte più rapide usando i dati dalla cache al posto di query ad altri server dei nomi. La durata della memorizzazione nella cache è determinata dalla proprietà "Durata" (TTL) di ogni record DNS. Valori più bassi implicano una scadenza della cache più breve e pertanto più round trip ai server dei nomi di Gestione traffico. Valori più alti indicano che potrebbe essere necessario più tempo per indirizzare il traffico da un endpoint non riuscito. Gestione traffico consente di configurare la durata (TTL) delle risposte DNS di Gestione traffico al fine di permettere all'utente di scegliere il valore ideale per bilanciare meglio le esigenze dell'applicazione.

## <a name="faq"></a>Domande frequenti

### <a name="what-ip-address-does-traffic-manager-use"></a>Quale indirizzo IP viene usato da Gestione traffico?

Come spiegato nella sezione Modalità di funzionamento di Gestione traffico, Gestione traffico funziona a livello di DNS. Invia le risposte DNS per indirizzare i client all'endpoint di servizio appropriato. I client si connettono quindi all'endpoint di servizio in modo diretto, senza passare per Gestione traffico.

Gestione traffico non prevede quindi un endpoint o indirizzo IP per la connessione dei client. Pertanto, se si vuole un indirizzo IP statico, per il servizio, questo deve essere configurato nel servizio, non in Gestione traffico.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Gestione traffico supporta sessioni "permanenti"?

Come spiegato [in precedenza](#how-clients-connect-using-traffic-manager), Gestione traffico opera a livello di DNS. Usa le risposte DNS per indirizzare i client all'endpoint di servizio appropriato. I client si connettono all'endpoint di servizio in modo diretto, senza passare per Gestione traffico. Gestione traffico non visualizza quindi il traffico HTTP tra il client e il server.

Inoltre l'indirizzo IP di origine della query DNS ricevuta da Gestione traffico appartiene al servizio DNS ricorsivo e non al client. Il servizio Gestione traffico non può quindi tracciare client singoli e non può implementare sessioni "permanenti". Questa limitazione è comune a tutti i sistemi di gestione del traffico basati su DNS e non è specifica di Gestione traffico.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Quando si usa Gestione traffico, viene visualizzato un errore HTTP. Perché?

Come spiegato [in precedenza](#how-clients-connect-using-traffic-manager), Gestione traffico opera a livello di DNS. Usa le risposte DNS per indirizzare i client all'endpoint di servizio appropriato. I client si connettono quindi all'endpoint di servizio in modo diretto, senza passare per Gestione traffico. Gestione traffico non vede il traffico HTTP tra client e server. Ogni errore HTTP visualizzato proviene quindi dall'applicazione. Per la connessione del client all'applicazione, è necessario che siano stati completati tutti i passaggi di risoluzione DNS. È inclusa qualsiasi interazione di Gestione traffico con il flusso del traffico dell'applicazione.

Eventuali verifiche più approfondite dovranno quindi concentrarsi sull'applicazione.

L'intestazione host HTTP inviata dal browser del client è l'origine dei problemi più comune. Assicurarsi che l'applicazione sia configurata per accettare l'intestazione host corretta per il nome di dominio in uso. Per gli endpoint che usano il Servizio app di Azure, vedere [Configurazione di un nome di dominio personalizzato per un'app Web nel servizio app di Azure con Gestione traffico](../app-service-web/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Qual è l'impatto sulle prestazioni dell'uso di Gestione traffico?

Come spiegato [in precedenza](#how-clients-connect-using-traffic-manager), Gestione traffico opera a livello di DNS. Dal momento che i client si connettono direttamente agli endpoint di servizio, dopo che è stata stabilita la connessione non si verifica alcun impatto sulle prestazioni.

Poiché Gestione traffico si integra con le applicazioni a livello di DNS, richiede l'inserimento di una ricerca DNS aggiuntiva nella catena di risoluzione DNS (vedere [Modalità di funzionamento di Gestione traffico](#traffic-manager-example)). L'impatto di Gestione traffico sul tempo di risoluzione DNS è minimo. Gestione traffico usa una rete globale di server dei nomi e reti [Anycast](https://en.wikipedia.org/wiki/Anycast) per garantire che le query DNS vengano sempre indirizzate al server dei nomi più vicino disponibile. Inoltre, la memorizzazione nella cache delle risposte DNS significa che la latenza DNS aggiuntiva associata all'uso di Gestione traffico si applica solo a una frazione di sessioni.

Il metodo Prestazioni instrada il traffico all'endpoint disponibile più vicino. Il risultato della rete è che l'impatto sulle prestazioni complessive associate a questo metodo dovrebbe essere minimo. Un aumento della latenza DNS deve essere compensato da minore latenza di rete all'endpoint.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Quali protocolli di applicazione possono essere usati con Gestione traffico?

Come spiegato [in precedenza](#how-clients-connect-using-traffic-manager), Gestione traffico opera a livello di DNS. Dopo il completamento della ricerca DNS, i client si connettono all'endpoint dell'applicazione direttamente, non tramite Gestione traffico. La connessione può pertanto usare un protocollo dell'applicazione. Tuttavia, i controlli dell'integrità degli endpoint di Gestione traffico richiedono un endpoint HTTP o HTTPS. L'endpoint per un controllo di integrità può essere diverso da quello dell'endpoint dell'applicazione a cui si connettono i client.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>È possibile usare Gestione traffico con un nome di dominio di tipo "naked" (senza www)?

No. Gli standard DNS non consentono la coesistenza tra record CNAME e altri record DNS dello stesso nome. Il vertice o la radice di una zona DNS contiene sempre due record DNS preesistenti: il record SOA e quello del server dei nomi autorevole. Non è quindi possibile creare un record CNAME al vertice della zona senza violare gli standard DNS.

Come spiegato nell'[esempio di Gestione traffico](#traffic-manager-example), Gestione traffico richiede un record CNAME DNS per eseguire il mapping del nome DNS personalizzato. Ad esempio, eseguire il mapping di www.contoso.com al nome DNS del profilo di Gestione traffico contoso.trafficmanager.net. Inoltre, il profilo di Gestione traffico restituisce un secondo record DNS CNAME per indicare l'endpoint a cui il client dovrebbe collegarsi.

Per risolvere questo problema, è consigliabile usare un reindirizzamento HTTP per indirizzare il traffico dal nome di dominio naked a un URL differente, che può quindi usare Gestione traffico. Ad esempio, il dominio naked "contoso.com" può reindirizzare gli utenti al dominio "www.contoso.com" CNAME, che punta al nome DNS di Gestione traffico.

Il supporto completo per i domini naked in Gestione traffico è riportato nel backlog delle funzionalità. È possibile registrare il supporto per questa funzionalità [votandolo sul sito dei commenti della community](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul [monitoraggio degli endpoint e sul failover automatico](traffic-manager-monitoring.md)di Gestione traffico.

Altre informazioni sui [metodi di routing](traffic-manager-routing-methods.md)di Gestione traffico.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png



<!--HONumber=Nov16_HO5-->


