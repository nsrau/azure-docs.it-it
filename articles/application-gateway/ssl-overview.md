---
title: Abilitazione di TLS end-to-end nel gateway applicazione di Azure
description: Questo articolo offre un'introduzione al supporto di TLS end-to-end del gateway applicazione.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: victorh
ms.openlocfilehash: 3d714b579bebb096745a47410da3f8f458e27161
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88723300"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Panoramica della terminazione TLS e di TLS end-to-end con il gateway applicazione

Transport Layer Security (TLS), noto in precedenza come Secure Sockets Layer (SSL), è la tecnologia di sicurezza standard per stabilire un collegamento crittografato tra un server Web e un browser. Questo collegamento garantisce che tutti i dati passati tra il server Web e i browser rimangano privati e crittografati. Il gateway applicazione supporta sia la terminazione TLS nel gateway che la crittografia TLS end-to-end.

## <a name="tls-termination"></a>Terminazione TLS

Il gateway applicazione supporta la terminazione TLS nel gateway, dopo la quale il traffico scorre generalmente non crittografato verso i server back-end. La terminazione TLS nel gateway applicazione offre numerosi vantaggi:

- **Miglioramento delle prestazioni** - L'operazione con l'impatto maggiore sulle prestazioni quando si esegue la decrittografia TLS è l'handshake iniziale. Per migliorare le prestazioni, il server che esegue la decrittografia memorizza nella cache gli ID di sessione TLS e gestisce i ticket della sessione TLS. Se questa operazione viene eseguita nel gateway applicazione, tutte le richieste dallo stesso client possono usare i valori memorizzati nella cache. Se viene eseguita nei server back-end, ogni volta che le richieste del client passano a un server diverso, il client deve ripetere l'autenticazione. L'uso di ticket TLS può contribuire a mitigare questo problema, ma non è supportato da tutti i client e può essere difficile da configurare e gestire.
- **Utilizzo migliore dei server back-end** - L'elaborazione SSL/TLS richiede un utilizzo intensivo della CPU, che aumenta con l'aumentare delle dimensioni delle chiavi. La rimozione di questa operazione dai server back-end consente loro di concentrarsi sull'operazione per cui sono più efficienti, ovvero la distribuzione di contenuti.
- **Routing intelligente** - Tramite la decrittografia del traffico il gateway applicazione ha accesso al contenuto delle richieste, ad esempio intestazioni, URI e così via, e può usare questi dati per il routing delle richieste.
- **Gestione dei certificati** - I certificati devono essere acquistati e installati solo nel gateway applicazione e non in tutti i server back-end. Questo consente di risparmiare tempo e denaro.

Per configurare la terminazione TLS, è necessario aggiungere un certificato TLS/SSL al listener per consentire al gateway applicazione di derivare una chiave simmetrica in base alla specifica del protocollo TLS/SSL. La chiave simmetrica viene quindi usata per crittografare e decrittografare il traffico inviato al gateway. Il certificato TLS/SSL deve essere in formato PFX (Personal Information Exchange). Questo formato di file consente l'esportazione della chiave privata necessaria al gateway applicazione per eseguire la crittografia e la decrittografia del traffico.

> [!IMPORTANT] 
> Il certificato nel listener richiede che l'intera catena di certificati venga caricata (il certificato radice dalla CA, i intermedi e il certificato foglia) per stabilire la catena di attendibilità. 


> [!NOTE] 
>
> Il gateway applicazione non fornisce alcuna funzionalità per creare un nuovo certificato o inviare una richiesta di certificato a un'autorità di certificazione.

Per il corretto funzionamento della connessione TLS, è necessario assicurarsi che il certificato TLS/SSL soddisfi le condizioni seguenti:

- La data e l'ora corrente devono trovarsi all'interno dell'intervallo "Valido da" e "Valido fino a" del certificato.
- Il "nome comune" del certificato deve corrispondere all'intestazione host nella richiesta. Se ad esempio il client effettua una richiesta a `https://www.contoso.com/`, il nome comune del certificato deve essere `www.contoso.com`.

### <a name="certificates-supported-for-tls-termination"></a>Certificati supportati per la terminazione TLS

Il gateway applicazione supporta i tipi di certificati seguenti:

- Certificato dell'autorità di certificazione (CA): un certificato CA è un certificato digitale emesso da un'autorità di certificazione (CA)
- Certificato di convalida estesa: un certificato di convalida esta è un certificato conforme alle linee guida per i certificati standard del settore. In questo modo la barra del localizzatore del browser diventa verde e viene pubblicato anche il nome della società.
- Certificato con caratteri jolly: questo certificato supporta un numero qualsiasi di sottodomini basati su *.sito.com, in cui il sottodominio sostituirà *. Tuttavia, non supporta sito.com, quindi, nel caso in cui gli utenti accedano al sito Web senza digitare "www", il certificato con caratteri jolly non lo coprirà.
- Certificati autofirmati: i browser client non considerano attendibili questi certificati e avvisano l'utente che il certificato del servizio virtuale non fa parte di una catena di certificati. I certificati autofirmati sono utili per i test o gli ambienti in cui gli amministratori controllano i client e possono ignorare in modo sicuro gli avvisi di sicurezza del browser. I carichi di lavoro di produzione non devono mai usare certificati autofirmati.

Per altre informazioni, vedere [Configurare la terminazione TLS con il gateway applicazione](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Dimensioni del certificato
Per informazioni sulle dimensioni massime supportate per i certificati TLS/SSL, vedere la sezione [Limiti del gateway applicazione](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits).

## <a name="end-to-end-tls-encryption"></a>Crittografia TLS end-to-end

È possibile che si vogliano evitare comunicazioni non crittografate con i server back-end, per soddisfare requisiti di sicurezza o di conformità specifici o perché l'applicazione può accettare solo connessioni sicure. Il gateway applicazione di Azure include la crittografia TLS end-to-end per supportare questi requisiti.

TLS end-to-end consente di crittografare e trasmettere in modo sicuro i dati sensibili al back-end mentre si usano le funzionalità di bilanciamento del carico di livello 7 del gateway applicazione. Queste funzionalità includono l'affinità di sessione basata su cookie, il routing basato su URL, il supporto per il routing basato su siti, la possibilità di riscrivere o inserire intestazioni X-Forwarded-* e così via.

Se configurato con la modalità di comunicazione TLS end-to-end, il gateway applicazione termina le sessioni TLS nel gateway ed esegue la decrittografia del traffico utente. Applica quindi le regole configurate per selezionare un'istanza del pool di back-end adeguata su cui instradare il traffico. Il gateway applicazione avvia a questo punto una nuova connessione TLS al server back-end e crittografa nuovamente i dati usando il certificato di chiave pubblica del server back-end prima di trasmettere la richiesta al back-end. Eventuali risposte dal server Web subiscono lo stesso processo in ritorno verso l'utente finale. Per abilitare TLS end-to-end si imposta la configurazione del protocollo nell'[impostazione HTTP di back-end](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) su HTTPS. Questa impostazione viene quindi applicata a un pool back-end.

Per il gateway applicazione e lo SKU WAF v1, i criteri TLS si applicano sia al traffico front-end che al traffico back-end. Nel front-end il gateway applicazione funge da server e impone i criteri. Nel back-end il gateway applicazione funge da client e invia le informazioni di protocollo/crittografia come preferenza durante l'handshake TLS.

Per il gateway applicazione e lo SKU WAF v2, i criteri TLS si applicano solo al traffico front-end e tutte le crittografie vengono offerte al server back-end, che ha il controllo per selezionare crittografie specifiche e la versione di TLS durante l'handshake.

Il gateway applicazione comunica solo con i server back-end che consentono di elencare il certificato con il gateway applicazione o i cui certificati sono firmati da autorità di certificazione note e il CN del certificato corrisponde al nome host nelle impostazioni del back-end HTTP. Questi includono i servizi di Azure attendibili, come Servizio app di Azure/App Web e Gestione API di Azure.

Se i certificati dei membri nel pool back-end non sono firmati da autorità di certificazione note, ogni istanza nel pool back-end con TLS end-to-end abilitato deve essere configurata con un certificato per consentire la comunicazione protetta. L'aggiunta del certificato garantisce che il gateway applicazione comunichi solo con istanze di back-end note, proteggendo ulteriormente la comunicazione end-to-end.

> [!NOTE] 
>
> Il certificato aggiunto all'**impostazione HTTP di back-end** per autenticare i server back-end può essere lo stesso del certificato aggiunto al **listener** per la terminazione TLS nel gateway applicazione o diverso per maggiore sicurezza.

![Scenario di TLS end-to-end][1]

In questo esempio, le richieste che usano TLS1.2 vengono instradate ai server back-end in Pool1 con TLS end-to-end.

## <a name="end-to-end-tls-and-allow-listing-of-certificates"></a>TLS end-to-end e Consenti l'elenco dei certificati

Il gateway applicazione comunica solo con istanze di back-end note che consentono di elencare il certificato con il gateway applicazione. Esistono alcune differenze nel processo di configurazione di TLS end-to-end a seconda della versione del gateway applicazione usata. Nella sezione seguente vengono illustrate singolarmente.

## <a name="end-to-end-tls-with-the-v1-sku"></a>TLS end-to-end con lo SKU v1

Per abilitare TLS end-to-end con i server back-end e in modo che il gateway applicazione instradi le richieste a tali server, i probe di integrità devono avere esito positivo e restituire una risposta di integrità.

Per i probe di integrità HTTPS, lo SKU v1 del gateway applicazione usa una corrispondenza esatta del certificato di autenticazione (chiave pubblica del certificato del server back-end e non del certificato radice) da caricare nelle impostazioni HTTP.

Sono quindi consentite solo le connessioni a backend noti e consentiti. I backend rimanenti sono considerati non integri dai probe di integrità. I certificati autofirmati vengono usati a scopo di test e non sono consigliati per i carichi di lavoro. Questi certificati devono essere consentiti nell'elenco con il gateway applicazione, come descritto nei passaggi precedenti prima di poter essere usati.

> [!NOTE]
> La configurazione di un certificato di autenticazione e un certificato radice trusted non è necessaria per servizi di Azure attendibili, come Servizio app di Azure. Vengono considerati attendibili per impostazione predefinita.

## <a name="end-to-end-tls-with-the-v2-sku"></a>TLS end-to-end con lo SKU v2

I certificati di autenticazione sono stati deprecati e sostituiti da certificati radice attendibili nello SKU v2 gateway applicazione. Funzionano in modo analogo ai certificati di autenticazione con alcune differenze principali:

- I certificati firmati da autorità di certificazione note il cui nome comune corrisponde al nome host nelle impostazioni HTTP di back-end non richiedono alcun passaggio aggiuntivo per il funzionamento di TLS end-to-end. 

   Ad esempio, se i certificati di back-end sono rilasciati da un'autorità di certificazione nota il cui nome comune è contoso.com e anche il campo host delle impostazioni HTTP di back-end è impostato su contoso.com, non sono necessarie altre operazioni. Se si configura il protocollo HTTPS nell'impostazione HTTP di back-end, sia il probe di integrità che il percorso dei dati saranno abilitati per TLS. Se si usano Servizio app di Azure o altri servizi Web di Azure come back-end, anche questi sono considerati implicitamente attendibili e non sono necessari altri passaggi per TLS end-to-end.
   
> [!NOTE] 
>
> Affinché un certificato TLS/SSL venga considerato attendibile, tale certificato del server back-end deve essere stato emesso da un'autorità di certificazione nota. Se il certificato non è stato emesso da un'autorità di certificazione attendibile, il gateway applicazione verificherà quindi se il certificato della CA emittente è stato emesso da una CA attendibile e così via fino a quando non viene rilevata una CA attendibile (e a questo punto viene stabilita una connessione sicura attendibile) o non è possibile trovare una CA attendibile (e a questo punto il gateway applicazione contrassegna il back-end come non integro). È quindi consigliabile che il certificato del server back-end contenga sia la CA radice che le CA intermedie.

- Se il certificato è autofirmato o firmato da intermediari sconosciuti, per abilitare TLS end-to-end nello SKU v2 deve essere definito un certificato radice attendibile. Il gateway applicazione comunica solo con back-end il cui certificato radice del certificato del server corrisponde a uno dei certificati dell'elenco dei certificati radice attendibili nell'impostazione HTTP di back-end associata al pool.

- Oltre alla corrispondenza del certificato radice, il gateway applicazione v2 verifica anche se l'impostazione Host specificata nell'impostazione HTTP di back-end corrisponde a quella del nome comune presentata dal certificato TLS/SSL del server back-end. Quando si tenta di stabilire una connessione TLS al back-end, il gateway applicazione v2 imposta l'estensione Indicazione nome server (SNI) sull'host specificato nell'impostazione HTTP di back-end.

- Se si sceglie **Seleziona nome host dalla destinazione back-end** anziché il campo host nell'impostazione http back-end, l'intestazione SNI è sempre impostata sul nome di dominio completo del pool back-end e il CN nel server back-end del certificato TLS/SSL deve corrispondere al relativo nome di dominio completo. I membri del pool di back-end con indirizzi IP non sono supportati in questo scenario.

- Il certificato radice è un certificato radice con codifica base64 dai certificati del server back-end.

## <a name="sni-differences-in-the-v1-and-v2-sku"></a>Differenze di SNI per gli SKU v1 e v2

Come indicato in precedenza, il gateway applicazione termina il traffico TLS dal client nel listener del gateway applicazione (o connessione front-end), decrittografa il traffico, applica le regole necessarie per determinare il server back-end a cui deve essere inoltrata la richiesta e stabilisce una nuova sessione TLS con il server back-end (o connessione back-end).

Le tabelle seguenti illustrano le differenze di SNI tra lo SKU v1 e v2 in termini di connessioni front-end e back-end.

### <a name="frontend-tls-connection-client-to-application-gateway"></a>Connessione TLS front-end (dal client al gateway applicazione)

---
Scenario | v1 | v2 |
| --- | --- | --- |
| Se il client specifica l'intestazione SNI e tutti i listener multisito sono abilitati con il flag "Require SNI" (Richiedi SNI) | Restituisce il certificato appropriato e, se il sito non esiste (in base a server_name), la connessione viene reimpostata. | Restituisce il certificato appropriato, se disponibile. In caso contrario, restituisce il certificato del primo listener HTTPS configurato (in ordine)|
| Se il client non specifica un'intestazione SNI e se tutte le intestazioni multisito sono abilitate con "Require SNI" (Richiedi SNI) | Reimposta la connessione | Restituisce il certificato del primo listener HTTPS configurato (in ordine)
| Se il client non specifica l'intestazione SNI e se è configurato un listener di base con un certificato | Restituisce il certificato configurato nel listener di base al client (certificato predefinito o di fallback) | Restituisce il certificato del primo listener HTTPS configurato (in ordine) |

### <a name="backend-tls-connection-application-gateway-to-the-backend-server"></a>Connessione TLS back-end (gateway applicazione al server back-end)

#### <a name="for-probe-traffic"></a>Per il traffico probe

---
Scenario | v1 | v2 |
| --- | --- | --- |
| Intestazione SNI (server_name) durante l'handshake TLS come FQDN | Imposta come FQDN dal pool back-end. In base a [RFC 6066](https://tools.ietf.org/html/rfc6066), gli indirizzi letterali IPv4 e IPv6 non sono consentiti nel nome host SNI. <br> **Nota:** il nome di dominio completo nel pool back-end deve essere risolto da DNS nell'indirizzo IP del server back-end (pubblico o privato) | L'intestazione SNI (server_name) è impostata come nome host dal probe personalizzato associato alle impostazioni HTTP (se configurato) oppure dal nome host indicato nelle impostazioni HTTP oppure dal nome di dominio completo indicato nel pool back-end. L'ordine di precedenza è probe personalizzato > impostazioni HTTP > pool back-end. <br> **Nota:** se i nomi host configurati in impostazioni HTTP e il probe personalizzato sono diversi, in base all'ordine di precedenza SNI verrà impostato come nome host dal probe personalizzato.
| Se l'indirizzo del pool back-end è un indirizzo IP (v1) o se il nome host del probe personalizzato è configurato come indirizzo IP (v2) | SNI (server_name) non verrà impostato. <br> **Nota:** In questo caso, il server back-end deve essere in grado di restituire un certificato predefinito/di fallback e questo dovrebbe essere consentito nell'elenco impostazioni HTTP in certificato di autenticazione. Se nel server back-end non sono configurati certificati predefiniti/di fallback ed è previsto SNI, il server potrebbe reimpostare la connessione con conseguenti errori di probe | Nell'ordine di precedenza sopra indicato, in presenza di un indirizzo IP come nome host, l'indicazione SNI non verrà impostata come da [RFC 6066](https://tools.ietf.org/html/rfc6066). <br> **Nota:** l'indicazione SNI non verrà impostata nei probe v2 anche se non viene configurato alcun probe personalizzato e non viene impostato alcun nome host nelle impostazioni HTTP o nel pool back-end |

> [!NOTE] 
> Se non è configurato un probe personalizzato, il gateway applicazione invia un probe predefinito nel formato- \<protocol\> ://127.0.0.1: \<port\> /. Un probe HTTPS predefinito, ad esempio, verrà inviato come https://127.0.0.1:443/. Si noti che il nome 127.0.0.1 qui indicato viene usato solo come intestazione host HTTP e come da RFC 6066 non verrà usato come intestazione SNI. Per altre informazioni sugli errori relativi ai probe di integrità, vedere la [guida alla risoluzione dei problemi di integrità di back-end](application-gateway-backend-health-troubleshooting.md).

#### <a name="for-live-traffic"></a>Per il traffico in tempo reale

---
Scenario | v1 | v2 |
| --- | --- | --- |
| Intestazione SNI (server_name) durante l'handshake TLS come FQDN | Imposta come FQDN dal pool back-end. In base a [RFC 6066](https://tools.ietf.org/html/rfc6066), gli indirizzi letterali IPv4 e IPv6 non sono consentiti nel nome host SNI. <br> **Nota:** il nome di dominio completo nel pool back-end deve essere risolto da DNS nell'indirizzo IP del server back-end (pubblico o privato) | L'intestazione SNI (server_name) viene impostata come nome host dalle impostazioni HTTP. In caso contrario, se è stata scelta l'opzione *PickHostnameFromBackendAddress* o se non è stato specificato alcun nome host, verrà impostata come nome di dominio completo nella configurazione del pool back-end.
| Se l'indirizzo del pool back-end è un indirizzo IP o un nome host non è impostato nelle impostazioni HTTP | L'indicazione SNI non verrà impostata come da [RFC 6066](https://tools.ietf.org/html/rfc6066) se la voce del pool back-end non è un nome di dominio completo | L'indicazione SNI verrà impostata come nome host dal nome di dominio completo di input dal client e il CN del certificato back-end deve corrispondere a questo nome host.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso i concetti di TLS end-to-end, passare a [Configurare TLS end-to-end usando un gateway applicazione con PowerShell](application-gateway-end-to-end-ssl-powershell.md) per creare un gateway applicazione usando TLS end-to-end.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
