<properties
   pageTitle="Come funziona Gestione traffico | Microsoft Azure"
   description="Questo articolo fornisce informazioni sul funzionamento di Gestione traffico"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn"/>

<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/07/2016"
   ms.author="sewhee"/>

# Modalità di funzionamento di Gestione traffico

Gestione traffico di Azure consente di controllare in che modo il traffico viene distribuito tra gli endpoint dell'applicazione. L'endpoint può essere qualsiasi endpoint con connessione Internet, situato all'interno o all'esterno di Azure.

Gestione traffico offre due vantaggi principali:

1. Distribuzione del traffico in base a uno dei diversi [metodi di routing del traffico](traffic-manager-routing-methods.md).
2. [Monitoraggio continuo dell'integrità degli endpoint](traffic-manager-monitoring.md) e failover automatico quando si verificano errori sugli endpoint.

Quando un utente finale tenta di connettersi all'endpoint di servizio, i relativi client (PC, telefono e così via) devono innanzitutto risolvere il nome DNS in questo endpoint sull'indirizzo IP. Il client si connette quindi a questo indirizzo IP per accedere al servizio.

**Il punto più importante da comprendere è che Gestione traffico lavora a livello di DNS.** Gestione traffico usa DNS per indirizzare gli utenti finali sugli endpoint di servizio specifici, in base al metodo di routing del traffico scelto e all'integrità dell'endpoint corrente. I client si connettono quindi **direttamente** agli endpoint selezionati. Gestione traffico non è un proxy e non visualizza il traffico tra il client e il servizio.

## Esempio di Gestione traffico

Contoso Corp ha sviluppato un nuovo portale per i partner. L'URL del portale è https://partners.contoso.com/login.aspx. L'applicazione è ospitata in Azure e, al fine di migliorare la disponibilità e ottimizzare le prestazioni globali, l'azienda desidera distribuire l'applicazione su 3 aree geografiche in tutto il mondo e usare Gestione traffico per distribuire gli utenti finali sull'endpoint disponibile più vicino.

Per ottenere questa configurazione:

- L'azienda distribuisce 3 istanze del servizio. I nomi DNS di queste distribuzioni sono "contoso-us.cloudapp.net", "contoso-eu.cloudapp.net" e "contoso-asia.cloudapp.net".
- L'azienda crea quindi un profilo di Gestione traffico con il nome "contoso.trafficmanager.net", configurato per usare il metodo di routing del traffico "Performance" tra i 3 endpoint indicati in alto.
- Infine, l'azienda configura il dominio personale "partners.contoso.com" in modo che punti a "contoso.trafficmanager.net" tramite il record DNS CNAME.

![Configurazione DNS di Gestione traffico][1]

> [AZURE.NOTE] Quando si usa un dominio personale con Gestione traffico di Azure, è necessario usare un record CNAME per scegliere il nome del dominio personale sul nome di dominio di Gestione traffico.

> A causa di una restrizione degli standard DNS, non è possibile creare un record CNAME sul "vertice" (o sulla radice) di un dominio. Non è quindi possibile creare un record CNAME per "contoso.com" (detto anche dominio "naked"). È possibile creare solo un record CNAME per un dominio contenuto in "contoso.com", ad esempio "www.contoso.com".

> Non è quindi possibile usare direttamente Gestione traffico con un dominio naked. Per risolvere il problema, è consigliabile usare un semplice reindirizzamento HTTP per indirizzare le richieste di "contoso.com" su un nome alternativo, ad esempio "www.contoso.com".

## Come si connettono i client tramite Gestione traffico

Quando un utente richiede la pagina https://partners.contoso.com/login.aspx (come descritto nell'esempio precedente), il client esegue i passaggi seguenti per risolvere il nome DNS e stabilire una connessione.

![Stabilire una connessione tramite Gestione traffico][2]

1.	Il client (PC, telefono e così via) esegue una query DNS per "partners.contoso.com" sul servizio DNS ricorsivo configurato. Un servizio DNS ricorsivo, anche denominato servizio "DNS locale", non ospita direttamente i domini DNS, ma viene usato dal client per delegare il lavoro di contattare i vari servizi DNS autorevoli su Internet necessari per risolvere un nome DNS.
2.	Il servizio DNS ricorsivo risolve il nome DNS "partners.contoso.com". Innanzitutto, il servizio DNS ricorsivo cerca i server dei nomi per il dominio "contoso.com". A questo punto, il servizio contatta i server dei nomi per richiedere il record DNS di "partners.contoso.com". Viene restituito il record CNAME su contoso.trafficmanager.net.
3.	Il servizio DNS ricorsivo trova i server dei nomi per il dominio "trafficmanager, net", che vengono forniti dal servizio Gestione traffico di Azure. Il servizio contatta questi server dei nomi per richiedere il record DNS di "contoso.trafficmanager.net".
4.	I server dei nomi di Gestione traffico ricevono la richiesta. I server scelgono quindi l'endpoint da restituire, in base a: a. Lo stato abilitato/disabilitato di ogni endpoint (gli endpoint disabilitati non vengono restituiti). b. L'integrità corrente di ogni endpoint, determinata dai controlli di integrità di Gestione traffico. Per altre informazioni, vedere Traffic Manager Endpoint Monitoring (Monitoraggio degli endpoint di Gestione traffico). c. Il metodo di routing del traffico scelto. Per altre informazioni, vedere Metodi di routing di Gestione traffico.
5.	L'endpoint scelto viene restituito come un altro record DNS CNAME. In questo caso, si supponga che venga restituito contoso-us.cloudapp.net.
6.	Il servizio DNS ricorsivo cerca ora i server dei nomi per il dominio "cloudapp.net". Il servizio contatta questi server dei nomi per richiedere il record DNS di "contoso-us.cloudapp.net". Viene restituito un record DNS "A" contenente l'indirizzo IP dell'endpoint di servizio situato negli Stati Uniti.
7.	Il servizio DNS ricorsivo restituisce al client i risultati consolidati della sequenza di risoluzioni dei nomi illustrata in alto.
8.	Il client riceve i risultati DNS dal servizio DNS ricorsivo e si connette all'indirizzo IP specificato. Si noti che il client si connette all'endpoint di servizio dell'applicazione in modo diretto, senza passare per Gestione traffico. Poiché si tratta di un endpoint HTTPS, viene eseguito l'handshake SSL/TLS necessario e quindi si esegue una richiesta HTTP GET per la pagina "/login.aspx".

Si noti che il servizio DNS ricorsivo memorizza nella cache le risposte DNS che riceve, così come agirà il client DNS sul dispositivo dell'utente finale. In questo modo le query DNS successive riceveranno risposte più rapide usando i dati dalla cache al posto di query ad altri server dei nomi. La durata della memorizzazione nella cache è determinata dalla proprietà "Durata" (TTL) di ogni record DNS. I valori più brevi generano una scadenza più rapida della cache e quindi un numero maggiore di round trip al server dei nomi di Gestione traffico, mentre una lunghezza superiore dei valori può richiedere più tempo per l'allontanamento del traffico da un endpoint compromesso. Gestione traffico consente di configurare la durata (TTL) delle risposte DNS di Gestione traffico al fine di permettere all'utente di scegliere il valore ideale per bilanciare meglio le esigenze dell'applicazione.

## Domande frequenti

### Quale indirizzo IP viene usato da Gestione traffico?

Come spiegato nella sezione Modalità di funzionamento di Gestione traffico, Gestione traffico funziona a livello di DNS. Usa le risposte DNS per indirizzare i client all'endpoint di servizio appropriato. I client si connettono quindi all'endpoint di servizio in modo diretto, senza passare per Gestione traffico.

Gestione traffico non prevede quindi un endpoint o indirizzo IP per la connessione dei client. Pertanto, se ad esempio è richiesto un indirizzo IP statico, questo deve essere configurato nel servizio, non in Gestione traffico.

### Gestione traffico supporta sessioni "permanenti"?

Come spiegato [in precedenza](#how-clients-connect-using-traffic-manager), Gestione traffico lavora a livello di DNS. Usa le risposte DNS per indirizzare i client all'endpoint di servizio appropriato. I client si connettono quindi all'endpoint di servizio in modo diretto, senza passare per Gestione traffico. Pertanto, Gestione traffico non vede il traffico HTTP tra client e server, compresi i cookie.

Si noti anche che l'indirizzo IP di origine della query DNS ricevuta da Gestione traffico è l'indirizzo IP del servizio DNS ricorsivo e non quello del client.

Il servizio Gestione traffico non può quindi identificare o tracciare client singoli e, pertanto, non può implementare sessioni "permanenti". Ciò è comune a tutti i sistemi di gestione del traffico basati su DNS e non è una restrizione all'uso specifica di Gestione traffico.

### Quando si usa Gestione traffico, viene visualizzato un errore HTTP. Perché?

Come spiegato [in precedenza](#how-clients-connect-using-traffic-manager), Gestione traffico lavora a livello di DNS. Usa le risposte DNS per indirizzare i client all'endpoint di servizio appropriato. I client si connettono quindi all'endpoint di servizio in modo diretto, senza passare per Gestione traffico.

Pertanto, il servizio Gestione traffico non vede il traffico HTTP tra client e server e non può generare errori a livello HTTP. Ogni errore HTTP visualizzato proviene dall'applicazione. Poiché il client si connette all'applicazione, questo significa anche che è necessario che la risoluzione DNS, incluso il ruolo di Gestione traffico, sia stata completata.

Eventuali verifiche più approfondite dovranno quindi concentrarsi sull'applicazione.

Un problema comune è che, quando si usa Gestione traffico, l'intestazione HTTP "host" passata dal browser all'applicazione mostra il nome di dominio usato nel browser. Potrebbe essere il nome di dominio di Gestione traffico (ad esempio myprofile.trafficmanager.net) se questo viene usato durante il test oppure potrebbe trattarsi del dominio personalizzato CNAME configurato in modo da fare riferimento al nome di dominio di Gestione traffico. In entrambi i casi, controllare che l'applicazione sia configurata per accettare l'intestazione host.

Se l'applicazione è ospitata nel servizio app di Azure, vedere [Configurazione di un nome di dominio personalizzato per un'app Web nel servizio app di Azure con Gestione traffico](../app-service-web/web-sites-traffic-manager-custom-domain-name.md).

### Qual è l'impatto sulle prestazioni dell'uso di Gestione traffico?

Come spiegato [in precedenza](#how-clients-connect-using-traffic-manager), Gestione traffico lavora a livello di DNS. Usa le risposte DNS per indirizzare i client all'endpoint di servizio appropriato. I client si connettono quindi all'endpoint di servizio in modo diretto, senza passare per Gestione traffico.

Dal momento che i client si connettono direttamente agli endpoint di servizio, dopo che è stata stabilita la connessione non si verifica alcun impatto sulle prestazioni.

Poiché Gestione traffico si integra con le applicazioni a livello di DNS, richiede l'inserimento di una ricerca DNS aggiuntiva nella catena di risoluzione DNS (vedere [Modalità di funzionamento di Gestione traffico](#traffic-manager-example)). L'impatto di Gestione traffico sul tempo di risoluzione DNS è minimo. Gestione traffico usa una rete globale di server dei nomi e reti Anycast per garantire che le query DNS vengano sempre indirizzate al server dei nomi più vicino disponibile. Inoltre, la memorizzazione nella cache delle risposte DNS significa che la latenza DNS aggiuntiva associata all'uso di Gestione traffico si applica solo a una frazione di sessioni.

Il risultato è che l'impatto sulle prestazioni complessive associato all'inclusione di Gestione traffico nell'applicazione dovrebbe essere minimo.

Inoltre, quando si usa il [metodo di routing del traffico "Prestazioni"](traffic-manager-routing-methods.md#performance-traffic-routing-method) di Gestione traffico, l'incremento nella latenza DNS dovrebbe essere abbondantemente compensato dal miglioramento nelle prestazioni ottenuto tramite il routing degli utenti finali all'endpoint disponibile più vicino.

### Quali protocolli di applicazione possono essere usati con Gestione traffico?
Come spiegato [in precedenza](#how-clients-connect-using-traffic-manager), Gestione traffico lavora a livello di DNS. Dopo il completamento della ricerca DNS, i client si connettono all'endpoint dell'applicazione direttamente, non tramite Gestione traffico. La connessione può pertanto usare un protocollo dell'applicazione.

Tuttavia, i controlli dell'integrità degli endpoint di Gestione traffico richiedono un endpoint HTTP o HTTPS. Questo può essere distinto dall'endpoint dell'applicazione a cui si connettono i client specificando una porta TCP diversa o un percorso URI nelle impostazioni di controllo dell'integrità del profilo di Gestione traffico.

### È possibile usare Gestione traffico con un nome di dominio "naked" (senza www)?

No, per il momento.

Il tipo di record DNS CNAME viene usato per creare un mapping da un nome DNS a un altro nome. Come spiegato nella sezione [Modalità di funzionamento di Gestione traffico](#traffic-manager-example), Gestione traffico richiede un record DNS CNAME per eseguire il mapping di un nome DNS personalizzato (ad esempio www.contoso.com) al nome DNS del profilo di Gestione traffico (ad esempio contoso.trafficmanager.net). Inoltre, il profilo stesso di Gestione traffico restituisce un secondo record DNS CNAME per indicare l'endpoint a cui il client dovrebbe collegarsi.

Gli standard DNS non consentono la coesistenza tra record CNAME e altri record DNS dello stesso tipo. Poiché il vertice (o radice) di una zona DNS contiene sempre due record DNS esistenti (i record SOA ed NS autorevoli), non è possibile creare un record CNAME al vertice della zona senza violare gli standard DNS.

Per risolvere il problema, è consigliabile che i servizi con dominio naked (senza www) che vogliono usare Gestione traffico usino un reindirizzamento HTTP per indirizzare il traffico dal dominio naked a un URL diverso, che potrà quindi usare Gestione traffico. Ad esempio, il dominio naked "contoso.com" può reindirizzare gli utenti al dominio "www.contoso.com", che può quindi usare Gestione traffico.

Il supporto completo per i domini naked in Gestione traffico è riportato nel backlog delle funzionalità. Se si è interessati a questa funzionalità, registrare il supporto [votandolo sul sito dei commenti della community](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

## Passaggi successivi

Altre informazioni sul [monitoraggio degli endpoint e sul failover automatico](traffic-manager-monitoring.md) di Gestione traffico.

Altre informazioni sui [metodi di routing](traffic-manager-routing-methods.md) di Gestione traffico.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

<!---HONumber=AcomDC_0824_2016-->