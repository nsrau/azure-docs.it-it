<properties 
	pageTitle="CENC con DRM multiplo e controllo di accesso: progettazione di riferimento e implementazione in Azure e in Servizi multimediali di Azure" 
	description="Informazioni su come ottenere la licenza per Microsoft® Smooth Streaming Client Porting Kit." 
	services="media-services" 
	documentationCenter="" 
	authors="willzhan"  
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2016"  
	ms.author="willzhan;kilroyh;yanmf;juliako"/>

#CENC con DRM multiplo e controllo di accesso: progettazione di riferimento e implementazione in Azure e in Servizi multimediali di Azure

##Parole chiave
 
Azure Active Directory, Servizi multimediali di Azure, Azure Media Player, crittografia dinamica, distribuzione di licenze, PlayReady, Widevine, FairPlay, Common Encryption (CENC), DRM multiplo, Axinom, DASH, EME, MSE, token Web JSON (JWT), attestazioni, browser moderni, rollover della chiave, chiave simmetrica, chiave asimmetrica, OpenID Connect, certificato X509.

##Contenuto dell'articolo

Questo articolo tratta gli argomenti seguenti:

- [Introduzione](media-services-cenc-with-multidrm-access-control.md#introduction)
	- [Panoramica dell'articolo](media-services-cenc-with-multidrm-access-control.md#overview-of-this-article)
- [Progettazione di riferimento](media-services-cenc-with-multidrm-access-control.md#a-reference-design)
- [Mapping della progettazione alla tecnologia per l'implementazione](media-services-cenc-with-multidrm-access-control.md#mapping-design-to-technology-for-implementation)
- [Implementazione](media-services-cenc-with-multidrm-access-control.md#implementation)
	- [Procedure di implementazione](media-services-cenc-with-multidrm-access-control.md#implementation-procedures)
	- [Problematiche di implementazione](media-services-cenc-with-multidrm-access-control.md#some-gotchas-in-implementation)
- [Argomenti aggiuntivi per l'implementazione](media-services-cenc-with-multidrm-access-control.md#additional-topics-for-implementation)
	- [HTTP o HTTPS](media-services-cenc-with-multidrm-access-control.md#http-or-https)
	- [Rollover della chiave per la firma di Azure Active Directory](media-services-cenc-with-multidrm-access-control.md#azure-active-directory-signing-key-rollover)
	- [Dov'è il token di accesso?](media-services-cenc-with-multidrm-access-control.md#where-is-the-access-token)
	- [Come funziona lo streaming live?](media-services-cenc-with-multidrm-access-control.md#what-about-live-streaming)
	- [Come funzionano i server licenze al di fuori di Servizi multimediali di Azure?](media-services-cenc-with-multidrm-access-control.md#what-about-license-servers-outside-of-azure-media-services)
	- [Come procedere per usare un servizio token di sicurezza personalizzato?](media-services-cenc-with-multidrm-access-control.md#what-if-i-want-to-use-a-custom-sts)
- [Sistema completato e test](media-services-cenc-with-multidrm-access-control.md#the-completed-system-and-test)
	- [Accesso utente](media-services-cenc-with-multidrm-access-control.md#user-login)
	- [Uso di Encrypted Media Extensions per PlayReady](media-services-cenc-with-multidrm-access-control.md#using-encrypted-media-extensipons-for-playready)
	- [Uso di EME per Widevine](media-services-cenc-with-multidrm-access-control.md#using-eme-for-widevine)
	- [Utenti non idonei](media-services-cenc-with-multidrm-access-control.md#not-entitled-users)
	- [Esecuzione del servizio token di sicurezza personalizzato](media-services-cenc-with-multidrm-access-control.md#running-custom-secure-token-service)
- [Riepilogo](media-services-cenc-with-multidrm-access-control.md#summary)

##Introduzione

È risaputo che la progettazione e la compilazione di un sottosistema DRM per una soluzione di streaming online o OTT sono attività complesse. Gli operatori e i provider video online in genere danno queste attività in outsourcing a provider di servizi DRM specializzati. L'obiettivo di questo documento è presentare una progettazione di riferimento e l'implementazione del sottosistema DRM end-to-end nella soluzione di streaming online o OTT.

Questo documento è destinato agli ingegneri che lavorano nel sottosistema DRM di soluzioni con più schermate/di streaming online o OTT, ma anche a chi è interessato al sottosistema DRM. Il presupposto è che i lettori abbiano familiarità con almeno una delle tecnologie DRM presenti sul mercato, ad esempio PlayReady, Widevine, FairPlay o Adobe Access.

DRM include anche CENC (Common Encryption) con DRM multiplo. Una delle tendenze principali del settore OTT e dello streaming online è quella di usare CENC con DRM nativo multiplo su diverse piattaforme client. La tendenza precedente era invece quella di usare un solo DRM e l'SDK client per più piattaforme client. Quando si usa CENC con DRM nativo multiplo, PlayReady e Widevine vengono crittografati in base alla specifica [Common Encryption (ISO/IEC 23001-7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/).

I vantaggi di CENC con DRM multiplo sono i seguenti:

1. Riduce il costo della crittografia perché viene usata una sola elaborazione crittografica per piattaforme diverse con i DRM nativi.
1. Riduce il costo della gestione degli asset crittografati perché è necessaria una sola copia degli asset crittografati.
1. Elimina il costo della licenza del client DRM perché il client DRM nativo è in genere gratuito sulla piattaforma nativa.

Microsoft, insieme ad alcuni dei principali leader di settore, ha promosso attivamente DASH e CENC. Servizi multimediali di Microsoft Azure ha fornito il supporto di DASH e CENC. Per gli annunci recenti, vedere i blog di Mingfei relativi all'[annuncio dell'anteprima pubblica dei servizi di distribuzione delle licenze Google Widevine in Servizi multimediali di Azure](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/) e all'[aggiunta in Servizi multimediali di Azure del pacchetto Google Widevine per la distribuzione del flusso DRM multiplo](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).

### Panoramica dell'articolo

Gli obiettivi di questo articolo sono i seguenti:

1. Fornire una progettazione di riferimento del sottosistema DRM usando CENC con DRM multiplo.
1. Fornire un'implementazione di riferimento nella piattaforma Microsoft Azure/Servizi multimediali di Azure.
1. Illustrare alcuni argomenti relativi alla progettazione e all'implementazione.

Nell'articolo, "DRM multiplo" fa riferimento a quanto segue:

1. Microsoft PlayReady
1. Google Widevine
1. Apple FairPlay (non ancora supportato da Servizi multimediali di Azure)

La tabella seguente riepiloga l'app nativa/piattaforma e i browser supportati da ogni DRM.

**Piattaforma client**|**Supporto DRM nativo**|**Browser/App**|**Formati di streaming**
----|------|----|----
**Smart TV, STB operatore, STB OTT**|Principalmente PlayReady e/o Widevine e/o altro|Linux, Opera, WebKit, altri|Vari formati
**Dispositivi Windows 10 (PC Windows, tablet Windows, Windows Phone, Xbox)**|PlayReady|MS Edge/IE11/EME<br/><br/><br/>UWP|DASH (per HLS, PlayReady non è supportato)<br/><br/>DASH, Smooth Streaming (per HLS, PlayReady non è supportato) 
**Dispositivi Android (telefoni, tablet, TV)**|Widevine|Chrome/EME|DASH
**iOS (iPhone, iPad), client OS X e Apple TV**|FairPlay|Safari 8+/EME|HLS
**Plug-in: Adobe Primetime**|Accesso Primetime|Plug-in del browser|HDS, HLS

Considerando lo stato attuale della distribuzione per ogni DRM, un servizio solitamente vuole implementare 2 o 3 DRM per assicurarsi di affrontare tutti i tipi di endpoint nel modo migliore.

Occorre raggiungere un compromesso tra la complessità della logica del servizio e la complessità sul lato client per ottenere un certo livello di esperienza utente nei vari client.

Per eseguire la selezione, tenere presente quanto segue:

- PlayReady viene implementato in modo nativo in tutti i dispositivi Windows e in alcuni dispositivi Android ed è disponibile tramite SDK software su praticamente qualsiasi piattaforma.
- Widevine viene implementato in modo nativo in tutti i dispositivi Android, in Chrome e in alcuni altri dispositivi.
- FairPlay è disponibile solo in iOS e nei client Mac OS o tramite iTunes.

Quindi, un DRM multiplo tipico corrisponde ai seguenti:

- Opzione 1: PlayReady e Widevine
- Opzione 2: PlayReady, Widevine e FairPlay


## Progettazione di riferimento

In questa sezione verrà presentata una progettazione di riferimento indipendente dalle tecnologie usate per implementarla.

Un sottosistema DRM può contenere i componenti seguenti:

1. Gestione della chiave
1. Creazione pacchetto DRM
1. Distribuzione di licenze DRM
1. Controllo dei diritti
1. Autenticazione/autorizzazione
1. Lettore
1. Origine/rete CDN

Il diagramma seguente illustra l'interazione generale tra i componenti in un sottosistema DRM.

![Sottosistema DRM con CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)
 
I "livelli" di base della progettazione sono tre:

1. Livello back office (in nero) non esposto esternamente.
1. Livello "rete perimetrale" (blu) contenente tutti gli endpoint pubblici.
1. Livello Internet pubblico (azzurro) contenente la rete CDN e i lettori con traffico su Internet pubblico.
 
Deve essere presente uno strumento di gestione del contenuto per controllare la protezione DRM, indipendentemente dal fatto che si tratti di crittografia statica o dinamica. Gli input per la crittografia DRM devono includere:

1. Contenuto video con velocità in bit multipla.
1. Chiave simmetrica.
1. URL di acquisizione delle licenze.

Durante la fase di riproduzione, il flusso generale è:

1. L'utente viene autenticato.
1. Viene creato il token di autorizzazione per l'utente.
1. Il contenuto protetto da DRM (manifesto) viene scaricato nel lettore.
1. Il lettore invia la richiesta di acquisizione di licenza ai server licenze con l'ID chiave e il token di autorizzazione.

Prima di passare all'argomento successivo, verrà brevemente illustrata la progettazione della gestione delle chiavi.

**Chiave simmetrica-ad-asset**|**Scenario**
------|---------------------------
1-a-1|È il caso più semplice. Consente il controllo più dettagliato, ma in genere comporta il costo maggiore di distribuzione delle licenze. È necessaria almeno una richiesta di licenza per ogni asset protetto.
1-a-molti|È possibile usare la stessa chiave simmetrica per più asset. Ad esempio, per tutti gli asset di un gruppo logico, come un genere o un subset del genere (o genere film), è possibile usare una singola chiave simmetrica.
Molti-a-1|Per ogni asset sono necessarie più chiavi simmetriche. <br/><br/>Ad esempio, se è necessario applicare la protezione CENC dinamica con DRM multiplo per MPEG-DASH e la crittografia AES-128 dinamica per HLS, sono necessarie due chiavi simmetriche distinte, entrambe con il proprio ContentKeyType. Per la chiave simmetrica usata per la protezione CENC dinamica è consigliabile usare ContentKeyType.CommonEncryption, mentre per la chiave simmetrica usata per la crittografia AES-128 dinamica è consigliabile usare ContentKeyType.EnvelopeEncryption.<br/><br/>Un altro esempio è quello della protezione CENC del contenuto DASH dove, in teoria, una chiave simmetrica può essere usata per proteggere il flusso video e un'altra chiave simmetrica per proteggere il flusso audio. 
Molti-a-molti|È una combinazione dei due scenari precedenti: per ogni asset multiplo nello stesso "gruppo" di asset viene usato un set di chiavi simmetriche.


Un altro fattore importante da considerare è l'uso di licenze persistenti e non persistenti.

Perché queste considerazioni sono importanti?

Hanno un impatto diretto sul costo della distribuzione delle licenze se si usa il cloud pubblico a questo scopo. Si considerino i due seguenti casi di progettazione:



1. Sottoscrizione mensile: usare una licenza persistente e il mapping chiave simmetrica-ad-asset 1-a-molti. Ad esempio, per tutti i film per bambini, viene usata un'unica chiave simmetrica per la crittografia. In questo caso: 

	N. totale di licenze richieste per tutti i film per bambini/dispositivo = 1

1. Sottoscrizione mensile: usare una licenza non persistente e il mapping 1-a-1 tra la chiave simmetrica e l'asset. In questo caso:

	N. totale di licenze richieste per tutti i film per bambini/dispositivo = [n. di film guardati] x [n. di sessioni]

Come si può notare facilmente, le due diverse progettazioni restituiscono modelli di richiesta di licenza molto diversi da cui deriva il costo della distribuzione delle licenze se il servizio di distribuzione delle licenze viene fornito da un cloud pubblico, ad esempio Servizi multimediali di Azure.

## Mapping della progettazione alla tecnologia per l'implementazione

Ora verrà eseguito il mapping della progettazione generica alle tecnologie nella piattaforma Microsoft Azure/Servizi multimediali di Azure, specificando la tecnologia da usare per ogni blocco predefinito.

La tabella seguente illustra il mapping:

**Blocco predefinito**|**Technology**
------|-------
**Lettore**|[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/)
**Provider di identità (IdP)**|Azure Active Directory
**Servizio token di sicurezza**|Azure Active Directory
**Flusso di lavoro protezione DRM**|Protezione dinamica di Servizi multimediali di Azure
**Distribuzione di licenze DRM**|1\. Distribuzione delle licenze di Servizi multimediali di Azure (PlayReady, Widevine, FairPlay) o <br/>2. Server licenze Axinom, <br/>3. Server licenze PlayReady personalizzato
**Origine**|Endpoint di streaming di Servizi multimediali di Azure
**Gestione della chiave**|Non necessaria per l'implementazione di riferimento
**Gestione del contenuto**|Applicazione console in C#

In altre parole, sia il provider di identità (IdP) che il servizio token di sicurezza saranno Azure AD. Come lettore si userà l'[API di Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). Sia Servizi multimediali di Azure che Azure Media Player supportano DASH e CENC con DRM multiplo.

Il diagramma seguente illustra la struttura e il flusso generali con il mapping alla tecnologia precedente.

![CENC in AMS](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Per configurare la crittografia CENC dinamica, lo strumento di gestione del contenuto userà gli input seguenti:

1. Contenuto aperto.
1. Chiave simmetrica dalla generazione/gestione delle chiavi.
1. URL di acquisizione delle licenze.
1. Un elenco di informazioni da Azure AD.

L'output dello strumento di gestione del contenuto sarà:

1. ContentKeyAuthorizationPolicy contenente la specifica relativa alla verifica di un token JWT da parte della distribuzione delle licenze e le specifiche relative alle licenze DRM.
1. AssetDeliveryPolicy contenente le specifiche sul formato di streaming, sulla protezione DRM e sugli URL di acquisizione delle licenze.

Durante la fase di esecuzione, il flusso è il seguente:

1. Durante l'autenticazione utente, viene generato un token JWT.
1. Una delle attestazioni contenuta nel token JWT è l'attestazione "groups" contenente l'ID oggetto gruppo di "EntitledUserGroup". Questa attestazione verrà usata per superare il controllo "entitlement check".
1. Il lettore scarica il manifesto client di un contenuto protetto da CENC e "vede" quanto segue:
	1. ID chiave. 
	1. Il contenuto è protetto da CENC.
	1. URL di acquisizione delle licenze.

1. Il lettore crea una richiesta di acquisizione della licenza basata sul browser/DRM supportato. Nella richiesta di acquisizione della licenza verranno inviati anche l'ID chiave e il token JWT. Il servizio di distribuzione delle licenze verificherà il token JWT e le attestazioni contenute prima di rilasciare la licenza necessaria.

##Implementazione

###Procedure di implementazione

L'implementazione includerà i passaggi seguenti:

1. Preparare uno o più asset di test: codificare/creare un pacchetto per un video di test in un MP4 frammentato a più velocità in bit in Servizi multimediali di Azure. Questo asset NON è protetto da DRM. La protezione DRM verrà applicata più avanti con la protezione dinamica.
1. Creare l'ID chiave e la chiave simmetrica (facoltativamente dal seme chiave). In questo caso, non è necessario un sistema di gestione delle chiavi perché viene usato un solo set di ID chiave e chiave simmetrica per un paio di asset di test.
1. Usare l'API AMS per configurare i servizi di distribuzione delle licenze con DRM multiplo per l'asset di test. Se si usano server licenze personalizzati della società o dei fornitori della società invece dei servizi di licenza in Servizi multimediali di Azure, è possibile saltare questo passaggio e specificare gli URL di acquisizione delle licenze nel passaggio relativo alla configurazione della distribuzione delle licenze. L'API AMS è necessaria per specificare alcune configurazioni dettagliate, ad esempio la restrizione dei criteri di autorizzazione, i modelli di risposta di licenza per servizi di licenza DRM diversi e così via. Attualmente il portale di Azure non fornisce ancora l'interfaccia utente necessaria per questa configurazione. Le info relative all'API e il codice di esempio sono disponibili nel documento di Julia Kornich: [Uso della crittografia comune dinamica PlayReady e/o Widevine](media-services-protect-with-drm.md). 
1. Usare l'API AMS per configurare i criteri di distribuzione per l'asset di test. Le info relative all'API e il codice di esempio sono disponibili nel documento di Julia Kornich: [Uso della crittografia comune dinamica PlayReady e/o Widevine](media-services-protect-with-drm.md).
1. Creare e configurare un tenant di Azure Active Directory in Azure.
1. Creare alcuni account utente e gruppi nel tenant di Azure Active Directory: è consigliabile creare almeno un gruppo "EntitledUser" e aggiungere un utente a questo gruppo. Gli utenti di questo gruppo supereranno il controllo dei diritti durante l'acquisizione della licenza, mentre gli utenti non appartenenti a questo gruppo non riusciranno a superare il controllo di autenticazione e non potranno acquisire alcuna licenza. Essere membro di questo gruppo "EntitledUser" è un'attestazione "groups" obbligatoria nel token JWT rilasciato da Azure AD. Questo requisito di attestazione deve essere specificato nel passaggio relativo alla configurazione dei servizi di distribuzione di licenze con DRM multiplo.
1. Creare un'app MVC ASP.NET che ospiterà il lettore video. Questa app ASP.NET verrà protetta con l'autenticazione utente nel tenant di Azure Active Directory. Le attestazioni appropriate verranno incluse nei token di accesso ottenuti dopo l'autenticazione utente. Per questo passaggio, è consigliata l'API OpenID Connect. È necessario installare i pacchetti NuGet seguenti:
	- Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
	- Install-Package Microsoft.Owin.Security.OpenIdConnect
	- Install-Package Microsoft.Owin.Security.Cookies
	- Install-Package Microsoft.Owin.Host.SystemWeb
	- Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
1. Creare un lettore usando l'[API di Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). L'[API ProtectionInfo di Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/) consente di specificare la tecnologia DRM da usare in una piattaforma DRM diversa.
1. Testare la matrice:

**DRM**|**Browser**|**Risultato per un utente idoneo**|**Risultato per un utente non idoneo**
---|---|-----|---------
**PlayReady**|MS Edge o IE11 in Windows 10|Succeed|Fail
**Widevine**|Chrome in Windows 10|Succeed|Fail
**FairPlay** |Da definire||

George Trifonov del team di Servizi multimediali di Azure ha scritto un blog con i passaggi dettagliati per configurare Azure Active Directory per un'app lettore ASP.NET MVC: [Integrare l'app basata su OWIN MVC di Servizi multimediali di Azure con Azure Active Directory e limitare la distribuzione di chiavi simmetriche in base ad attestazioni JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

George ha scritto anche un blog relativo all'[autenticazione di token JWT in Servizi multimediali di Azure e alla crittografia dinamica](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/). Qui invece è disponibile il suo [esempio di integrazione di Azure AD con la distribuzione delle chiavi di Servizi multimediali di Azure](https://github.com/AzureMediaServicesSamples/Key-delivery-with-AAD-integration/).

Per informazioni su Azure Active Directory:

- Le informazioni per gli sviluppatori sono disponibili nella [Guida per gli sviluppatori di Azure Active Directory](../active-directory/active-directory-developers-guide.md).
- Le informazioni per gli amministratori sono disponibili in [Amministrare la directory di Azure AD](../active-directory/active-directory-administer.md).

### Problematiche di implementazione

Esistono alcuni "trabocchetti" nell'implementazione. L'elenco seguente di "trabocchetti" dovrebbe essere d'aiuto per risolvere eventuali problemi.

1. L'URL dell'**autorità di certificazione** deve terminare con **"/"**.  

	**Audience** deve corrispondere all'ID client dell'applicazione lettore ed è anche consigliabile aggiungere **"/"** alla fine dell'URL dell'autorità di certificazione.

		<add key="ida:audience" value="[Application Client ID GUID]" />
		<add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" /> 

	In [JWT Decoder](http://jwt.calebb.net/) verranno visualizzati **aud** e **iss** come nel token JWT seguente:
	
	![Primo trabocchetto](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)


2. Aggiungere le autorizzazioni all'applicazione in AAD (nella scheda Configura dell'applicazione). Questa operazione è obbligatoria per ogni applicazione (versioni locali e distribuite).

	![Secondo trabocchetto](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)


3. Usare l'autorità di certificazione corretta durante la configurazione della protezione CENC dinamica:

		<add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
	
	La seguente non funzionerà:
	
		<add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
	
	Il GUID è l'ID tenant di AAD. Il GUID è presente nel popup Endpoint nel portale di Azure.

4. Concedere i privilegi delle attestazioni di appartenenza al gruppo. Verificare che nel file manifesto dell'applicazione AAD, sia presente quanto segue:

	"groupMembershipClaims": "All" (il valore predefinito è Null).

5. Impostare l'oggetto TokenType appropriato quando si creano i requisiti relativi alle restrizioni.

		objTokenRestrictionTemplate.TokenType = TokenType.JWT;

	Dopo l'aggiunta del supporto di JWT (AAD) oltre a SWT (ACS), l'oggetto TokenType predefinito è TokenType.JWT. Se si usa SWT/ACS, è necessario impostarlo su TokenType.SWT.

## Argomenti aggiuntivi per l'implementazione
Ora verranno illustrati alcuni argomenti aggiuntivi sulla progettazione e l'implementazione.

###HTTP o HTTPS?

L'applicazione lettore MVC ASP.NET compilata deve supportare quanto segue:

1. Autenticazione utente con Azure AD, che deve usare HTTPS.
1. Scambio di token JWT tra il client e Azure AD, che deve usare HTTPS.
1. Acquisizione della licenza DRM dal client, che deve essere gestita tramite HTTPS se la distribuzione delle licenze avviene tramite Servizi multimediali di Azure. Naturalmente, la famiglia di prodotti PlayReady non obbliga a usare HTTPS per la distribuzione delle licenze. Se il server delle licenze PlayReady non rientra nei Servizi multimediali di Azure, usare HTTP o HTTPS.

L'applicazione lettore ASP.NET dovrà quindi usare HTTPS come procedura consigliata. Ciò significa che Azure Media Player sarà in una pagina con HTTPS. Tuttavia, per lo streaming è preferibile HTTP, perciò è necessario considerare il problema del contenuto misto.

1. Il browser non consente il contenuto misto, ma lo consentono i plug-in come Silverlight e il plug-in OSMF per Smooth e DASH. Il contenuto misto pone una questione di sicurezza a causa della minaccia derivante dalla possibilità di inserire codice JS dannoso che può mettere a rischio i dati dei clienti. I browser lo bloccano per impostazione predefinita e finora il solo modo per aggirare il problema è intervenire sul lato server (origine), per consentire tutti i domini (sia https che http), ma probabilmente nemmeno questa è una buona idea.
1. È consigliabile evitare il contenuto misto: entrambi devono usare HTTP o HTTPS. Quando gestisce il contenuto misto, la tecnologia silverlightSS richiede la cancellazione di un avviso di contenuto misto. La tecnologia flashSS gestisce il contenuto misto senza avvisi di contenuto misto.
1. Se l'endpoint di streaming è stato creato prima di agosto 2014, non supporterà HTTPS. In questo caso, creare e usare un nuovo endpoint di streaming per HTTPS.

Nell'implementazione di riferimento, per i contenuti protetti da DRM, sia l'applicazione che il flusso useranno HTTTPS. Per i contenuti aperti, per il lettore non è necessaria l'autenticazione o la licenza, quindi è possibile usare HTTP o HTTPS.

### Rollover della chiave per la firma di Azure Active Directory

Questo è un aspetto importante da considerare per l'implementazione. In caso contrario, il sistema completato smetterà di funzionare completamente entro 6 settimane al massimo.

Azure AD usa lo standard di settore per stabilire una relazione di trust tra se stesso e le applicazioni che usano Azure AD. In particolare, Azure AD usa una chiave per la firma costituita da una coppia di chiavi pubblica e privata. Quando Azure AD crea un token di sicurezza contenente informazioni sull'utente, questo token viene firmato da Azure AD con la chiave privata prima che venga inviato di nuovo all'applicazione. Per verificare che il token sia valido e sia stato realmente originato da Azure AD, l'applicazione deve convalidare la firma del token usando la chiave pubblica esposta da Azure AD contenuta nel documento di metadati federativi del tenant. Questa chiave pubblica e la chiave per la firma da cui deriva sono le stesse usate per tutti i tenant in Azure AD.

Info dettagliate sul rollover della chiave di Azure AD sono disponibili nel documento: [Informazioni importanti sul rollover della chiave di firma in Azure AD](http://msdn.microsoft.com/library/azure/dn641920.aspx/).

Tra la [coppia di chiavi pubblica-privata](https://login.windows.net/common/discovery/keys/):

- La chiave privata viene usata da Azure Active Directory per generare un token JWT.
- La chiave pubblica viene usata da un'applicazione, ad esempio i servizi di distribuzione delle licenze DRM in AMS, per verificare il token JWT.
 
Per motivi di sicurezza, Azure Active Directory fa girare questo certificato periodicamente (ogni 6 settimane). In caso di violazioni della sicurezza, il rollover della chiave può essere eseguito in qualsiasi momento. Quindi i servizi di distribuzione delle licenze in AMS devono aggiornare la chiave pubblica usata quando Azure AD fa ruotare la coppia di chiavi. In caso contrario, l'autenticazione token in AMS non riuscirà e non verrà rilasciata alcuna licenza.

Questo risultato viene ottenuto impostando TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument quando si configurano i servizi di distribuzione delle licenze DRM.

Il flusso del token JWT è il seguente:

1.	Azure AD rilascerà il token JWT con la chiave privata corrente per un utente autenticato.
2.	Quando un lettore visualizza un contenuto protetto da CENC con DRM multiplo, individuerà prima il token JWT rilasciato da Azure AD.
3.	Il lettore invia la richiesta di acquisizione della licenza con il token JWT ai servizi di distribuzione delle licenze in AMS.
4.	I servizi di distribuzione delle licenze in AMS useranno la chiave pubblica corrente/valida di Azure AD per verificare il token JWT, prima di rilasciare le licenze.

I servizi di distribuzione delle licenze DRM cercheranno sempre la chiave pubblica corrente/valida di Azure AD. La chiave pubblica presentata da Azure AD sarà la chiave usata per verificare un token JWT rilasciato da Azure AD.

Che cosa accade se il rollover della chiave viene eseguito dopo che AAD ha generato un token JWT, ma prima che il token JWT venga inviato dai lettori ai servizi di distribuzione delle licenze DRM in AMS per la verifica?

Poiché il rollover di una chiave può essere eseguito in qualsiasi momento, nel documento di metadati federativi è sempre disponibile più di una chiave pubblica valida. La distribuzione delle licenze di Servizi multimediali di Azure può usare qualsiasi chiave specificata nel documento, perché di una chiave può essere eseguito il rollover a breve, un'altra può essere quella sostitutiva e così via.

### Dov'è il token di accesso?

Se si esamina come un'app Web chiama un'app per le API in [Identità applicazione con concessione delle credenziali client OAuth 2.0](active-directory-authentication-scenarios.md#web-application-to-web-api), il flusso di autenticazione è il seguente:

1.	Un utente esegue l'accesso ad Azure AD nell'applicazione Web (vedere [Da Web browser ad applicazione Web](active-directory-authentication-scenarios.md#web-browser-to-web-application)).
2.	L'endpoint di autorizzazione di Azure AD reindirizza di nuovo l'agente utente all'applicazione client con un codice di autorizzazione. L'agente utente restituisce il codice di autorizzazione all'URI di reindirizzamento dell'applicazione client.
3.	L'applicazione Web deve acquisire un token di accesso per l'autenticazione nell'API Web e il recupero della risorsa desiderata. Esegue una richiesta all'endpoint token di Azure AD, fornendo credenziali, ID client e URI ID applicazione dell'API Web. Presenta il codice di autorizzazione per dimostrare che l'utente ha acconsentito.
4.	Azure AD autentica l'applicazione e restituisce un token di accesso JWT usato per chiamare l'API Web.
5.	Su HTTPS l'applicazione Web usa il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Bearer" nell'intestazione dell'autorizzazione della richiesta all'API Web. L'API Web convalida quindi il token JWT e, se la convalida riesce, restituisce la risorsa desiderata.

In questo flusso di "identità dell'applicazione" l'API Web confida che l'applicazione Web abbia autenticato l'utente. Per questo motivo il modello è definito sottosistema attendibile. Il [diagramma in questa pagina](http://msdn.microsoft.com/library/azure/dn645542.aspx/) illustra come il codice di autorizzazione garantisca il funzionamento del flusso.

Nell'acquisizione della licenza con la restrizione token si sta seguendo lo stesso modello di sottosistema attendibile. E il servizio di distribuzione delle licenze in Servizi multimediali di Azure è la risorsa API Web, ovvero la "risorsa back-end" a cui un'applicazione Web deve accedere. Dov'è quindi il token di accesso?

In realtà il token di accesso viene ottenuto da Azure AD. Al termine dell'autenticazione utente, viene restituito il codice di autorizzazione. Il codice di autorizzazione viene quindi usato, con l'ID client e la chiave app, per ottenere in cambio il token di accesso. Il token di accesso serve per accedere a un'applicazione "puntatore" che punta a un servizio di distribuzione delle licenze di Servizi multimediali di Azure o lo rappresenta.

È necessario registrare e configurare l'app "puntatore" in Azure AD seguendo questa procedura:

1.	Nel tenant di Azure AD

	- aggiungere un'applicazione (risorsa) con l'URL di accesso: 

	https://[resource_name].azurewebsites.net/ e

	- l'URL dell'ID app: 
	
	https://[aad_tenant_name].onmicrosoft.com/[resource_name]; 
2.	Aggiungere una nuova chiave per l'app risorsa.
3.	Aggiornare il file manifesto dell'app in modo che il valore della proprietà groupMembershipClaims sia il seguente: "groupMembershipClaims": "All".  
4.	Nell'app Azure AD che punta all'app Web del lettore, nella sezione "Autorizzazioni per altre applicazioni" aggiungere l'app risorsa aggiunta nel passaggio 1. In "Autorizzazioni delegate" selezionare la casella di controllo "Accedi a [nome\_risorsa]". L'app Web avrà così l'autorizzazione per creare i token di accesso per accedere all'app risorsa. È consigliabile eseguire questa operazione sia per la versione locale che per quella distribuita dell'app Web se si sviluppa con Visual Studio e l'app Web di Azure.
	
Quindi il token JWT rilasciato da Azure AD è in realtà il token di accesso per accedere a questa risorsa "puntatore".

### Come funziona lo streaming live?

Nella parte precedente la discussione si è concentrata sugli asset su richiesta. E per quanto riguarda lo streaming live?

L'aspetto positivo è che è possibile usare esattamente la stessa progettazione e la stessa implementazione per proteggere lo streaming live in Servizi multimediali di Azure, trattando l'asset associato a un programma come "asset VOD".

In particolare, è risaputo che, per eseguire lo streaming live in Servizi multimediali di Azure, è necessario creare un canale, quindi un programma nel canale. Per creare il programma, è necessario creare un asset che conterrà l'archivio live per il programma. Per fornire la protezione CENC con DRM multiplo del contenuto live, è sufficiente applicare all'asset la stessa configurazione/elaborazione che si applicherebbe se fosse un "asset VOD" prima di avviare il programma.

### Come funzionano i server licenze al di fuori di Servizi multimediali di Azure?

I clienti spesso investono in una farm di server licenze nel proprio data center o ospitata da provider di servizi DRM. Fortunatamente, la protezione del contenuto di Servizi multimediali di Azure consente di operare in modalità ibrida: i contenuti sono ospitati e protetti in modo dinamico in Servizi multimediali di Azure, mentre le licenze DRM vengono distribuite da server al di fuori di Servizi multimediali di Azure. In questo caso, considerare le modifiche seguenti:

1. Secure Token Service deve rilasciare token che siano accettabili e verificabili dalla farm di server licenze. Ad esempio, il server licenze Widevine fornito da Axinom richiede uno specifico token JWT contenente un elemento "entitlement message". Per rilasciare tale token JWT, è quindi necessario un servizio token di sicurezza. Gli autori hanno completato tale implementazione, i cui dettagli sono disponibili nel documento seguente del [Centro di documentazione di Azure](https://azure.microsoft.com/documentation/): [Uso di Axinom per fornire licenze Widevine ai Servizi multimediali di Azure](media-services-axinom-integration.md). 
1. Non è più necessario configurare il servizio di distribuzione delle licenze (ContentKeyAuthorizationPolicy) in Servizi multimediali di Azure. È sufficiente fornire gli URL di acquisizione delle licenze (per PlayReady, Widevine e FairPlay) quando si configura AssetDeliveryPolicy durante la configurazione di CENC con DRM multiplo.
 
### Come procedere per usare un servizio token di sicurezza personalizzato?

Un cliente può scegliere di usare un servizio token di sicurezza personalizzato per fornire i token JWT per diversi morivi. Eccone alcuni:

1.	Il provider di identità (IdP) usato dal cliente non supporta il servizio token di sicurezza. In questo caso un servizio token di sicurezza personalizzato può essere una soluzione.
2.	Il cliente può avere bisogno di un controllo più flessibile o più rigido nell'integrazione del servizio token di sicurezza con il sistema di fatturazione sottoscrittore del cliente. Un operatore MVPD, ad esempio, può offrire più pacchetti sottoscrittore OTT, ad esempio Premium, Basic, Sport e così via. L'operatore può associare le attestazioni in un token al pacchetto di un sottoscrittore in modo che siano disponibili solo i contenuti del pacchetto corretto. In questo caso, un servizio token di sicurezza personalizzato fornisce la flessibilità e il controllo necessari.

Quando si usa un servizio token di sicurezza personalizzato, è necessario apportare due modifiche:

1.	Quando si configura un servizio di distribuzione delle licenze per un asset, è necessario specificare la chiave di sicurezza usata per la verifica dal servizio token di sicurezza personalizzato (per altri dettagli, vedere di seguito) invece della chiave corrente di Azure Active Directory.
2.	Quando viene generato un token JTW, viene specificata una chiave di sicurezza invece della chiave privata del certificato X509 corrente in Azure Active Directory.

Esistono due tipi di chiavi di sicurezza:

1.	Chiave simmetrica: la stessa chiave viene usata sia per la generazione che per la verifica di un token JWT.
2.	Chiave asimmetrica: un coppia di chiavi pubblica-privata in un certificato X509 viene usata con la chiave privata per la crittografia/generazione di un token JWT e con la chiave pubblica per la verifica del token.

####Nota tecnica

Se si usa .NET Framework/C# come piattaforma di sviluppo, il certificato X509 usato per la chiave di sicurezza asimmetrica deve avere una lunghezza della chiave pari almeno a 2048 bit. È un requisito della classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey in .NET Framework. In caso contrario, verrà generata un'eccezione simile alla seguente:

IDX10630: 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' per la firma non può essere inferiore a '2048' bit.

## Sistema completato e test

Verranno illustrati alcuni scenari nel sistema end-to-end completato per offrire ai lettori una panoramica generale del comportamento prima che ottengano un account di accesso.

L'applicazione Web del lettore e l'account di accesso sono disponibili [qui](https://openidconnectweb.azurewebsites.net/).

Se è necessario uno scenario "non integrato" in cui gli asset video sono ospitati in Servizi multimediali di Azure non protetti o protetti da DRM, ma senza autenticazione token (viene rilasciata una licenza a chiunque la richieda), è possibile testarlo senza accedere (passando a HTTP se il flusso video è su HTTP).

Se è necessario uno scenario end-to-end integrato in cui gli asset video usano la protezione DRM dinamica in Servizi multimediali di Azure, con l'autenticazione token e il token JWT generato da Azure AD, è necessario effettuare l'accesso.

### Accesso utente

Per testare il sistema DRM end-to-end integrato, è necessario che sia stato creato o aggiunto un "account".

Quale account?

Anche se in origine l'accesso ad Azure era consentito solo agli utenti con account Microsoft, ora è invece consentito l'accesso agli utenti di entrambi i sistemi. A questo scopo, tutte le proprietà di Azure devono considerare Azure AD come attendibile per l'autenticazione, Azure AD deve autenticare gli utenti aziendali e creare una relazione federativa in cui Azure AD considera attendibile il sistema di identità utente degli account Microsoft per autenticare gli utenti privati. Di conseguenza, Azure AD è ora in grado di autenticare gli account Microsoft "guest" e gli account Azure AD "nativi".

Poiché Azure AD considera attendibile il dominio account Microsoft (MSA), è possibile aggiungere qualsiasi account da uno dei domini seguenti al tenant di Azure AD personalizzato e usare l'account per l'accesso:

**Nome di dominio**|**Dominio**
-----------|----------
**Dominio del tenant di Azure AD personalizzato**|nome.onmicrosoft.com
**Dominio aziendale**|microsoft.com
**Dominio account Microsoft (MSA)**|outlook.com, live.com, hotmail.com

È possibile contattare gli autori per farsi creare o aggiungere un account.

Di seguito sono riportati gli screenshot di alcune pagine di accesso usate da diversi account dominio.

**Account dominio del tenant di Azure AD personalizzato**: in questo caso, viene visualizzata la pagina di accesso personalizzata del dominio del tenant di Azure AD.

![Account dominio del tenant di Azure AD personalizzato](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Account dominio Microsoft con smart card**: in questo caso, viene visualizzata la pagina di accesso personalizzata dall'IT aziendale Microsoft con autenticazione a due fattori.

![Account dominio del tenant di Azure AD personalizzato](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Account Microsoft (MSA)**: in questo caso, viene visualizzata la pagina di accesso dell'account Microsoft per i clienti.

![Account dominio del tenant di Azure AD personalizzato](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### Uso di Encrypted Media Extensions per PlayReady

In un browser moderno con il supporto EME (Encrypted Media Extensions) per PlayReady, ad esempio IE 11 in Windows 8.1 e versioni successive e il browser Microsoft Edge in Windows 10, PlayReady sarà il sistema DRM sottostante per EME.

![Uso di EME per PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Nel lettore è presente un'area scura perché la protezione di PlayReady impedisce di acquisire schermate da un video protetto.

La schermata seguente mostra i plug-in del lettore e il supporto MSE/EME.

![Uso di EME per PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME in Microsoft Edge e IE 11 in Windows 10 consente di chiamare [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) sui dispositivi Windows 10 che lo supportano. PlayReady SL3000 sblocca il flusso di contenuti premium avanzati (4 KB, HDR e così via) e nuovi modelli di distribuzione dei contenuti (finestra anticipata per contenuti avanzati).

Concentrarsi sui dispositivi Windows: PlayReady è il solo DRM nell'hardware disponibile nei dispositivi Windows (PlayReady SL3000). Un servizio di streaming può usare PlayReady tramite EME o un'applicazione UWP e offrire una migliore qualità video usando PlayReady SL3000 anziché un altro DRM. In genere, i contenuti 2K passeranno attraverso Chrome o Firefox e i contenuti 4K attraverso Microsoft Edge/IE11 o un'applicazione UWP sullo stesso dispositivo (a seconda delle impostazioni di servizio e dell'implementazione).


#### Uso di EME per Widevine

In un browser moderno con il supporto EME/Widevine, ad esempio Chrome 41+ in Windows 10, Windows 8.1, Mac OSX Yosemite e Chrome in Android 4.4.4, Google Widevine è il sistema DRM dietro EME.

![Uso di EME per Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Si noti che Widevine non impedisce di acquisire schermate da un video protetto.

![Uso di EME per Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### Utenti non idonei

Se un utente non è membro del gruppo di utenti idonei, l'utente non potrà superare il controllo "entitlement check" e il servizio di licenza con DRM multiplo rifiuterà di rilasciare la licenza richiesta, come illustrato sotto. La descrizione dettagliata è "License acquire failed", come previsto dalla progettazione.

![Utenti non idonei](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### Esecuzione del servizio token di sicurezza personalizzato

Per lo scenario dell'esecuzione del servizio token di sicurezza personalizzato, il token JWT verrà rilasciato dal servizio token di sicurezza personalizzato usando la chiave simmetrica o asimmetrica.

Uso della chiave simmetrica (con Chrome):

![Esecuzione del servizio token di sicurezza personalizzato](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Uso della chiave asimmetrica tramite un certificato X509 (con un browser Microsoft moderno).

![Esecuzione del servizio token di sicurezza personalizzato](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

In entrambi i casi precedenti, l'autenticazione utente è la stessa, ovvero tramite Azure AD. L'unica differenza è che i token JWT vengono rilasciati dal servizio token di sicurezza personalizzato invece che da Azure AD. Ovviamente, quando si configura la protezione CENC dinamica, la restrizione del servizio di distribuzione delle licenze specifica il tipo di token JWT, una chiave simmetrica o asimmetrica.

## Riepilogo

In questo documento è stata illustrata la crittografia CENC con DRM nativo multiplo e il controllo di accesso tramite l'autenticazione token: la progettazione e l'implementazione con Azure, Servizi multimediali di Azure e Azure Media Player.

- È stata presentata una progettazione di riferimento contenente tutti i componenti necessari nel sottosistema DRM/CENC.
- Implementazione di riferimento in Azure, Servizi multimediali di Azure e Azure Media Player.
- Sono stati illustrati anche alcuni argomenti direttamente collegati alla progettazione e all'implementazione.


##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###Ringraziamenti 

William Zhang, Mingfei Yan, Roland Le Franc, Kilroy Hughes, Julia Kornich

<!---HONumber=AcomDC_0204_2016-->