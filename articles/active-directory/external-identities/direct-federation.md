---
title: Federazione diretta con un provider di identità per B2B - Azure AD
description: Configurare la federazione diretta con un provider di identità SAML o WS-Fed per consentire agli utenti guest di accedere alle app Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/24/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cb0223b338457ad5eeea0b0bb40593f57a0d3aa
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442083"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Federazione diretta con Active Directory Federation Services (AD FS) e provider di terze parti per utenti guest (anteprima)

> [!NOTE]
>  La federazione diretta è una funzionalità in anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo descrive come configurare la federazione diretta con un'altra organizzazione per la collaborazione B2B. È possibile configurare la federazione diretta con qualsiasi organizzazione il cui provider di identità (IdP) supporti il protocollo SAML 2.0 o WS-Fed.
Quando si configura la federazione diretta con il provider di identità di un partner, i nuovi utenti guest di tale dominio possono usare il proprio account aziendale gestito dal provider di identità per accedere al tenant di Azure AD e iniziare a collaborare con l'utente. Non è necessario che l'utente guest crei un account Azure AD separato.
> [!NOTE]
> Gli utenti guest di federazione diretta devono accedere tramite un collegamento che include il contesto tenant, ad esempio `https://myapps.microsoft.com/?tenantid=<tenant id>` o `https://portal.azure.com/<tenant id>` oppure, nel caso di un dominio verificato, `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`. È possibile usare anche collegamenti diretti alle applicazioni e alle risorse, purché includano tale contesto. Gli utenti di federazione diretta attualmente non possono accedere tramite endpoint comuni privi di un contesto tenant. Ad esempio, l'uso di `https://myapps.microsoft.com`, `https://portal.azure.com` o `https://teams.microsoft.com` genera un errore.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Quando un utente guest viene autenticato tramite federazione diretta?
Dopo aver configurato la federazione diretta con un'organizzazione, tutti i nuovi utenti guest invitati verranno autenticati tramite federazione diretta. È importante notare che la configurazione della federazione diretta non modifica il metodo di autenticazione per gli utenti guest che hanno già riscattato un invito da parte dell'utente. Di seguito sono riportati alcuni esempi:
 - Se gli utenti guest hanno già riscattato gli inviti e successivamente si è configurata la federazione diretta con l'organizzazione, gli utenti guest continueranno a usare lo stesso metodo di autenticazione usato prima che fosse configurata la federazione diretta.
 - Se si configura la federazione diretta con un'organizzazione partner, si invitano utenti guest e quindi l'organizzazione partner passa successivamente ad Azure AD, gli utenti guest che hanno già riscattato gli inviti continueranno a usare la federazione diretta, fintanto che esistono i criteri di federazione diretti nel tenant.
 - Se si elimina la federazione diretta con un'organizzazione partner, gli utenti guest che attualmente usano la federazione diretta non potranno più eseguire l'accesso.

In tutti questi scenari è possibile aggiornare il metodo di autenticazione di un utente guest eliminando l'account utente guest dalla directory e invitandolo di nuovo.

La federazione diretta è associata agli spazi dei nomi di dominio, ad esempio contoso.com e fabrikam.com. Quando si stabilisce una configurazione di federazione diretta con Active Directory Federation Services o un provider di identità di terze parti, le organizzazioni associano uno o più spazi dei nomi di dominio a questi provider di identità. 

## <a name="end-user-experience"></a>Esperienza utente finale 
Con la federazione diretta, gli utenti guest possono accedere al tenant di Azure AD usando il proprio account aziendale. Quando accedono alle risorse condivise e viene richiesto di eseguire l'accesso, gli utenti di federazione diretta vengono reindirizzati al proprio provider di identità. Dopo aver eseguito l'accesso, vengono reindirizzati ad Azure AD per accedere alle risorse. I token di aggiornamento degli utenti di federazione diretta sono validi per 12 ore, ovvero il valore di [lunghezza predefinita per il token di aggiornamento pass-through](../develop/active-directory-configurable-token-lifetimes.md#exceptions) in Azure AD. Se per il provider di identità federato ha abilitato l'accesso Single Sign-On, l'utente userà l'esperienza di accesso SSO e non verrà visualizzato alcun messaggio di richiesta di accesso dopo l'autenticazione iniziale.

## <a name="limitations"></a>Limitazioni

### <a name="dns-verified-domains-in-azure-ad"></a>Domini con verifica DNS in Azure AD
Il dominio di cui si vuole eseguire la Federazione con deve ***non**essere verificato da DNS in Azure ad. È consentito configurare la federazione diretta con tenant di Azure AD non gestiti (verificati tramite posta elettronica o "virali") perché sono senza verifica DNS.

### <a name="authentication-url"></a>URL di autenticazione
La federazione diretta è consentita solo per i criteri in cui il dominio dell'URL di autenticazione corrisponde al dominio di destinazione o in cui l'URL di autenticazione è uno di questi provider di identità consentiti (questo elenco è soggetto a modifiche):

-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   oktapreview.com
-   okta-emea.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Ad esempio, quando si configura la Federazione diretta per _ * fabrikam. com * *, l'URL di autenticazione `https://fabrikam.com/adfs` passerà la convalida. Anche un host nello stesso dominio, ad esempio `https://sts.fabrikam.com/adfs`, supererà la convalida. Tuttavia, per l'URL di autenticazione `https://fabrikamconglomerate.com/adfs` o `https://fabrikam.com.uk/adfs` per lo stesso dominio la convalida non verrà superata.

### <a name="signing-certificate-renewal"></a>Rinnovo del certificato di firma
Se si specifica l'URL dei metadati nelle impostazioni del provider di identità, Azure AD rinnoverà automaticamente il certificato di firma alla scadenza. Tuttavia, se il certificato viene ruotato per qualsiasi motivo prima della data di scadenza o se non si specifica un URL di metadati, Azure AD non riuscirà a rinnovarlo. In questo caso, sarà necessario aggiornare manualmente il certificato di firma.

### <a name="limit-on-federation-relationships"></a>Limite per le relazioni di federazione
Attualmente è supportato un massimo di 1.000 relazioni di federazione. Questo limite include sia le [federazioni interne](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) che le federazioni dirette.

### <a name="limit-on-multiple-domains"></a>Limite per più domini
Attualmente non è supportata la federazione diretta con più domini dallo stesso tenant.

## <a name="frequently-asked-questions"></a>Domande frequenti
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>È possibile configurare la federazione diretta con un dominio per il quale esiste un tenant non gestito (verificato tramite posta elettronica)? 
Sì. Se il dominio non è stato verificato e il tenant non ha subito un'[acquisizione della proprietà dell'amministratore](../enterprise-users/domains-admin-takeover.md) è possibile configurare la federazione diretta con tale dominio. I tenant non gestiti o verificati tramite posta elettronica vengono creati quando un utente riscatta un invito B2B o esegue un'iscrizione self-service ad Azure AD usando un dominio attualmente non esistente. È possibile configurare la federazione diretta con questi domini. Se si tenta di configurare la federazione diretta con un dominio con verifica DNS nel portale di Azure o tramite PowerShell, verrà visualizzato un errore.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Se sono abilitate sia la federazione diretta e sia l'autenticazione con passcode monouso, quale metodo avrà la precedenza?
Quando viene stabilita la federazione diretta con un'organizzazione partner, ha la precedenza sull'autenticazione con passcode monouso tramite posta elettronica per i nuovi utenti guest di tale organizzazione. Se un utente guest ha riscattato un invito usando l'autenticazione con passcode monouso prima della configurazione della federazione diretta, continuerà a usare l'autenticazione con passcode monouso. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>La federazione diretta consente di risolvere i problemi di accesso causati da tenancy parzialmente sincronizzata?
No, in questo scenario è consigliabile usare la funzionalità di [passcode monouso tramite posta elettronica](one-time-passcode.md). Una "tenancy parzialmente sincronizzata" indica un tenant Azure AD partner in cui le identità utente locali non sono completamente sincronizzate nel cloud. Un utente guest la cui identità non esiste ancora nel cloud, ma che tenta di riscattare l'invito B2B non riuscirà a eseguire l'accesso. La funzionalità passcode monouso consente a questo utente guest di eseguire l'accesso. La funzionalità di federazione diretta aiuta a risolvere i problemi relativi a scenari in cui l'utente guest dispone di account aziendale gestito dal provider di identità personale, ma l'organizzazione non ha alcuna presenza in Azure AD.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Passaggio 1: Configurare il provider di identità dell'organizzazione partner
In primo luogo, l'organizzazione partner deve configurare il provider di identità con le attestazioni e i trust di relying party necessari. 

> [!NOTE]
> Per illustrare come configurare un provider di identità per la federazione diretta, si userà Active Directory Federation Services (AD FS) come esempio. Vedere l'articolo [Configurare la federazione diretta con Active Directory Federation Services](direct-federation-adfs.md), che fornisce esempi su come configurare Active Directory Federation Services come provider di identità SAML 2.0 o WS-Fed in preparazione per la federazione diretta.

### <a name="saml-20-configuration"></a>Configurazione di SAML 2.0

È possibile configurare Azure AD B2B per la federazione con i provider di identità che usano il protocollo SAML con alcuni requisiti specifici indicati di seguito. Per altre informazioni sulla configurazione di una relazione di trust tra il provider di identità SAML e Azure AD, vedere [Usare un provider di identità SAML 2.0 per l'accesso Single Sign-On](../hybrid/how-to-connect-fed-saml-idp.md).  

> [!NOTE]
> Per il dominio di destinazione per la federazione diretta non deve essere stata eseguita la verifica DNS in Azure AD. Il dominio dell'URL di autenticazione deve corrispondere al dominio di destinazione oppure deve essere il dominio di un provider di identità consentito. Per informazioni dettagliate, vedere la sezione [Limitazioni](#limitations). 

#### <a name="required-saml-20-attributes-and-claims"></a>Attestazioni e attributi SAML 2.0 necessari
Le tabelle seguenti illustrano i requisiti per attributi e attestazioni specifici che devono essere configurati nel provider di identità di terze parti. Per configurare la federazione diretta, è necessario che gli attributi seguenti vengano ricevuti nella risposta SAML 2.0 del provider di identità. Questi attributi possono essere configurati tramite il collegamento al file XML del servizio token di sicurezza online o mediante immissione manuale.

Attributi necessari per la risposta SAML 2.0 dal provider di identità:

|Attributo  |valore  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Destinatari     |`urn:federation:MicrosoftOnline`         |
|Issuer     |URI dell'autorità emittente del provider di identità partner, ad esempio `http://www.example.com/exk10l6w90DHM0yi...`         |


Attestazioni necessarie per il token SAML 2.0 rilasciato dal provider di identità:

|Attributo  |valore  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Configurazione WS-Fed 
È possibile configurare Azure AD B2B per la federazione con i provider di identità che usano il protocollo WS-Fed con alcuni requisiti specifici indicati di seguito. Attualmente, i due provider WS-Fed che sono stati testati per la compatibilità con Azure AD sono Active Directory Federation Services e Shibboleth. Per altre informazioni sulla definizione di un trust di relying party tra un provider compatibile con WS-Fed e Azure AD, vedere il documento relativo all'integrazione del servizio token di sicurezza tramite i protocolli WS disponibile nella [documentazione sulla compatibilità dei provider di identità per Azure AD](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> Per il dominio di destinazione per la federazione diretta non deve essere stata eseguita la verifica DNS in Azure AD. Il dominio dell'URL di autenticazione deve corrispondere al dominio di destinazione oppure deve essere il dominio di un provider di identità consentito. Per informazioni dettagliate, vedere la sezione [Limitazioni](#limitations). 

#### <a name="required-ws-fed-attributes-and-claims"></a>Attestazioni e attributi WS-Fed necessari

Le tabelle seguenti illustrano i requisiti per attributi e attestazioni specifici che devono essere configurati nel provider di identità WS-Fed di terze parti. Per configurare la federazione diretta, è necessario che gli attributi seguenti vengano ricevuti nel messaggio WS-Fed del provider di identità. Questi attributi possono essere configurati tramite il collegamento al file XML del servizio token di sicurezza online o mediante immissione manuale.

Attributi necessari nel messaggio WS-Fed dal provider di identità:
 
|Attributo  |valore  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Destinatari     |`urn:federation:MicrosoftOnline`         |
|Issuer     |URI dell'autorità emittente del provider di identità partner, ad esempio `http://www.example.com/exk10l6w90DHM0yi...`         |

Attestazioni necessarie per il token WS-Fed rilasciato dal provider di identità:

|Attributo  |valore  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Passaggio 2: Configurare la federazione diretta in Azure AD 
Si procederà ora alla configurazione della federazione con il provider di identità configurato nel passaggio 1 in Azure AD. È possibile usare il portale di Azure AD o PowerShell. Potrebbero essere necessari da 5 a 10 minuti prima che i criteri di federazione diretta abbiano effetto. Durante questo intervallo di tempo, non tentare di riscattare un invito per il dominio di federazione diretta. Gli attributi seguenti sono obbligatori:
- URI dell'autorità emittente del provider di identità partner
- Endpoint di autenticazione passiva del provider di identità partner (è supportato solo HTTPS)
- Certificato

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Per configurare la federazione diretta nel portale di Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com/). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Identità esterne** > **Tutti i provider di identità**.
3. Selezionare quindi **Nuovo provider di identità SAML/WS-Fed**.

    ![Screenshot che mostra il pulsante per l'aggiunta di un nuovo provider di identità SAML o WS-Fed](media/direct-federation/new-saml-wsfed-idp.png)

4. Nella pagina **Nuovo provider di identità SAML/WS-Fed** selezionare **SAML** o **WS-Fed** in **Protocollo del provider di identità**.

    ![Screenshot che mostra il pulsante di analisi nella pagina del provider di identità SAML o WS-Fed](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Immettere il nome di dominio dell'organizzazione partner, che sarà il nome di dominio di destinazione per la federazione diretta
6. È possibile caricare un file di metadati per popolare i dettagli dei metadati. Se si sceglie di immettere i metadati manualmente, specificare le informazioni seguenti:
   - Nome di dominio del provider di identità partner
   - ID entità del provider di identità partner
   - Endpoint del richiedente passivo del provider di identità partner
   - Certificato
   > [!NOTE]
   > L'URL dei metadati è facoltativo, ma è consigliabile specificarlo. Se si specifica l'URL dei metadati, Azure AD può rinnovare automaticamente il certificato di firma alla scadenza. Se il certificato viene ruotato per qualsiasi motivo prima della data di scadenza o se non si specifica un URL di metadati, Azure AD non riuscirà a rinnovarlo. In questo caso, sarà necessario aggiornare manualmente il certificato di firma.

7. Selezionare **Salva**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Per configurare la federazione diretta in Azure AD con PowerShell

1. Installare la versione più recente del modulo di Azure AD PowerShell per Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). Per la procedura dettagliata, vedere la sezione [Installare il modulo AzureADPreview più recente](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module) nella guida di avvio rapido per l'aggiunta di un utente guest. 
2. Eseguire il comando seguente: 
   ```powershell
   Connect-AzureAD
   ```
1. Al prompt di accesso, accedere con l'account di amministratore globale gestito. 
2. Eseguire i comandi seguenti, sostituendo i valori dal file di metadati di federazione. Per Active Directory Federation Services Server e Okta, il file di federazione è federationmetadata.xml, ad esempio: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`. 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Passaggio 3: Testare la federazione diretta in Azure AD
A questo punto, testare la configurazione della federazione diretta invitando un nuovo utente guest B2B. Per informazioni dettagliate, vedere [Aggiungere utenti di Collaborazione B2B di Azure AD nel portale di Azure](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Come si modifica una relazione di federazione diretta?

1. Accedere al [portale di Azure](https://portal.azure.com/). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Identità esterne**.
3. Selezionare **Tutti i provider di identità**
4. In **Provider di identità SAML/WS-Fed** selezionare il provider.
5. Nel riquadro dei dettagli del provider di identità aggiornare i valori.
6. Selezionare **Salva**.


## <a name="how-do-i-remove-direct-federation"></a>Come si rimuove la federazione diretta?
È possibile rimuovere la configurazione di federazione diretta. In tal caso, gli utenti guest della federazione che hanno già riscattato gli inviti non potranno più eseguire l'accesso. È tuttavia possibile concedere loro nuovamente l'accesso alle risorse eliminandoli dalla directory e invitandoli di nuovo. Per rimuovere la federazione diretta con un provider di identità nel portale di Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com/). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Identità esterne**.
3. Selezionare **Tutti i provider di identità**.
4. Selezionare il provider di identità, quindi selezionare **Elimina**. 
5. Selezionare **Sì** per confermare l'eliminazione. 

Per rimuovere la federazione diretta con un provider di identità usando PowerShell:
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

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sull' [esperienza di riscatto degli inviti](redemption-experience.md) quando gli utenti esterni possono accedere con diversi provider di identità.