---
title: Progettazione di un sistema di protezione del contenuto con DRM multiplo e controllo di accesso - Servizi multimediali di Azure | Microsoft Docs
description: Informazioni su come ottenere la licenza per Microsoft Smooth Streaming Client Porting Kit.
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: ffbf53c0bb0aaf2832afecc2d0df935f04eeff19
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310322"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Progettazione di un sistema di protezione del contenuto con DRM multiplo e controllo di accesso 

La progettazione e la creazione di un sottosistema DRM (Digital Rights Management) per una soluzione di streaming online o OTT (Over-The-Top) sono attività complesse. Gli operatori e i provider di video online in genere esternalizzano queste attività a provider di servizi DRM specializzati. L'obiettivo di questo documento è presentare informazioni di riferimento per la progettazione e l'implementazione di un sottosistema DRM end-to-end in una soluzione di streaming online o OTT.

Questo documento è destinato agli ingegneri che utilizzano i sottosistemi DRM di soluzioni multischermo e di streaming online o OTT, ma anche a chiunque sia interessato ai sottosistemi DRM. Il presupposto è che i lettori abbiano familiarità con almeno una delle tecnologie DRM presenti sul mercato, ad esempio PlayReady, Widevine, FairPlay o Adobe Access.

In questo articolo, con DRM multiplo si intendono i tre DRM supportati da Servizi multimediali di Azure: la crittografia comune (CENC) per PlayReady e Widevine, FairPlay e la crittografia con chiave non crittografata AES-128. Una tendenza diffusa nel settore delle soluzioni di streaming online e OTT è rappresentata dall'uso di DRM nativi su varie piattaforme client. Tale tendenza si discosta da quella precedente, che usava una tecnologia DRM singola e il relativo SDK client su varie piattaforme client. Quando si usa la crittografia CENC con DRM nativo multiplo, PlayReady e Widevine vengono crittografati in base alla specifica [Common Encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/).

I vantaggi offerti dall'uso del DRM multiplo nativo per la protezione del contenuto sono i seguenti:

* Riduzione del costo della crittografia, perché viene usata una sola elaborazione crittografica per più piattaforme con DRM nativi.
* Riduzione del costo della gestione degli asset, perché è necessaria una sola copia degli asset nell'archiviazione.
* Eliminazione del costo della licenza del client DRM, perché il client DRM nativo è in genere gratuito sulla piattaforma nativa.

### <a name="goals-of-the-article"></a>Obiettivi dell'articolo

Gli obiettivi di questo articolo sono:

* Fornire una progettazione di riferimento di un sottosistema DRM che usi tutti e tre i DRM (CENC per DASH, FairPlay per HLS e PlayReady per Smooth Streaming).
* Fornire informazioni di riferimento per l'implementazione in piattaforme Azure e Servizi multimediali di Azure.
* Illustrare alcuni argomenti relativi alla progettazione e all'implementazione.

La tabella seguente riepiloga il supporto del DRM nativo in diverse piattaforme e il supporto di EME in diversi browser.

| **Piattaforma client** | **DRM nativo** | **EME** |
| --- | --- | --- |
| **Smart TV, STB** | PlayReady, Widevine e/o altro | Browser incorporato/EME per PlayReady e/o Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/Internet Explorer 11 per PlayReady|
| **Dispositivi Android (telefoni, tablet, TV)** |Widevine |Chrome per Widevine |
| **iOS** | FairPlay | Safari per FairPlay (da iOS 11.2) |
| **macOS** | FairPlay | Safari per FairPlay (da Safari 9 e versioni successive su Mac OS X 10.11 El Capitan e versioni successive)|
| **tvOS** | FairPlay | |

In base allo stato attuale della distribuzione per ogni DRM, un servizio in genere prevede l'implementazione di 2 o 3 DRM per garantire che tutti i tipi di endpoint vengano indirizzati nel modo migliore.

Occorre raggiungere un compromesso tra la complessità della logica del servizio e la complessità sul lato client per ottenere un certo livello di esperienza utente nei vari client.

Per scegliere l'opzione migliore, tenere presente quanto segue:

* PlayReady viene implementato in modo nativo in tutti i dispositivi Windows e in alcuni dispositivi Android ed è disponibile tramite SDK software su praticamente qualsiasi piattaforma.
* Widevine viene implementato in modo nativo in tutti i dispositivi Android, in Chrome e in alcuni altri dispositivi. Widevine è anche supportato nei browser Firefox e Opera tramite DASH.
* FairPlay è disponibile in iOS, macOS e tvOS.


## <a name="a-reference-design"></a>Progettazione di riferimento
Questa sezione presenta informazioni di riferimento per una progettazione indipendente dalle tecnologie usate per implementarla.

Un sottosistema DRM può contenere i componenti seguenti:

* Gestione della chiave
* Creazione di pacchetti con crittografia DRM
* Distribuzione di licenze DRM
* Controllo dei diritti e dell'accesso
* Autenticazione/autorizzazione utente
* App Player
* Rete per la distribuzione di contenuti (rete CDN)/origine

Il diagramma seguente illustra l'interazione generale tra i componenti in un sottosistema DRM:

![Sottosistema DRM con CENC](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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

| **Blocco predefinito** | **Technology** |
| --- | --- |
| **Lettore** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Provider di identità (IdP)** |Azure Active Directory (Azure AD) |
| **Servizio token di sicurezza** |Azure AD |
| **Flusso di lavoro protezione DRM** |Protezione dinamica di Servizi multimediali di Azure |
| **Distribuzione di licenze DRM** |* Distribuzione delle licenze di Servizi multimediali (PlayReady, Widevine, FairPlay) <br/>* Server licenze Axinom <br/>* Server licenze PlayReady personalizzato |
| **Origine** |Endpoint di streaming di Servizi multimediali di Azure |
| **Gestione delle chiavi** |Non necessaria per l'implementazione di riferimento |
| **Gestione dei contenuti** |Applicazione console in C# |

In altri termini, da Azure AD vengono forniti sia IdP che STS. L'[API di Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/) viene usata per il lettore. Sia Servizi multimediali di Azure che Azure Media Player supportano CENC su DASH, FairPlay su HLS, PlayReady su Smooth Streaming e la crittografia AES-128 per DASH, HLS e Smooth Streaming.

Il diagramma seguente illustra la struttura e il flusso generali con il mapping alla tecnologia precedente:

![Crittografia CENC in Servizi multimediali](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Per configurare la protezione del contenuto DRM, lo strumento di gestione dei contenuti usa gli input seguenti:

* Contenuto aperto
* Chiave simmetrica dalla gestione delle chiavi
* URL di acquisizione delle licenze
* Un elenco di informazioni da Azure AD, ad esempio i destinatari, l'autorità di certificazione e le attestazioni dei token

Di seguito è illustrato l'output dello strumento di gestione dei contenuti:

* ContentKeyPolicy descrive il modello di licenza DRM per ogni tipo di DRM in uso
* ContentKeyPolicyRestriction descrive il controllo di accesso prima dell'emissione di una licenza DRM
* Streamingpolicy descrive le varie combinazioni di DRM/modalità di crittografia/protocollo di streaming/formato del contenitore per lo streaming
* StreamingLocator descrive la chiave simmetrica o il vettore di inizializzazione usato per la crittografia e gli URL di streaming 

Di seguito è illustrato il flusso in fase di runtime:

* In seguito all'autenticazione dell'utente, viene generato un token JWT.
* Una delle attestazioni nel token JWT è l'attestazione "groups", contenente l'ID oggetto gruppo EntitledUserGroup. Questa attestazione viene usata per passare il controllo dei diritti.
* Il lettore scarica il manifesto client di un contenuto protetto dalla crittografia CENC e identifica quanto segue:
   * ID chiave
   * Contenuto protetto dalla crittografia DRM.
   * URL di acquisizione delle licenze.
* Il lettore crea una richiesta di acquisizione della licenza basata sul browser/DRM supportato. Nella richiesta di acquisizione della licenza vengono inviati anche l'ID chiave e il token JWT. Il servizio di distribuzione delle licenze verifica il token JWT e le attestazioni contenute prima di rilasciare la licenza necessaria.

## <a name="implementation"></a>Implementazione
### <a name="implementation-procedures"></a>Procedure di implementazione
L'implementazione è costituita dai passaggi seguenti:

1. Preparare l'asset di test. Codificare/creare un pacchetto per un video di test in formato MP4 frammentato a più velocità in bit in Servizi multimediali. Questo asset *non* è protetto da DRM. La protezione DRM viene applicata più avanti con la protezione dinamica.

2. Creare un ID chiave e una chiave simmetrica (facoltativamente da un seme chiave). In questa caso, non è necessario il sistema di gestione delle chiavi perché per poche attività di test sono sufficienti un solo ID chiave e la chiave simmetrica.

3. Usare l'API di Servizi multimediali per configurare i servizi di distribuzione delle licenze con DRM multiplo per l'asset di test. Se si usano i server licenze personalizzati dall'azienda o dai fornitori dell'azienda anziché i servizi licenze in Servizi multimediali, è possibile ignorare questo passaggio. È possibile specificare gli URL di acquisizione delle licenze quando si configura il servizio di distribuzione delle licenze. L'API di Servizi multimediali è necessaria per specificare alcune configurazioni dettagliate, ad esempio la restrizione dei criteri di autorizzazione e i modelli di risposta delle licenze per servizi licenze DRM diversi. Attualmente il portale di Azure non fornisce l'interfaccia utente necessaria per questa configurazione. Per informazioni sull'API ed esempi di codice, vedere [Usare la crittografia comune dinamica Widevine e/o PlayReady](protect-with-drm.md).

4. Usare l'API di Servizi multimediali per configurare i criteri di distribuzione per l'asset di test. Per informazioni sull'API ed esempi di codice, vedere [Usare la crittografia comune dinamica Widevine e/o PlayReady](protect-with-drm.md).

5. Creare e configurare un tenant di Azure AD in Azure.

6. Creare alcuni gruppi e account utente nel tenant di Azure AD. Creare almeno un gruppo "Utente con diritti" e aggiungere un utente a questo gruppo. Gli utenti in questo gruppo passeranno il controllo dei diritti nella fase di acquisizione di una licenza. Gli utenti non presenti in questo gruppo non passeranno il controllo di autenticazione e non potranno acquisire una licenza. L'appartenenza al gruppo "Utente con diritti" è un'attestazione di gruppo obbligatoria nel token JWT rilasciato da Azure AD. Questo requisito di attestazione viene specificato nel passaggio di configurazione dei servizi di distribuzione delle licenze con DRM multiplo.

7. Creare un'app MVC ASP.NET per ospitare un lettore video. Questa app ASP.NET è protetta con l'autenticazione utente nel tenant di Azure AD. Le attestazioni appropriate sono incluse nei token di accesso ottenuti dopo l'autenticazione utente. Per questo passaggio, è consigliata l'API OpenID Connect. Installare i pacchetti NuGet seguenti:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Creare un lettore usando l'[API di Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Usare l'[API ProtectionInfo di Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/) per specificare la tecnologia DRM da usare in varie piattaforme DRM.

9. La tabella seguente illustra la matrice di test:

    | **DRM** | **Browser** | **Risultato per un utente con diritti** | **Risultato per un utente senza diritti** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge o Internet Explorer 11 in Windows 10 |Succeed |Fail |
    | **Widevine** |Chrome, Firefox, Opera |Succeed |Fail |
    | **FairPlay** |Safari su macOS      |Succeed |Fail |
    | **AES-128** |Browser più moderni  |Succeed |Fail |

Per informazioni su come impostare Azure AD per un'app lettore MVC ASP.NET, vedere [Integrate an Azure Media Services OWIN MVC-based app with Azure Active Directory and restrict content key delivery based on JWT claims](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/) (Integrare un'app basata su OWIN MVC di Servizi multimediali di Azure con Azure Active Directory e limitare la distribuzione di chiavi simmetriche in base ad attestazioni JWT).

Per altre informazioni, vedere [JWT token authentication in Azure Media Services and dynamic encryption](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/) (Autenticazione di token JWT in Servizi multimediali di Azure e crittografia dinamica).  

Per informazioni su Azure AD:

* Le informazioni per gli sviluppatori sono disponibili nella [Guida per gli sviluppatori di Azure Active Directory](../../active-directory/develop/v1-overview.md).
* Le informazioni per gli amministratori sono disponibili in [Amministrare la directory di Azure AD](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Problematiche di implementazione

Usare le informazioni seguenti per la risoluzione dei problemi di implementazione.

* L'URL dell'autorità di certificazione deve terminare con "/". Il destinatario deve essere l'ID client dell'applicazione lettore. Inoltre, aggiungere "/" alla fine dell'URL dell'autorità di certificazione.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    In [JWT Decoder](http://jwt.calebb.net/) vengono visualizzati **aud** e **iss** come nel token JWT seguente:

    ![Token JSON Web](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Aggiungere autorizzazioni all'applicazione in Azure AD nella scheda **Configura** dell'applicazione. Le autorizzazioni sono obbligatorie per ogni applicazione, indipendentemente dal tipo di versione: locale e distribuita.

    ![Autorizzazioni](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* Usare l'autorità di certificazione corretta quando si configura la protezione CENC dinamica.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Lo scenario seguente non funziona:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    Il GUID è l'ID tenant di Azure AD. Il GUID si trova nel menu di scelta rapida **Endpoint** nel portale di Azure.

* Concedere i privilegi delle attestazioni di appartenenza al gruppo. Verificare che nel file manifesto dell'applicazione Azure AD sia presente quanto segue: 

    "groupMembershipClaims": "All"    (il valore predefinito è null)

* Impostare l'oggetto TokenType appropriato quando si creano i requisiti relativi alle restrizioni.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Poiché viene aggiunto il supporto di JWT (Azure AD) oltre a SWT (ACS), l'oggetto TokenType predefinito è TokenType.JWT. Se si usa SWT/ACS, è necessario impostare il token su TokenType.SWT.

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

![Account dominio del tenant di Azure AD personalizzato - Uno](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Account dominio Microsoft con smart card**: pagina di accesso personalizzata dall'IT aziendale Microsoft con autenticazione a due fattori.

![Account dominio del tenant di Azure AD personalizzato - Due](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Account Microsoft**: pagina di accesso dell'account Microsoft per gli utenti.

![Account dominio del tenant di Azure AD personalizzato - Tre](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Usare Encrypted Media Extensions per PlayReady

In un browser moderno con il supporto EME (Encrypted Media Extensions) per PlayReady, ad esempio Internet Explorer 11 in Windows 8.1 o versioni successive e il browser Microsoft Edge in Windows 10, PlayReady è il sistema DRM sottostante per EME.

![Usare EME per PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

Nel lettore è presente un'area scura perché la protezione di PlayReady impedisce di acquisire schermate da un video protetto.

La schermata seguente mostra i plug-in del lettore e il supporto per Microsoft Security Essentials (MSE)/EME:

![Plug-in del lettore per PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

EME in Microsoft Edge e Internet Explorer 11 in Windows 10 consente di chiamare [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) sui dispositivi Windows 10 che lo supportano. PlayReady SL3000 sblocca il flusso di contenuti premium avanzati (4 KB, HDR) e nuovi modelli di distribuzione dei contenuti (per contenuti avanzati).

Per quanto riguarda i dispositivi Windows, PlayReady è il solo DRM nell'hardware disponibile nei dispositivi Windows (PlayReady SL3000). Un servizio di streaming può usare PlayReady tramite EME o un'applicazione UWP (Universal Windows Platform) e offrire una migliore qualità video usando PlayReady SL3000 anziché un altro DRM. In genere, il contenuto fino a 2 KB passa attraverso Chrome o Firefox e il contenuto fino a 4 KB passa attraverso Microsoft Edge, Internet Explorer 11 o un'applicazione UWP sullo stesso dispositivo. La quantità dipende dalle impostazioni del servizio e dall'implementazione.

#### <a name="use-eme-for-widevine"></a>Usare EME per Widevine

In un browser moderno con il supporto EME/Widevine, ad esempio Chrome 41+ in Windows 10, Windows 8.1, Mac OSX Yosemite e Chrome in Android 4.4.4, Google Widevine è il sistema DRM dietro EME.

![Usare EME per Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine non impedisce di acquisire schermate da un video protetto.

![Plug-in del lettore per Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>Usare EME per FairPlay

Analogamente, è possibile testare contenuto protetto FairPlay in questo lettore di test in Safari su macOS o iOS 11.2 e versioni successive.

Assicurarsi di inserire "FairPlay" come protectionInfo.type e di specificare l'URL corretto per il certificato dell'applicazione in FPS AC Path (percorso del certificato dell'applicazione FairPlay Streaming).

### <a name="unentitled-users"></a>Utenti senza diritti

Se un utente non appartiene al gruppo "Utenti con diritti", non soddisfa il controllo dei diritti. Il servizio licenze DRM multiplo quindi rifiuta di rilasciare la licenza richiesta, come illustrato. La descrizione dettagliata è "License acquire failed" (Acquisizione licenza non riuscita), come previsto dalla progettazione.

![Utenti senza diritti](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Eseguire un servizio token di sicurezza personalizzato

Se si esegue un servizio token di sicurezza personalizzato, il token JWT viene rilasciato dal servizio token di sicurezza personalizzato tramite una chiave simmetrica o asimmetrica.

La schermata seguente illustra uno scenario che usa una chiave simmetrica (con Chrome):

![Servizio token di sicurezza personalizzato con una chiave simmetrica](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

La schermata seguente illustra uno scenario che usa una chiave asimmetrica tramite un certificato X509 (con un browser moderno Microsoft):

![Servizio token di sicurezza personalizzato con una chiave asimmetrica](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

In entrambi i casi precedenti, l'autenticazione utente è la stessa, ovvero viene eseguita tramite Azure AD. L'unica differenza è che i token JWT vengono rilasciati dal servizio token di sicurezza personalizzato invece che da Azure AD. Quando si configura la protezione CENC dinamica, la restrizione del servizio di distribuzione delle licenze specifica il tipo di token JWT, una chiave simmetrica o asimmetrica.

## <a name="next-steps"></a>Passaggi successivi

* [Domande frequenti](frequently-asked-questions.md)
* [Panoramica della protezione del contenuto](content-protection-overview.md)
* [Proteggere i contenuti con DRM](protect-with-drm.md)
