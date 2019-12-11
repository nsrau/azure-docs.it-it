---
title: Fornire attestazioni facoltative alle app Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Come aggiungere attestazioni personalizzate o aggiuntive ai token SAML 2,0 e JSON Web Tokens (JWT) rilasciati da Azure Active Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/08/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcaf347eb91f8777b56bb2ea4d26985b2d75f645
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967224"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Procedura: fornire attestazioni facoltative all'app Azure AD

Gli sviluppatori di applicazioni possono utilizzare attestazioni facoltative nelle applicazioni Azure AD per specificare le attestazioni desiderate nei token inviati alla propria applicazione. 

Le attestazioni facoltative possono essere usate per:

- Selezionare attestazioni aggiuntive da includere nei token per l'applicazione.
- Modificare il comportamento di determinate attestazioni che Azure AD restituisce nei token.
- Aggiungere e accedere ad attestazioni personalizzate per l'applicazione.

Per gli elenchi di attestazioni standard, vedere la documentazione relativa ai [token di accesso e alle](access-tokens.md) attestazioni [id_token](id-tokens.md) . 

Mentre le attestazioni facoltative sono supportate nei token di formato v 1.0 e v 2.0, oltre che nei token SAML, forniscono la maggior parte del valore durante il passaggio dalla versione 1.0 alla versione 2.0. Uno degli obiettivi dell'endpoint della [piattaforma di identità Microsoft v 2.0](active-directory-appmodel-v2-overview.md) è costituito da dimensioni di token inferiori per garantire prestazioni ottimali da parte dei client. Di conseguenza, diverse attestazioni incluse in precedenza nei token ID e di accesso non sono più presenti nei token v2.0 e devono essere richieste espressamente per ogni applicazione.

**Tabella 1: applicabilità**

| Tipo di account | Token v1.0 | Token v2.0  |
|--------------|---------------|----------------|
| Account Microsoft personale  | N/D  | Supportato |
| Account Azure AD      | Supportato | Supportato |

## <a name="v10-and-v20-optional-claims-set"></a>set di attestazioni facoltative v 1.0 e v 2.0

Il set di attestazioni facoltative disponibili per impostazione predefinita per l'uso da parte delle applicazioni è riportato di seguito. Per aggiungere attestazioni facoltative personalizzate per l'applicazione, vedere le [estensioni della directory](#configuring-directory-extension-optional-claims) più avanti in questo articolo. Quando si aggiungono le attestazioni al **token di accesso**, questo verrà applicato ai token di accesso richiesti *per* l'applicazione (un'API Web), non *a quelli dell'* applicazione. Ciò garantisce che, indipendentemente dal client che accede all'API, il token di accesso usato per l'autenticazione nell'API contenga i dati corretti.

> [!NOTE]
> La maggior parte di queste attestazioni può essere inclusa in token JWT per v1.0 e v2.0, ma non in token SAML, salvo dove diversamente indicato nella colonna Tipo di token. Gli account utente supportano un subset di tali attestazioni, contrassegnato nella colonna "tipo di utente".  Molte delle attestazioni elencate non si applicano agli utenti consumer (non hanno tenant, quindi `tenant_ctry` non ha alcun valore).  

**Tabella 2: set di attestazioni facoltativo v 1.0 e v 2.0**

| name                       |  Description   | Tipo di token | Tipo di utente | Note  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Ora dell'ultima autenticazione dell'utente. Vedere la specifica di OpenID Connect.| Token JSON Web        |           |  |
| `tenant_region_scope`      | Area del tenant della risorsa. | Token JSON Web        |           | |
| `home_oid`                 | Per gli utenti guest, l'ID oggetto dell'utente nel tenant home dell'utente.| Token JSON Web        |           | |
| `sid`                      | ID sessione, usato per la disconnessione utente per sessione. | Token JSON Web        |  Account personali e Azure AD.   |         |
| `platf`                    | Piattaforma del dispositivo.    | Token JSON Web        |           | Limitato ai dispositivi gestiti che possono verificare il tipo di dispositivo.|
| `verified_primary_email`   | Originato da PrimaryAuthoritativeEmail dell'utente.      | Token JSON Web        |           |         |
| `verified_secondary_email` | Originato da SecondaryAuthoritativeEmail dell'utente.   | Token JSON Web        |           |        |
| `enfpolids`                | ID criteri applicati. Elenco degli ID criteri valutati per l'utente corrente. | Token JSON Web |  |  |
| `vnet`                     | Informazioni sull'identificatore di rete virtuale. | Token JSON Web        |           |      |
| `fwd`                      | Indirizzo IP.| Token JSON Web    |   | Aggiunge l'indirizzo IPv4 originale del client richiedente (quando si trova in una rete virtuale). |
| `ctry`                     | Paese dell'utente. | Token JSON Web |  | Azure AD restituisce l'attestazione facoltativa `ctry` se presente e il valore dell'attestazione è un codice paese di due lettere standard, ad esempio FR, JP, SZ e così via. |
| `tenant_ctry`              | Paese del tenant della risorsa. | Token JSON Web | | |
| `xms_pdl`          | Posizione dei dati preferita   | Token JSON Web | | Per i tenant multi-Geo, questo è il codice di tre lettere che mostra l'area geografica in cui si trova l'utente. Per ulteriori informazioni, vedere la [documentazione Azure ad Connect sul percorso dati preferito](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Ad esempio: `APC` per Asia Pacifico. |
| `xms_pl`                   | Lingua preferita dell'utente  | Token JSON Web ||La lingua preferita dell'utente, se impostata. Originato dal proprio tenant principale, negli scenari di accesso guest. LL-CC formattato ("en-us"). |
| `xms_tpl`                  | Lingua preferita del tenant| Token JSON Web | | La lingua preferita del tenant risorse, se impostata. LL formattato ("en"). |
| `ztdid`                    | ID distribuzione completamente automatico | Token JSON Web | | L'identità del dispositivo usata per [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Indirizzo di posta elettronica di riferimento, se l'utente ne ha uno.  | JWT, SAML | MSA, Azure AD | Questo valore è incluso per impostazione predefinita se l'utente è un ospite nel tenant.  Per gli utenti gestiti (quelli all'interno del tenant) deve essere richiesto tramite questa attestazione facoltativa oppure, solo per la versione 2.0, con l'ambito OpenID.  Per gli utenti gestiti, l'indirizzo di posta elettronica deve essere impostato nel [portale di amministrazione di Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Formattazione facoltativa per le attestazioni di gruppo |JWT, SAML| |Usato in combinazione con l'impostazione GroupMembershipClaims nel [manifesto dell'applicazione](reference-app-manifest.md), che è necessario impostare anche. Per informazioni dettagliate, vedere il [gruppo Claims](#configuring-groups-optional-claims) below. Per ulteriori informazioni sulle attestazioni di gruppo [, vedere How to configure Group Claims](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Stato dell'account degli utenti nel tenant. | JWT, SAML | | Se l'utente è membro del tenant, il valore è `0`. Se si tratta di un utente guest, il valore è `1`. |
| `upn`                      | Attestazione UserPrincipalName. | JWT, SAML  |           | Benché questa attestazione sia inclusa automaticamente, è possibile specificarla come attestazione facoltativa per collegare proprietà aggiuntive in modo da modificarne il comportamento nel caso dell'utente guest.  |

## <a name="v20-specific-optional-claims-set"></a>set di attestazioni facoltative specifiche v 2.0

Queste attestazioni sono sempre incluse nei token di Azure AD v 1.0, ma non sono inclusi nei token v 2.0, a meno che non vengano richiesti. Queste attestazioni sono applicabili solo a token JWT (token ID e token di accesso). 

**Tabella 3: attestazioni facoltative solo v 2.0**

| Attestazione JWT     | name                            | Description                                | Note |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Indirizzo IP                      | Indirizzo IP da cui il client ha effettuato l'accesso.   |       |
| `onprem_sid`  | ID di sicurezza locale |                                             |       |
| `pwd_exp`     | Ora di scadenza della password        | Data e ora in cui scade la password. |       |
| `pwd_url`     | URL per la modifica della password             | URL che l'utente può visitare per cambiare la password.   |   |
| `in_corp`     | All'interno della rete aziendale        | Segnala se il client sta effettuando l'accesso dalla rete aziendale. In caso contrario, l'attestazione non viene inclusa.   |  In base alle impostazioni degli [indirizzi IP attendibili](../authentication/howto-mfa-mfasettings.md#trusted-ips) nell'autenticazione a più fattori.    |
| `nickname`    | Nome alternativo                        | Nome aggiuntivo per l'utente, distinto dal nome o dal cognome. | 
| `family_name` | Cognome                       | Fornisce il cognome, il cognome o il nome della famiglia dell'utente come definito nell'oggetto utente. <br>"family_name":"Miller" | Supportato in MSA e Azure AD   |
| `given_name`  | Nome                      | Fornisce il primo o il nome "specificato" dell'utente, come impostato nell'oggetto utente.<br>"given_name": "Frank"                   | Supportato in MSA e Azure AD  |
| `upn`         | Nome dell'entità utente | Identificatore dell'utente che può essere usato con il parametro username_hint.  Non si tratta di un identificatore permanente per l'utente, pertanto non deve essere usato per inserire dati. | Per la configurazione dell'attestazione, vedere le [proprietà aggiuntive](#additional-properties-of-optional-claims) seguenti. |

### <a name="additional-properties-of-optional-claims"></a>Proprietà aggiuntive delle attestazioni facoltative

Alcuni attestazioni facoltative possono essere configurate per modificare il modo in cui che viene restituita l'attestazione. Queste proprietà aggiuntive vengono usate principalmente per facilitare la migrazione di applicazioni locali con previsioni di dati diverse, ad esempio `include_externally_authenticated_upn_without_hash` consente l'uso di client che non possono gestire cancelletti (`#`) nell'UPN.

**Tabella 4: valori per la configurazione di attestazioni facoltative**

| Nome proprietà  | Nome proprietà aggiuntiva | Description |
|----------------|--------------------------|-------------|
| `upn`          |                          | Utilizzabile per le risposte SAML e JWT e per i token v1.0 e v2.0. |
|                | `include_externally_authenticated_upn`  | Include l'UPN guest così come è archiviato nel tenant della risorsa. Ad esempio: `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Come sopra, tranne che i cancelletti (`#`) vengono sostituiti da caratteri di sottolineatura (`_`), ad esempio `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Esempio di proprietà aggiuntive

    ```json
        "optionalClaims": 
         {
             "idToken": [ 
            { 
                      "name": "upn", 
                      "essential": false,
                  "additionalProperties": [ "include_externally_authenticated_upn"]  
                    }
                 ]
        }
    ```

Questo oggetto OptionalClaims fa in modo che il token ID restituito al client includa un altro UPN con il tenant home aggiuntivo e informazioni sul tenant risorse. Questa operazione modificherà l'attestazione `upn` nel token solo se l'utente è un ospite nel tenant (che usa un IDP diverso per l'autenticazione). 

## <a name="configuring-optional-claims"></a>Configurazione di attestazioni facoltative

> [!IMPORTANT]
> I token di accesso vengono **sempre** generati usando il manifesto della risorsa, non il client.  Quindi, nella richiesta `...scope=https://graph.microsoft.com/user.read...` la risorsa è Graph.  Il token di accesso viene pertanto creato utilizzando il manifesto del grafo, non il manifesto del client.  La modifica del manifesto per l'applicazione non causerà mai l'aspetto diverso dei token per Graph.  Per verificare che le modifiche apportate `accessToken` siano attive, richiedere un token per l'applicazione, non un'altra app.  

È possibile configurare attestazioni facoltative per l'applicazione tramite l'interfaccia utente o il manifesto dell'applicazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Dopo essersi autenticati, scegliere il tenant di Azure AD selezionandolo nell'angolo superiore destro della pagina.
1. Selezionare **Azure Active Directory** dal menu a sinistra.
1. Nella sezione **Gestisci** selezionare **registrazioni app**.
1. Selezionare nell'elenco l'applicazione per la quale si desidera configurare attestazioni facoltative.

**Configurazione di attestazioni facoltative tramite l'interfaccia utente:**

[![Mostra come configurare attestazioni facoltative tramite l'interfaccia utente](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. Nella sezione **Gestisci** selezionare **configurazione token (anteprima)** .
2. Selezionare **Aggiungi attestazione facoltativa**.
3. Selezionare il tipo di token che si desidera configurare.
4. Selezionare le attestazioni facoltative da aggiungere.
5. Fare clic su **Aggiungi**.

**Configurazione di attestazioni facoltative tramite il manifesto dell'applicazione:**

[![Mostra come configurare attestazioni facoltative usando il manifesto dell'app](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. Nella sezione **Gestisci** selezionare **manifesto**. Si apre un editor manifesto basato sul Web che consente di modificare il manifesto. Facoltativamente è possibile selezionare **Scarica**, modificare il manifesto in locale e quindi usare **Carica** per riapplicarlo all'applicazione. Per ulteriori informazioni sul manifesto dell'applicazione, vedere l' [articolo informazioni sul manifesto dell'applicazione Azure ad](reference-app-manifest.md).

    La seguente voce del manifesto dell'applicazione aggiunge le attestazioni facoltative auth_time, IPADDR e UPN ai token ID, Access e SAML.

    ```json
        "optionalClaims":  
           {
              "idToken": [
                    {
                          "name": "auth_time", 
                          "essential": false
                     }
              ],
              "accessToken": [
                     {
                            "name": "ipaddr", 
                            "essential": false
                      }
              ],
              "saml2Token": [
                      {
                            "name": "upn", 
                            "essential": false
                       },
                       {
                            "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                            "source": "user", 
                            "essential": false
                       }
               ]
           }
       ```

2. When finished, click **Save**. Now the specified optional claims will be included in the tokens for your application.    


### OptionalClaims type

Declares the optional claims requested by an application. An application can configure optional claims to be returned in each of three types of tokens (ID token, access token, SAML 2 token) it can receive from the security token service. The application can configure a different set of optional claims to be returned in each token type. The OptionalClaims property of the Application entity is an OptionalClaims object.

**Table 5: OptionalClaims type properties**

| Name        | Type                       | Description                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Collection (OptionalClaim) | The optional claims returned in the JWT ID token. |
| `accessToken` | Collection (OptionalClaim) | The optional claims returned in the JWT access token. |
| `saml2Token`  | Collection (OptionalClaim) | The optional claims returned in the SAML token.   |

### OptionalClaim type

Contains an optional claim associated with an application or a service principal. The idToken, accessToken, and saml2Token properties of the [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) type is a collection of OptionalClaim.
If supported by a specific claim, you can also modify the behavior of the OptionalClaim using the AdditionalProperties field.

**Table 6: OptionalClaim type properties**

| Name                 | Type                    | Description                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | The name of the optional claim.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | The source (directory object) of the claim. There are predefined claims and user-defined claims from extension properties. If the source value is null, the claim is a predefined optional claim. If the source value is user, the value in the name property is the extension property from the user object. |
| `essential`            | Edm.Boolean             | If the value is true, the claim specified by the client is necessary to ensure a smooth authorization experience for the specific task requested by the end user. The default value is false.                                                                                                             |
| `additionalProperties` | Collection (Edm.String) | Additional properties of the claim. If a property exists in this collection, it modifies the behavior of the optional claim specified in the name property.                                                                                                                                               |
## Configuring directory extension optional claims

In addition to the standard optional claims set, you can also configure tokens to include directory schema extensions. For more info, see [Directory schema extensions](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). This feature is useful for attaching additional user information that your app can use – for example, an additional identifier or important configuration option that the user has set. See the bottom of this page for an example.

> [!NOTE]
> - Directory schema extensions are an Azure AD-only feature, so if your application manifest requests a custom extension and an MSA user logs into your app, these extensions will not be returned.
> - Azure AD optional claims only work with Azure AD Graph extensions and do not work with Microsoft Graph directory extensions. Both APIs require the `Directory.ReadWriteAll` permission, which can only be consented by admins.

### Directory extension formatting

When configuring directory extension optional claims using the application manifest, use the full name of the extension (in the format: `extension_<appid>_<attributename>`). The `<appid>` must match the ID of the application requesting the claim. 

Within the JWT, these claims will be emitted with the following name format:  `extn.<attributename>`.

Within the SAML tokens, these claims will be emitted with the following URI format: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## Configuring groups optional claims

   > [!NOTE]
   > The ability to emit group names for users and groups synced from on-premises is Public Preview.

This section covers the configuration options under optional claims for changing the group attributes used in group claims from the default group objectID to attributes synced from on-premises Windows Active Directory. You can configure groups optional claims for your application through the UI or application manifest.

> [!IMPORTANT]
> See [Configure group claims for applications with Azure AD](../hybrid/how-to-connect-fed-group-claims.md) for more details including important caveats for the public preview of group claims from on-premises attributes.

**Configuring groups optional claims through the UI:**
1. Sign in to the [Azure portal](https://portal.azure.com)
1. After you've authenticated, choose your Azure AD tenant by selecting it from the top right corner of the page
1. Select **Azure Active Directory** from the left hand menu
1. Under the **Manage** section, select **App registrations**
1. Select the application you want to configure optional claims for in the list
1. Under the **Manage** section, select **Token configuration (preview)**
2. Select **Add groups claim**
3. Select the group types to return (**All Groups**, **SecurityGroup** or **DirectoryRole**). The **All Groups** option includes **SecurityGroup**, **DirectoryRole** and **DistributionList**
4. Optional: click on the specific token type properties to modify the groups claim value to contain on premises group attributes or to change the claim type to a role
5. Click **Save**

**Configuring groups optional claims through the application manifest:**
1. Sign in to the [Azure portal](https://portal.azure.com)
1. After you've authenticated, choose your Azure AD tenant by selecting it from the top right corner of the page
1. Select **Azure Active Directory** from the left hand menu
1. Select the application you want to configure optional claims for in the list
1. Under the **Manage** section, select **Manifest**
3. Add the following entry using the manifest editor:

   The valid values are:

   - "All" (this option includes SecurityGroup, DirectoryRole and DistributionList)
   - "SecurityGroup"
   - "DirectoryRole"

   For example:

    ```json
        "groupMembershipClaims": "SecurityGroup"
    ```

   Per impostazione predefinita, il gruppo ObjectID verrà emesso nel valore dell'attestazione del gruppo.  Per modificare il valore dell'attestazione in modo che contenga gli attributi del gruppo locale o per modificare il tipo di attestazione in role, usare la configurazione di OptionalClaims come indicato di seguito:

3. Imposta la configurazione del nome del gruppo attestazioni facoltative.

   Se si vuole raggruppare nel token gli attributi di gruppo AD locali nella sezione attestazioni facoltative, specificare a quale tipo di token deve essere applicata l'attestazione facoltativa, il nome dell'attestazione facoltativa richiesta ed eventuali proprietà aggiuntive desiderate.  È possibile elencare più tipi di token:

   - idToken per il token ID OIDC
   - accessToken per il token di accesso OAuth/OIDC
   - Saml2Token per i token SAML.

   > [!NOTE]
   > Il tipo Saml2Token si applica ai token di formato SAML 1.1 e SAML 2.0

   Per ogni tipo di token pertinente, modificare l'attestazione groups in modo da usare la sezione OptionalClaims nel manifesto. Lo schema OptionalClaims è il seguente:

    ```json
       {
       "name": "groups",
       "source": null,
       "essential": false,
       "additionalProperties": []
       }
    ```

   | Schema delle attestazioni facoltativo | Value |
   |----------|-------------|
   | **nome** | Deve essere "groups" |
   | **origine** | Non usato. Omettere o specificare null |
   | **essenziale** | Non usato. Omettere o specificare false |
   | **additionalProperties:** | Elenco di proprietà aggiuntive.  Le opzioni valide sono "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   In additionalProperties è necessario solo un "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Se è presente più di un oggetto, viene usato il primo e tutti gli altri vengono ignorati.

   Per alcune applicazioni sono necessarie informazioni sul gruppo relative all'utente nell'attestazione del ruolo.  Per modificare il tipo di attestazione in da un'attestazione di gruppo a un'attestazione di ruolo, aggiungere "emit_as_roles" alle proprietà aggiuntive.  I valori del gruppo verranno emessi nell'attestazione del ruolo.

   > [!NOTE]
   > Se viene utilizzato "emit_as_roles", qualsiasi ruolo applicazione configurato per l'assegnazione dell'utente non verrà visualizzato nell'attestazione del ruolo

**Esempi:**

1) Creare gruppi come nomi di gruppo nei token di accesso OAuth in formato dnsDomainName\sAMAccountName

    
    **Configurazione interfaccia utente:**

    [![Mostra come configurare attestazioni facoltative tramite l'interfaccia utente](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Voce del manifesto dell'applicazione:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) Creare i nomi dei gruppi da restituire nel formato netbiosDomain\sAMAccountName come attestazione dei ruoli nei token ID SAML e OIDC

    **Configurazione interfaccia utente:**

    [![Mostra come configurare attestazioni facoltative tramite l'interfaccia utente](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Voce del manifesto dell'applicazione:**
    
    ```json
        "optionalClaims": {
        "saml2Token": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }],
        "idToken": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }]
    ``` 
     

## <a name="optional-claims-example"></a>Esempio di attestazioni facoltative

In questa sezione è riportato uno scenario che mostra come usare la funzionalità delle attestazioni facoltative per l'applicazione.
Sono disponibili più opzioni per l'aggiornamento delle proprietà di configurazione di un'identità di applicazione per abilitare e configurare le attestazioni facoltative:
-    È possibile usare l'interfaccia utente di **configurazione del token (anteprima)** (vedere l'esempio seguente)
-    È possibile usare il **manifesto** (vedere l'esempio seguente). Leggere prima il documento [Informazioni sul manifesto dell'applicazione di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) per un'introduzione al manifesto.
-   È anche possibile scrivere un'applicazione che usa l'[API Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) per aggiornare l'applicazione. Il [riferimento per entità e tipi complessi](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) nella guida di riferimento sull'API Graph consente di configurare le attestazioni facoltative.

**Esempio:** Nell'esempio seguente si useranno l'interfaccia utente di **configurazione del token (anteprima)** e il **manifesto** per aggiungere attestazioni facoltative ai token di accesso, ID e SAML destinati all'applicazione. Verranno aggiunte diverse attestazioni facoltative a ogni tipo di token che l'applicazione può ricevere:
-    I token ID conterranno ora l'UPN per gli utenti federati nel formato esteso (`<upn>_<homedomain>#EXT#@<resourcedomain>`).
-    I token di accesso richiesti da altri client per l'applicazione includeranno ora l'attestazione auth_time
-    I token SAML conterranno ora l'estensione dello schema della directory skypeId (in questo esempio l'ID per l'app è ab603c56068041afb2f6832e2a17e237). I token SAML esporranno l'ID Skype come `extension_skypeId`.

**Configurazione interfaccia utente:**

1. Accedere al [portale di Azure](https://portal.azure.com)

1. Dopo essersi autenticati, scegliere il tenant di Azure AD selezionandolo nell'angolo superiore destro della pagina.

1. Selezionare **Azure Active Directory** dal menu a sinistra.

1. Nella sezione **Gestisci** selezionare **registrazioni app**.

1. Trovare l'applicazione per cui si vogliono configurare le attestazioni facoltative e fare clic sul nome.

1. Nella sezione **Gestisci** fare clic su **configurazione token (anteprima)** .

1. Selezionare **Aggiungi attestazione facoltativa**, selezionare il tipo di token **ID** , selezionare **UPN** nell'elenco di attestazioni e quindi fare clic su **Aggiungi**.

1. Selezionare **Aggiungi attestazione facoltativa**, selezionare il tipo di token di **accesso** , selezionare **auth_time** dall'elenco di attestazioni e quindi fare clic su **Aggiungi**.

1. Dalla schermata Panoramica della configurazione del token fare clic sull'icona a matita accanto a **UPN**, fare clic sull'interruttore **autenticato esternamente** e quindi fare clic su **Salva**.

1. Selezionare **Aggiungi attestazione facoltativa**, selezionare il tipo di token **SAML** , selezionare **Extn. skypeID** dall'elenco di attestazioni (applicabile solo se è stato creato un oggetto utente Azure ad denominato skypeID), quindi fare clic su **Aggiungi**.

    [![Mostra come configurare attestazioni facoltative tramite l'interfaccia utente](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Configurazione del manifesto:**
1. Accedere al [portale di Azure](https://portal.azure.com).
1. Dopo essersi autenticati, scegliere il tenant di Azure AD selezionandolo nell'angolo superiore destro della pagina.
1. Selezionare **Azure Active Directory** dal menu a sinistra.
1. Trovare l'applicazione per cui si vogliono configurare le attestazioni facoltative e fare clic sul nome.
1. Nella sezione **Gestisci** fare clic su **manifesto** per aprire l'editor manifesto inline.
1. È possibile modificare direttamente il manifesto usando l'editor. Il manifesto segue lo schema per [Entity Application]. (https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) e formatta automaticamente il manifesto dopo il salvataggio. Alla proprietà `OptionalClaims` verranno aggiunti nuovi elementi.

    ```json
            "optionalClaims": {
                "idToken": [ 
                     { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                     }
                     ],
                "accessToken": [ 
                      {
                        "name": "auth_time", 
                        "essential": false
                      }
                     ],
            "saml2Token": [ 
                  { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": true
                  }
                 ]
        ``` 


1. When you're finished updating the manifest, click **Save** to save the manifest.

## Next steps

Learn more about the standard claims provided by Azure AD.

- [ID tokens](id-tokens.md)
- [Access tokens](access-tokens.md)
