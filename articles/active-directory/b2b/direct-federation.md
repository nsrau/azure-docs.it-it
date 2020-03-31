---
title: Federazione diretta con un provider di identità per B2B - Azure ADDirect federation with an identity provider for B2B - Azure AD
description: Eseguire direttamente la federazione con un provider di identità SAML o WS-Fed in modo che i guest possano accedere alle app di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b99a80a90df8fcfc5efe6dfa0c2cd7e8e5e04e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050889"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Federazione diretta con ADFS e provider di terze parti per gli utenti guest (anteprima)Direct federation with AD FS and third-party providers for guest users (preview)
|     |
| --- |
| La federazione diretta è una funzionalità di anteprima pubblica di Azure Active Directory.Direct federation is a public preview feature of Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

In questo articolo viene descritto come configurare la federazione diretta con un'altra organizzazione per la collaborazione B2B. È possibile configurare la federazione diretta con qualsiasi organizzazione il cui provider di identità (IdP) supporta il protocollo SAML 2.0 o WS-Fed.
Quando si configura la federazione diretta con l'IdP di un partner, i nuovi utenti guest di tale dominio possono usare il proprio account aziendale gestito da IdP per accedere al tenant di Azure AD e iniziare a collaborare con l'utente. Non è necessario che l'utente guest crei un account Azure AD separato.
> [!NOTE]
> Gli utenti guest della federazione diretta devono accedere utilizzando `https://myapps.microsoft.com/?tenantid=<tenant id>` un `https://portal.azure.com/<tenant id>`collegamento che include il contesto del tenant ( ad esempio, o , o nel caso di un dominio verificato, `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`). È possibile usare anche collegamenti diretti alle applicazioni e alle risorse, purché includano tale contesto. Gli utenti della federazione diretta non sono attualmente in grado di accedere utilizzando endpoint comuni che non hanno alcun contesto tenant. Ad esempio, `https://myapps.microsoft.com` `https://portal.azure.com`utilizzando `https://teams.microsoft.com` , o verrà generato un errore.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Quando un utente guest viene autenticato con la federazione diretta?
Dopo aver configurato la federazione diretta con un'organizzazione, tutti i nuovi utenti guest invitati verranno autenticati tramite la federazione diretta. È importante notare che la configurazione della federazione diretta non modifica il metodo di autenticazione per gli utenti guest che hanno già riscattato un invito da te. Di seguito sono riportati alcuni esempi:
 - Se gli utenti guest hanno già riscattato gli inviti da te e successivamente hai configurato la federazione diretta con la loro organizzazione, tali utenti guest continueranno a utilizzare lo stesso metodo di autenticazione che hanno usato prima di impostare la federazione diretta.
 - Se si configura la federazione diretta con un'organizzazione partner e si invitano gli utenti guest e successivamente l'organizzazione partner si sposta in Azure AD, gli utenti guest che hanno già riscattato gli inviti continueranno a usare la federazione diretta, a condizione che il criteri di federazione nel tenant esistente.
 - Se si elimina la federazione diretta con un'organizzazione partner, tutti gli utenti guest che attualmente utilizzano la federazione diretta non saranno in grado di accedere.

In uno di questi scenari, è possibile aggiornare il metodo di autenticazione di un utente guest eliminando l'account utente guest dalla directory e invitandolo nuovamente.

La federazione diretta è legata agli spazi dei nomi di dominio, ad esempio contoso.com e fabrikam.com. Quando si stabilisce una configurazione di federazione diretta con ADFS o un IdP di terze parti, le organizzazioni associano uno o più spazi dei nomi di dominio a questi IdP. 

## <a name="end-user-experience"></a>Esperienza utente finale 
Con la federazione diretta, gli utenti guest accedono al tenant di Azure AD usando il proprio account aziendale. Quando accedono alle risorse condivise e viene richiesto l'accesso, gli utenti della federazione diretta vengono reindirizzati al proprio IdP. Dopo aver eseguito correttamente l'accesso, vengono restituiti ad Azure AD per accedere alle risorse. I token di aggiornamento degli utenti della federazione diretta sono validi per 12 ore, ovvero la lunghezza predefinita per il token di [aggiornamento pass-through](../develop/active-directory-configurable-token-lifetimes.md#exceptions) in Azure AD. Se l'IDP federato ha SSO abilitato, l'utente sperimenterà SSO e non verrà visualizzato alcun prompt di accesso dopo l'autenticazione iniziale.

## <a name="limitations"></a>Limitazioni

### <a name="dns-verified-domains-in-azure-ad"></a>Domini verificati tallato dal DNS in Azure AD
Il dominio con cui si vuole eseguire la federazione ***non*** deve essere verificato taDNS in Azure AD. È possibile configurare la federazione diretta con tenant di Azure AD non gestiti (verificati tramite posta elettronica o "virali") perché non sono verificati tramite DNS.

### <a name="authentication-url"></a>URL di autenticazione
La federazione diretta è consentita solo per i criteri in cui il dominio dell'URL di autenticazione corrisponde al dominio di destinazione o in cui l'URL di autenticazione è uno di questi provider di identità consentiti (questo elenco è soggetto a modifiche):
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Ad esempio, quando si configura la federazione diretta per **fabrikam.com**, l'URL `https://fabrikam.com/adfs` di autenticazione supererà la convalida. Un host nello stesso dominio passerà `https://sts.fabrikam.com/adfs`anche, ad esempio . Tuttavia, l'URL `https://fabrikamconglomerate.com/adfs` di autenticazione o `https://fabrikam.com.uk/adfs` per lo stesso dominio non passerà.

### <a name="signing-certificate-renewal"></a>Rinnovo del certificato di firma
Se si specifica l'URL dei metadati nelle impostazioni del provider di identità, Azure AD rinnoverà automaticamente il certificato di firma alla scadenza. Tuttavia, se il certificato viene ruotato per qualsiasi motivo prima dell'ora di scadenza o se non si specifica un URL dei metadati, Azure AD non sarà in grado di rinnovarlo. In questo caso, sarà necessario aggiornare manualmente il certificato di firma.

### <a name="limit-on-federation-relationships"></a>Limite delle relazioni di federazione
Attualmente è supportato un massimo di 1.000 relazioni di federazione. Questo limite include sia [le federazioni interne](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) che le federazioni dirette.

### <a name="limit-on-multiple-domains"></a>Limite su più domini
Al momento non è supportata la federazione diretta con più domini dallo stesso tenant.

## <a name="frequently-asked-questions"></a>Domande frequenti
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>È possibile configurare la federazione diretta con un dominio per il quale esiste un tenant non gestito (verificato tramite posta elettronica)? 
Sì. Se il dominio non è stato verificato e il tenant non è stato sottoposto a [un'acquisizione da parte dell'amministratore,](../users-groups-roles/domains-admin-takeover.md)è possibile configurare la federazione diretta con tale dominio. I tenant non gestiti o verificati tramite posta elettronica vengono creati quando un utente riscatta un invito B2B o esegue un'iscrizione self-service per Azure AD usando un dominio attualmente inesistente. È possibile configurare la federazione diretta con questi domini. Se si tenta di configurare la federazione diretta con un dominio verificato DNS, nel portale di Azure o tramite PowerShell, verrà visualizzato un errore.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Se la federazione diretta e l'autenticazione monocodice tramite posta elettronica sono entrambe abilitate, quale metodo ha la precedenza?
Quando viene stabilita la federazione diretta con un'organizzazione partner, ha la precedenza sull'autenticazione monocodice tramite posta elettronica per i nuovi utenti guest dell'organizzazione. Se un utente guest ha riscattato un invito utilizzando l'autenticazione di un passcode monouso prima di configurare la federazione diretta, continuerà a utilizzare l'autenticazione monocodice una tantando. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>La federazione diretta risolve i problemi di accesso a causa di una tenancy parzialmente sincronizzata?
No, in questo scenario deve essere utilizzata la funzionalità di [passcode monouso](one-time-passcode.md) della posta elettronica. Una "tenancy parzialmente sincronizzata" si riferisce a un tenant di Azure AD partner in cui le identità utente locali non sono completamente sincronizzate nel cloud. Un guest la cui identità non esiste ancora nel cloud, ma che tenta di riscattare l'invito B2B non sarà in grado di accedere. La funzionalità di passcode monouso consentirebbe a questo guest di accedere. La funzionalità di federazione diretta risolve scenari in cui il guest ha il proprio account aziendale gestito da IdP, ma l'organizzazione non ha alcuna presenza di Azure AD.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Passaggio 1: Configurare il provider di identità dell'organizzazione partnerStep 1: Configure the partner organization's identity provider
In primo luogo, l'organizzazione partner deve configurare il proprio provider di identità con le attestazioni necessarie e le attendibilità della relying party. 

> [!NOTE]
> Per illustrare come configurare un provider di identità per la federazione diretta, verrà utilizzato Active Directory Federation Services (ADFS) come esempio. Vedere l'articolo [Configure direct federation with AD FS](direct-federation-adfs.md), che fornisce esempi su come configurare ADFS come provider di identità SAML 2.0 o WS-Fed in preparazione per la federazione diretta.

### <a name="saml-20-configuration"></a>Configurazione di SAML 2.0

Azure AD B2B può essere configurato per la federazione con provider di identità che usano il protocollo SAML con requisiti specifici elencati di seguito. Per altre informazioni sull'impostazione di una relazione di trust tra il provider di identità SAML e Azure AD, vedere Usare un provider di [identità SAML 2.0 (IdP) per Single Sign-On.](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp)  

> [!NOTE]
> Il dominio di destinazione per la federazione diretta non deve essere verificato DNS in Azure AD. Il dominio dell'URL di autenticazione deve corrispondere al dominio di destinazione o deve essere il dominio di un provider di identità consentito. Vedere la sezione [Limitazioni](#limitations) per i dettagli. 

#### <a name="required-saml-20-attributes-and-claims"></a>Attributi e attestazioni SAML 2.0 obbligatori
Nelle tabelle seguenti vengono illustrati i requisiti per attributi e attestazioni specifici che devono essere configurati nel provider di identità di terze parti. Per configurare la federazione diretta, è necessario ricevere i seguenti attributi nella risposta SAML 2.0 dal provider di identità. Questi attributi possono essere configurati collegando al file XML del servizio token di sicurezza online o immettendoli manualmente.

Attributi obbligatori per la risposta SAML 2.0 dall'IdP:

|Attributo  |valore  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Destinatari     |`urn:federation:MicrosoftOnline`         |
|Issuer     |L'URI dell'autorità emittente dell'IdP del partner, ad esempio`http://www.example.com/exk10l6w90DHM0yi...`         |


Attestazioni necessarie per il token SAML 2.0 emesso dall'IdP:

|Attributo  |valore  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Configurazione WS-Fed 
Azure AD B2B può essere configurato per la federazione con provider di identità che usano il protocollo WS-Fed con alcuni requisiti specifici, come indicato di seguito. Attualmente, i due provider WS-Fed sono stati testati per la compatibilità con Azure AD includono AD FS e Shibboleth. Per ulteriori informazioni sulla creazione di una relying party trust tra un provider compatibile con WS-Fed con Azure AD, vedere "STS Integration Paper using WS Protocols" disponibile in [Azure AD Identity Provider Compatibility Docs](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> Il dominio di destinazione per la federazione diretta non deve essere verificato DNS in Azure AD. Il dominio dell'URL di autenticazione deve corrispondere al dominio di destinazione o al dominio di un provider di identità consentito. Vedere la sezione [Limitazioni](#limitations) per i dettagli. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Attributi e rivendicazioni WS-Fed richiesti

Nelle tabelle seguenti vengono illustrati i requisiti per attributi e attestazioni specifici che devono essere configurati presso il provider di identità WS-Fed di terze parti. Per impostare la federazione diretta, i seguenti attributi devono essere ricevuti nel messaggio WS-Fed dal provider di identità. Questi attributi possono essere configurati collegando al file XML del servizio token di sicurezza online o immettendoli manualmente.

Attributi obbligatori nel messaggio WS-Fed dell'IdP:
 
|Attributo  |valore  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Destinatari     |`urn:federation:MicrosoftOnline`         |
|Issuer     |L'URI dell'autorità emittente dell'IdP del partner, ad esempio`http://www.example.com/exk10l6w90DHM0yi...`         |

Attestazioni richieste per il token WS-Fed emesso dall'IdP:

|Attributo  |valore  |
|---------|---------|
|Idimmutabile     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Passaggio 2: Configurare la federazione diretta in Azure ADStep 2: Configure direct federation in Azure AD 
Successivamente, si configurerà la federazione con il provider di identità configurato nel passaggio 1 in Azure AD. È possibile usare il portale di Azure AD o PowerShell.You can use either the Azure AD portal or PowerShell. Potrebbero essere necessari 5-10 minuti prima che il criterio di federazione diretta abbia effetto. Durante questo periodo, non tentare di riscattare un invito per il dominio federativo diretto. Gli attributi seguenti sono obbligatori:
- URI dell'autorità emittente dell'IdP del partner
- Endpoint di autenticazione passiva dell'IdP del partner (è supportata solo https)
- Certificato

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Per configurare la federazione diretta nel portale di Azure ADTo configure direct federation in the Azure AD portal

1. Passare al [portale di Azure](https://portal.azure.com/). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Relazioni aziendali**.
3. Selezionare Provider di **identità**, quindi **nuovo IDP SAML/WS-Fed**.

    ![Screenshot che mostra il pulsante per aggiungere un nuovo IDP SAML o WS-Fed](media/direct-federation/new-saml-wsfed-idp.png)

4. Nella pagina **New SAML/WS-Fed IdP,** in Protocollo provider di **identità**, selezionare **SAML** o **WS-FED**.

    ![Screenshot che mostra il pulsante di analisi nella pagina SAML o WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Immettere il nome di dominio dell'organizzazione partner, che sarà il nome di dominio di destinazione per la federazione diretta
6. È possibile caricare un file di metadati per popolare i dettagli dei metadati. Se si sceglie di immettere i metadati manualmente, immettere le seguenti informazioni:
   - Nome di dominio dell'IdP partner
   - ID entità dell'IdP del partner
   - Endpoint richiedente passivo dell'IdP partner
   - Certificato
   > [!NOTE]
   > L'URL dei metadati è facoltativo, tuttavia è consigliabile. Se si specifica l'URL dei metadati, Azure AD può rinnovare automaticamente il certificato di firma alla scadenza. Se il certificato viene ruotato per qualsiasi motivo prima dell'ora di scadenza o se non si specifica un URL dei metadati, Azure AD non sarà in grado di rinnovarlo. In questo caso, sarà necessario aggiornare manualmente il certificato di firma.

7. Selezionare **Salva**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Per configurare la federazione diretta in Azure AD tramite PowerShellTo configure direct federation in Azure AD using PowerShell

1. Installare la versione più recente del modulo di Azure AD PowerShell per Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). Se sono necessari passaggi dettagliati, la guida introduttiva per l'aggiunta di un utente guest include la sezione [Installare il modulo AzureADPreview più recente.](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module) 
2. Eseguire il comando seguente: 
   ```powershell
   Connect-AzureAD
   ```
1. Al prompt di accesso, accedere con l'account di amministratore globale gestito. 
2. Eseguire i comandi seguenti, sostituendo i valori del file di metadati federativi. Per server ADFS e Okta, il file di federazione `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`è federationmetadata.xml, ad esempio: . 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Passaggio 3: Testare la federazione diretta in Azure ADStep 3: Test direct federation in Azure AD
Eseguire ora il test della configurazione della federazione diretta invitando un nuovo utente guest B2B. Per informazioni dettagliate, vedere Aggiungere utenti di Collaborazione B2B di Azure AD nel portale di Azure.For details, see [Add Azure AD B2B collaboration users in the Azure portal.](add-users-administrator.md)
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Come si modifica una relazione di federazione diretta?

1. Passare al [portale di Azure](https://portal.azure.com/). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Relazioni aziendali**.
3. Selezionare **Provider di identità**
4. In **Provider di identità SAML/WS-Fed**selezionare il provider.
5. Nel riquadro dei dettagli del provider di identità aggiornare i valori.
6. Selezionare **Salva**.


## <a name="how-do-i-remove-direct-federation"></a>Come si rimuove la federazione diretta?
È possibile rimuovere la configurazione della federazione diretta. In tal caso, gli utenti guest della federazione diretta che hanno già riscattato i loro inviti non potranno accedere. Ma puoi dare loro di nuovo l'accesso alle tue risorse eliminandole dalla directory e invitandole di nuovo. Per rimuovere la federazione diretta con un provider di identità nel portale di Azure AD:To remove direct federation with an identity provider in the Azure AD portal:

1. Passare al [portale di Azure](https://portal.azure.com/). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Relazioni aziendali**.
3. Selezionare **Provider di identità**.
4. Selezionare il provider di identità e quindi **Elimina**. 
5. Selezionare **Sì** per confermare l'eliminazione. 

Per rimuovere la federazione diretta con un provider di identità tramite PowerShell:To remove direct federation with an identity provider by using PowerShell:
1. Installare la versione più recente del modulo di Azure AD PowerShell per Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Eseguire il comando seguente: 
   ```powershell
   Connect-AzureAD
   ```
3. Al prompt di accesso, accedere con l'account di amministratore globale gestito. 
4. Immettere il comando seguente:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```
