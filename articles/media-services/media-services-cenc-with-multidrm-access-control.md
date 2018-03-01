---
title: 'Crittografia CENC con DRM multiplo e controllo di accesso: informazioni di riferimento per la progettazione e l''implementazione in Azure e Servizi multimediali di Azure | Microsoft Docs'
description: Informazioni su come ottenere la licenza per Microsoft Smooth Streaming Client Porting Kit.
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.assetid: 7814739b-cea9-4b9b-8370-538702e5c615
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;kilroyh;yanmf;juliako
ms.openlocfilehash: cac1513d805e01f2c9633b3b318ad6808027904e
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>Crittografia CENC con DRM multiplo e controllo di accesso: informazioni di riferimento per la progettazione e l'implementazione in Azure e Servizi multimediali di Azure
 
## <a name="introduction"></a>Introduzione
La progettazione e la creazione di un sottosistema DRM (Digital Right Management) per una soluzione di streaming online o OTT (Over-The-Top) sono attività complesse. Gli operatori e i provider di video online in genere esternalizzano queste attività a provider di servizi DRM specializzati. L'obiettivo di questo documento è presentare informazioni di riferimento per la progettazione e l'implementazione di un sottosistema DRM end-to-end in una soluzione di streaming online o OTT.

Questo documento è destinato agli ingegneri che utilizzano i sottosistemi DRM di soluzioni multischermo e di streaming online o OTT, ma anche a chiunque sia interessato ai sottosistemi DRM. Il presupposto è che i lettori abbiano familiarità con almeno una delle tecnologie DRM presenti sul mercato, ad esempio PlayReady, Widevine, FairPlay o Adobe Access.

L'articolo illustra inoltre la crittografia comune (CENC) con DRM multiplo. Una tendenza diffusa nel settore delle soluzioni di streaming online e OTT è rappresentata dall'uso della crittografia CENC con DRM nativo multiplo su varie piattaforme client. Tale tendenza si discosta da quella precedente, che usava una tecnologia DRM singola e il relativo SDK client su varie piattaforme client. Quando si usa la crittografia CENC con DRM nativo multiplo, PlayReady e Widevine vengono crittografati in base alla specifica [Common Encryption (ISO/IEC 23001-7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/).

Di seguito sono elencati i vantaggi dell'uso della crittografia CENC con DRM multiplo:

* Riduzione del costo della crittografia, perché viene usata una sola elaborazione crittografica per più piattaforme con DRM nativi.
* Riduzione del costo della gestione degli asset crittografati, perché è necessaria una sola copia degli asset crittografati.
* Eliminazione del costo della licenza del client DRM, perché il client DRM nativo è in genere gratuito sulla piattaforma nativa.

Microsoft, insieme ad alcuni dei principali leader di settore, promuove attivamente DASH e CENC. Servizi multimediali di Azure fornisce il supporto per DASH e CENC. Per gli annunci recenti, vedere i blog seguenti:

*  [Annuncio dei servizi di distribuzione delle licenze Google Widevine in Servizi multimediali di Azure](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services adds Google Widevine packaging for delivering a multi-DRM stream](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) (Aggiunta del pacchetto Google Widevine per la distribuzione di un flusso DRM multiplo in Servizi multimediali di Azure)  

### <a name="overview-of-this-article"></a>Panoramica dell'articolo
Gli obiettivi di questo articolo sono:

* Fornire informazioni di riferimento per la progettazione di un sottosistema DRM che usi la crittografia CENC con DRM multiplo.
* Fornire informazioni di riferimento per l'implementazione in piattaforme Azure e Servizi multimediali.
* Illustrare alcuni argomenti relativi alla progettazione e all'implementazione.

Nell'articolo, l'espressione "DRM multiplo" fa riferimento a quanto segue:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

La tabella seguente riepiloga le app e le piattaforme native, nonché i browser supportati da ogni DRM.

| **Piattaforma client** | **Supporto DRM nativo** | **Browser/app** | **Formati di streaming** |
| --- | --- | --- | --- |
| **Smart TV, STB operatore, STB OTT** |Principalmente PlayReady e/o Widevine e/o altro |Linux, Opera, WebKit, altri |Vari formati |
| **Dispositivi Windows 10 (PC Windows, tablet Windows, Windows Phone, Xbox)** |PlayReady |MS Edge/IE11/EME<br/><br/><br/>Piattaforma UWP (Universal Windows Platform) |DASH (per HLS, PlayReady non è supportato)<br/><br/>DASH, Smooth Streaming (per HLS, PlayReady non è supportato) |
| **Dispositivi Android (telefoni, tablet, TV)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), client OS X e Apple TV** |FairPlay |Safari 8+/EME |HLS |


In base allo stato attuale della distribuzione per ogni DRM, un servizio in genere prevede l'implementazione di 2 o 3 DRM per garantire che tutti i tipi di endpoint vengano indirizzati nel modo migliore.

Occorre raggiungere un compromesso tra la complessità della logica del servizio e la complessità sul lato client per ottenere un certo livello di esperienza utente nei vari client.

Per scegliere l'opzione migliore, tenere presente quanto segue:

* PlayReady viene implementato in modo nativo in tutti i dispositivi Windows e in alcuni dispositivi Android ed è disponibile tramite SDK software su praticamente qualsiasi piattaforma.
* Widevine viene implementato in modo nativo in tutti i dispositivi Android, in Chrome e in alcuni altri dispositivi.
* FairPlay è disponibile solo in iOS e nei client Mac OS o tramite iTunes.

Per l'implementazione di una soluzione con DRM multiplo sono disponibili due opzioni:

* PlayReady e Widevine
* PlayReady, Widevine e FairPlay

## <a name="a-reference-design"></a>Informazioni di riferimento per la progettazione
Questa sezione presenta informazioni di riferimento per una progettazione indipendente dalle tecnologie usate per implementarla.

Un sottosistema DRM può contenere i componenti seguenti:

* Gestione della chiave
* Creazione pacchetto DRM
* Distribuzione di licenze DRM
* Controllo dei diritti
* Autenticazione/autorizzazione
* Lettore
* Rete per la distribuzione di contenuti (rete CDN)/origine

Il diagramma seguente illustra l'interazione generale tra i componenti in un sottosistema DRM:

![Sottosistema DRM con CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

La progettazione è costituita da tre livelli di base:

* Livello back office (nero) non esposto esternamente.
* Livello di rete perimetrale (blu) contenente tutti gli endpoint rivolti al pubblico.
* Livello Internet pubblico (azzurro) contenente lettori e rete CDN con traffico Internet pubblico.

Deve anche essere presente uno strumento per la gestione dei contenuti che consenta di controllare la protezione DRM, indipendentemente dall'uso di una crittografia statica o dinamica. Gli input per la crittografia DRM includono:

* Contenuto video con velocità in bit multipla
* Chiave simmetrica
* URL di acquisizione delle licenze

Di seguito è illustrato il flusso generale durante la fase di riproduzione:

* L'utente viene autenticato.
* Viene creato un token di autorizzazione per l'utente.
* Il contenuto protetto da DRM (manifesto) viene scaricato nel lettore.
* Il lettore invia una richiesta di acquisizione della licenza ai server licenze con l'ID chiave e il token di autorizzazione.

La sezione seguente illustra la progettazione della gestione della chiave.

| **Mapping tra chiave simmetrica e asset** | **Scenario** |
| --- | --- |
| 1 a 1 |È il caso più semplice. Consente il controllo più dettagliato, ma in genere comporta un costo maggiore di distribuzione delle licenze. È necessaria almeno una richiesta di licenza per ogni asset protetto. |
| 1 a molti |È possibile usare la stessa chiave simmetrica per più asset. Ad esempio, per tutti gli asset di un gruppo logico, come un genere o un subset del genere (ad esempio, genere di film), è possibile usare una singola chiave simmetrica. |
| Molti a 1 |Per ogni asset sono necessarie più chiavi simmetriche. <br/><br/>Ad esempio, se è necessario applicare la protezione CENC dinamica con DRM multiplo per MPEG-DASH e la crittografia AES-128 dinamica per HLS, sono necessarie due chiavi simmetriche distinte. Ogni chiave simmetrica deve disporre del relativo tipo ContentKeyType. Per la chiave simmetrica usata per la protezione CENC dinamica, usare ContentKeyType.CommonEncryption. Per la chiave simmetrica usata per la crittografia AES-128 dinamica, usare ContentKeyType.EnvelopeEncryption.<br/><br/>Come ulteriore esempio, nella protezione CENC del contenuto DASH, in teoria, è possibile usare una chiave simmetrica per proteggere il flusso video e un'altra chiave simmetrica per proteggere il flusso audio. |
| Molti a molti |È una combinazione dei due scenari precedenti. Per ogni asset multiplo nello stesso gruppo di asset viene usato un set di chiavi simmetriche. |

Un altro fattore importante da considerare è l'uso di licenze permanenti e non permanenti.

Perché queste considerazioni sono importanti?

Se si usa un cloud pubblico per la distribuzione delle licenze, le licenze permanenti e non permanenti hanno un impatto diretto sul costo di distribuzione delle licenze. A titolo di esempio, si considerino i due casi di progettazione seguenti:

* Sottoscrizione mensile: usare una licenza permanente e il mapping 1 a molti tra la chiave simmetrica e l'asset. Ad esempio, per tutti i film per bambini, viene usata un'unica chiave simmetrica per la crittografia. In questo caso:

    Numero totale di licenze richieste per tutti i film per bambini/dispositivo = 1

* Sottoscrizione mensile: usare una licenza non permanente e il mapping 1 a 1 tra la chiave simmetrica e l'asset. In questo caso:

    Numero totale di licenze richieste per tutti i film per bambini/dispositivo = [numero di film guardati] x [numero di sessioni]

Questi due diversi casi di progettazione corrispondono a modelli di richieste di licenze differenti. I modelli sono, a loro volta, associati a costi di distribuzione delle licenze differenti se il servizio di distribuzione delle licenze viene fornito da un cloud pubblico, ad esempio Servizi multimediali.

## <a name="map-design-to-technology-for-implementation"></a>Mapping della progettazione alla tecnologia per l'implementazione
A questo punto, viene eseguito il mapping della progettazione generica alle tecnologie nella piattaforma Azure/Servizi multimediali, specificando la tecnologia da usare per ogni blocco predefinito.

La tabella seguente illustra il mapping:

| **Blocco predefinito** | **Tecnologia** |
| --- | --- |
| **Lettore** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Provider di identità (IdP)** |Azure Active Directory (Azure AD) |
| **Servizio token di sicurezza (STS)**  |Azure AD |
| **Flusso di lavoro protezione DRM** |Protezione dinamica di Servizi multimediali |
| **Distribuzione di licenze DRM** |* Distribuzione delle licenze di Servizi multimediali (PlayReady, Widevine, FairPlay) <br/>* Server licenze Axinom <br/>* Server licenze PlayReady personalizzato |
| **Origine** |Endpoint di streaming di Servizi multimediali |
| **Gestione delle chiavi** |Non necessaria per l'implementazione di riferimento |
| **Gestione dei contenuti** |Applicazione console in C# |

In altri termini, con Azure AD vengono usati sia IdP che STS. L'[API di Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/) viene usata per il lettore. Sia Servizi multimediali che Media Player supportano DASH e CENC con DRM multiplo.

Il diagramma seguente illustra la struttura e il flusso generali con il mapping alla tecnologia precedente:

![Crittografia CENC in Servizi multimediali](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Per configurare la crittografia CENC dinamica, lo strumento di gestione dei contenuti usa gli input seguenti:

* Contenuto aperto
* Chiave simmetrica dalla generazione/gestione delle chiavi
* URL di acquisizione delle licenze
* Elenco di informazioni da Azure AD

Di seguito è illustrato l'output dello strumento di gestione dei contenuti:

* ContentKeyAuthorizationPolicy contiene la specifica relativa alla verifica di un token JWT da parte della distribuzione delle licenze e le specifiche relative alle licenze DRM.
* AssetDeliveryPolicy contiene le specifiche sul formato di streaming, sulla protezione DRM e sugli URL di acquisizione delle licenze.

Di seguito è illustrato il flusso in fase di runtime:

* In seguito all'autenticazione dell'utente, viene generato un token JWT.
* Una delle attestazioni nel token JWT è l'attestazione "groups", contenente l'ID oggetto gruppo EntitledUserGroup. Questa attestazione viene usata per passare il controllo dei diritti.
* Il lettore scarica il manifesto client di un contenuto protetto dalla crittografia CENC e identifica quanto segue:
   * ID chiave
   * Contenuto protetto dalla crittografia CENC
   * URL di acquisizione delle licenze
* Il lettore crea una richiesta di acquisizione della licenza basata sul browser/DRM supportato. Nella richiesta di acquisizione della licenza vengono inviati anche l'ID chiave e il token JWT. Il servizio di distribuzione delle licenze verifica il token JWT e le attestazioni contenute prima di rilasciare la licenza necessaria.

## <a name="implementation"></a>Implementazione
### <a name="implementation-procedures"></a>Procedure di implementazione
L'implementazione è costituita dai passaggi seguenti:

1. Preparare l'asset di test. Codificare/creare un pacchetto per un video di test in formato MP4 frammentato a più velocità in bit in Servizi multimediali. Questo asset *non* è protetto da DRM. La protezione DRM viene applicata più avanti con la protezione dinamica.

2. Creare un ID chiave e una chiave simmetrica (facoltativamente da un seme chiave). In questa caso, non è necessario il sistema di gestione delle chiavi perché per poche attività di test sono sufficienti un solo ID chiave e la chiave simmetrica.

3. Usare l'API di Servizi multimediali per configurare i servizi di distribuzione delle licenze con DRM multiplo per l'asset di test. Se si usano i server licenze personalizzati dall'azienda o dai fornitori dell'azienda anziché i servizi licenze in Servizi multimediali, è possibile ignorare questo passaggio. È possibile specificare gli URL di acquisizione delle licenze quando si configura il servizio di distribuzione delle licenze. L'API di Servizi multimediali è necessaria per specificare alcune configurazioni dettagliate, ad esempio la restrizione dei criteri di autorizzazione e i modelli di risposta delle licenze per servizi licenze DRM diversi. Attualmente il portale di Azure non fornisce l'interfaccia utente necessaria per questa configurazione. Per informazioni sull'API ed esempi di codice, vedere [Usare la crittografia comune dinamica Widevine e/o PlayReady](media-services-protect-with-playready-widevine.md).

4. Usare l'API di Servizi multimediali per configurare i criteri di distribuzione per l'asset di test. Per informazioni sull'API ed esempi di codice, vedere [Usare la crittografia comune dinamica Widevine e/o PlayReady](media-services-protect-with-playready-widevine.md).

5. Creare e configurare un tenant di Azure AD in Azure.

6. Creare alcuni gruppi e account utente nel tenant di Azure AD. Creare almeno un gruppo "Utente con diritti" e aggiungere un utente a questo gruppo. Gli utenti in questo gruppo passeranno il controllo dei diritti nella fase di acquisizione di una licenza. Gli utenti non presenti in questo gruppo non passeranno il controllo di autenticazione e non potranno acquisire una licenza. L'appartenenza al gruppo "Utente con diritti" è un'attestazione di gruppo obbligatoria nel token JWT rilasciato da Azure AD. Questo requisito di attestazione viene specificato nel passaggio di configurazione dei servizi di distribuzione delle licenze con DRM multiplo.

7. Creare un'app MVC ASP.NET per ospitare un lettore video. Questa app ASP.NET è protetta con l'autenticazione utente nel tenant di Azure AD. Le attestazioni appropriate sono incluse nei token di accesso ottenuti dopo l'autenticazione utente. Per questo passaggio, è consigliata l'API OpenID Connect. Installare i pacchetti NuGet seguenti:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Creare un lettore usando l'[API di Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). Usare l'[API ProtectionInfo di Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/) per specificare la tecnologia DRM da usare in varie piattaforme DRM.

9. La tabella seguente illustra la matrice di test:

    | **DRM** | **Browser** | **Risultato per un utente con diritti** | **Risultato per un utente senza diritti** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge o Internet Explorer 11 in Windows 10 |Operazione riuscita |Operazione non riuscita |
    | **Widevine** |Chrome in Windows 10 |Operazione riuscita |Operazione non riuscita |
    | **FairPlay** |Da definire | | |

Per informazioni su come impostare Azure AD per un'app lettore MVC ASP.NET, vedere [Integrate an Azure Media Services OWIN MVC-based app with Azure Active Directory and restrict content key delivery based on JWT claims](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/) (Integrare un'app basata su OWIN MVC di Servizi multimediali di Azure con Azure Active Directory e limitare la distribuzione di chiavi simmetriche in base ad attestazioni JWT).

Per altre informazioni, vedere [JWT token authentication in Azure Media Services and dynamic encryption](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/) (Autenticazione di token JWT in Servizi multimediali di Azure e crittografia dinamica).  

Per informazioni su Azure AD:

* Le informazioni per gli sviluppatori sono disponibili nella [Guida per gli sviluppatori di Azure Active Directory](../active-directory/active-directory-developers-guide.md).
* Le informazioni per gli amministratori sono disponibili in [Amministrare la directory di Azure AD](../active-directory/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Problematiche di implementazione
Usare le informazioni seguenti per la risoluzione dei problemi di implementazione.

* L'URL dell'autorità di certificazione deve terminare con "/". Il destinatario deve essere l'ID client dell'applicazione lettore. Inoltre, aggiungere "/" alla fine dell'URL dell'autorità di certificazione.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    In [JWT Decoder](http://jwt.calebb.net/) vengono visualizzati **aud** e **iss** come nel token JWT seguente:

    ![Token JSON Web](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Aggiungere autorizzazioni all'applicazione in Azure AD nella scheda **Configura** dell'applicazione. Le autorizzazioni sono obbligatorie per ogni applicazione, indipendentemente dal tipo di versione: locale e distribuita.

    ![Autorizzazioni](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* Usare l'autorità di certificazione corretta quando si configura la protezione CENC dinamica.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Lo scenario seguente non funziona:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    Il GUID è l'ID tenant di Azure AD. Il GUID si trova nel menu di scelta rapida **Endpoint** nel portale di Azure.

* Concedere i privilegi delle attestazioni di appartenenza al gruppo. Verificare che nel file manifesto dell'applicazione Azure AD sia presente quanto segue: 

    "groupMembershipClaims": "All"    (il valore predefinito è Null)

* Impostare l'oggetto TokenType appropriato quando si creano i requisiti relativi alle restrizioni.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Poiché viene aggiunto il supporto di JWT (Azure AD) oltre a SWT (ACS), l'oggetto TokenType predefinito è TokenType.JWT. Se si usa SWT/ACS, è necessario impostare il token su TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Argomenti aggiuntivi per l'implementazione
Questa sezione illustra alcuni argomenti aggiuntivi sulla progettazione e sull'implementazione.

### <a name="http-or-https"></a>Protocollo HTTP o HTTPS?
L'applicazione lettore MVC ASP.NET deve supportare quanto segue:

* Autenticazione utente con Azure AD, che deve usare il protocollo HTTPS.
* Scambio di token JWT tra il client e Azure AD, che deve usare il protocollo HTTPS.
* Acquisizione della licenza DRM dal client, che deve essere gestita tramite protocollo HTTPS se la distribuzione delle licenze avviene tramite Servizi multimediali. La famiglia di prodotti PlayReady non obbliga a usare il protocollo HTTPS per la distribuzione delle licenze. Se il server delle licenze PlayReady non rientra nei Servizi multimediali, usare il protocollo HTTP o HTTPS.

L'applicazione lettore ASP.NET usa il protocollo HTTPS come procedura consigliata, quindi Media Player si trova in una pagina in HTTPS. Tuttavia, per lo streaming è preferibile usare il protocollo HTTP. È quindi necessario considerare il problema del contenuto misto.

* Il browser non consente il contenuto misto, ma lo consentono i plug-in come Silverlight e OSMF per Smooth e DASH. Il contenuto misto pone una questione di sicurezza a causa della minaccia derivante dalla possibilità di inserire codice JavaScript dannoso che può mettere a rischio i dati dei clienti. Per impostazione predefinita, il browser blocca questa funzionalità. Finora il solo modo per aggirare il problema è intervenire sul lato server (origine), per consentire tutti i domini (sia HTTPS che HTTP), ma probabilmente nemmeno questa è una buona idea.
* Evitare il contenuto misto. Sia l'applicazione lettore che Media Player devono usare il protocollo HTTP o HTTPS. Quando gestisce il contenuto misto, la tecnologia silverlightSS richiede la cancellazione di un avviso di contenuto misto. La tecnologia flashSS gestisce il contenuto misto senza avvisi di contenuto misto.
* Se l'endpoint di streaming è stato creato prima di agosto 2014, non supporterà il protocollo HTTPS. In questo caso, creare e usare un nuovo endpoint di streaming per il protocollo HTTPS.

Nelle informazioni di riferimento sull'implementazione per i contenuti protetti da DRM, sia l'applicazione che il flusso usano il protocollo HTTPS. Per i contenuti aperti, per il lettore non è necessaria l'autenticazione o la licenza, è quindi possibile usare il protocollo HTTP o HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Rollover della chiave di firma di Azure Active Directory
Il rollover della chiave di firma è un aspetto importante da tenere in considerazione nella fase di implementazione. Se si ignora questo passaggio, è possibile che il sistema finito smetta completamente di funzionare entro al massimo sei settimane.

Azure AD usa gli standard di settore per stabilire una relazione di trust tra questo servizio di gestione delle identità e le applicazioni che lo usano. In particolare, Azure AD usa una chiave per la firma costituita da una coppia di chiavi pubblica e privata. Quando Azure AD crea un token di sicurezza contenente informazioni sull'utente, questo token viene firmato da Azure AD con una chiave privata prima di essere inviato all'applicazione. Per verificare che il token sia valido e originato da Azure AD, l'applicazione deve convalidare la firma del token. L'applicazione usa la chiave pubblica esposta da Azure AD che è contenuta nel documento metadati federazione del tenant. Questa chiave pubblica e la chiave di firma da cui deriva sono le stesse usate per tutti i tenant in Azure AD.

Per altre informazioni sul rollover della chiave di Azure AD, vedere [Rollover della chiave di firma in Azure Active Directory](../active-directory/active-directory-signing-key-rollover.md).

In merito alla [coppia di chiavi pubblica-privata](https://login.microsoftonline.com/common/discovery/keys/):

* La chiave privata viene usata da Azure AD per generare un token JWT.
* La chiave pubblica viene usata da un'applicazione, ad esempio i servizi di distribuzione delle licenze DRM in Servizi multimediali, per verificare il token JWT.

Per motivi di sicurezza, Azure AD consente di ruotare il certificato periodicamente (ogni sei settimane). In caso di violazioni della sicurezza, il rollover della chiave può essere eseguito in qualsiasi momento. Pertanto, i servizi di distribuzione delle licenze in Servizi multimediali devono aggiornare la chiave pubblica usata al momento della rotazione della coppia di chiavi da parte di Azure AD. In caso contrario, l'autenticazione del token in Servizi multimediali ha esito negativo e non viene rilasciata alcuna licenza.

Per configurare il servizio, impostare TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument quando si configurano i servizi di distribuzione delle licenze DRM.

Di seguito è illustrato il flusso del token JWT:

* Azure AD rilascia il token JWT con la chiave privata corrente per un utente autenticato.
* Quando un lettore visualizza un contenuto protetto dalla crittografia CENC con DRM multiplo, individua innanzitutto il token JWT rilasciato da Azure AD.
* Il lettore invia una richiesta di acquisizione della licenza con il token JWT ai servizi di distribuzione delle licenze in Servizi multimediali.
* I servizi di distribuzione delle licenze in Servizi multimediali usano la chiave pubblica corrente/valida di Azure AD per verificare il token JWT, prima di rilasciare le licenze.

I servizi di distribuzione delle licenze DRM cercano sempre la chiave pubblica corrente/valida di Azure AD. La chiave pubblica presentata da Azure AD è quella usata per verificare un token JWT rilasciato da Azure AD.

Che cosa accade se il rollover della chiave viene eseguito dopo che Azure AD ha generato un token JWT, ma prima che il token JWT venga inviato dai lettori ai servizi di distribuzione delle licenze DRM in Servizi multimediali per la verifica?

Poiché il rollover di una chiave può essere eseguito in qualsiasi momento, nel documento metadati federazione è sempre disponibile più di una chiave pubblica valida. Il servizio di distribuzione delle licenze di Servizi multimediali può usare una qualsiasi delle chiavi specificate nel documento. Poiché di una chiave può essere eseguito il rollback immediatamente, un'altra può sostituirla e così via.

### <a name="where-is-the-access-token"></a>Dov'è il token di accesso?
Se si esamina come un'app Web chiama un'app per le API in [Identità applicazione con concessione delle credenziali client OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md#web-application-to-web-api), il flusso di autenticazione è il seguente:

* Un utente accede ad Azure AD nell'applicazione Web. Per altre informazioni, vedere [Da Web browser ad applicazione Web](../active-directory/develop/active-directory-authentication-scenarios.md#web-browser-to-web-application).
* L'endpoint di autorizzazione di Azure AD reindirizza di nuovo l'agente utente all'applicazione client con un codice di autorizzazione. L'agente utente restituisce il codice di autorizzazione all'URI di reindirizzamento dell'applicazione client.
* L'applicazione Web deve acquisire un token di accesso per l'autenticazione nell'API Web e il recupero della risorsa desiderata. Esegue una richiesta all'endpoint di token di Azure AD e fornisce credenziali, ID client e URI dell'ID applicazione dell'API Web. Presenta il codice di autorizzazione per dimostrare che l'utente ha acconsentito.
* Azure AD autentica l'applicazione e restituisce un token di accesso JWT che viene usato per chiamare l'API Web.
* Sul protocollo HTTPS l'applicazione Web usa il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Bearer" nell'intestazione dell'autorizzazione della richiesta all'API Web. L'API Web convalida quindi il token JWT. Se la convalida ha esito positivo, restituisce la risorsa desiderata.

In questo flusso di identità dell'applicazione, l'API Web fa affidamento sul fatto che l'applicazione Web abbia autenticato l'utente. Per questo motivo il modello è definito sottosistema attendibile. Il [diagramma del flusso di autorizzazione](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) illustra il funzionamento del flusso per la concessione del codice di autorizzazione.

L'acquisizione della licenza con la restrizione token segue lo stesso modello di sottosistema attendibile. Il servizio di distribuzione delle licenze in Servizi multimediali è la risorsa API Web, o la "risorsa back-end" a cui un'applicazione Web deve accedere. Dov'è quindi il token di accesso?

Il token di accesso si ottiene da Azure AD. Al termine dell'autenticazione utente, viene restituito un codice di autorizzazione. Il codice di autorizzazione viene quindi usato, con l'ID client e la chiave app, per ottenere in cambio il token di accesso. Il token di accesso viene usato per accedere a un'applicazione "puntatore" che punta a un servizio di distribuzione delle licenze di Servizi multimediali o lo rappresenta.

Per registrare e configurare l'app "puntatore" in Azure AD, seguire questa procedura:

1. Nel tenant di Azure AD:

   * Aggiungere un'applicazione (risorsa) con l'URL di accesso https://[nome_risorsa].azurewebsites.net/. 
   * Aggiungere un'ID app con l'URL https://[nome_tenant_aad].onmicrosoft.com/[nome_risorsa].

2. Aggiungere una nuova chiave per l'app risorsa.

3. Aggiornare il file manifesto dell'app in modo che il valore della proprietà groupMembershipClaims sia "groupMembershipClaims": "All".

4. Nell'app Azure AD che punta all'app Web del lettore, nella sezione **Autorizzazioni per altre applicazioni** aggiungere l'app risorsa aggiunta nel passaggio 1. In **Autorizzazioni delegate** selezionare **Accedi a [nome_risorsa]**. Questa opzione consente all'app Web di creare i token di accesso per accedere all'app risorsa. È consigliabile eseguire questa operazione sia per la versione locale che per quella distribuita dell'app Web se si sviluppa con Visual Studio e l'app Web di Azure.

Il token JWT rilasciato da Azure AD è il token di accesso usato per accedere alla risorsa puntatore.

### <a name="what-about-live-streaming"></a>Come funziona lo streaming live?
L'articolo ha finora illustrato casi relativi agli asset su richiesta. Come funziona lo streaming live?

È possibile usare esattamente la stessa progettazione e la stessa implementazione per proteggere lo streaming live in Servizi multimediali, trattando l'asset associato a un programma come asset video on demand (VOD).

In particolare, per eseguire lo streaming live in Servizi multimediali, è necessario creare un canale e quindi un programma nel canale. Per creare il programma, è necessario creare un asset che contenga l'archivio live per il programma. Per fornire la protezione CENC con DRM multiplo del contenuto live, applicare all'asset la stessa configurazione/elaborazione che si applicherebbe se fosse un asset VOD prima di avviare il programma.

### <a name="what-about-license-servers-outside-media-services"></a>Come funzionano i server licenze all'esterno di Servizi multimediali?
I clienti spesso investono in una farm di server licenze nel proprio data center oppure ospitata da provider di servizi DRM. Con la protezione del contenuto di Servizi multimediali, è possibile operare in modalità ibrida. È possibile ospitare e proteggere dinamicamente i contenuti in Servizi multimediali, mentre le licenze DRM vengono distribuite dal server all'esterno di Servizi multimediali. In questo caso, tenere presenti le modifiche seguenti:

* Il servizio token di sicurezza deve rilasciare token che siano accettabili e verificabili dalla farm di server licenze. Ad esempio, i server licenze Widevine forniti da Axinom richiedono uno specifico token JWT contenente un elemento "entitlement message". Per rilasciare tale token JWT, è quindi necessario un servizio token di sicurezza. Per informazioni su questo tipo di implementazione, consultare la [Documentazione di Microsoft Azure](https://azure.microsoft.com/documentation/) e vedere [Uso di Axinom per fornire licenze Widevine ai Servizi multimediali di Azure](media-services-axinom-integration.md).
* Non è più necessario configurare il servizio di distribuzione delle licenze (ContentKeyAuthorizationPolicy) in Servizi multimediali. È sufficiente fornire gli URL di acquisizione delle licenze (per PlayReady, Widevine e FairPlay) quando si configura AssetDeliveryPolicy durante la configurazione della crittografia CENC con DRM multiplo.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Come procedere per usare un servizio token di sicurezza personalizzato?
È possibile che un cliente scelga di usare un servizio token di sicurezza personalizzato per fornire i token JWT. I motivi includono:

* Il provider di identità usato dal cliente non supporta il servizio token di sicurezza. In questo caso, può essere opportuno usare un servizio token di sicurezza personalizzato.
* Il cliente può avere bisogno di un controllo più flessibile o più rigido nell'integrazione del servizio token di sicurezza con il sistema di fatturazione sottoscrittore del cliente. Un operatore MVPD, ad esempio, può offrire più pacchetti sottoscrittore OTT: Premium, Basic, Sport e così via. L'operatore può associare le attestazioni in un token al pacchetto di un sottoscrittore in modo che siano disponibili solo i contenuti del pacchetto corretto. In questo caso, un servizio token di sicurezza personalizzato fornisce la flessibilità e il controllo necessari.

Quando si usa un servizio token di sicurezza personalizzato, è necessario apportare due modifiche:

* Quando si configura un servizio di distribuzione delle licenze per un asset, è necessario specificare la chiave di sicurezza usata per la verifica dal servizio token di sicurezza personalizzato invece della chiave corrente di Azure AD. Di seguito sono riportate altre informazioni. 
* Quando viene generato un token JTW, viene specificata una chiave di sicurezza invece della chiave privata del certificato X509 corrente in Azure AD.

Esistono due tipi di chiavi di sicurezza:

* Chiave simmetrica: la stessa chiave viene usata per generare e verificare un token JWT.
* Chiave asimmetrica: un coppia di chiavi pubblica-privata in un certificato X509 viene usata con una chiave privata per la crittografia/generazione di un token JWT e con la chiave pubblica per la verifica del token.

> [!NOTE]
> Se si usa .NET Framework/C# come piattaforma di sviluppo, il certificato X509 usato per la chiave di sicurezza asimmetrica deve avere una lunghezza della chiave pari almeno a 2048 bit. È un requisito della classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey in .NET Framework. In caso contrario, viene generata un'eccezione simile alla seguente:

> IDX10630: 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' per la firma non può essere inferiore a '2048' bit.

## <a name="the-completed-system-and-test"></a>Sistema completato e test
Questa sezione illustra gli scenari seguenti nel sistema end-to-end completato per offrire una panoramica generale di quanto avviene prima di ottenere un account di accesso:

* Se è necessario uno scenario non integrato:

    * Per le risorse video ospitate in Servizi multimediali non protette o protette da DRM, ma senza autenticazione tramite token (viene rilasciata una licenza a chiunque ne faccia richiesta), è possibile eseguire il test senza effettuare l'accesso. Se il flusso video è su HTTP, passare a questo protocollo.

* Se è necessario uno scenario end-to-end:

    * Per le risorse video protette da DRM dinamico in Servizi multimediali, con autenticazione tramite token e token JWT generato da Azure AD, è necessario eseguire l'accesso.

Per l'applicazione Web lettore e il relativo accesso, vedere [questo sito Web](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Accesso utente
Per testare il sistema DRM end-to-end integrato, è necessario che sia stato creato o aggiunto un account.

Quale account?

Anche se in origine l'accesso ad Azure era consentito solo agli utenti con account Microsoft, ora l'accesso è consentito agli utenti di entrambi i sistemi. Tutte le proprietà di Azure ora considerano attendibile l'autenticazione con Azure AD e questo servizio esegue l'autenticazione degli utenti dell'organizzazione. È stata stabilita una relazione di federazione in cui Azure AD considera attendibile il sistema di identità utente degli account Microsoft per l'autenticazione degli utenti. Di conseguenza, Azure AD può eseguire l'autenticazione degli account Microsoft guest e degli account Azure AD nativi.

Poiché Azure AD considera attendibile il dominio degli account Microsoft, è possibile aggiungere qualsiasi account da uno dei domini seguenti al tenant di Azure AD personalizzato e usare l'account per eseguire l'accesso:

| **Nome di dominio** | **Dominio** |
| --- | --- |
| **Dominio del tenant di Azure AD personalizzato** |nome.onmicrosoft.com |
| **Dominio aziendale** |microsoft.com |
| **Dominio degli account Microsoft** |outlook.com, live.com, hotmail.com |

È possibile contattare gli autori per farsi creare o aggiungere un account.

Di seguito sono riportati gli screenshot di alcune pagine di accesso usate da diversi account dominio:

**Account dominio del tenant di Azure AD personalizzato**: pagina di accesso personalizzata del dominio del tenant di Azure AD personalizzato.

![Account dominio del tenant di Azure AD personalizzato](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Account dominio Microsoft con smart card**: pagina di accesso personalizzata dall'IT aziendale Microsoft con autenticazione a due fattori.

![Account dominio del tenant di Azure AD personalizzato](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Account Microsoft**: pagina di accesso dell'account Microsoft per gli utenti.

![Account dominio del tenant di Azure AD personalizzato](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Usare Encrypted Media Extensions per PlayReady
In un browser moderno con il supporto EME (Encrypted Media Extensions) per PlayReady, ad esempio Internet Explorer 11 in Windows 8.1 o versioni successive e il browser Microsoft Edge in Windows 10, PlayReady è il sistema DRM sottostante per EME.

![Usare EME per PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Nel lettore è presente un'area scura perché la protezione di PlayReady impedisce di acquisire schermate da un video protetto.

La schermata seguente mostra i plug-in del lettore e il supporto per Microsoft Security Essentials (MSE)/EME:

![Plug-in del lettore per PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME in Microsoft Edge e Internet Explorer 11 in Windows 10 consente di chiamare [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) sui dispositivi Windows 10 che lo supportano. PlayReady SL3000 sblocca il flusso di contenuti premium avanzati (4 KB, HDR) e nuovi modelli di distribuzione dei contenuti (per contenuti avanzati).

Per quanto riguarda i dispositivi Windows, PlayReady è il solo DRM nell'hardware disponibile nei dispositivi Windows (PlayReady SL3000). Un servizio di streaming può usare PlayReady tramite EME o un'applicazione UWP (Universal Windows Platform) e offrire una migliore qualità video usando PlayReady SL3000 anziché un altro DRM. In genere, il contenuto fino a 2 KB passa attraverso Chrome o Firefox e il contenuto fino a 4 KB passa attraverso Microsoft Edge, Internet Explorer 11 o un'applicazione UWP sullo stesso dispositivo. La quantità dipende dalle impostazioni del servizio e dall'implementazione.

#### <a name="use-eme-for-widevine"></a>Usare EME per Widevine
In un browser moderno con il supporto EME/Widevine, ad esempio Chrome 41+ in Windows 10, Windows 8.1, Mac OSX Yosemite e Chrome in Android 4.4.4, Google Widevine è il sistema DRM dietro EME.

![Usare EME per Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine non impedisce di acquisire schermate da un video protetto.

![Plug-in del lettore per Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Utenti senza diritti
Se un utente non appartiene al gruppo "Utenti con diritti", non soddisfa il controllo dei diritti. Il servizio licenze DRM multiplo quindi rifiuta di rilasciare la licenza richiesta, come illustrato. La descrizione dettagliata è "License acquire failed" (Acquisizione licenza non riuscita), come previsto dalla progettazione.

![Utenti senza diritti](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Eseguire un servizio token di sicurezza personalizzato
Se si esegue un servizio token di sicurezza personalizzato, il token JWT viene rilasciato dal servizio token di sicurezza personalizzato tramite una chiave simmetrica o asimmetrica.

La schermata seguente illustra uno scenario che usa una chiave simmetrica (con Chrome):

![Servizio token di sicurezza personalizzato con una chiave simmetrica](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

La schermata seguente illustra uno scenario che usa una chiave asimmetrica tramite un certificato X509 (con un browser moderno Microsoft):

![Servizio token di sicurezza personalizzato con una chiave asimmetrica](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

In entrambi i casi precedenti, l'autenticazione utente è la stessa, ovvero viene eseguita tramite Azure AD. L'unica differenza è che i token JWT vengono rilasciati dal servizio token di sicurezza personalizzato invece che da Azure AD. Quando si configura la protezione CENC dinamica, la restrizione del servizio di distribuzione delle licenze specifica il tipo di token JWT, una chiave simmetrica o asimmetrica.

## <a name="summary"></a>Riepilogo
Questo documento ha illustrato la crittografia CENC con DRM nativo multiplo e il controllo di accesso tramite l'autenticazione token: la progettazione e l'implementazione con Azure, Servizi multimediali e Media Player.

* Sono state presentate informazioni di riferimento sulla progettazione contenente tutti i componenti necessari in un sottosistema DRM/CENC.
* Sono state presentate informazioni di riferimento sull'implementazione in Azure, Servizi multimediali e Media Player.
* Sono stati anche illustrati alcuni argomenti direttamente correlati alla progettazione e all'implementazione.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
