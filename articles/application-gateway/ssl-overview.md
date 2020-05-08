---
title: Abilitazione di TLS end-to-end nel gateway applicazione di Azure
description: Questo articolo è una panoramica del supporto TLS end-to-end del gateway applicazione.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 286c9329be38055808571d8d32c724d27a61cbf3
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855868"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Panoramica della terminazione TLS e del TLS end-to-end con il gateway applicazione

Transport Layer Security (TLS), noto in precedenza come Secure Sockets Layer (SSL), è la tecnologia di sicurezza standard per stabilire un collegamento crittografato tra un server Web e un browser. Questo collegamento garantisce che tutti i dati passati tra il server Web e i browser rimangano privati e crittografati. Il gateway applicazione supporta sia la terminazione TLS sia il gateway che la crittografia TLS end-to-end.

## <a name="tls-termination"></a>Terminazione TLS

Il gateway applicazione supporta la terminazione TLS nel gateway, dopo il quale il traffico passa in genere non crittografato ai server back-end. Esistono diversi vantaggi derivanti dall'interruzione TLS nel gateway applicazione:

- **Miglioramento delle prestazioni** : il maggiore impatto sulle prestazioni quando si esegue la decrittografia TLS è l'handshake iniziale. Per migliorare le prestazioni, il server che esegue la decrittografia memorizza nella cache gli ID di sessione TLS e gestisce i ticket della sessione TLS. Se questa operazione viene eseguita nel gateway applicazione, tutte le richieste dello stesso client possono usare i valori memorizzati nella cache. Se viene eseguita nei server back-end, ogni volta che le richieste del client passano a un server diverso, il client deve ripetere l'autenticazione. L'uso di ticket TLS può contribuire a mitigare questo problema, ma non è supportato da tutti i client e può essere difficile da configurare e gestire.
- **Maggiore utilizzo dei server back-end: l'** elaborazione SSL/TLS richiede un utilizzo intensivo della CPU e sta aumentando il numero di dimensioni delle chiavi. La rimozione di questo lavoro dai server back-end consente loro di concentrarsi su ciò che sono più efficienti in, per la distribuzione di contenuti.
- **Routing intelligente** : tramite la decrittografia del traffico, il gateway applicazione ha accesso al contenuto della richiesta, ad esempio intestazioni, Uri e così via, e può usare questi dati per indirizzare le richieste.
- **Gestione dei certificati** : è necessario acquistare e installare solo i certificati nel gateway applicazione e non in tutti i server back-end. Questo consente di risparmiare tempo e denaro.

Per configurare la terminazione TLS, è necessario aggiungere un certificato TLS/SSL al listener per consentire al gateway applicazione di derivare una chiave simmetrica in base alla specifica del protocollo TLS/SSL. La chiave simmetrica viene quindi usata per crittografare e decrittografare il traffico inviato al gateway. Il certificato TLS/SSL deve essere in formato PFX (Personal Information Exchange). Questo formato di file consente l'esportazione della chiave privata necessaria al gateway applicazione per eseguire la crittografia e la decrittografia del traffico.

> [!IMPORTANT] 
> Si noti che il certificato nel listener richiede il caricamento dell'intera catena di certificati. 


> [!NOTE] 
>
> Il gateway applicazione non fornisce alcuna funzionalità per creare un nuovo certificato o inviare una richiesta di certificato a un'autorità di certificazione.

Per il corretto funzionamento della connessione TLS, è necessario assicurarsi che il certificato TLS/SSL soddisfi le condizioni seguenti:

- Che la data e l'ora correnti siano comprese nell'intervallo di date "valido da" e "valido a" del certificato.
- Il "nome comune" del certificato deve corrispondere all'intestazione host nella richiesta. Se ad esempio il client effettua una richiesta a `https://www.contoso.com/`, il nome comune del certificato deve essere `www.contoso.com`.

### <a name="certificates-supported-for-tls-termination"></a>Certificati supportati per la terminazione TLS

Il gateway applicazione supporta i tipi di certificati seguenti:

- Certificato CA (autorità di certificazione): un certificato della CA è un certificato digitale emesso da un'autorità di certificazione (CA)
- Certificato EV (Extended Validation): un certificato EV è un certificato conforme alle linee guida per i certificati standard del settore. In questo modo la barra di localizzazione del browser diventa verde e viene pubblicato anche il nome della società.
- Certificato con caratteri jolly: questo certificato supporta un numero qualsiasi di sottodomini basati su *. site.com, in cui il sottodominio sostituirà *. Tuttavia, non supporta site.com, quindi, nel caso in cui gli utenti accedano al sito Web senza digitare il "www", il certificato con caratteri jolly non lo coprirà.
- Certificati autofirmati: i browser client non considerano attendibili questi certificati e avvisano l'utente che il certificato del servizio virtuale non fa parte di una catena di trust. I certificati autofirmati sono utili per i test o gli ambienti in cui gli amministratori controllano i client e possono ignorare in modo sicuro gli avvisi di sicurezza del browser. I carichi di lavoro di produzione non devono mai usare certificati autofirmati.

Per altre informazioni, vedere [configurare la terminazione TLS con il gateway applicazione](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Dimensioni del certificato
Per informazioni sulle dimensioni massime del certificato TLS/SSL supportate, vedere la sezione [limiti del gateway applicazione](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) .

## <a name="end-to-end-tls-encryption"></a>Crittografia TLS end-to-end

Alcuni clienti potrebbero non volere la comunicazione non crittografata con i server back-end. Questo potrebbe dipendere da requisiti di sicurezza e conformità o da un'applicazione in grado di accettare solo connessioni protette. Per tali applicazioni, il gateway applicazione supporta la crittografia TLS end-to-end.

TLS end-to-end consente di trasmettere in modo sicuro dati sensibili al back-end crittografato sfruttando al tempo stesso i vantaggi delle funzionalità di bilanciamento del carico di livello 7 fornite dal gateway applicazione. Alcune di queste funzionalità sono l'affinità di sessione basata su cookie, il routing basato su URL, il supporto per il routing basato su siti o la possibilità di inserire intestazioni X-Forwarded-*.

Se configurato con la modalità di comunicazione TLS end-to-end, il gateway applicazione termina le sessioni TLS nel gateway ed esegue la decrittografia del traffico utente. Applica quindi le regole configurate per selezionare un'istanza del pool di back-end adeguata su cui instradare il traffico. Il gateway applicazione avvia quindi una nuova connessione TLS al server back-end e crittografa nuovamente i dati usando il certificato di chiave pubblica del server back-end prima di trasmettere la richiesta al back-end. Eventuali risposte dal server Web subiscono lo stesso processo in ritorno verso l'utente finale. Il protocollo TLS end-to-end viene abilitato impostando l'impostazione del protocollo nell' [impostazione http back](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) -end su HTTPS, che viene quindi applicata a un pool back-end.

I criteri TLS si applicano al traffico front-end e back-end. Sul front-end, il gateway applicazione funge da server e impone i criteri. Nel back-end, il gateway applicazione funge da client e invia le informazioni di protocollo/crittografia come preferenza durante l'handshake TLS.

Il gateway applicazione comunica solo con le istanze back-end in cui il certificato è stato inserito nell'elenco elementi consentiti con il gateway applicazione o i cui certificati sono firmati da autorità di certificazione note in cui il CN del certificato corrisponde al nome host nelle impostazioni del back-end HTTP. Questi includono i servizi di Azure attendibili, come app Azure app Web del servizio e gestione API di Azure.

Se i certificati dei membri nel pool back-end non sono firmati da autorità di certificazione note, ogni istanza nel pool back-end con TLS abilitato end-to-end deve essere configurata con un certificato per consentire la comunicazione protetta. L'aggiunta del certificato garantisce che il gateway applicazione comunichi solo con istanze di back-end note, proteggendo ulteriormente la comunicazione end-to-end.

> [!NOTE] 
>
> La configurazione del certificato di autenticazione non è necessaria per i servizi di Azure attendibili, ad esempio le app Web del servizio app Azure e gestione API di Azure.

> [!NOTE] 
>
> Il certificato aggiunto all' **impostazione http back-end** per autenticare i server back-end può corrispondere al certificato aggiunto al **listener** per la terminazione TLS nel gateway applicazione o a un livello diverso per la sicurezza avanzata.

![scenario TLS end-to-end][1]

In questo esempio, le richieste che usano TLS 1.2 vengono indirizzate ai server back-end in pool1 usando TLS end-to-end.

## <a name="end-to-end-tls-and-whitelisting-of-certificates"></a>TLS end-to-end e elenco elementi consentiti dei certificati

Il gateway applicazione comunica solo con istanze back-end note, il cui certificato è incluso nell'elenco dei consentiti del gateway applicazione. Per abilitare l'aggiunta dei certificati all'elenco dei consentiti, è necessario caricare nel gateway applicazione la chiave pubblica dei certificati dei server back-end (non il certificato radice). Sono quindi consentite solo le connessioni a back-end noti e inclusi nell'elenco. Gli altri back-end generano un errore del gateway. I certificati autofirmati vengono usati a scopo di test e non sono consigliati per i carichi di lavoro. Prima dell'uso, questi certificati devono essere aggiunti all'elenco dei consentiti nel gateway applicazione, come descritto nei passaggi precedenti.

> [!NOTE]
> L'installazione di certificati di autenticazione non è necessaria per servizi di Azure attendibili, come ad esempio Servizio app di Azure.

## <a name="end-to-end-tls-with-the-v2-sku"></a>TLS end-to-end con lo SKU V2

I certificati di autenticazione sono stati deprecati e sostituiti da certificati radice attendibili nello SKU v2 gateway applicazione. Funzionano in modo analogo ai certificati di autenticazione con alcune differenze principali:

- I certificati firmati da autorità di certificazione note il cui CN corrisponde al nome host nelle impostazioni back-end HTTP non richiedono alcun passaggio aggiuntivo per il funzionamento di TLS end-to-end. 

   Ad esempio, se i certificati di back-end sono rilasciati da un'autorità di certificazione nota il cui nome comune è contoso.com e anche il campo host delle impostazioni HTTP di back-end è impostato su contoso.com, non sono necessarie altre operazioni. È possibile impostare il protocollo di impostazione http back-end su HTTPS e il probe di integrità e il percorso dati verrebbero abilitati per TLS. Se si usa app Azure servizio o altri servizi Web di Azure come back-end, anche questi vengono considerati implicitamente attendibili e non sono necessari altri passaggi per TLS end-to-end.
   
> [!NOTE] 
>
> Affinché un certificato TLS/SSL venga considerato attendibile, il certificato del server back-end deve essere stato emesso da un'autorità di certificazione inclusa nell'archivio attendibile del gateway applicazione. se il certificato non è stato emesso da un'autorità di certificazione attendibile, il gateway applicazione verificherà quindi se il certificato della CA emittente è stato emesso da una CA attendibile e così via fino a quando non viene rilevata una CA attendibile (a questo punto viene stabilita una connessione protetta attendibile) o non è possibile trovare una CA attendibile (a questo punto il gateway applicazione contrassegna il back-end non integro). È quindi consigliabile che il certificato del server back-end contenga sia la radice sia le CA intermedie.

- Se il certificato è autofirmato o è firmato da intermediari sconosciuti, per abilitare TLS end-to-end nella SKU V2 è necessario definire un certificato radice attendibile. Il gateway applicazione comunicherà solo con back-end il cui certificato radice del certificato del server corrisponde a uno dei certificati dell'elenco dei certificati radice attendibili nell'impostazione HTTP di back-end associata al pool.

> [!NOTE] 
>
> Il certificato autofirmato deve essere parte di una catena di certificati. Un certificato autofirmato singolo senza catena non è supportato nello SKU V2.

- Oltre alla corrispondenza del certificato radice, il gateway applicazione convalida anche se l'impostazione host specificata nell'impostazione http back-end corrisponde a quella del nome comune (CN) presentato dal certificato TLS/SSL del server back-end. Quando si tenta di stabilire una connessione TLS al back-end, il gateway applicazione imposta l'estensione Indicazione nome server (SNI) sull'host specificato nell'impostazione http back-end.
- Se si sceglie **Seleziona nome host dall'indirizzo back-end** anziché il campo host nell'impostazione http back-end, l'intestazione SNI è sempre impostata sull'FQDN del pool back-end e il CN nel server back-end TLS/SSL deve corrispondere al relativo nome di dominio completo. I membri del pool back-end con indirizzi IP non sono supportati in questo scenario.
- Il certificato radice è un certificato radice codificato con codifica base64 dai certificati del server back-end.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le informazioni sul TLS end-to-end, vedere [configurare TLS end-to-end usando il gateway applicazione con PowerShell](application-gateway-end-to-end-ssl-powershell.md) per creare un gateway applicazione usando TLS end-to-end.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
