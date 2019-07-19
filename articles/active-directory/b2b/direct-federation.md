---
title: Configurare la Federazione diretta con un provider di identità per B2B-Azure Active Directory | Microsoft Docs
description: Eseguire direttamente la Federazione con un provider di identità SAML o WS-Fed, in modo che gli utenti possano accedere alle app Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 113e178d39ec776b63a0b38c55035f3493586ea2
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233863"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Federazione diretta con AD FS e provider di terze parti per utenti Guest (anteprima)
|     |
| --- |
| Direct Federation è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Questo articolo descrive come configurare la Federazione diretta con un'altra organizzazione per la collaborazione B2B. È possibile configurare la Federazione diretta con qualsiasi organizzazione il cui provider di identità (IdP) supporta il protocollo SAML 2,0 o WS-Fed.
Quando si configura la Federazione diretta con l'IdP di un partner, i nuovi utenti guest di tale dominio possono usare il proprio account aziendale gestito da IdP per accedere al tenant di Azure AD e iniziare a collaborare con l'utente. Non è necessario che l'utente Guest crei un account Azure AD separato.
> [!NOTE]
> Gli utenti guest di federazione diretta devono accedere usando un collegamento che includa il contesto del tenant ( `https://myapps.microsoft.com/?tenantid=<tenant id>` ad `https://portal.azure.com/<tenant id>`esempio, o o nel caso `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`di un dominio verificato). È possibile usare anche collegamenti diretti alle applicazioni e alle risorse, purché includano tale contesto. Gli utenti di federazione diretta non sono attualmente in grado di eseguire l'accesso usando endpoint comuni senza contesto tenant. Ad esempio, se `https://myapps.microsoft.com`si `https://portal.azure.com`USA, `https://teams.microsoft.com` o verrà generato un errore.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Quando un utente guest esegue l'autenticazione con la Federazione diretta?
Dopo aver configurato la Federazione diretta con un'organizzazione, tutti i nuovi utenti Guest invitati verranno autenticati tramite federazione diretta. È importante notare che la configurazione della Federazione diretta non modifica il metodo di autenticazione per gli utenti guest che hanno già riscattato un invito da parte dell'utente. Ecco alcuni esempi:
 - Se gli utenti Guest hanno già riscattato gli inviti e successivamente si è configurata la Federazione diretta con l'organizzazione, gli utenti Guest continueranno a usare lo stesso metodo di autenticazione usato prima di configurare la Federazione diretta.
 - Se si configura la Federazione diretta con un'organizzazione partner e si invitano utenti guest e quindi l'organizzazione partner passa successivamente a Azure AD, gli utenti guest che hanno già riscattato gli inviti continueranno a usare la Federazione diretta, purché il criteri di Federazione nel tenant esistente.
 - Se si elimina la Federazione diretta con un'organizzazione partner, gli utenti guest che attualmente usano la Federazione diretta non saranno in grado di eseguire l'accesso.

In uno di questi scenari, è possibile aggiornare il metodo di autenticazione di un utente Guest eliminando l'account utente guest dalla directory e riinvitandoli.

La Federazione diretta è associata agli spazi dei nomi di dominio, ad esempio contoso.com e fabrikam.com. Quando si stabilisce una configurazione di federazione diretta con AD FS o un IdP di terze parti, le organizzazioni associano uno o più spazi dei nomi di dominio a questi IDP. 

## <a name="end-user-experience"></a>Esperienza utente finale 
Con la Federazione diretta, gli utenti guest possono accedere al tenant di Azure AD usando il proprio account aziendale. Quando accedono alle risorse condivise e viene richiesto di eseguire l'accesso, gli utenti di federazione diretta vengono reindirizzati al proprio IdP. Dopo aver eseguito l'accesso, vengono restituiti Azure AD per accedere alle risorse. I token di aggiornamento degli utenti della Federazione diretta sono validi per 12 ore, la [lunghezza predefinita per il token di aggiornamento passthrough](../develop/active-directory-configurable-token-lifetimes.md#exceptions) in Azure ad. Se per l'IdP federato è abilitato SSO, l'utente verificherà l'accesso SSO e non verrà visualizzato alcun messaggio di richiesta di accesso dopo l'autenticazione iniziale.

## <a name="limitations"></a>Limitazioni

### <a name="dns-verified-domains-in-azure-ad"></a>Domini verificati DNS in Azure AD
La Federazione diretta è consentita solo per i domini che ***non*** sono verificati DNS in Azure ad. La Federazione diretta è consentita per i tenant non gestiti (verificati tramite posta elettronica o "virali") Azure AD perché non sono verificati dal DNS.
### <a name="authentication-url"></a>URL di autenticazione
La Federazione diretta è consentita solo per i criteri in cui il dominio dell'URL di autenticazione corrisponde al dominio di destinazione o in cui l'URL di autenticazione è uno dei provider di identità consentiti (questo elenco è soggetto a modifiche):
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Ad esempio, quando si configura la Federazione diretta per **Fabrikam.com**, l'URL `https://fabrikam.com/adfs` di autenticazione passerà la convalida. Viene anche passato un host nello stesso dominio, ad esempio `https://sts.fabrikam.com/adfs`. Tuttavia, l'URL `https://fabrikamconglomerate.com/adfs` di autenticazione o `https://fabrikam.com.uk/adfs` per lo stesso dominio non passerà.

### <a name="signing-certificate-renewal"></a>Rinnovo del certificato di firma
Se si specifica l'URL dei metadati nelle impostazioni del provider di identità, Azure AD rinnoverà automaticamente il certificato di firma alla scadenza. Tuttavia, se il certificato viene ruotato per qualsiasi motivo prima della data di scadenza o se non si specifica un URL di metadati, Azure AD non sarà in grado di rinnovarlo. In questo caso, sarà necessario aggiornare manualmente il certificato di firma.

### <a name="limit-on-federation-relationships"></a>Limite per le relazioni di Federazione
Attualmente è supportato un massimo di 1.000 relazioni di Federazione. Questo limite include sia [federazioni interne](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) che federazioni dirette.
## <a name="frequently-asked-questions"></a>Domande frequenti
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>È possibile configurare la Federazione diretta con un dominio per il quale esiste un tenant non gestito (verificato tramite posta elettronica)? 
Sì. Se il dominio non è stato verificato e il tenant non ha subito un' [acquisizione dell'amministratore](../users-groups-roles/domains-admin-takeover.md), è possibile configurare la Federazione diretta. I tenant non gestiti o verificati tramite posta elettronica vengono creati quando un utente Riscatta un invito B2B o esegue un'iscrizione self-service per Azure AD usando un dominio attualmente non esistente. È possibile configurare la Federazione diretta con questi domini. Se si tenta di configurare la Federazione diretta con un dominio verificato da DNS, nel portale di Azure o tramite PowerShell, verrà visualizzato un errore.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Se sono entrambe abilitate la Federazione diretta e l'autenticazione con password monouso, il metodo avrà la precedenza?
Quando la Federazione diretta viene stabilita con un'organizzazione partner, ha la precedenza sull'autenticazione del codice di posta elettronica monouso per i nuovi utenti guest di tale organizzazione. Se un utente Guest ha riscattato un invito usando l'autenticazione di una sola volta, prima di configurare la Federazione diretta, continuerà a usare l'autenticazione con un solo momento del codice. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>I problemi di accesso diretti alla Federazione sono dovuti a una locazione parzialmente sincronizzata?
No, in questo scenario deve essere utilizzata la funzionalità di accesso monouso per la [posta elettronica](one-time-passcode.md) . Un "tenant parzialmente sincronizzato" si riferisce a un partner Azure AD tenant in cui le identità utente locali non sono completamente sincronizzate nel cloud. Un guest la cui identità non esiste ancora nel cloud, ma che tenta di riscattare l'invito B2B non sarà in grado di eseguire l'accesso. Il servizio di accesso monouso consente a questo Guest di eseguire l'accesso. La funzionalità di federazione diretta risolve scenari in cui il Guest dispone di un proprio account aziendale gestito da IdP, ma l'organizzazione non ha alcuna Azure AD presenza.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Passaggio 1: Configurare il provider di identità dell'organizzazione partner
Per prima cosa, l'organizzazione partner deve configurare il provider di identità con le attestazioni richieste e i trust relying party. 

> [!NOTE]
> Per illustrare come configurare un provider di identità per la Federazione diretta, si userà Active Directory Federation Services (AD FS) come esempio. Vedere l'articolo [configurare la Federazione diretta con ad FS](direct-federation-adfs.md), che fornisce esempi di come configurare ad FS come provider di identità SAML 2,0 o WS-Fed in preparazione per la Federazione diretta.

### <a name="saml-20-configuration"></a>Configurazione SAML 2,0

Azure AD B2B può essere configurato per la Federazione con provider di identità che usano il protocollo SAML con requisiti specifici elencati di seguito. Per altre informazioni sulla configurazione di una relazione di trust tra il provider di identità SAML e Azure AD, vedere [usare un provider di identità saml 2,0 per Single Sign-on](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp).  

> [!NOTE]
> Si noti che il dominio di destinazione per la Federazione diretta non deve essere verificato da DNS in Azure AD. Il dominio dell'URL di autenticazione deve corrispondere al dominio di destinazione oppure deve essere il dominio di un provider di identità consentito. Per informazioni dettagliate, vedere la sezione [limitazioni](#limitations) . 

#### <a name="required-saml-20-attributes-and-claims"></a>Attestazioni e attributi SAML 2,0 richiesti
Le tabelle seguenti illustrano i requisiti per attributi e attestazioni specifici che devono essere configurati nel provider di identità di terze parti. Per configurare la Federazione diretta, è necessario che i seguenti attributi vengano ricevuti nella risposta SAML 2,0 del provider di identità. Questi attributi possono essere configurati tramite il collegamento al file XML del servizio token di sicurezza online oppure immetterli manualmente.

Attributi obbligatori per la risposta SAML 2,0 da IdP:

|Attributo  |Value  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Audience     |`urn:federation:MicrosoftOnline`         |
|Issuer     |URI dell'autorità emittente dell'IdP partner, ad esempio`http://www.example.com/exk10l6w90DHM0yi...`         |


Attestazioni necessarie per il token SAML 2,0 emesso da IdP:

|Attributo  |Value  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Configurazione WS-Fed 
Azure AD B2B può essere configurato per la Federazione con provider di identità che usano il protocollo WS-Fed con alcuni requisiti specifici, come indicato di seguito. Attualmente, i due provider WS-Fed sono stati testati per la compatibilità con Azure AD includono AD FS e Shibboleth. Per ulteriori informazioni sulla definizione di un trust relying party tra un provider compatibile con WS-Fed con Azure AD, vedere il documento relativo all'integrazione del servizio STS con i protocolli WS disponibile nella [Azure ad documentazione sulla compatibilità del provider di identità](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> Il dominio di destinazione per la Federazione diretta non deve essere verificato da DNS in Azure AD. Il dominio dell'URL di autenticazione deve corrispondere al dominio di destinazione o al dominio di un provider di identità consentito. Per informazioni dettagliate, vedere la sezione [limitazioni](#limitations) . 

#### <a name="required-ws-fed-attributes-and-claims"></a>Attestazioni e attributi WS-Fed richiesti

Le tabelle seguenti illustrano i requisiti per attributi e attestazioni specifici che devono essere configurati nel provider di identità WS-Fed di terze parti. Per configurare la Federazione diretta, è necessario che nel messaggio WS-Fed del provider di identità vengano ricevuti gli attributi seguenti. Questi attributi possono essere configurati tramite il collegamento al file XML del servizio token di sicurezza online oppure immetterli manualmente.

Attributi obbligatori nel messaggio WS-Fed dall'IdP:
 
|Attributo  |Valore  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Audience     |`urn:federation:MicrosoftOnline`         |
|Issuer     |URI dell'autorità emittente dell'IdP partner, ad esempio`http://www.example.com/exk10l6w90DHM0yi...`         |

Attestazioni necessarie per il token WS-Fed emesso da IdP:

|Attributo  |Value  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Passaggio 2: Configurare la Federazione diretta in Azure AD 
Si configurerà quindi la Federazione con il provider di identità configurato nel passaggio 1 Azure AD. È possibile usare il portale di Azure AD o PowerShell. Potrebbero essere necessari 5-10 minuti prima che i criteri di federazione diretta abbiano effetto. Durante questo periodo, non tentare di riscattare un invito per il dominio federativo diretto. Gli attributi seguenti sono obbligatori:
- URI dell'autorità emittente dell'IdP partner
- Endpoint di autenticazione passiva dell'IdP partner (è supportato solo HTTPS)
- Certificate

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Per configurare la Federazione diretta nel portale di Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com/). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Relazioni aziendali**.
3. Selezionare **provider di identità**, quindi selezionare **nuovo IDP SAML/WS-Fed**.

    ![Screenshot che mostra il pulsante per l'aggiunta di un nuovo IdP SAML o WS-Fed](media/direct-federation/new-saml-wsfed-idp.png)

4. Nella pagina nuovo provider di identità **SAML/WS-Fed** , in **protocollo del provider di identità**, selezionare **SAML** o **WS-Fed**.

    ![Screenshot che mostra il pulsante di analisi nella pagina di IdP SAML o WS-Fed](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Immettere il nome di dominio dell'organizzazione partner, che sarà il nome di dominio di destinazione per la Federazione diretta
6. È possibile caricare un file di metadati per popolare i dettagli dei metadati. Se si sceglie di immettere i metadati manualmente, immettere le informazioni seguenti:
   - Nome di dominio dell'IdP partner
   - ID entità dell'IdP partner
   - Endpoint del richiedente passivo dell'IdP partner
   - Certificate
   > [!NOTE]
   > L'URL dei metadati è facoltativo, ma è consigliabile. Se si specifica l'URL dei metadati, Azure AD possibile rinnovare automaticamente il certificato di firma al termine della scadenza. Se il certificato viene ruotato per qualsiasi motivo prima della data di scadenza o se non si specifica un URL dei metadati, Azure AD non sarà in grado di rinnovarlo. In questo caso, sarà necessario aggiornare manualmente il certificato di firma.

7. Selezionare **Salva**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Per configurare la Federazione diretta in Azure AD tramite PowerShell

1. Installare la versione più recente del modulo di Azure AD PowerShell per Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). Se sono necessari passaggi dettagliati, la Guida introduttiva per l'aggiunta di un utente Guest include la sezione [installare il modulo AzureADPreview più recente](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module). 
2. Eseguire il comando seguente: 
   ```powershell
   Connect-AzureAD
   ```
1. Al prompt di accesso, accedere con l'account di amministratore globale gestito. 
2. Eseguire i comandi seguenti, sostituendo i valori dal file di metadati della Federazione. Per AD FS server e Okta, il file di federazione è FederationMetadata. XML, ad esempio `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`:. 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Passaggio 3: Test della Federazione diretta in Azure AD
A questo punto, testare la configurazione della Federazione diretta invitando un nuovo utente Guest B2B. Per informazioni dettagliate, vedere [aggiungere Azure ad utenti di collaborazione B2B nel portale di Azure](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Ricerca per categorie modificare una relazione di federazione diretta?

1. Accedere al [portale di Azure](https://portal.azure.com/). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Relazioni aziendali**.
3. Selezionare i **provider di identità**
4. In **SAML/WS-Fed Identity Providers**selezionare il provider.
5. Nel riquadro dei dettagli del provider di identità aggiornare i valori.
6. Selezionare **Salva**.


## <a name="how-do-i-remove-direct-federation"></a>Ricerca per categorie rimuovere la Federazione diretta?
È possibile rimuovere la configurazione della Federazione diretta. In tal caso, gli utenti guest della Federazione che hanno già riscattato gli inviti non saranno in grado di eseguire l'accesso. È tuttavia possibile concedere loro l'accesso alle risorse tramite l'eliminazione dalla directory e la loro reinvito. Per rimuovere la Federazione diretta con un provider di identità nel portale di Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com/). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Relazioni aziendali**.
3. Selezionare **provider di identità**.
4. Selezionare il provider di identità e quindi fare clic su **Elimina**. 
5. Selezionare **Sì** per confermare l'eliminazione. 

Per rimuovere la Federazione diretta con un provider di identità usando PowerShell:
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
