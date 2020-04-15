---
title: Abilitazione di TLS end-to-end nel gateway applicazione di AzureEnabling end to end TLS on Azure Application Gateway
description: Questo articolo è una panoramica del supporto TLS end to end del gateway applicazione.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: ae80b49c3bfb40743665768622d3f4a8a6990c12
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311856"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Panoramica della terminazione TLS e TLS end-to-end con il gateway applicazione

Transport Layer Security (TLS), precedentemente noto come Secure Sockets Layer (SSL), è la tecnologia di sicurezza standard per stabilire un collegamento crittografato tra un server web e un browser. Questo collegamento garantisce che tutti i dati passati tra il server Web e i browser rimangano privati e crittografati. Il gateway applicazione supporta sia la terminazione TLS al gateway che la crittografia TLS end-to-end.

## <a name="tls-termination"></a>Terminazione TLS

Il gateway applicazione supporta la terminazione TLS nel gateway, dopo di che il traffico scorre in genere non crittografato verso i server back-end. L'operazione offre una serie di vantaggi all'esecuzione della terminazione TLS nel gateway applicazione:There are a number of advantages of doing TLS termination at the application gateway:

- **Prestazioni migliorate:** il più grande impatto sulle prestazioni quando si esegue la decrittografia TLS è l'handshake iniziale. Per migliorare le prestazioni, il server che esegue la decrittografia memorizza nella cache gli ID di sessione TLS e gestisce i ticket di sessione TLS. Se questa operazione viene eseguita nel gateway applicazione, tutte le richieste provenienti dallo stesso client possono utilizzare i valori memorizzati nella cache. Se viene eseguita nei server back-end, ogni volta che le richieste del client vengono eseguite in un server diverso, il client deve eseguire nuovamente l'autenticazione. L'utilizzo dei ticket TLS può aiutare a mitigare questo problema, ma non sono supportati da tutti i client e possono essere difficili da configurare e gestire.
- **Migliore utilizzo dei server back-end: l'elaborazione** SSL/TLS richiede un utilizzo intensivo della CPU e sta diventando sempre più intensivo con l'aumentare delle dimensioni delle chiavi. La rimozione di questo lavoro dai server back-end consente loro di concentrarsi su ciò in cui sono più efficienti, producendo contenuti.
- **Routing intelligente:** decrittografando il traffico, il gateway applicazione ha accesso al contenuto della richiesta, ad esempio intestazioni, URI e così via, e può usare questi dati per instradare le richieste.
- **Gestione dei certificati:** i certificati devono essere acquistati e installati solo nel gateway applicazione e non in tutti i server back-end. Ciò consente di risparmiare tempo e denaro.

Per configurare la terminazione TLS, è necessario aggiungere un certificato TLS/SSL al listener per consentire al gateway applicazione di derivare una chiave simmetrica in base alla specifica del protocollo TLS/SSL. La chiave simmetrica viene quindi utilizzata per crittografare e decrittografare il traffico inviato al gateway. Il certificato TLS/SSL deve essere in formato PFX (Personal Information Exchange). Questo formato di file consente l'esportazione della chiave privata necessaria al gateway applicazione per eseguire la crittografia e la decrittografia del traffico.

> [!NOTE] 
>
> Il gateway applicazione non fornisce alcuna funzionalità per creare un nuovo certificato o inviare una richiesta di certificato a un'autorità di certificazione.

Affinché la connessione TLS funzioni, è necessario assicurarsi che il certificato TLS/SSL soddisfi le seguenti condizioni:

- La data e l'ora correnti sono compresi nell'intervallo di date "Valido da" e "Valido a" sul certificato.
- Il "nome comune" del certificato deve corrispondere all'intestazione host nella richiesta. Se ad esempio il client effettua una richiesta a `https://www.contoso.com/`, il nome comune del certificato deve essere `www.contoso.com`.

### <a name="certificates-supported-for-tls-termination"></a>Certificati supportati per la terminazione TLS

Il gateway applicazione supporta i seguenti tipi di certificati:

- Certificato CA (Autorità di certificazione): un certificato CA è un certificato digitale emesso da un'autorità di certificazione (CA)
- Certificato EV (Convalida estesa): un certificato EV è un certificato conforme alle linee guida del certificato standard del settore. Questo trasformerà la barra del localizzatore del browser verde e pubblicherà anche il nome della società.
- Certificato con caratteri jolly: questo certificato supporta un numero qualsiasi di sottodomini basati su site.com, in cui il sottodominio sostituirà il file . Tuttavia, non supporta site.com, quindi nel caso in cui gli utenti accedano al tuo sito web senza digitare il "www" iniziale, il certificato con caratteri jolly non coprirà questo.
- Certificati autofirmati: i browser client non considerano attendibili questi certificati e avvisano l'utente che il certificato del servizio virtuale non fa parte di una catena di attendibilità. I certificati autofirmati sono adatti per i test o per gli ambienti in cui gli amministratori controllano i client e possono ignorare in modo sicuro gli avvisi di sicurezza del browser. I carichi di lavoro di produzione non devono mai usare certificati autofirmati.

Per ulteriori informazioni, vedere [configurare la terminazione TLS con il gateway applicazione](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Dimensione del certificato
Controllare la sezione [Limiti del gateway applicazione](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) per conoscere la dimensione massima del certificato TLS/SSL supportata.

## <a name="end-to-end-tls-encryption"></a>Crittografia TLS end-to-end

Alcuni clienti potrebbero non desiderare comunicazioni non crittografate con i server back-end. Questo potrebbe dipendere da requisiti di sicurezza e conformità o da un'applicazione in grado di accettare solo connessioni protette. Per tali applicazioni, il gateway applicazione supporta la crittografia TLS end-to-end.

TLS end-to-end consente di trasmettere in modo sicuro i dati sensibili al back-end crittografati pur sfruttando i vantaggi delle funzionalità di bilanciamento del carico di livello 7 offerte dal gateway applicazione. Alcune di queste funzionalità sono l'affinità di sessione basata su cookie, il routing basato su URL, il supporto per il routing basato su siti o la possibilità di inserire intestazioni X-Forwarded-*.

Se configurato con la modalità di comunicazione TLS end-to-end, il gateway applicazione termina le sessioni TLS nel gateway e decrittografa il traffico utente. Applica quindi le regole configurate per selezionare un'istanza del pool di back-end adeguata su cui instradare il traffico. Il gateway applicazione avvia quindi una nuova connessione TLS al server back-end e crittografa nuovamente i dati utilizzando il certificato di chiave pubblica del server back-end prima di trasmettere la richiesta al back-end. Eventuali risposte dal server Web subiscono lo stesso processo in ritorno verso l'utente finale. TLS end-to-end viene abilitato impostando l'impostazione del protocollo in [Backend HTTP Setting](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) su HTTPS, che viene quindi applicato a un pool back-end.

Il criterio TLS si applica al traffico front-end e back-end. Nel front-end, il gateway applicazione funge da server e applica i criteri. Sul back-end, Il gateway applicazione funge da client e invia le informazioni di protocollo/cifratura come preferenza durante l'handshake TLS.

Il gateway applicazione comunica solo con le istanze back-end che hanno inserito il certificato nella whitelist con il gateway applicazione o i cui certificati sono firmati da autorità CA note in cui il CN del certificato corrisponde al nome host nelle impostazioni di back-end HTTP. Questi includono i servizi di Azure attendibili, ad esempio le app Web del servizio app di Azure e Gestione API di Azure.These include the trusted Azure services such as Azure App service web apps and Azure API Management.

Se i certificati dei membri del pool back-end non sono firmati da autorità CA note, ogni istanza nel pool back-end con TLS end-to-end abilitato deve essere configurata con un certificato per consentire comunicazioni protette. L'aggiunta del certificato garantisce che il gateway applicazione comunichi solo con istanze di back-end note, proteggendo ulteriormente la comunicazione end-to-end.

> [!NOTE] 
>
> La configurazione del certificato di autenticazione non è necessaria per i servizi di Azure attendibili, ad esempio le app Web del servizio app di Azure e Gestione API di Azure.Authentication certificate setup is not required for trusted Azure services such as Azure App service web apps and Azure API Management.

> [!NOTE] 
>
> Il certificato aggiunto **all'impostazione HTTP back-end** per autenticare i server back-end può essere lo stesso del certificato aggiunto al **listener** per la terminazione TLS nel gateway dell'applicazione o diverso per una sicurezza avanzata.

![scenario tls end-to-end][1]

In questo esempio, le richieste che utilizzano TLS1.2 vengono instradate ai server back-end in Pool1 utilizzando TLS end-end.

## <a name="end-to-end-tls-and-whitelisting-of-certificates"></a>TLS end-to-end e whitelisting dei certificati

Il gateway applicazione comunica solo con istanze back-end note, il cui certificato è incluso nell'elenco dei consentiti del gateway applicazione. Per abilitare l'aggiunta dei certificati all'elenco dei consentiti, è necessario caricare nel gateway applicazione la chiave pubblica dei certificati dei server back-end (non il certificato radice). Sono quindi consentite solo le connessioni a back-end noti e inclusi nell'elenco. Gli altri back-end generano un errore del gateway. I certificati autofirmati vengono usati a scopo di test e non sono consigliati per i carichi di lavoro. Prima dell'uso, questi certificati devono essere aggiunti all'elenco dei consentiti nel gateway applicazione, come descritto nei passaggi precedenti.

> [!NOTE]
> L'installazione di certificati di autenticazione non è necessaria per servizi di Azure attendibili, come ad esempio Servizio app di Azure.

## <a name="end-to-end-tls-with-the-v2-sku"></a>TLS end-to-end con lo SKU v2

I certificati di autenticazione sono stati deprecati e sostituiti da certificati radice attendibili nello SKU v2 gateway applicazione. Funzionano in modo analogo ai certificati di autenticazione con alcune differenze principali:

- I certificati firmati da autorità CA note il cui CN corrisponde al nome host nelle impostazioni di back-end HTTP non richiedono alcun passaggio aggiuntivo per il funzionamento di TLS end-to-end. 

   Ad esempio, se i certificati di back-end sono rilasciati da un'autorità di certificazione nota il cui nome comune è contoso.com e anche il campo host delle impostazioni HTTP di back-end è impostato su contoso.com, non sono necessarie altre operazioni. È possibile impostare il protocollo di impostazione http back-end su HTTPS e sia il probe di integrità che il percorso dei dati saranno abilitati per TLS. Se si usa il servizio app di Azure o altri servizi Web di Azure come back-end, anche questi sono implicitamente attendibili e non sono necessari ulteriori passaggi per il TLS end-to-end.
   
> [!NOTE] 
>
> Affinché un certificato TLS/SSL sia considerato attendibile, tale certificato del server back-end deve essere stato emesso da una CA inclusa nell'archivio attendibile del gateway applicazione.Se il certificato non è stato emesso da una CA attendibile, il gateway applicazione controllerà se il certificato della CA emittente è stato emesso da una CA attendibile e così via fino a quando non viene trovata una CA attendibile (a quel punto verrà stabilita una connessione sicura e attendibile) o non sarà possibile trovare alcuna CA attendibile (a quel punto l'applicazione non verrà trovata). Pertanto, è consigliabile che il certificato del server back-end contenga sia le CA radice che le CA intermedie.

- Se il certificato è autofirmato o firmato da intermediari sconosciuti, per abilitare TLS end-to-end nella sKU v2 è necessario definire un certificato radice attendibile. Il gateway applicazione comunicherà solo con back-end il cui certificato radice del certificato del server corrisponde a uno dei certificati dell'elenco dei certificati radice attendibili nell'impostazione HTTP di back-end associata al pool.

> [!NOTE] 
>
> Il certificato autofirmato deve far parte di una catena di certificati. Un singolo certificato autofirmato senza catena non è supportato nello SKU V2.

- Oltre alla corrispondenza del certificato radice, il gateway applicazione convalida anche se l'impostazione Host specificata nell'impostazione http back-end corrisponde a quella del nome comune (CN) presentata dal certificato TLS/SSL del server back-end. Quando si tenta di stabilire una connessione TLS al back-end, Gateway applicazione imposta l'estensione Server Name Indication (SNI) per l'host specificato nell'impostazione http back-end.
- Se si **sceglie seleziona nome host dall'indirizzo back-end** anziché il campo Host nell'impostazione http back-end, l'intestazione SNI è sempre impostata sull'FQDN del pool back-end e il CN nel server back-end TLS/SSL deve corrispondere al relativo FQDN. I membri del pool back-end con indirizzi IP non sono supportati in questo scenario.
- Il certificato radice è un certificato radice codificato con codifica base64 dai certificati del server back-end.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso che si è appreso che si desidera conoscere TLS end-to-end, passare a [Configurare TLS end-to-end usando il gateway applicazione con PowerShell](application-gateway-end-to-end-ssl-powershell.md) per creare un gateway applicazione usando TLS end-to-end.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
