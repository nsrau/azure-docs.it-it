---
title: "CENC con il controllo tramite più DRM e accesso: un progetto di riferimento e l'implementazione in Azure e servizi multimediali di Azure | Documenti Microsoft"
description: Vengono fornite informazioni sulla licenza di Microsoft Smooth Streaming Client Porting Kit.
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>CENC con il controllo tramite più DRM e accesso: un progetto di riferimento e l'implementazione in Azure e servizi multimediali di Azure
 
## <a name="introduction"></a>Introduzione
Progettazione e creazione di un sottosistema di digital rights management (DRM) per un over-the-top (OTT) o soluzioni di flusso in linea è un'attività complessa. Operatori/online provider video affidano in genere questa attività per i provider di servizi DRM specializzate. L'obiettivo di questo documento è presente un progetto di riferimento e l'implementazione di un sottosistema DRM end-to-end in una soluzione di flusso in linea o un OTT.

I lettori di destinazione per questo documento sono tecnici che operano in sottosistemi DRM di OTT o soluzioni di flusso/multiscreen online o i lettori di utenti interessati a sottosistemi DRM. Il presupposto è che familiarità con almeno una delle tecnologie DRM sul mercato, ad esempio PlayReady, Widevine, FairPlay o accesso Adobe Reader.

È inoltre possibile includere la discussione di DRM, common encryption (CENC) con multi-DRM. Una tendenza principale nel flusso in linea e del settore OTT consiste nell'utilizzare CENC con DRM più nativa su varie piattaforme client. Questa tendenza è uno spostamento a quello precedente utilizzato un singolo DRM e il relativo client SDK per diverse piattaforme client. Quando si utilizza CENC con DRM più native, PlayReady sia Widevine vengono crittografati in base il [Common Encryption (CENC ISO/IEC 23001-7)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) specifica.

I vantaggi di CENC con multi-DRM vengono:

* Consente di ridurre il costo di crittografia, perché viene utilizzato un processo di crittografia solo per le piattaforme diverse con relativo DRMs nativo.
* Riduce i costi di gestione asset crittografato perché è necessaria una sola copia di un asset crittografato.
* Elimina client DRM costi della licenza perché il client nativo di DRM è in genere gratuito per la piattaforma nativa.

Microsoft è un promotore active di trattino e CENC con alcuni principali del settore. Servizi multimediali di Azure fornisce il supporto per DASH e CENC. Per gli annunci di recente, vedere i seguenti blog:

*  [Annuncio dei servizi di distribuzione delle licenze Google Widevine in Servizi multimediali di Azure](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Servizi multimediali di Azure aggiunge Google Widevine pacchetti per il recapito di un flusso di multi-DRM](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="overview-of-this-article"></a>Panoramica dell'articolo
Gli obiettivi di questo articolo sono:

* Fornire un progetto di riferimento di un sottosistema DRM che utilizza CENC con multi-DRM.
* Fornire un'implementazione di riferimento su una piattaforma di servizi di Azure o dei supporti.
* Discutere alcuni argomenti di progettazione e implementazione.

Nell'articolo, il termine "multi-DRM" vengono trattati i seguenti prodotti:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

Nella tabella seguente sono riepilogate le app di piattaforma nativa/nativo e browser supportati da ogni DRM.

| **Piattaforma client** | **Supporto nativo DRM** | **Browser/app** | **Formati di streaming** |
| --- | --- | --- | --- |
| **Smart TV, STB operatore, STB OTT** |Principalmente PlayReady e/o Widevine e/o altro |Linux, Opera, WebKit, altri |Vari formati |
| **Dispositivi Windows 10 (PC Windows, i Tablet Windows, Windows Phone, Xbox)** |PlayReady |MS Edge/IE11/EME<br/><br/><br/>Piattaforma UWP (Universal Windows Platform) |TRATTINO (per HLS, PlayReady non è supportato)<br/><br/>DASH, Smooth Streaming (per HLS, PlayReady non è supportato) |
| **Dispositivi Android (telefoni, tablet, TV)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), client OS X e Apple TV** |FairPlay |Safari 8+/EME |HLS |


Considerando lo stato corrente della distribuzione per ogni DRM, un servizio richiede in genere implementare due o tre DRMs per assicurarsi che tutti i tipi di endpoint si indirizza il migliore.

Occorre raggiungere un compromesso tra la complessità della logica del servizio e la complessità sul lato client per ottenere un certo livello di esperienza utente nei vari client.

Per effettuare la selezione, tenere presenti:

* PlayReady è implementata in modo nativo in ogni dispositivo di Windows, in alcuni dispositivi Android e disponibili tramite il SDK di software su praticamente qualsiasi piattaforma.
* Widevine viene implementato in modo nativo in tutti i dispositivi Android, Chrome e altri dispositivi.
* FairPlay è disponibile solo in iOS e nei client Mac OS o tramite iTunes.

Sono disponibili due opzioni per un tipico multi-DRM:

* PlayReady e Widevine
* PlayReady, Widevine e FairPlay

## <a name="a-reference-design"></a>Progettazione di riferimento
In questa sezione viene presentato un progetto di riferimento che è indipendente per le tecnologie utilizzate per l'implementazione.

Un sottosistema DRM può contenere i seguenti componenti:

* Gestione della chiave
* Creazione pacchetto DRM
* Distribuzione di licenze DRM
* Controllo dei diritti
* Autenticazione/autorizzazione
* Lettore
* Rete origine/contenuti (CDN)

Il diagramma seguente illustra l'interazione di alto livello tra i componenti in un sottosistema DRM:

![Sottosistema DRM con CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

La progettazione prevede tre livelli di base:

* Un livello di back-office (nero) non è esposta esternamente.
* Un livello di rete Perimetrale (blu scuro) contiene tutti gli endpoint rivolte al pubblico.
* Un livello di internet pubblico (celeste) contiene la rete CDN e lettori con il traffico attraverso la rete internet pubblica.

Inoltre dovrebbe esserci uno strumento di gestione dei contenuti di controllo protezione DRM, indipendentemente dal fatto sia la crittografia statica o dinamica. Gli input per la crittografia DRM includono:

* Contenuto del video MBR
* Chiave simmetrica
* URL di acquisizione di licenze

Ecco il flusso di alto livello durante la fase di riproduzione:

* L'utente è autenticato.
* Viene creato un token di autorizzazione per l'utente.
* Il contenuto protetto con DRM (manifesto) viene scaricato al lettore.
* Il giocatore invia una richiesta di acquisizione licenza per il server licenze con una chiave ID e un token di autorizzazione.

Nella sezione seguente viene illustrata la progettazione della gestione delle chiavi.

| **Entità ContentKey-a-asset** | **Scenario** |
| --- | --- |
| 1-a-1 |È il caso più semplice. Consente il controllo più dettagliato, Ma in questo modo vengono in genere comporta il costo più elevato di recapito licenza. Come minimo, è necessaria per ogni asset protetto con una richiesta di licenza. |
| 1-a-molti |È possibile usare la stessa chiave simmetrica per più asset. Ad esempio, per tutte le risorse in un gruppo logico, ad esempio un genere o il subset di un genere o gene movie, è possibile utilizzare una singola chiave simmetrica. |
| Molti-a-1 |Per ogni asset sono necessarie più chiavi simmetriche. <br/><br/>Ad esempio, se è necessario applicare la protezione CENC dinamica con multi-DRM per MPEG-DASH e la crittografia dinamica AES-128 per HLS, è necessario due chiavi simmetriche separate. Ogni chiave simmetrica è necessario un proprio ContentKeyType. (Per la chiave simmetrica utilizzata per la protezione CENC dinamica, utilizzare ContentKeyType.CommonEncryption. Per la chiave simmetrica utilizzata per la crittografia dinamica AES-128, utilizzare ContentKeyType.EnvelopeEncryption).<br/><br/>Ad esempio, nella protezione CENC del contenuto DASH, in teoria, è possibile utilizzare una chiave simmetrica per proteggere il flusso video e un'altra chiave simmetrica per proteggere il flusso audio. |
| Molti-a-molti |Combinazione dei due scenari precedenti. Un set di chiavi simmetriche è usato per ognuna delle attività più nello stesso gruppo di risorse. |

Un altro fattore importante da considerare è l'utilizzo di licenze permanenti e non persistenti.

Perché queste considerazioni sono importanti?

Se si utilizza un cloud pubblico per il recapito di licenza, licenze permanenti e non persistenti avere un impatto diretto al costo di recapito di licenza. Due casi di progettazione seguenti hanno lo scopo di illustrare:

* Una sottoscrizione mensile: utilizzare una licenza permanente e mapping di key-asset contenuto 1-a-molti. Ad esempio, per i film dei figli, è utilizzare una singola chiave simmetrica per la crittografia. In questo caso:

    Numero totale di licenze richiesto per filmati/dispositivo tutti i figli = 1

* Una sottoscrizione mensile: utilizzare una licenza non persistente e mapping 1 a 1 tra risorse e di chiave simmetrica. In questo caso:

    Numero totale di licenze richiesto per filmati/dispositivo tutti i figli = [numero di filmati controllata] x [numero di sessioni]

Due diversi modelli generare modelli di richieste di licenza molto diversi. I diversi motivi comportare costo se il servizio di recapito licenza viene fornito da un cloud pubblico, ad esempio servizi multimediali di recapito di licenze diverso.

## <a name="map-design-to-technology-for-implementation"></a>Eseguire il mapping di progettazione per la tecnologia per l'implementazione
Successivamente, la struttura generica viene eseguito il mapping per le tecnologie della piattaforma di servizi multimediali di Azure/specificando la tecnologia da utilizzare per ogni blocco di compilazione.

Nella tabella seguente viene illustrato il mapping.

| **Blocchi predefiniti** | **Technology** |
| --- | --- |
| **Lettore** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Provider di identità (IDP)** |Azure Active Directory (Azure AD) |
| **Servizio token di sicurezza (STS)** |Azure AD |
| **Flusso di lavoro protezione DRM** |Protezione dinamica di servizi multimediali |
| **Distribuzione di licenze DRM** |* Recapito di licenza di servizi multimediali (PlayReady, Widevine, FairPlay) <br/>* Server licenze Axinom <br/>* Server di licenze PlayReady personalizzato |
| **Origine** |Endpoint di streaming di servizi multimediali |
| **Gestione delle chiavi** |Non necessaria per l'implementazione di riferimento |
| **Gestione dei contenuti** |Applicazione console in C# |

In altre parole, sia IDP e servizio token di sicurezza vengono utilizzati con Azure AD. Il [API di Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/) viene utilizzato per il lettore. Servizi multimediali e Media Player supportano entrambi DASH e CENC con multi-DRM.

Il diagramma seguente mostra la struttura complessiva e flusso con il mapping di tecnologia precedente:

![CENC su servizi multimediali](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Per impostare la crittografia dinamica CENC, lo strumento di gestione dei contenuti Usa i seguenti input:

* Apri contenuto
* Chiave simmetrica dalla chiave di generazione e gestione
* URL di acquisizione di licenze
* Un elenco di informazioni da Azure AD

Di seguito è riportato l'output dello strumento di gestione dei contenuti:

* ContentKeyAuthorizationPolicy contiene le specifiche di licenza DRM e specifica in modo recapito licenza consente di verificare un JSON Web Token JWT ().
* AssetDeliveryPolicy contiene le specifiche sui flussi di formato, protezione DRM e gli URL di acquisizione di licenze.

Ecco il flusso in fase di esecuzione:

* Quando l'autenticazione utente, viene generato un JWT.
* Tra le attestazioni contenute nel token JWT è un'attestazione di gruppi che contiene l'oggetto gruppo ID EntitledUserGroup. Questa attestazione viene utilizzata per passare il controllo sui diritti.
* Windows Media player Scarica manifesto client del contenuto protetto con CENC e identifica le operazioni seguenti:
   * ID chiave.
   * Il contenuto è protetto CENC.
   * URL di acquisizione delle licenze.
* Windows Media player effettua una richiesta di acquisizione licenza in base a/DRM i browser supportati. La licenza acquisizione richiesta, la chiave che ID e il token JWT vengono inviati. Il servizio di recapito licenza verifica il token JWT e le attestazioni contenute prima di emettere la licenza necessaria.

## <a name="implementation"></a>Implementazione
### <a name="implementation-procedures"></a>Procedure di implementazione
L'implementazione include i passaggi seguenti:

1. Preparare l'attività di test. Codificare/creazione pacchetto di un test di video a più velocità in bit frammentati MP4 in servizi multimediali. Questo asset è *non* protetta con DRM. Protezione DRM avviene protezione dinamica in un secondo momento.

2. Creare una chiave ID e una chiave simmetrica (facoltativamente da un seme chiave). In questo caso, il sistema di gestione delle chiavi non è necessaria perché una sola chiave ID e la chiave simmetrica sono necessari per un paio di attività di test.

3. Utilizzare l'API di servizi multimediali per configurare servizi di recapito più DRM licenza per l'asset di test. Se si utilizza il server licenze personalizzati dall'azienda o fornitori della società anziché servizi di licenza in servizi multimediali, è possibile ignorare questo passaggio. È possibile specificare gli URL di acquisizione licenza nel passaggio quando si configura il recapito di licenza. L'API di servizi multimediali è necessaria per specificare alcune configurazioni dettagliate, ad esempio restrizione dei criteri di autorizzazione e i modelli di risposta di licenza per diversi servizi di licenza DRM. In questo momento, il portale di Azure non fornisce l'interfaccia utente necessaria per questa configurazione. Per informazioni a livello di API e codice di esempio, vedere [usare PlayReady e/o Widevine crittografia comune dinamica](media-services-protect-with-playready-widevine.md).

4. Utilizzare l'API di servizi multimediali per configurare i criteri di recapito di asset per l'asset di test. Per informazioni a livello di API e codice di esempio, vedere [usare PlayReady e/o Widevine crittografia comune dinamica](media-services-protect-with-playready-widevine.md).

5. Creare e configurare un tenant di Azure AD in Azure.

6. Creare alcuni gruppi e account utente nel tenant di Azure AD. Creare almeno un gruppo "Diritto utente" e aggiungere un utente a questo gruppo. Gli utenti in questo gruppo di passare il controllo sui diritti acquisizione della licenza. Gli utenti non di questo gruppo non passa il controllo di autenticazione e non è possibile acquisire una licenza. L'appartenenza al gruppo "Utenti intitolata" è un'attestazione di gruppi necessari nel token JWT rilasciati da Azure AD. Specificare questo requisito di attestazione nel passaggio quando si configurano servizi multimediali di multi-DRM licenza.

7. Creare un'applicazione MVC ASP.NET per ospitare un lettore video. Questa applicazione ASP.NET è protetto con l'autenticazione utente nel tenant di Azure AD. Attestazioni appropriate sono incluse nel token di accesso ottenuto dopo l'autenticazione utente. Si consiglia di OpenID Connect API per questo passaggio. Installare i pacchetti NuGet seguenti:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Creare un lettore utilizzando il [API di Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). Utilizzare il [API di Azure Media Player ProtectionInfo](http://amp.azure.net/libs/amp/latest/docs/) per specificare la tecnologia DRM da utilizzare su diverse piattaforme DRM.

9. Nella tabella seguente viene illustrata la matrice di test.

    | **DRM** | **Browser** | **Risultato per il diritto utente** | **Risultati per l'utente unentitled** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge o Internet Explorer 11 in Windows 10 |Succeed |Fail |
    | **Widevine** |Chrome in Windows 10 |Succeed |Fail |
    | **FairPlay** |Da definire | | |

Per informazioni su come configurare Azure AD per un'app di Windows Media player di ASP.NET MVC, vedere [integra un'app basata su MVC OWIN servizi multimediali di Azure con Azure Active Directory e limitare il recapito del contenuto di chiave in base alle attestazioni JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Per ulteriori informazioni, vedere [l'autenticazione con token JWT in servizi multimediali di Azure e la crittografia dinamica](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Per informazioni su Azure AD:

* È possibile trovare informazioni per gli sviluppatori nel [Guida per gli sviluppatori di Azure Active Directory](../active-directory/active-directory-developers-guide.md).
* È possibile trovare informazioni amministratore negli [directory tenant di Azure AD amministrare](../active-directory/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Alcuni problemi di implementazione
Utilizzare le informazioni sulla risoluzione dei problemi seguenti per informazioni su problemi di implementazione.

* L'autorità emittente URL deve terminare con "/". Il gruppo di destinatari deve essere l'ID di Windows Media player applicazione client. Inoltre, aggiungere "/" alla fine dell'URL dell'autorità di certificazione.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    Nel [JWT decodificatore](http://jwt.calebb.net/), vedrai **aud** e **iss**, come illustrato nel token JWT:

    ![Token JSON Web](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Aggiungere le autorizzazioni per l'applicazione in Azure AD sul **configura** scheda dell'applicazione. Sono necessarie autorizzazioni per ogni applicazione, le versioni locali e distribuite.

    ![Autorizzazioni](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* Utilizzare l'emittente corretto quando si configura la protezione CENC dinamica.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Non utilizzare le operazioni seguenti:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    Il GUID è l'ID del tenant di Azure AD. Il GUID è reperibile nel **endpoint** menu di scelta rapida nel portale di Azure.

* Concedere i privilegi delle attestazioni di appartenenza al gruppo. Assicurarsi che di seguito è riportato nel file manifesto dell'applicazione di Azure AD: 

    "groupMembershipClaims": "All" (il valore predefinito è null)

* Impostare il TokenType corretto durante la creazione di requisiti di limitazione.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Poiché si aggiunge il supporto per i JWT (Azure AD), oltre a SWT (ACS), il valore predefinito TokenType è TokenType.JWT. Se si usa SWT/ACS, è necessario impostare il token per TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Argomenti aggiuntivi per l'implementazione
Questa sezione illustra alcuni argomenti aggiuntivi nella progettazione e implementazione.

### <a name="http-or-https"></a>HTTP o HTTPS?
L'applicazione di Windows Media player di ASP.NET MVC deve supportare le operazioni seguenti:

* Autenticazione degli utenti tramite Azure AD, ovvero HTTPS.
* Exchange JWT tra il client e Azure AD, ovvero HTTPS.
* Acquisizione della licenza DRM dal client, che deve essere gestito tramite HTTPS se il recapito di licenza viene fornito da servizi multimediali. La famiglia di prodotti PlayReady non impone HTTPS per il recapito di licenza. Se il server licenze PlayReady è all'esterno di servizi multimediali, è possibile utilizzare HTTP o HTTPS.

L'applicazione di Windows Media player ASP.NET usa HTTPS come procedura consigliata, in modo Media Player è presente in una pagina in HTTPS. Tuttavia, HTTP è preferibile per lo streaming, pertanto è necessario tenere presente il problema di contenuto misto.

* Il browser non accetta contenuto misto. Ma i plug-in Silverlight e il plug-in per OSMF smooth e TRATTINI. Contenuto misto è un problema di sicurezza a causa delle minacce del possibilità di inserire JavaScript dannoso, che può generare i dati dei clienti a rischio. Browser bloccare questa funzionalità per impostazione predefinita. L'unico modo per aggirare il è sul lato server (origine), consentendo a tutti i domini (indipendentemente dal protocollo HTTPS o HTTP). ma probabilmente nemmeno questa è una buona idea.
* Evitare di contenuto misto. L'applicazione di lettore e il lettore deve usare HTTP o HTTPS. Durante la riproduzione di contenuto misto, il tecnico silverlightSS richiede la cancellazione di un avviso di contenuto misto. Il tecnico flashSS gestisce il contenuto misto senza un avviso di contenuto misto.
* Se l'endpoint di streaming è stato creato prima di agosto 2014, essa non supporta HTTPS. In questo caso, creare e utilizzare un nuovo endpoint di streaming per HTTPS.

Nell'implementazione di riferimento per contenuti protetti con DRM, l'applicazione e streaming sono in HTTPS. Per aprire contenuti, il lettore non è necessaria l'autenticazione o una licenza, pertanto è possibile utilizzare HTTP o HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Rollover della chiave per la firma di Azure Active Directory
Rollover della chiave di firma è un punto importante da prendere in considerazione nell'implementazione. Se si ignora, il sistema finito infine smette di funzionare completamente entro sei settimane al massimo.

Azure AD Usa gli standard del settore per stabilire un trust tra l'elemento e le applicazioni che usano Azure AD. In particolare, Azure AD usa una chiave per la firma costituita da una coppia di chiavi pubblica e privata. Quando Azure AD crea un token di sicurezza che contiene informazioni sull'utente, è firmato da Azure AD con una chiave privata prima che venga inviato all'applicazione. Per verificare che il token sia valido e originati da Azure AD, l'applicazione deve convalidare la firma del token. L'applicazione utilizza la chiave pubblica esposta da Azure AD che è contenuto nel documento di metadati di federazione del tenant. La chiave pubblica e la chiave di firma da cui deriva, è lo stesso utilizzato per tutti i tenant di Azure AD.

Per ulteriori informazioni sul rollover della chiave di Azure AD, vedere [informazioni importanti sul rollover della chiave di firma in Azure AD](../active-directory/active-directory-signing-key-rollover.md).

Tra le [coppia di chiavi pubblica / privata](https://login.microsoftonline.com/common/discovery/keys/):

* La chiave privata viene utilizzata da Azure AD per generare un token JWT.
* La chiave pubblica viene utilizzata da un'applicazione, ad esempio servizi di recapito DRM licenze in servizi multimediali per verificare il token JWT.

Per motivi di sicurezza, Azure AD consente di ruotare il certificato periodicamente (ogni 6 settimane). In caso di violazione di sicurezza, il rollover della chiave può verificarsi a qualsiasi momento. Pertanto, i servizi di recapito di licenze in servizi multimediali è necessario aggiornare la chiave pubblica utilizzata come Azure AD ruota la coppia di chiavi. In caso contrario, l'autenticazione del token in servizi multimediali non riesce e non viene eseguita alcuna licenza.

Per configurare questo servizio, impostare TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument quando si configurano i servizi di recapito licenza DRM.

Di seguito viene illustrato il flusso JWT:

* Azure AD rilascia il token JWT con la chiave privata corrente per un utente autenticato.
* Quando un lettore rileva un CENC con contenuto protetto con DRM a multipla, individua innanzitutto il token JWT rilasciati da Azure AD.
* Il lettore invia una richiesta di acquisizione di licenza con il token JWT a servizi di recapito delle licenze in servizi multimediali.
* I servizi di recapito di licenze in servizi multimediali di utilizzano la chiave pubblica corrente/valido da Azure AD per verificare il token JWT prima di rilasciare le licenze.

Servizi multimediali di licenza DRM verificare sempre per la chiave pubblica corrente/valido da Azure AD. La chiave pubblica presentata da Azure AD è la chiave utilizzata per verificare un token JWT rilasciati da Azure AD.

Il rollover della chiave, cosa accade se si verifica dopo che Azure AD genera un token JWT ma prima di inviata il token JWT da lettori di servizi multimediali di licenza DRM in servizi multimediali per la verifica?

Poiché una chiave può essere eseguita il rollover in qualsiasi momento, più di una chiave pubblica valida è sempre disponibile nel documento di metadati di federazione. Recapito licenze di servizi multimediali è possibile utilizzare i tasti specificati nel documento. Poiché una chiave può essere eseguito il rollback appena, un altro potrebbe essere relativa sostituzione e così via.

### <a name="where-is-the-access-token"></a>Dove si trova il token di accesso?
Se si osserva come un'app web chiama un'API app in [identità applicazione con concessione di credenziali client OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md#web-application-to-web-api), il flusso di autenticazione è il seguente:

* Un utente accede ad Azure AD nell'applicazione web. Per ulteriori informazioni, vedere [browser all'applicazione web](../active-directory/develop/active-directory-authentication-scenarios.md#web-browser-to-web-application).
* L'endpoint di autorizzazione di Azure AD reindirizza di nuovo l'agente utente all'applicazione client con un codice di autorizzazione. L'agente utente restituisce il codice di autorizzazione all'URI di reindirizzamento dell'applicazione client.
* L'applicazione Web deve acquisire un token di accesso per l'autenticazione nell'API Web e il recupero della risorsa desiderata. Effettua una richiesta all'endpoint token Azure AD e fornisce le credenziali, ID client e URI ID applicazione dell'API web. Presenta il codice di autorizzazione per dimostrare che l'utente ha concesso il consenso.
* Azure AD autentica l'applicazione e restituisce un token di accesso JWT usato per chiamare l'API web.
* Su HTTPS, l'applicazione web Usa il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Bearer" nell'intestazione "Authorization" della richiesta all'API web. L'API web convalida quindi il token JWT. Se la convalida ha esito positivo, restituisce la risorsa desiderata.

In questo flusso di identità di applicazione, l'API web suppone che l'applicazione web autenticato l'utente. Per questo motivo il modello è definito sottosistema attendibile. Il [diagramma di flusso di autorizzazione](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) viene descritto come autorizzazione-codice-flusso di concessione di funzionamento.

Acquisizione della licenza con restrizione token segue lo stesso modello di sottosistema attendibile. Il servizio di distribuzione di licenze in servizi multimediali è il risorsa API web, o "back-end resource" che deve accedere a un'applicazione web. Dov'è quindi il token di accesso?

Il token di accesso viene ottenuto da Azure AD. Dopo l'autenticazione dell'utente, viene restituito un codice di autorizzazione. Il codice di autorizzazione viene quindi usato, con l'ID client e la chiave dell'applicazione, per scambiare il token di accesso. Il token di accesso utilizzato per accedere a un'applicazione di "puntatore" che fa riferimento a o rappresenta il servizio di distribuzione di licenze di servizi multimediali.

Per registrare e configurare l'app del puntatore in Azure AD, procedere come segue:

1. Nel tenant di Azure AD:

   * Aggiungere un'applicazione (risorsa) con il https://[resource_name].azurewebsites.net/ URL sign-on. 
   * Aggiungere un ID di app con l'URL di https://[aad_tenant_name].onmicrosoft.com/[resource_name].

2. Aggiungere una nuova chiave per l'applicazione della risorsa.

3. Aggiornare il file manifesto dell'applicazione in modo che la proprietà groupMembershipClaims ha il valore "groupMembershipClaims": "All".

4. Nell'app di Azure AD che punta all'applicazione web di Windows Media player, nella sezione **autorizzazioni per altre applicazioni**, aggiungere l'app di risorsa che è stato aggiunto nel passaggio 1. In **autorizzazione delegata**selezionare **accesso [resource_name]**. Questa opzione consente l'autorizzazione di app web per creare i token di accesso, accedono all'app di risorse. Eseguire questa operazione per le versioni locali e distribuite dell'applicazione web se si sviluppa in Visual Studio e l'app web di Azure.

Il token JWT rilasciati da Azure AD è il token di accesso utilizzato per accedere alla risorsa di puntatore.

### <a name="what-about-live-streaming"></a>Come funziona lo streaming live?
La discussione precedente ha come obiettivo asset su richiesta. Come funziona lo streaming live?

È possibile utilizzare esattamente la stessa progettazione e implementazione per la protezione in tempo reale dello streaming in servizi multimediali considerarli gli asset associati a un programma come asset VOD.

In particolare, per eseguire live streaming in servizi multimediali, è necessario creare un canale e quindi creare un programma con il canale. Per creare il programma, è necessario creare un asset che contiene l'archivio in tempo reale per il programma. Per fornire la protezione del contenuto in tempo reale più DRM CENC, applicare il programma di installazione o l'elaborazione dei stesso all'asset come se fosse un asset VOD prima di avviare il programma.

### <a name="what-about-license-servers-outside-media-services"></a>Il server licenze all'esterno di servizi multimediali?
Spesso, i clienti investito in una farm di server licenze nel proprio data center o uno ospitati dai provider di servizi DRM. Con Media Services la protezione del contenuto, è possibile eseguire in modalità ibrida. Contenuto può essere ospitato e protetti in modo dinamico in servizi multimediali, mentre le licenze DRM vengono recapitate dal server all'esterno di servizi multimediali. In questo caso, prendere in considerazione le seguenti modifiche:

* Servizio token di sicurezza deve rilasciare token accettabili che possono essere verificati con la farm di server licenze. Ad esempio, il server licenze Widevine fornito da Axinom richiedono un JWT specifico che contiene un messaggio di diritti. Pertanto, è necessario disporre di un servizio token di sicurezza per l'emissione di un JWT di questo tipo. Per informazioni su questo tipo di implementazione, visitare il [Centro documentazione di Azure](https://azure.microsoft.com/documentation/) e vedere [Axinom utilizzare recapitare licenze Widevine di servizi multimediali di Azure](media-services-axinom-integration.md).
* Non è necessario configurare il servizio di recapito di licenze (ContentKeyAuthorizationPolicy) in servizi multimediali. È necessario fornire la licenza gli URL di acquisizione (per PlayReady, Widevine e FairPlay) quando si configura AssetDeliveryPolicy impostare CENC con multi-DRM.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Come procedere per usare un servizio token di sicurezza personalizzato?
Un cliente potrebbe scegliere di utilizzare un servizio token di sicurezza personalizzato per fornire Jwt. I motivi includono:

* Provider di identità usati dal cliente non supporta il servizio token di sicurezza. In questo caso, un servizio token di sicurezza personalizzato potrebbe essere un'opzione.
* Il cliente potrebbe essere necessario controllo più flessibile o una stretta integrazione servizio token di sicurezza con server di sottoscrizione del cliente, il sistema di fatturazione. Ad esempio, un operatore MVPD potrebbe offrire più pacchetti sottoscrittore OTT, ad esempio premium, basic e sportive. Potrebbe essere necessario l'operatore di associare le attestazioni in un token con il pacchetto di un sottoscrittore in modo che solo il contenuto in un pacchetto specifico viene resi disponibile. In questo caso, un servizio token di sicurezza personalizzato fornisce la flessibilità e il controllo necessari.

Quando si utilizza un servizio token di sicurezza personalizzato, è necessario apportare due modifiche:

* Quando si configura il servizio di recapito licenze per un asset, è necessario specificare la chiave di sicurezza utilizzata per la verifica dal servizio token di sicurezza personalizzata anziché la chiave corrente da Azure AD. (Ulteriori dettagli sul). 
* Quando viene generato un token JTW, anziché la chiave privata del X509 corrente viene specificata una chiave di sicurezza certificato in Azure AD.

Esistono due tipi di chiavi di sicurezza:

* Chiave simmetrica: la stessa chiave viene usata per generare e verificare un JWT.
* Chiave asimmetrica: una coppia di chiavi pubblica / privata in un X509 certificato viene usato con una chiave privata per crittografare o generare un token JWT e con la chiave pubblica per verificare il token.

> [!NOTE]
> Se si utilizza .NET Framework / c# come piattaforma di sviluppo, X509 certificato utilizzato per una chiave di sicurezza asimmetrico deve avere una lunghezza della chiave di almeno 2048. È un requisito della classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey in .NET Framework. In caso contrario, viene generata l'eccezione seguente:

> IDX10630: 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' per la firma non può essere inferiore a '2048' bit.

## <a name="the-completed-system-and-test"></a>Sistema completato e test
In questa sezione illustra gli scenari seguenti nel sistema end-to-end completato in modo da avere un'immagine di base del comportamento prima di ottenere un account di accesso:

* Se è necessario uno scenario non integrata:

    * Per risorse video ospitati in servizi multimediali che sono uno non protetto o protetto da DRM ma senza l'autenticazione con token (rilasciare una licenza a chi ne ha richiesto), è possibile eseguirne il test senza eseguire l'accesso. Se il flusso video è su HTTP, passare a HTTP.

* Se è necessario uno scenario integrato end-to-end:

    * Per le risorse video dinamica DRM la protezione dati in servizi multimediali con l'autenticazione del token e JWT generato da Azure AD, è necessario eseguire l'accesso.

Per l'applicazione web di Windows Media player e il relativo segno-in, vedere [il sito Web](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Accesso utente
Per testare il sistema DRM integrato end-to-end, è necessario disporre di un account creati o aggiunti.

Quale account?

Benché Azure originariamente consentisse l'accesso solo agli utenti di account Microsoft, è ora consentito l'accesso da parte degli utenti da entrambi i sistemi. Tutte le proprietà di Azure ora attendibili di Azure AD per l'autenticazione e Azure AD autentica gli utenti dell'organizzazione. Creazione di una relazione di federazione in cui Azure AD considera attendibile il sistema di identità Microsoft account utente per l'autenticazione degli utenti. Di conseguenza, Azure AD possono autenticarsi guest Microsoft Azure AD anche come nativi account.

Poiché Azure AD considera attendibile il dominio dell'account Microsoft, è possibile aggiungere qualsiasi account da uno qualsiasi dei seguenti domini con Azure AD personalizzati del tenant e usare l'account per accedere:

| **Nome di dominio** | **Dominio** |
| --- | --- |
| **Dominio del tenant di Azure AD personalizzato** |nome.onmicrosoft.com |
| **Dominio aziendale** |microsoft.com |
| **Dominio dell'account Microsoft** |outlook.com, live.com, hotmail.com |

È possibile contattare uno qualsiasi degli autori di un account creati o aggiunti automaticamente.

Le schermate seguenti mostrano diverse delle pagine di accesso utilizzate dall'account di dominio diversi:

**Account di dominio del tenant AD Azure personalizzato**: la pagina di accesso personalizzata di Azure AD personalizzato dominio del tenant.

![Account dominio del tenant di Azure AD personalizzato](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Account di dominio di Microsoft con smart card**: la pagina di accesso personalizzata da aziendale di Microsoft IT con l'autenticazione a due fattori.

![Account dominio del tenant di Azure AD personalizzato](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Account Microsoft**: pagina di accesso dell'account Microsoft per i consumer.

![Account dominio del tenant di Azure AD personalizzato](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Utilizzare le estensioni multimediali crittografati per PlayReady
In un browser moderno con le estensioni di supporto crittografato (EME) per il supporto di PlayReady, ad esempio Internet Explorer 11 in Windows 8.1 o versioni successive e il browser Microsoft Edge in Windows 10, PlayReady è il DRM per EME sottostante.

![Utilizzare EME per PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

L'area di Windows Media player scuro è perché protezione PlayReady impedisce l'esecuzione di una cattura di schermata di video protetto.

La schermata seguente mostra il plug-in Windows Media player e Microsoft Security Essentials (MSE) / EME supporta:

![Windows Media Player plug-in per PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

Consente di EME in Microsoft Edge e Internet Explorer 11 in Windows 10 [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) da richiamare su dispositivi Windows 10 che la supportano. PlayReady SL3000 Sblocca il flusso del contenuto premium migliorate (4K, HDR) e dei nuovi modelli di distribuzione (per il contenuto avanzato) del contenuto.

Per concentrare l'attenzione sui dispositivi Windows, PlayReady è l'unico DRM nell'hardware disponibile nei dispositivi Windows (PlayReady SL3000). Un servizio di streaming è possibile usare PlayReady tramite EME o un'applicazione UWP e offrono una migliore qualità video tramite PlayReady SL3000 rispetto a un altro DRM. In genere, il contenuto fino a 2 KB attraversa Chrome o Firefox e contenuto backup ai flussi di 4 K tramite Microsoft Edge o Internet Explorer 11 o un'applicazione UWP sullo stesso dispositivo. La quantità dipende dall'implementazione e le impostazioni del servizio.

#### <a name="use-eme-for-widevine"></a>Utilizzare EME per Widevine
In un browser moderno con il supporto EME/Widevine, ad esempio Chrome 41+ in Windows 10, Windows 8.1, Mac OSX Yosemite e Chrome in Android 4.4.4, Google Widevine è il sistema DRM dietro EME.

![Utilizzare EME per Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine non impedisce di apportare una cattura di schermata di video protetto.

![Windows Media Player plug-in per Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Utenti unentitled
Se un utente non è un membro del gruppo "Utenti intitolata", l'utente non soddisfa il controllo sui diritti. Il servizio di licenza di multi-DRM quindi Rifiuta rilasciare la licenza richiesta, come illustrato. Descrizione dettagliata è "acquisire licenze non è riuscita," che è quello previsto.

![Utenti unentitled](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Eseguire un servizio token di sicurezza personalizzato
Se si esegue un servizio token di sicurezza personalizzato, il token JWT viene generato dal servizio token di sicurezza personalizzato utilizzando un simmetrica o asimmetrica.

Nella schermata seguente viene illustrato uno scenario che utilizza una chiave simmetrica (con Chrome):

![Servizio token di sicurezza personalizzato con una chiave simmetrica](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Nella schermata seguente viene illustrato uno scenario che utilizza una chiave asimmetrica tramite un X509 (tramite un browser moderno Microsoft) del certificato:

![Servizio token di sicurezza personalizzato con una chiave asimmetrica](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

In entrambi i casi precedenti, l'autenticazione utente non cambia. Ha luogo tramite Azure AD. L'unica differenza è che Jwt vengono rilasciati dal servizio token di sicurezza personalizzato invece di Azure AD. Quando si configura la protezione CENC dinamica, la restrizione di servizio di recapito licenza specifica il tipo di token JWT, di una chiave simmetrica o asimmetrica.

## <a name="summary"></a>Summary
Questo documento discusso CENC con controllo DRM e accesso più nativo tramite l'autenticazione del token, la progettazione e la relativa implementazione, tramite Azure, servizi multimediali e Media Player.

* Un progetto di riferimento è stato specificato che contiene tutti i componenti necessari in un sottosistema DRM/CENC.
* Un'implementazione di riferimento è stata presentata in Azure, servizi multimediali e Media Player.
* Sono state illustrate anche alcuni argomenti direttamente coinvolti nella progettazione e nell'implementazione.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
