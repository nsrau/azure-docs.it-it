---
title: Abilitazione di SSL end-to-end nel gateway applicazione Azure
description: Questo articolo offre un'introduzione al supporto di SSL end-to-end del gateway applicazione.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 1259e755642563a7baad5496bc84ed736d5499f8
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65849815"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Panoramica della terminazione SSL e SSL end to end con il Gateway applicazione

Sicuro Sockets Layer (SSL) è la tecnologia di protezione standard per stabilire un collegamento crittografato tra un server web e un browser. Questo collegamento garantisce che tutti i dati passati tra il server web e i browser rimangono crittografati e privati. Il gateway applicazione supporta entrambi terminazione SSL nel gateway, nonché di crittografia SSL end-to-end.

## <a name="ssl-termination"></a>Terminazione SSL

Il gateway applicazione supporta la terminazione SSL nel gateway, dopo la quale il traffico scorre generalmente non crittografato verso i server back-end. Esistono una serie di vantaggi di esecuzione della terminazione SSL nel gateway applicazione:

- **Miglioramento delle prestazioni** – la maggiore sulle prestazioni quando si esegue la decrittografia SSL sono handshake iniziale. Per migliorare le prestazioni, il server esegue la decrittografia vengono memorizzati nella cache gli ID di sessione SSL e gestisce i ticket di sessione TLS. Se questa operazione viene eseguita in corrispondenza del gateway applicazione, tutte le richieste dallo stesso client possono usare i valori memorizzati nella cache. Se questa operazione viene eseguita nei server back-end, ogni volta che le richieste del client passare a un altro server il client deve quindi re‑authenticate. L'utilizzo di ticket TLS può aiutare a ridurre questo problema, ma non sono supportati da tutti i client e può essere difficile configurare e gestire.
- **Miglior utilizzo dei server back-end** – l'elaborazione di SSL/TLS con utilizzo intensivo della CPU e sta diventando sempre più complesse come aumentare le dimensioni della chiave. Rimozione di questo lavoro dai server back-end consente loro di concentrarsi su ciò che sono più efficienti nella distribuzione di contenuti.
- **Il routing intelligente** – eseguendo la decrittografia del traffico, il gateway applicazione può accedere al contenuto della richiesta, ad esempio intestazioni, URI e così via e può usare questi dati per indirizzare le richieste.
- **Gestione dei certificati** – i certificati devono solo essere acquistato e installati nel gateway applicazione e non tutti i server back-end. Ciò consente di risparmiare tempo e denaro.

Per configurare la terminazione SSL, è necessario un certificato SSL da aggiungere al listener per consentire al gateway applicazione derivare una chiave simmetrica in base alla specifica del protocollo SSL. La chiave simmetrica viene quindi usata per crittografare e decrittografare il traffico inviato al gateway. Il certificato SSL deve essere nel formato di scambio di informazioni personali (PFX). Questo formato di file consente l'esportazione della chiave privata necessaria al gateway applicazione per eseguire la crittografia e la decrittografia del traffico.

> [!NOTE] 
>
> Il gateway applicazione non fornisce alcuna funzionalità per creare un nuovo certificato o inviare una richiesta di certificato a un'autorità di certificazione.

Per la connessione SSL a funzionare, è necessario assicurarsi che il certificato SSL soddisfi le condizioni seguenti:

- La data e ora correnti è all'interno della "Valido da" e l'intervallo di date "Valid a" per il certificato.
- Il "nome comune" del certificato deve corrispondere all'intestazione host nella richiesta. Se ad esempio il client effettua una richiesta a `https://www.contoso.com/`, il nome comune del certificato deve essere `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Certificati è supportati per la terminazione SSL

Il gateway applicazione supporta i tipi di certificati seguenti:

- Certificato della CA (autorità di certificazione): Un certificato della CA è un certificato digitale emesso da un'autorità di certificazione (CA)
- Certificato EV (Extended convalida): Linee guida per un certificato standard di settore, è un certificato di convalida estesa. Verrà barra localizzatore del browser in verde e pubblicare anche nome della società.
- Certificato con caratteri jolly: Questo certificato supporta un numero qualsiasi di sottodomini basata *. site.com, in cui il sottodominio sostituirebbe il *. , Tuttavia, nepodporuje funkci site.com, in modo che nel caso in cui gli utenti sono che accedono al sito senza digitare il prefisso "www", il certificato con caratteri jolly non offriranno copertura che.
- Certificati autofirmati: I browser client non attendibili questi certificati e avvertiranno l'utente che il certificato del servizio virtuale non fa parte di una catena di trust. I certificati autofirmati sono utili per il test o ambienti in cui gli amministratori di controllano i client in modo sicuro possono ignorare gli avvisi di sicurezza del browser. I carichi di lavoro di produzione non utilizzare mai certificati autofirmati.

Per altre informazioni, vedere [configura la terminazione SSL con il gateway applicazione](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Dimensioni del certificato
Il file di scambio di informazioni personali (PFX) con le informazioni del certificato SSL non deve essere maggiore di 10 KB.

## <a name="end-to-end-ssl-encryption"></a>-To-end della crittografia SSL

Alcuni clienti non può essere consigliabile lanciare comunicazioni non crittografate verso i server back-end. Questo potrebbe dipendere da requisiti di sicurezza e conformità o da un'applicazione in grado di accettare solo connessioni protette. Per tali applicazioni, il gateway applicazione supporta ora la crittografia SSL end-to-end.

La crittografia SSL end-to-end consente di trasmettere in modo sicuro dati sensibili crittografati al back-end, usufruendo comunque dei vantaggi delle funzionalità di bilanciamento del carico di livello 7 offerte dal gateway applicazione. Alcune di queste funzionalità sono l'affinità di sessione basata su cookie, il routing basato su URL, il supporto per il routing basato su siti o la possibilità di inserire intestazioni X-Forwarded-*.

Se configurato con la modalità di comunicazione SSL end-to-end, il gateway applicazione termina le sessioni SSL nel gateway ed esegue la decrittografia del traffico utente. Applica quindi le regole configurate per selezionare un'istanza del pool di back-end adeguata su cui instradare il traffico. Il gateway applicazione avvia a questo punto una nuova connessione SSL al server back-end e crittografa nuovamente i dati usando il certificato di chiave pubblica del server back-end prima di trasmettere la richiesta al back-end. Eventuali risposte dal server Web subiscono lo stesso processo in ritorno verso l'utente finale. -To-end SSL viene abilitata impostando l'impostazione del protocollo [impostazione HTTP back-end](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) su HTTPS, che viene quindi applicato a un pool di back-end.

I criteri SSL viene applicata al traffico front-end e back-end. Nel front-end, il Gateway applicazione funge da server e vengono applicati i criteri. Al back-end, il Gateway applicazione funge da client e invia le informazioni sul protocollo/pacchetti di crittografia come la preferenza durante l'handshake SSL.

Il gateway applicazione comunichi solo con le istanze back-end che sono entrambi consentiti il certificato con il gateway applicazione o i cui certificati sono firmati dalle autorità per autorità di certificazione nota in comune del certificato corrisponde al nome host nel HTTP impostazioni di back-end. Sono inclusi i servizi di Azure attendibili, ad esempio App web del servizio App di Azure e gestione API di Azure.

Se i certificati dei membri nel pool di back-end non sono firmati da autorità di autorità di certificazione ben note, ogni istanza nel pool di back-end con SSL end to end abilitato deve essere configurata con un certificato per consentire la comunicazione sicura. L'aggiunta del certificato garantisce che il gateway applicazione comunichi solo con istanze di back-end note, proteggendo ulteriormente la comunicazione end-to-end.

> [!NOTE] 
>
> Installazione di certificati di autenticazione non è necessaria per servizi di Azure attendibili, ad esempio App web del servizio App di Azure e gestione API di Azure.

> [!NOTE] 
>
> Il certificato aggiunto al **impostazione HTTP back-end** per eseguire l'autenticazione back-end di server possono essere lo stesso come il certificato aggiunto al **listener** per la terminazione SSL nel gateway applicazione o diverso per sicurezza avanzata.

![Scenario di SSL end-to-end][1]

In questo esempio, le richieste che usano TLS1.2 vengono instradate ai server back-end in Pool1 con SSL end-to-end.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>SSL end-to-end e aggiunta dei certificati all'elenco dei consentiti

Il gateway applicazione comunica solo con istanze back-end note, il cui certificato è incluso nell'elenco dei consentiti del gateway applicazione. Per abilitare l'aggiunta dei certificati all'elenco dei consentiti, è necessario caricare nel gateway applicazione la chiave pubblica dei certificati dei server back-end (non il certificato radice). Sono quindi consentite solo le connessioni a back-end noti e inclusi nell'elenco. Gli altri back-end generano un errore del gateway. I certificati autofirmati vengono usati a scopo di test e non sono consigliati per i carichi di lavoro. Prima dell'uso, questi certificati devono essere aggiunti all'elenco dei consentiti nel gateway applicazione, come descritto nei passaggi precedenti.

> [!NOTE]
> L'installazione di certificati di autenticazione non è necessaria per servizi di Azure attendibili, come ad esempio Servizio app di Azure.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>SSL end-to-end con SKU v2

I certificati di autenticazione sono stati deprecati e sostituiti da certificati radice attendibili nello SKU v2 gateway applicazione. Funzionano in modo analogo ai certificati di autenticazione con alcune differenze principali:

- I certificati firmati da autorità di certificazione note il cui nome comune corrisponde al nome host nelle impostazioni HTTP di back-end non richiedono alcun passaggio aggiuntivo per il funzionamento di SSL end-to-end. 

   Ad esempio, se i certificati di back-end sono rilasciati da un'autorità di certificazione nota il cui nome comune è contoso.com e anche il campo host delle impostazioni HTTP di back-end è impostato su contoso.com, non sono necessarie altre operazioni. Se si configura il protocollo come HTTPS nell'impostazione HTTP di back-end, sia il probe di integrità che il percorso dei dati saranno abilitati per SSL. Se si usa servizio App di Azure o altri servizi web di Azure come back-end, questi sono considerati implicitamente attendibili anche e non sono altri passaggi necessari per SSL end-to-end.
- Se il certificato è autofirmato o firmato da intermediari sconosciuti, per abilitare SSL end-to-end nello SKU v2 deve essere definito un certificato radice attendibile. Il gateway applicazione comunicherà solo con back-end il cui certificato radice del certificato del server corrisponde a uno dei certificati dell'elenco dei certificati radice attendibili nell'impostazione HTTP di back-end associata al pool.
- Oltre alla corrispondenza del certificato radice, il gateway applicazione verifica anche se l'impostazione Host specificata nell'impostazione HTTP di back-end corrisponde a quella del nome comune presentata dal certificato SSL del server back-end. Quando si tenta di stabilire una connessione SSL al back-end, il gateway applicazione imposta l'estensione Indicazione nome server (SNI) sull'host specificato nell'impostazione HTTP di back-end.
- Se si sceglie **pick hostname from backend address** (selezionare il nome host dall'indirizzo di back-end) anziché il campo Host nell'impostazione HTTP di back-end, l'intestazione SNI è sempre impostata sul nome di dominio completo del pool di back-end e il nome comune sul certificato SSL del server back-end deve corrispondere al nome di dominio completo. I membri del pool back-end con indirizzi IP non sono supportati in questo scenario.
- Il certificato radice è un certificato radice codificato con codifica base64 dai certificati del server back-end.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso i concetti di SSL end-to-end, passare a [Configurare SSL end-to-end usando un gateway applicazione con PowerShell](application-gateway-end-to-end-ssl-powershell.md) per creare un gateway applicazione usando SSL end-to-end.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
