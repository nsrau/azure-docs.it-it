---
title: Abilitazione di SSL end-to-end nel gateway applicazione Azure
description: Questo articolo offre un'introduzione al supporto di SSL end-to-end del gateway applicazione.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 199fcdf2ebf10852906b842f09fe7beafd2acdb5
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326618"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Panoramica della terminazione SSL e del protocollo SSL end-to-end con il gateway applicazione

Secure Sockets Layer (SSL) è la tecnologia di sicurezza standard per stabilire un collegamento crittografato tra un server Web e un browser. Questo collegamento garantisce che tutti i dati passati tra il server Web e i browser rimangano privati e crittografati. Il gateway applicazione supporta la terminazione SSL nel gateway, nonché la crittografia SSL end-to-end.

## <a name="ssl-termination"></a>Terminazione SSL

Il gateway applicazione supporta la terminazione SSL nel gateway, dopo la quale il traffico scorre generalmente non crittografato verso i server back-end. La terminazione SSL nel gateway applicazione presenta diversi vantaggi:

- **Miglioramento delle prestazioni** : il maggiore impatto sulle prestazioni quando si esegue la decrittografia SSL è l'handshake iniziale. Per migliorare le prestazioni, il server che esegue la decrittografia memorizza nella cache gli ID di sessione SSL e gestisce i ticket della sessione TLS. Se questa operazione viene eseguita nel gateway applicazione, tutte le richieste dello stesso client possono usare i valori memorizzati nella cache. Se viene eseguita nei server back-end, ogni volta che le richieste del client passano a un server diverso, il client deve eseguire di nuovo l'autenticazione. L'uso di ticket TLS può contribuire a mitigare questo problema, ma non è supportato da tutti i client e può essere difficile da configurare e gestire.
- **Maggiore utilizzo dei server back-end: l'** elaborazione SSL/TLS richiede un utilizzo intensivo della CPU e sta aumentando il numero di dimensioni delle chiavi. La rimozione di questo lavoro dai server back-end consente loro di concentrarsi su ciò che sono più efficienti in, per la distribuzione di contenuti.
- **Routing intelligente** : tramite la decrittografia del traffico, il gateway applicazione ha accesso al contenuto della richiesta, ad esempio intestazioni, Uri e così via, e può usare questi dati per indirizzare le richieste.
- **Gestione dei certificati** : è necessario acquistare e installare solo i certificati nel gateway applicazione e non in tutti i server back-end. Questo consente di risparmiare tempo e denaro.

Per configurare la terminazione SSL, è necessario aggiungere un certificato SSL al listener per consentire al gateway applicazione di derivare una chiave simmetrica come specifica del protocollo SSL. La chiave simmetrica viene quindi usata per crittografare e decrittografare il traffico inviato al gateway. Il certificato SSL deve essere in formato PFX (Personal Information Exchange). Questo formato di file consente l'esportazione della chiave privata necessaria al gateway applicazione per eseguire la crittografia e la decrittografia del traffico.

> [!NOTE] 
>
> Il gateway applicazione non fornisce alcuna funzionalità per creare un nuovo certificato o inviare una richiesta di certificato a un'autorità di certificazione.

Per il corretto funzionamento della connessione SSL, è necessario assicurarsi che il certificato SSL soddisfi le condizioni seguenti:

- Che la data e l'ora correnti siano comprese nell'intervallo di date "valido da" e "valido a" del certificato.
- Il "nome comune" del certificato deve corrispondere all'intestazione host nella richiesta. Se ad esempio il client effettua una richiesta a `https://www.contoso.com/`, il nome comune del certificato deve essere `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Certificati supportati per la terminazione SSL

Il gateway applicazione supporta i tipi di certificati seguenti:

- Certificato CA (autorità di certificazione): Un certificato CA è un certificato digitale emesso da un'autorità di certificazione (CA)
- Certificato EV (Extended Validation): Un certificato EV è un certificato standard di settore. In questo modo verrà riattivata la barra di localizzazione del browser e verrà pubblicato anche il nome della società.
- Certificato con caratteri jolly: Questo certificato supporta un numero qualsiasi di sottodomini basati su *. site.com, in cui il sottodominio sostituirà *. Tuttavia, non supporta site.com, quindi, nel caso in cui gli utenti accedano al sito Web senza digitare il "www", il certificato con caratteri jolly non lo coprirà.
- Certificati autofirmati: I browser client non considerano attendibile questi certificati e avvisano l'utente che il certificato del servizio virtuale non fa parte di una catena di trust. I certificati autofirmati sono utili per i test o gli ambienti in cui gli amministratori controllano i client e possono ignorare in modo sicuro gli avvisi di sicurezza del browser. I carichi di lavoro di produzione non devono mai usare certificati autofirmati.

Per altre informazioni, vedere [configurare la terminazione SSL con il gateway applicazione](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Dimensioni del certificato
Per informazioni sulle dimensioni massime del certificato SSL supportate, vedere la sezione [limiti del gateway applicazione](https://docs.microsoft.com/azure/azure-subscription-service-limits#application-gateway-limits) .

## <a name="end-to-end-ssl-encryption"></a>Crittografia SSL end-to-end

Alcuni clienti potrebbero non volere la comunicazione non crittografata con i server back-end. Questo potrebbe dipendere da requisiti di sicurezza e conformità o da un'applicazione in grado di accettare solo connessioni protette. Per tali applicazioni, il gateway applicazione supporta ora la crittografia SSL end-to-end.

La crittografia SSL end-to-end consente di trasmettere in modo sicuro dati sensibili crittografati al back-end, usufruendo comunque dei vantaggi delle funzionalità di bilanciamento del carico di livello 7 offerte dal gateway applicazione. Alcune di queste funzionalità sono l'affinità di sessione basata su cookie, il routing basato su URL, il supporto per il routing basato su siti o la possibilità di inserire intestazioni X-Forwarded-*.

Se configurato con la modalità di comunicazione SSL end-to-end, il gateway applicazione termina le sessioni SSL nel gateway ed esegue la decrittografia del traffico utente. Applica quindi le regole configurate per selezionare un'istanza del pool di back-end adeguata su cui instradare il traffico. Il gateway applicazione avvia a questo punto una nuova connessione SSL al server back-end e crittografa nuovamente i dati usando il certificato di chiave pubblica del server back-end prima di trasmettere la richiesta al back-end. Eventuali risposte dal server Web subiscono lo stesso processo in ritorno verso l'utente finale. SSL end-to-end viene abilitato impostando l'impostazione del protocollo nell' [impostazione http back](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) -end su HTTPS, che viene quindi applicata a un pool back-end.

I criteri SSL si applicano al traffico front-end e back-end. Sul front-end, il gateway applicazione funge da server e impone i criteri. Nel back-end, il gateway applicazione funge da client e invia le informazioni di protocollo/crittografia come preferenza durante l'handshake SSL.

Il gateway applicazione comunica solo con le istanze back-end in cui il certificato è stato inserito nell'elenco elementi consentiti con il gateway applicazione o i cui certificati sono firmati da autorità di certificazione note in cui il CN del certificato corrisponde al nome host in HTTP impostazioni back-end. Questi includono i servizi di Azure attendibili, come app Azure app Web del servizio e gestione API di Azure.

Se i certificati dei membri nel pool back-end non sono firmati da autorità di certificazione note, ogni istanza nel pool back-end con SSL end-to-end abilitato deve essere configurata con un certificato per consentire la comunicazione protetta. L'aggiunta del certificato garantisce che il gateway applicazione comunichi solo con istanze di back-end note, proteggendo ulteriormente la comunicazione end-to-end.

> [!NOTE] 
>
> La configurazione del certificato di autenticazione non è necessaria per i servizi di Azure attendibili, ad esempio le app Web del servizio app Azure e gestione API di Azure.

> [!NOTE] 
>
> Il certificato aggiunto all' **impostazione http back-end** per autenticare i server back-end può essere lo stesso del certificato aggiunto al **listener** per la terminazione SSL nel gateway applicazione o diverso per una maggiore sicurezza.

![Scenario di SSL end-to-end][1]

In questo esempio, le richieste che usano TLS1.2 vengono instradate ai server back-end in Pool1 con SSL end-to-end.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>SSL end-to-end e aggiunta dei certificati all'elenco dei consentiti

Il gateway applicazione comunica solo con istanze back-end note, il cui certificato è incluso nell'elenco dei consentiti del gateway applicazione. Per abilitare l'aggiunta dei certificati all'elenco dei consentiti, è necessario caricare nel gateway applicazione la chiave pubblica dei certificati dei server back-end (non il certificato radice). Sono quindi consentite solo le connessioni a back-end noti e inclusi nell'elenco. Gli altri back-end generano un errore del gateway. I certificati autofirmati vengono usati a scopo di test e non sono consigliati per i carichi di lavoro. Prima dell'uso, questi certificati devono essere aggiunti all'elenco dei consentiti nel gateway applicazione, come descritto nei passaggi precedenti.

> [!NOTE]
> L'installazione di certificati di autenticazione non è necessaria per servizi di Azure attendibili, come ad esempio Servizio app di Azure.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>SSL end-to-end con SKU v2

I certificati di autenticazione sono stati deprecati e sostituiti da certificati radice attendibili nello SKU v2 gateway applicazione. Funzionano in modo analogo ai certificati di autenticazione con alcune differenze principali:

- I certificati firmati da autorità di certificazione note il cui nome comune corrisponde al nome host nelle impostazioni HTTP di back-end non richiedono alcun passaggio aggiuntivo per il funzionamento di SSL end-to-end. 

   Ad esempio, se i certificati di back-end sono rilasciati da un'autorità di certificazione nota il cui nome comune è contoso.com e anche il campo host delle impostazioni HTTP di back-end è impostato su contoso.com, non sono necessarie altre operazioni. Se si configura il protocollo come HTTPS nell'impostazione HTTP di back-end, sia il probe di integrità che il percorso dei dati saranno abilitati per SSL. Se si usa app Azure servizio o altri servizi Web di Azure come back-end, anche questi vengono considerati implicitamente attendibili e non sono necessari altri passaggi per SSL end-to-end.
   
> [!NOTE] 
>
> Affinché un certificato SSL possa essere considerato attendibile, il certificato del server back-end deve essere stato emesso da un'autorità di certificazione inclusa nell'archivio attendibile del gateway applicazione. se il certificato non è stato emesso da una CA attendibile, il gateway applicazione verificherà verificare se il certificato della CA emittente è stato emesso da un'autorità di certificazione attendibile e così via fino a quando non viene trovata una CA attendibile (a questo punto viene stabilita una connessione protetta attendibile) o non è possibile trovare alcuna autorità di certificazione attendibile. a quel punto il gateway applicazione contrassegna il Unhe back-end althy). È quindi consigliabile che il certificato del server back-end contenga sia le CA radice che intermidie.

- Se il certificato è autofirmato o firmato da intermediari sconosciuti, per abilitare SSL end-to-end nello SKU v2 deve essere definito un certificato radice attendibile. Il gateway applicazione comunicherà solo con back-end il cui certificato radice del certificato del server corrisponde a uno dei certificati dell'elenco dei certificati radice attendibili nell'impostazione HTTP di back-end associata al pool.

> [!NOTE] 
>
> Il certificato autofirmato deve essere parte di una catena di certificati. Un certificato autofirmato singolo senza catena non è supportato nello SKU V2.

- Oltre alla corrispondenza del certificato radice, il gateway applicazione verifica anche se l'impostazione Host specificata nell'impostazione HTTP di back-end corrisponde a quella del nome comune presentata dal certificato SSL del server back-end. Quando si tenta di stabilire una connessione SSL al back-end, il gateway applicazione imposta l'estensione Indicazione nome server (SNI) sull'host specificato nell'impostazione HTTP di back-end.
- Se si sceglie **pick hostname from backend address** (selezionare il nome host dall'indirizzo di back-end) anziché il campo Host nell'impostazione HTTP di back-end, l'intestazione SNI è sempre impostata sul nome di dominio completo del pool di back-end e il nome comune sul certificato SSL del server back-end deve corrispondere al nome di dominio completo. I membri del pool back-end con indirizzi IP non sono supportati in questo scenario.
- Il certificato radice è un certificato radice codificato con codifica base64 dai certificati del server back-end.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso i concetti di SSL end-to-end, passare a [Configurare SSL end-to-end usando un gateway applicazione con PowerShell](application-gateway-end-to-end-ssl-powershell.md) per creare un gateway applicazione usando SSL end-to-end.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
