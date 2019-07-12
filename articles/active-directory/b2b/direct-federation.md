---
title: Configurare la federazione diretta con un provider di identità per B2B - Azure Active Directory | Microsoft Docs
description: La federazione diretta con un provider di identità SAML o WS-Fed in modo che gli utenti guest possono accedere alle App Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bc3c1325e8379082134e2cbec1586f7d338ee61
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797931"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Federazione diretta con AD FS e i provider di terze parti per gli utenti guest (anteprima)
|     |
| --- |
| La federazione diretta è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Questo articolo descrive come configurare la federazione diretta con un'altra organizzazione per la collaborazione B2B. È possibile configurare la federazione diretta con qualsiasi organizzazione con provider di identità (IdP) supporta il protocollo SAML 2.0 o WS-Fed.
Quando si configura federazione diretta con provider di identità di un partner, nuovi utenti guest da quel dominio possono usare il proprio account aziendali gestiti da provider di identità per accedere al tenant di Azure AD e iniziare a collaborare con te. Non è necessario per l'utente guest creare un oggetto separato account Azure AD.
> [!NOTE]
> Indirizzare gli utenti guest di federazione necessario accedere usando un collegamento che include il contesto del tenant (ad esempio, `https://myapps.microsoft.com/?tenantid=<tenant id>` oppure `https://portal.azure.com/<tenant id>`, o nel caso di un dominio verificato, `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`). È possibile usare anche collegamenti diretti alle applicazioni e alle risorse, purché includano tale contesto. Gli utenti di federazione diretta sono attualmente in grado di accedere usando gli endpoint comuni che non hanno alcun contesto tenant. Ad esempio, usando `https://myapps.microsoft.com`, `https://portal.azure.com`, o `https://teams.microsoft.com` comporterà un errore.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Quando un utente guest viene autenticato con federazione diretta?
Dopo aver configurato la federazione diretta con un'organizzazione, tutti i nuovi utenti guest invitati verranno autenticati tramite federazione diretta. È importante notare che l'impostazione di federazione non cambia il metodo di autenticazione per gli utenti guest che hai già riscattato un invito da parte dell'utente. Ecco alcuni esempi:
 - Se gli utenti guest hanno già riscattato gli inviti da e successivamente configurare la federazione diretta con la propria organizzazione, gli utenti guest continuerà a usare lo stesso metodo di autenticazione che vengono usati prima di configurare la federazione diretta.
 - Se si imposta la federazione diretta con un'organizzazione partner e invitare utenti guest e quindi viene spostato in un secondo momento l'organizzazione partner ad Azure AD, gli utenti guest che hanno già riscattato gli inviti continuerà a usare la federazione diretta, purché diretto esiste un criterio di federazione nel tenant.
 - Se si elimina federazione diretta con un'organizzazione partner, tutti gli utenti guest attualmente tramite federazione diretta sarà possibile eseguire l'accesso.

In uno di questi scenari, è possibile aggiornare il metodo di autenticazione dell'utente guest da eliminare l'account utente guest dalla directory e li reinserimento.

La federazione diretta è associata a spazi dei nomi di dominio, ad esempio contoso.com e fabrikam.com. Quando si stabilisce una configurazione di federazione diretta con AD FS o un provider di identità di terze parti, le organizzazioni associano uno o più domain gli spazi dei nomi per questi provider di identità. 

## <a name="end-user-experience"></a>Esperienza utente finale 
Con la federazione diretta, gli utenti guest di accedere al tenant di Azure AD usando il proprio account aziendale. Quando accedono alle risorse condivise e viene richiesto per l'accesso, gli utenti di federazione vengono reindirizzati a loro provider di identità. Dopo l'esito positivo accesso, vengono restituiti ad Azure AD per accedere alle risorse. Indirizzare i token di aggiornamento degli utenti di federazione sono validi per 12 ore, il [predefinito di lunghezza per il token di aggiornamento di pass-through](../develop/active-directory-configurable-token-lifetimes.md#exceptions) in Azure AD. Se il provider di identità federato ha abilitato l'accesso SSO, l'utente verificherà SSO e non verrà visualizzato alcun messaggio di accesso dopo l'autenticazione iniziale.

## <a name="limitations"></a>Limitazioni

### <a name="dns-verified-domains-in-azure-ad"></a>Domini verificati DNS di Azure AD
Indirizzare la federazione è consentita solo per domini che siano ***non*** DNS verificati in Azure AD. La federazione diretta è consentita per i tenant non gestiti AD Azure (verificati tramite posta elettronica o "virale") perché non sono verificati DNS.
### <a name="authentication-url"></a>URL di autenticazione
La federazione diretta è consentita solo per i criteri in cui il dominio dell'URL di autenticazione corrispondente al dominio di destinazione o dove l'URL di autenticazione è uno di questi provider di identità consentiti (in questo elenco è soggetto a modifiche):
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Ad esempio, quando si configura per la federazione diretta **fabrikam.com**, l'URL di autenticazione `https://fabrikam.com/adfs` supererà la convalida. Un host nello stesso dominio passa anche, ad esempio `https://sts.fabrikam.com/adfs`. Tuttavia, l'URL di autenticazione `https://fabrikamconglomerate.com/adfs` o `https://fabrikam.com.uk/adfs` per non passa lo stesso dominio.

### <a name="signing-certificate-renewal"></a>Rinnovo del certificato di firma
Se si specifica l'URL dei metadati nelle impostazioni del provider di identità, Azure AD consente di rinnovare il certificato di firma dopo la scadenza. Tuttavia, se il certificato viene ruotato per qualsiasi motivo prima dell'ora di scadenza, o se non si specifica un URL di metadati, Azure AD sarà possibile rinnovare il certificato. In questo caso, è necessario aggiornare manualmente il certificato di firma.
## <a name="frequently-asked-questions"></a>Domande frequenti
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>È possibile impostare la federazione diretta con un dominio per cui è presente un tenant non gestito (verificati tramite posta elettronica)? 
Sì. Se non è stato verificato il dominio e il tenant non ha subito un' [un'acquisizione amministratore](../users-groups-roles/domains-admin-takeover.md), è possibile configurare la federazione diretta. I tenant non gestiti o verificati tramite posta elettronica, vengono creati quando l'utente Riscatta un invito di B2B o esegue un'iscrizione Self-Service per Azure AD usando un dominio che attualmente non esiste. È possibile configurare la federazione diretta con questi domini. Se si prova a configurare la federazione diretta con un dominio verificato DNS, nel portale di Azure o tramite PowerShell, si verrà visualizzato un errore.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Se la federazione diretta e l'autenticazione di passcode monouso inviato tramite posta elettronica sono entrambi abilitati, il metodo ha la precedenza?
Quando viene stabilita la federazione diretta con un'organizzazione partner, ha la precedenza rispetto all'autenticazione di passcode monouso inviato tramite posta elettronica per i nuovi utenti guest di tale organizzazione. Se un utente guest riscattato un invito tramite l'autenticazione di passcode monouso, prima di configurare la federazione diretta, continueranno a usare l'autenticazione di passcode monouso. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Indirizzare problemi di accesso indirizzo federazione a causa di una tenancy parzialmente sincronizzata?
No, il [inviare tramite posta elettronica di passcode monouso](one-time-passcode.md) funzionalità deve essere usata in questo scenario. Un "parzialmente sincronizzato tenancy" si riferisce a un tenant di Azure AD di partner in cui le identità degli utenti locali non sono completamente sincronizzati con il cloud. Un utente guest, ma la cui identità non esiste ancora nel cloud che tenta di riscattare l'invito di B2B non è possibile eseguire l'accesso. La funzionalità di passcode monouso consentirebbe questo guest per l'accesso. La funzionalità di federazione diretta è destinato a scenari in cui l'utente guest ha il proprio account aziendale gestito IdP, ma l'organizzazione è presente Azure AD affatto.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Passaggio 1: Configurare il provider di identità dell'organizzazione partner
In primo luogo, l'organizzazione partner deve configurare i provider di identità con le attestazioni necessarie e i trust della relying party. 

> [!NOTE]
> Per illustrare come configurare un provider di identità per la federazione diretta, useremo Active Directory Federation Services (ADFS) come esempio. Vedere l'articolo [configurare la federazione diretta con AD FS](direct-federation-adfs.md), che offre esempi di come configurare AD FS come provider di identità SAML 2.0 o WS-Fed in preparazione per la federazione diretta.

### <a name="saml-20-configuration"></a>Configurazione di SAML 2.0

Azure AD B2B può essere configurato per attuare la federazione con provider di identità che usano il protocollo SAML con requisiti specifici elencati di seguito. Per altre informazioni sulla configurazione di una relazione di trust tra il provider di identità SAML e Azure AD, vedere [usare un Provider di identità SAML 2.0 (IdP) per Single Sign-On](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp).  

> [!NOTE]
> Si noti il dominio di destinazione per la federazione diretta non devono essere verificato DNS in Azure AD. Il dominio di URL di autenticazione deve corrispondere al dominio di destinazione oppure deve essere il dominio del provider di identità consentiti. Vedere le [limitazioni](#limitations) sezione per informazioni dettagliate. 

#### <a name="required-saml-20-attributes-and-claims"></a>Obbligatori attributi SAML 2.0 e le attestazioni
Le tabelle seguenti illustrano i requisiti per gli attributi specifici e le attestazioni che devono essere configurate per il provider di identità di terze parti. Per configurare la federazione diretta, gli attributi seguenti devono essere ricevuti nella risposta dal provider di identità SAML 2.0. Questi attributi possono essere configurati mediante il collegamento al servizio token di sicurezza online file XML o immesse manualmente.

Attributi obbligatori per la risposta SAML 2.0 da IdP:

|Attributo  |Value  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Audience     |`urn:federation:MicrosoftOnline`         |
|Issuer     |L'URI dell'autorità emittente del partner provider di identità, ad esempio `http://www.example.com/exk10l6w90DHM0yi...`         |


Attestazioni necessarie per il token SAML 2.0 rilasciato dal provider di identità:

|Attributo  |Value  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Configurazione di WS-Fed 
Azure AD B2B può essere configurato per attuare la federazione con provider di identità che usano il protocollo WS-Fed con alcuni requisiti specifici, come indicato di seguito. Attualmente, i due provider di WS-Fed sono stati testati per garantire la compatibilità con Azure AD includono AD FS e Shibboleth. Per altre informazioni su come stabilire un trust della relying party tra un provider conforme a WS-Fed con Azure AD, vedere il documento di servizio token di sicurezza Integration"usando protocolli WS" disponibile nel [documentazione di Azure AD Identity Provider compatibilità](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> Il dominio di destinazione per la federazione diretta non deve essere verificato DNS in Azure AD. Il dominio di URL di autenticazione deve corrispondere al dominio di destinazione o il dominio del provider di identità consentiti. Vedere le [limitazioni](#limitations) sezione per informazioni dettagliate. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Obbligatori gli attributi di WS-Fed e attestazioni

Le tabelle seguenti illustrano i requisiti per gli attributi specifici e le attestazioni che devono essere configurate per il provider di identità WS-Fed di terze parti. Per configurare la federazione diretta, gli attributi seguenti devono essere ricevuti nel messaggio di WS-Fed dal provider di identità. Questi attributi possono essere configurati mediante il collegamento al servizio token di sicurezza online file XML o immesse manualmente.

Attributi richiesti nel messaggio di WS-Fed dal provider di identità:
 
|Attributo  |Value  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Audience     |`urn:federation:MicrosoftOnline`         |
|Issuer     |L'URI dell'autorità emittente del partner provider di identità, ad esempio `http://www.example.com/exk10l6w90DHM0yi...`         |

Attestazioni necessarie per il token WS-Fed rilasciato dal provider di identità:

|Attributo  |Value  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Passaggio 2: Configurare la federazione diretta in Azure AD 
Successivamente, si configurerà la federazione con provider di identità configurato nel passaggio 1 in Azure AD. È possibile usare il portale di Azure AD o PowerShell. Potrebbe richiedere circa 5-10 minuti prima che il criterio di federazione diretta abbia effetto. Durante questo periodo, non tentare di riscattare un invito per il dominio federazione diretta. Gli attributi seguenti sono obbligatori:
- URI del partner provider di identità dell'autorità emittente
- Endpoint di autenticazione passiva del partner provider di identità (solo https è supportato)
- Certificate

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Per configurare la federazione diretta nel portale di Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com/). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Relazioni aziendali**.
3. Selezionare **provider di identità**, quindi selezionare **IdP nuovo SAML/WS-Fed**.

    ![Screenshot che mostra il pulsante per l'aggiunta di un nuovo SAML o WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp.png)

4. Nel **IdP nuovo SAML/WS-Fed** nella pagina **protocollo del provider di identità**, selezionare **SAML** oppure **WS-FED**.

    ![Screenshot che mostra il pulsante di analisi nella pagina SAML o WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Immettere il nome di dominio dell'organizzazione partner, che sarà il nome di dominio di destinazione per la federazione diretta
6. È possibile caricare un file di metadati per popolare i dettagli dei metadati. Se si sceglie di metadati di input manualmente, immettere le informazioni seguenti:
   - Nome di dominio del partner provider di identità
   - ID di entità del provider di identità partner
   - Endpoint richiedente passivo del partner provider di identità
   - Certificate
   > [!NOTE]
   > URL dei metadati è facoltativa, ma è consigliabile utilizzarla. Se si specifica l'URL dei metadati, Azure AD può rinnovare automaticamente il certificato di firma quando scade. Se il certificato viene ruotato per qualsiasi motivo prima dell'ora di scadenza o se non si specifica un URL di metadati, Azure AD sarà possibile rinnovare il certificato. In questo caso, è necessario aggiornare manualmente il certificato di firma.

7. Selezionare **Salva**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Per configurare la federazione diretta in Azure AD usando PowerShell

1. Installare la versione più recente del modulo di Azure AD PowerShell per Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). (Se è necessaria la procedura dettagliata, la Guida introduttiva per l'aggiunta di un utente guest è inclusa la sezione [installare il modulo più recente di AzureADPreview](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Eseguire il comando seguente: 
   ```powershell
   Connect-AzureAD
   ```
1. Al prompt di accesso, accedere con l'account di amministratore globale gestito. 
2. Eseguire i comandi seguenti, sostituendo i valori dal file di metadati di federazione. Per Server AD FS e Okta, il file di federazione è FederationMetadata, ad esempio: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`. 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Passaggio 3: Test di federazione in Azure AD
Ora testare la configurazione di federazione diretta invitare un nuovo utente guest B2B. Per informazioni dettagliate, vedere [gli utenti di collaborazione aggiungere Azure AD B2B nel portale di Azure](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Come si modifica una relazione di federazione diretta?

1. Accedere al [portale di Azure](https://portal.azure.com/). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Relazioni aziendali**.
3. Selezionare **provider di identità**
4. Sotto **provider di identità SAML/WS-Fed**, selezionare il provider.
5. Nel riquadro dei dettagli del provider di identità, aggiornare i valori.
6. Selezionare **Salva**.


## <a name="how-do-i-remove-direct-federation"></a>Come si rimuove la federazione diretta?
È possibile rimuovere la configurazione di federazione diretta. Se esegue l'operazione, gli utenti guest di federazione diretta che hanno già riscattato gli inviti sarà in grado di accedere. Ma è possibile assegnare loro l'accesso alle risorse di nuovo necessario eliminarle dalla directory e li reinserimento. Per rimuovere la federazione diretta con un provider di identità nel portale di Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com/). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Relazioni aziendali**.
3. Selezionare **provider di identità**.
4. Selezionare il provider di identità e quindi selezionare **Elimina**. 
5. Selezionare **Sì** per confermare l'eliminazione. 

Per rimuovere la federazione diretta con un provider di identità tramite PowerShell:
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
