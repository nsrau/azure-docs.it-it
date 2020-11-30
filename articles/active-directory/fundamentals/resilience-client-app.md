---
title: Aumentare la resilienza dell'autenticazione e dell'autorizzazione nelle applicazioni client sviluppate
titleSuffix: Microsoft identity platform
description: Linee guida per aumentare la resilienza dell'autenticazione e dell'autorizzazione nell'applicazione client tramite la piattaforma di identità Microsoft
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: 9189d4d8cda5f9fcfce7e6ac2097414aa29f0a68
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96317470"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-client-applications-you-develop"></a>Aumentare la resilienza dell'autenticazione e dell'autorizzazione nelle applicazioni client sviluppate

Questa sezione fornisce indicazioni sulla creazione di resilienza nelle applicazioni client che usano la piattaforma di identità Microsoft e Azure Active Directory per l'accesso degli utenti e per l'esecuzione di azioni per conto di tali utenti.

## <a name="use-the-microsoft-authentication-library-msal"></a>Utilizzare Microsoft Authentication Library (MSAL)

[Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) è un componente chiave della piattaforma di [identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop). Semplifica e gestisce l'acquisizione, la gestione, la memorizzazione nella cache e l'aggiornamento dei token e usa le procedure consigliate per la resilienza. MSAL è progettato per abilitare una soluzione sicura senza che gli sviluppatori debbano preoccuparsi dei dettagli di implementazione.

MSAL memorizza nella cache i token e usa un modello di acquisizione di token invisibile all'utente. Serializza inoltre automaticamente la cache dei token sulle piattaforme che forniscono in modo nativo archiviazione sicura come Windows UWP, iOS e Android. Gli sviluppatori possono personalizzare il comportamento di serializzazione quando si usa [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization), [MSAL.NET](https://docs.microsoft.com/azure/active-directory/develop/msal-net-token-cache-serialization), [MSAL per Java](https://docs.microsoft.com/azure/active-directory/develop/msal-java-token-cache-serialization)e [MSAL per Python](https://docs.microsoft.com/azure/active-directory/develop/msal-python-token-cache-serialization).

![Immagine del dispositivo con l'applicazione e usando MSAL per chiamare Microsoft Identity](media/resilience-client-app/resilience-with-microsoft-authentication-library.png)

Quando si usa MSAL, si ottengono la memorizzazione dei token nella cache, l'aggiornamento e l'acquisizione di token invisibile all'utente usando il modello seguente.

```csharp
try
{
    result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
    result = await app.AcquireToken(scopes).WithClaims(ex.Claims).ExecuteAsync()
}
```

MSAL può in alcuni casi aggiornare in modo proattivo i token. Quando Microsoft Identity rilascia un token di lunga durata, può inviare al client informazioni per il tempo ottimale per l'aggiornamento del token ("Refresh \_ in"). MSAL aggiornerà in modo proattivo il token in base a queste informazioni. L'app continuerà a essere eseguita mentre il token precedente è valido, ma avrà un intervallo di tempo più lungo per eseguire un'altra acquisizione di token riuscita.

### <a name="stay-up-to-date"></a>Rimani sempre aggiornato

Gli sviluppatori devono disporre di un processo per l'aggiornamento alla versione più recente di MSAL. L'autenticazione fa parte della sicurezza dell'app e l'app deve rimanere aggiornata con i miglioramenti della sicurezza contenuti nelle nuove versioni di MSAL. Si tratta in genere di una procedura consigliata per le librerie in fase di sviluppo continuo. in questo modo sarà possibile ottenere il codice più aggiornato rispetto alla resilienza delle app. Poiché Microsoft Identity continua a innovare su modi per aumentare la resilienza delle applicazioni, le app che usano la versione più recente di MSAL saranno le più preparate per la creazione di queste innovazioni.

[Verificare la versione MSAL.js più recente e le note sulla versione](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[Controllare la versione più recente di MSAL .NET e le note sulla versione](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases)

[Verificare la versione più recente di MSAL Python e le note sulla versione](https://github.com/AzureAD/microsoft-authentication-library-for-python/releases)

[Controllare la versione più recente di MSAL e le note sulla versione di Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/releases)

[Controllare la versione più recente di MSAL iOS e macOS e le note sulla versione](https://github.com/AzureAD/microsoft-authentication-library-for-objc/releases)

[Controllare la versione più recente di MSAL Android e le note sulla versione](https://github.com/AzureAD/microsoft-authentication-library-for-android/releases)

[Controllare la versione più recente di MSAL angolari e le note sulla versione](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[Controllare la versione più recente di Microsoft. Identity. Web e le note sulla versione](https://github.com/AzureAD/microsoft-identity-web/releases)

## <a name="if-not-using-msal-use-these-resilient-patterns-for-token-handling"></a>Se non si usa MSAL, usare questi modelli resilienti per la gestione dei token

In generale, un'applicazione che usa l'autenticazione moderna chiamerà un endpoint per recuperare i token che autenticano l'utente o autorizzare l'applicazione a chiamare le API protette. MSAL è progettato per gestire i dettagli dell'autenticazione e implementa diversi modelli per migliorare la resilienza del processo. Usare le linee guida in questa sezione per implementare le procedure consigliate Se si sceglie di usare una libreria diversa da MSAL. Se si usa MSAL, è possibile ottenere tutte queste procedure consigliate gratuitamente, poiché MSAL le implementa automaticamente.

### <a name="cache-tokens"></a>Token della cache

Le app devono memorizzare correttamente nella cache i token ricevuti da Microsoft Identity. Quando l'app riceve i token, la risposta HTTP che contiene i token contiene anche una proprietà "expires_in" che indica all'applicazione il tempo di memorizzazione nella cache e il riutilizzo del token. In particolare, le applicazioni usano la proprietà "expires_in" per determinare la durata del token. L'applicazione non deve mai tentare di decodificare un token di accesso all'API.

![Un'applicazione che effettua una chiamata a Microsoft Identity, ma la chiamata passa attraverso una cache di token sul dispositivo che esegue l'applicazione](media/resilience-client-app/token-cache.png)

L'uso del token memorizzato nella cache impedisce il traffico non necessario tra l'app e l'identità Microsoft e rende l'app meno soggetta a errori di acquisizione dei token riducendo il numero di chiamate di acquisizione dei token. I token memorizzati nella cache migliorano anche le prestazioni dell'applicazione, in quanto l'app deve bloccare l'acquisizione di token meno. L'utente può rimanere connesso all'applicazione per la durata della durata del token.

### <a name="serialize-and-persist-tokens"></a>Serializzare e salvare in maniera permanente i token

Le app devono serializzare in modo sicuro la cache dei token per rendere permanente i token tra le istanze dell'app. I token possono essere riutilizzati a condizione che siano entro la durata valida. I [token di aggiornamento](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)e, sempre più, i token di [accesso](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)vengono emessi per diverse ore. Questo tempo valido può estendersi a un utente che avvia l'applicazione molte volte. Quando l'app viene avviata, deve verificare se è presente un token di accesso o di aggiornamento valido che può essere usato. Questa operazione aumenterà la resilienza e le prestazioni dell'app, in quanto evita le chiamate non necessarie a Microsoft Identity.

![Un'applicazione che effettua una chiamata a Microsoft Identity, ma la chiamata passa attraverso una cache di token, nonché un archivio di token sul dispositivo che esegue l'applicazione.](media/resilience-client-app/token-store.png)

L'archiviazione dei token persistente deve essere controllata e crittografata nell'identità di processo o utente proprietario. Su piattaforme come dispositivi mobili, Windows e Mac, lo sviluppatore dovrebbe sfruttare le funzionalità predefinite per l'archiviazione delle credenziali.

### <a name="acquire-tokens-silently"></a>Acquisisci i token in modo invisibile all'utente

Il processo di autenticazione di un utente o di recupero dell'autorizzazione per chiamare un'API può richiedere più passaggi nell'identità Microsoft. Ad esempio, quando l'utente accede per la prima volta, potrebbe essere necessario immettere le credenziali ed eseguire un'autenticazione a più fattori tramite un SMS. Ogni passaggio consente di aggiungere una dipendenza dalla risorsa che fornisce tale servizio. L'esperienza ottimale per l'utente e quella con le dipendenze minime consiste nel tentare di acquisire un token in modo invisibile all'utente per evitare questi passaggi aggiuntivi prima di richiedere l'interazione dell'utente.

![Diagramma che mostra i vari servizi all'interno di un'identità Microsoft che può essere necessario eseguire per completare il processo di autenticazione o autorizzazione di un utente](media/resilience-client-app/external-dependencies.png)

L'acquisizione di un token inizia automaticamente con l'uso di un token valido dalla cache dei token dell'app. Se non è disponibile alcun token valido, l'app deve tentare di acquisire un token usando un token di aggiornamento, se disponibile, e l'endpoint del token. Se nessuna di queste opzioni è disponibile, l'app deve acquisire un token usando il parametro "prompt = None". Verrà utilizzato l'endpoint di autorizzazione, ma non verrà visualizzata alcuna interfaccia utente. Se l'identità Microsoft può fornire un token all'app senza interagire con l'utente, lo sarà. Se nessuno di questi metodi genera un token, un utente dovrà eseguire di nuovo l'autenticazione in modo interattivo.

> [!NOTE]
> In generale, le app devono evitare di usare prompt come "login" e "consenso" poiché forzano l'interazione dell'utente anche quando non è necessaria alcuna interazione.

## <a name="handle-service-responses-properly"></a>Gestire correttamente le risposte del servizio

Sebbene le applicazioni debbano gestire tutte le risposte di errore, esistono alcune risposte che possono influisca sulla resilienza. Se l'applicazione riceve un codice di risposta HTTP 429, troppe richieste, l'identità Microsoft limita le richieste. Se l'app continua a eseguire troppe richieste, la limitazione continuerà a impedire che l'app riceva i token. L'applicazione non deve tentare di acquisire di nuovo un token fino a dopo il tempo, in secondi, nel campo della risposta Retry-After è stato superato. La ricezione di una risposta 429 spesso indica che l'applicazione non memorizza nella cache e riutilizza correttamente i token. Gli sviluppatori devono esaminare il modo in cui i token vengono memorizzati nella cache e riutilizzati nell'applicazione.

Quando un'applicazione riceve un codice di risposta 5xx HTTP, l'app non deve immettere un ciclo di ripetizione dei tentativi veloce. Quando è presente, l'applicazione deve rispettare lo stesso Retry-After gestione come per una risposta 429. Se non viene fornita alcuna intestazione Retry-After dalla risposta, è consigliabile implementare un nuovo tentativo di disattivazione esponenziale con il primo tentativo dopo almeno 5 secondi dopo la risposta.

Quando si verifica il timeout di una richiesta, le applicazioni non devono eseguire immediatamente nuovi tentativi. Implementare un nuovo tentativo di disattivazione esponenziale con il primo tentativo di almeno 5 secondi dopo la risposta.

## <a name="evaluate-options-for-retrieving-authorization-related-information"></a>Valutare le opzioni per il recupero delle informazioni relative all'autorizzazione

Per molte applicazioni e API sono necessarie informazioni specifiche sull'utente per prendere decisioni di autorizzazione. Esistono diversi modi per ottenere queste informazioni da un'applicazione. Ogni metodo presenta vantaggi e svantaggi. Gli sviluppatori dovrebbero pesare questi per determinare quale strategia è migliore per la resilienza nell'app.

### <a name="tokens"></a>Tokens

I token Identity (ID) e i token di accesso contengono attestazioni standard che forniscono informazioni sull'oggetto. Sono documentati in [token ID piattaforma Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/id-tokens) e [token di accesso della piattaforma Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/access-tokens). Se le informazioni necessarie per l'app sono già presenti nel token, la tecnica più efficiente per il recupero dei dati consiste nell'usare le attestazioni del token in quanto in questo modo verrà salvata l'overhear di una chiamata di rete aggiuntiva per recuperare le informazioni separatamente. Un minor numero di chiamate di rete implica una resilienza complessiva più elevata per l'applicazione.

> [!NOTE]
> Alcune applicazioni chiamano l'endpoint UserInfo per recuperare le attestazioni relative all'utente che ha eseguito l'autenticazione. Le informazioni disponibili nel token ID che l'app può ricevere sono un superset delle informazioni che può ottenere dall'endpoint UserInfo. L'app deve usare il token ID per ottenere informazioni sull'utente anziché chiamare l'endpoint UserInfo.

Uno sviluppatore di app può aumentare le attestazioni di token standard con [attestazioni facoltative](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims). Un'attestazione facoltativa comune è rappresentata dai [gruppi](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims#configuring-groups-optional-claims). Esistono diversi modi per aggiungere attestazioni di gruppo. L'opzione "gruppo di applicazioni" include solo i gruppi assegnati all'applicazione. Le opzioni "tutti" o "gruppi di sicurezza" includono i gruppi di tutte le app nello stesso tenant, che possono aggiungere molti gruppi al token. È importante valutare l'effetto del caso, in quanto può potenzialmente negare l'efficienza acquisita richiedendo i gruppi nel token causando un aumento del token e persino richiedendo chiamate aggiuntive per ottenere l'elenco completo dei gruppi.

Invece di usare i gruppi nel token, è possibile usare e includere i ruoli dell'app. Gli sviluppatori possono definire i [ruoli dell'app](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per le app e le API che il cliente può gestire dalla propria directory usando il portale o le API. I professionisti IT possono quindi assegnare ruoli a utenti e gruppi diversi per controllare chi ha accesso al contenuto e alle funzionalità. Quando viene emesso un token per l'applicazione o l'API, i ruoli assegnati all'utente saranno disponibili nell'attestazione Roles nel token. Il recupero di queste informazioni direttamente in un token può salvare chiamate API aggiuntive.

Infine, gli amministratori IT possono anche aggiungere attestazioni in base a informazioni specifiche in un tenant. Ad esempio, un'azienda può avere un'estensione per avere un ID utente specifico dell'organizzazione.

In tutti i casi, l'aggiunta di informazioni dalla directory direttamente a un token può essere efficiente e aumentare la resilienza delle app riducendo il numero di dipendenze dell'app. D'altra parte, non risolve alcun problema di resilienza perché non è in grado di acquisire un token. È necessario aggiungere attestazioni facoltative solo per gli scenari principali dell'applicazione. Se l'app richiede informazioni solo per la funzionalità di amministrazione, è consigliabile che l'applicazione ottenga tali informazioni solo se necessario.

### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph fornisce un endpoint API unificato per accedere ai dati Microsoft 365 che descrivono i modelli di produttività, identità e sicurezza in un'organizzazione. Le applicazioni che usano Microsoft Graph possono potenzialmente usare qualsiasi informazione in Microsoft 365 per le decisioni di autorizzazione.

Le app richiedono solo un singolo token per accedere a tutti i Microsoft 365. Questa operazione è più flessibile rispetto all'utilizzo delle API precedenti specifiche per Microsoft 365 componenti come Microsoft Exchange o Microsoft SharePoint, in cui sono necessari più token.

Quando si usano le API di Microsoft Graph, è consigliabile usare un [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview). Gli SDK Microsoft Graph sono progettati per semplificare la creazione di applicazioni di alta qualità, efficienti e resilienti che accedono a Microsoft Graph.

Per le decisioni relative alle autorizzazioni, gli sviluppatori devono considerare quando usare le attestazioni disponibili in un token come alternativa ad alcune chiamate Microsoft Graph. Come indicato in precedenza, gli sviluppatori possono richiedere i gruppi, i ruoli dell'app e le attestazioni facoltative nei token. In termini di resilienza, l'uso di Microsoft Graph per l'autorizzazione richiede chiamate di rete aggiuntive basate su identità Microsoft (per ottenere il token per accedere Microsoft Graph) e Microsoft Graph stessa. Tuttavia, se l'applicazione si basa già su Microsoft Graph come livello dati, il fatto di fare affidamento sul grafico per l'autorizzazione non è un rischio aggiuntivo da intraprendere.

## <a name="use-broker-authentication-on-mobile-devices"></a>Usare l'autenticazione Broker nei dispositivi mobili

Nei dispositivi mobili, l'uso di un broker di autenticazione come Microsoft Authenticator migliorerà la resilienza. Il Broker aggiunge vantaggi rispetto a quelli disponibili con altre opzioni, ad esempio il browser di sistema o una WebView incorporata. Il broker di autenticazione può usare un [token di aggiornamento primario](https://docs.microsoft.com/azure/active-directory/devices/concept-primary-refresh-token) (PRT) che contiene attestazioni relative all'utente e al dispositivo e può essere usato per ottenere i token di autenticazione per accedere ad altre applicazioni dal dispositivo. Quando si usa un PRT per richiedere l'accesso a un'applicazione, le attestazioni del dispositivo e dell'autenticazione a più fattori sono considerate attendibili dal Azure AD. Ciò aumenta la resilienza evitando passaggi aggiuntivi per autenticare nuovamente il dispositivo. Gli utenti non potranno più richiedere più richieste di autenticazione a più fattori sullo stesso dispositivo, aumentando quindi la resilienza riducendo le dipendenze dai servizi esterni e migliorando l'esperienza utente.

![Un'applicazione che effettua una chiamata a Microsoft Identity, ma la chiamata passa attraverso una cache di token, nonché un archivio di token e un broker di autenticazione sul dispositivo che esegue l'applicazione.](media/resilience-client-app/authentication-broker.png)

L'autenticazione Broker è automaticamente supportata da MSAL. È possibile trovare altre informazioni sull'uso dell'autenticazione negoziata nelle pagine seguenti:

- [Configurare SSO in macOS e iOS](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-macos-ios#sso-through-authentication-broker-on-ios)
- [Come abilitare l'accesso Single Sign-on tra app in Android usando MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-android-single-sign-on)

## <a name="adopt-continuous-access-evaluation"></a>Adozione della valutazione di accesso continuo

La [valutazione di accesso continuo](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) è uno sviluppo recente che può migliorare la sicurezza e la resilienza dell'applicazione con token di lunga durata. CAE è uno standard di settore emergente sviluppato nel gruppo di lavoro dei segnali e degli eventi condivisi di OpenID Foundation. Con CAE è possibile revocare un token di accesso in base [agli eventi critici](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation#critical-event-evaluation) e alla [valutazione dei criteri](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation#conditional-access-policy-evaluation-preview), anziché affidarsi a una durata del token breve. Per alcune API delle risorse, poiché i rischi e i criteri vengono valutati in tempo reale, CAE può aumentare notevolmente la durata del token fino a 28 ore. Poiché le applicazioni e le API delle risorse adottano il CAE, Microsoft Identity sarà in grado di rilasciare token di accesso revocabili e validi per periodi di tempo prolungati. Questi token di lunga durata verranno aggiornati in modo proattivo da MSAL.

Mentre CAE è in fase iniziale, è possibile [sviluppare applicazioni client che trarranno vantaggio da CAE](../develop/app-resilience-continuous-access-evaluation.md) quando le risorse (API) utilizzate dall'applicazione adottano il CAE. Poiché più risorse adottano il CAE, l'applicazione sarà in grado di acquisire anche i token abilitati per CAE per queste risorse. L'API di Microsoft Graph e gli [SDK di Microsoft Graph](https://docs.microsoft.com/graph/sdks/sdks-overview), in anteprima la funzionalità CAE è la prima 2021. Se si vuole partecipare all'anteprima pubblica di Microsoft Graph con CAE, è possibile segnalare che si è interessati a questo articolo: [https://aka.ms/GraphCAEPreview](https://aka.ms/GraphCAEPreview) .

Se si sviluppano API delle risorse, si consiglia di partecipare al gruppo di [eventi e ai segnali condivisi](https://openid.net/wg/sse/). Stiamo lavorando a questo gruppo per abilitare la condivisione degli eventi di sicurezza tra i provider di identità e di risorse Microsoft.

## <a name="next-steps"></a>Passaggi successivi

- [Come usare le API abilitate per la valutazione dell'accesso continuo nelle applicazioni](../develop/app-resilience-continuous-access-evaluation.md)
- [Rafforzare la resilienza nelle applicazioni daemon](resilience-daemon-app.md)
- [Resilienza della compilazione nell'infrastruttura di gestione delle identità e dell'accesso](resilience-in-infrastructure.md)
- [Resilienza delle build nei sistemi CIAM](resilience-b2c.md)
