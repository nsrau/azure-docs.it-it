<properties
   pageTitle="Come funziona Gestione traffico | Microsoft Azure"
   description="Questo articolo fornisce informazioni sul funzionamento di Gestione traffico"
   services="traffic-manager"
   documentationCenter=""
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn"/>

<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/25/2016"
   ms.author="jonatul"/>

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

## Passaggi successivi

Altre informazioni sul [monitoraggio degli endpoint e sul failover automatico](traffic-manager-monitoring.md) di Gestione traffico.

Altre informazioni sui [metodi di routing](traffic-manager-routing-methods.md) di Gestione traffico.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

<!---HONumber=AcomDC_0525_2016-->