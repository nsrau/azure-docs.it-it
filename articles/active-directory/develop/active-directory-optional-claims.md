---
title: Fornire attestazioni facoltative alle app di Azure AD. Azure
titleSuffix: Microsoft identity platform
description: Come aggiungere attestazioni personalizzate o aggiuntive ai token SAML 2.0 e JSON Web Tokens (JWT) emessi da Azure Active Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 3/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 23d83b59c510f2565b2f66f78dad56c9c9592dd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136518"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Procedura: fornire attestazioni facoltative all'app Azure ADHow to: Provide optional claims to your Azure AD app

Gli sviluppatori di applicazioni possono usare attestazioni facoltative nelle applicazioni Azure AD per specificare le attestazioni che desiderano nei token inviati all'applicazione. 

Le attestazioni facoltative possono essere usate per:

- Selezionare attestazioni aggiuntive da includere nei token per l'applicazione.
- Modificare il comportamento di determinate attestazioni che Azure AD restituisce nei token.
- Aggiungere e accedere ad attestazioni personalizzate per l'applicazione.

Per gli elenchi di attestazioni standard, vedere il [token](access-tokens.md) di accesso [e](id-tokens.md) id_token documentazione sulle attestazioni. 

Mentre le attestazioni facoltative sono supportate sia nei token di formato v1.0 e v2.0, così come nei token SAML, forniscono la maggior parte del loro valore quando si passa da v1.0 a v2.0. Uno degli obiettivi dell'endpoint della piattaforma di [identità Microsoft 2.0 è](active-directory-appmodel-v2-overview.md) la dimensione di token di dimensioni inferiori per garantire prestazioni ottimali da parte dei client. Di conseguenza, diverse attestazioni incluse in precedenza nei token ID e di accesso non sono più presenti nei token v2.0 e devono essere richieste espressamente per ogni applicazione.

**Tabella 1: applicabilità**

| Tipo di account | Token v1.0 | Token v2.0  |
|--------------|---------------|----------------|
| Account Microsoft personale  | N/D  | Supportato |
| Account Azure AD      | Supportato | Supportato |

## <a name="v10-and-v20-optional-claims-set"></a>Set di attestazioni facoltative v1.0 e v2.0

Il set di attestazioni facoltative disponibili per impostazione predefinita per l'uso da parte delle applicazioni è riportato di seguito. Per aggiungere attestazioni facoltative personalizzate per l'applicazione, vedere le [estensioni della directory](#configuring-directory-extension-optional-claims) più avanti in questo articolo. Quando si aggiungono attestazioni al token di **accesso**, le attestazioni si applicano ai token di accesso richiesti *per* l'applicazione (un'API Web), non alle attestazioni richieste *dall'applicazione.* Indipendentemente dal modo in cui il client accede all'API, i dati corretti sono presenti nel token di accesso utilizzato per l'autenticazione con l'API.

> [!NOTE]
> La maggior parte di queste attestazioni può essere inclusa in token JWT per v1.0 e v2.0, ma non in token SAML, salvo dove diversamente indicato nella colonna Tipo di token. Gli account consumer supportano un sottoinsieme di queste attestazioni, contrassegnato nella colonna "Tipo utente".  Molte delle attestazioni elencate non si applicano agli `tenant_ctry` utenti consumer (non hanno alcun tenant, quindi non hanno alcun valore).  

**Tabella 2: set di attestazioni facoltative v1.0 e v2.0**

| Nome                       |  Descrizione   | Tipo di token | Tipo di utente | Note  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Ora dell'ultima autenticazione dell'utente. Vedere la specifica di OpenID Connect.| Token JSON Web        |           |  |
| `tenant_region_scope`      | Area del tenant della risorsa. | Token JSON Web        |           | |
| `home_oid`                 | Per gli utenti guest, l'ID oggetto dell'utente nel tenant home dell'utente.| Token JSON Web        |           | |
| `sid`                      | ID sessione, utilizzato per la disconnessione da utente per sessione. | Token JSON Web        |  Account personali e di Azure AD.   |         |
| `platf`                    | Piattaforma del dispositivo.    | Token JSON Web        |           | Limitato ai dispositivi gestiti che possono verificare il tipo di dispositivo.|
| `verified_primary_email`   | Originato da PrimaryAuthoritativeEmail dell'utente.      | Token JSON Web        |           |         |
| `verified_secondary_email` | Originato da SecondaryAuthoritativeEmail dell'utente.   | Token JSON Web        |           |        |
| `enfpolids`                | ID criteri applicati. Elenco degli ID criteri valutati per l'utente corrente. | Token JSON Web |  |  |
| `vnet`                     | Informazioni sull'identificatore di rete virtuale. | Token JSON Web        |           |      |
| `fwd`                      | Indirizzo IP.| Token JSON Web    |   | Aggiunge l'indirizzo IPv4 originale del client richiedente (quando si trova in una rete virtuale). |
| `ctry`                     | Paese dell'utente. | Token JSON Web |  | Azure AD restituisce l'attestazione facoltativa `ctry` se presente e il valore dell'attestazione è un codice paese di due lettere standard, ad esempio FR, JP, SZ e così via. |
| `tenant_ctry`              | Paese del tenant della risorsa. | Token JSON Web | | |
| `xms_pdl`          | Posizione dei dati preferita   | Token JSON Web | | Per i tenant multi-geografici, la posizione dei dati preferita è il codice di tre lettere che mostra l'area geografica in cui si trova l'utente. Per altre info, vedi la documentazione di [Azure AD Connect sul percorso dati preferito.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation)<br/>Ad esempio: `APC` per Asia Pacifico. |
| `xms_pl`                   | Lingua preferita dell'utente  | Token JSON Web ||La lingua preferita dell'utente, se impostata. Originato dal proprio tenant principale, negli scenari di accesso guest. LL-CC formattato ("en-us"). |
| `xms_tpl`                  | Lingua preferita del tenant| Token JSON Web | | La lingua preferita del tenant risorse, se impostata. LL formattato ("en"). |
| `ztdid`                    | ID distribuzione completamente automatico | Token JSON Web | | L'identità del dispositivo usata per [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Indirizzo di posta elettronica di riferimento, se l'utente ne ha uno.  | JWT, SAML | MSA, Azure AD | Questo valore è incluso per impostazione predefinita se l'utente è un ospite nel tenant.  Per gli utenti gestiti (gli utenti all'interno del tenant), deve essere richiesto tramite questa attestazione facoltativa o, solo nella versione 2.0, con l'ambito OpenID.  Per gli utenti gestiti, l'indirizzo di posta elettronica deve essere impostato nel [portale di amministrazione di Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Formattazione facoltativa per le attestazioni di gruppoOptional formatting for group claims |JWT, SAML| |Utilizzato insieme all'impostazione GroupMembershipClaims nel [manifesto dell'applicazione](reference-app-manifest.md), che deve essere impostato. Per informazioni dettagliate, vedere [Attestazioni di gruppo](#configuring-groups-optional-claims) di seguito. Per altre informazioni sulle attestazioni di gruppo, vedere [Come configurare le attestazioni](../hybrid/how-to-connect-fed-group-claims.md) di gruppoFor more information about group claims, see How to configure group claims
| `acct`             | Stato dell'account degli utenti nel tenant. | JWT, SAML | | Se l'utente è membro del tenant, il valore è `0`. Se si tratta di un utente guest, il valore è `1`. |
| `upn`                      | Attestazione UserPrincipalName. | JWT, SAML  |           | Benché questa attestazione sia inclusa automaticamente, è possibile specificarla come attestazione facoltativa per collegare proprietà aggiuntive in modo da modificarne il comportamento nel caso dell'utente guest.  |

## <a name="v20-specific-optional-claims-set"></a>set di attestazioni facoltative specifico della versione 2.0

Queste attestazioni sono sempre incluse nei token di Azure AD v1.0, ma non nei token v2.0 a meno che non siano richieste. Queste attestazioni sono applicabili solo per JWT (token ID e token di accesso). 

**Tabella 3: attestazioni facoltative solo v2.0**

| Attestazione JWT     | Nome                            | Descrizione                                | Note |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Indirizzo IP                      | Indirizzo IP da cui il client ha effettuato l'accesso.   |       |
| `onprem_sid`  | ID di sicurezza locale |                                             |       |
| `pwd_exp`     | Ora di scadenza della password        | Data e ora in cui scade la password. |       |
| `pwd_url`     | URL per la modifica della password             | URL che l'utente può visitare per cambiare la password.   |   |
| `in_corp`     | All'interno della rete aziendale        | Segnala se il client sta effettuando l'accesso dalla rete aziendale. In caso contrario, la richiesta non è inclusa.   |  In base alle impostazioni degli [indirizzi IP attendibili](../authentication/howto-mfa-mfasettings.md#trusted-ips) nell'autenticazione a più fattori.    |
| `nickname`    | Nome alternativo                        | Un nome aggiuntivo per l'utente. Il soprannome è separato dal nome o dal cognome. Richiede `profile` l'ambito.| 
| `family_name` | Cognome                       | Fornisce il cognome, il cognome o il cognome dell'utente come definito nell'oggetto utente. <br>"family_name":"Miller" | Supportato in MSA e Azure AD. Richiede `profile` l'ambito.   |
| `given_name`  | Nome                      | Fornisce il primo o il nome "dato" dell'utente, impostato sull'oggetto utente.<br>"given_name": "Frank"                   | Supportato in MSA e Azure AD.  Richiede `profile` l'ambito. |
| `upn`         | Nome entità utente | Identificatore dell'utente che può essere usato con il parametro username_hint.  Non si tratta di un identificatore permanente per l'utente, pertanto non deve essere usato per inserire dati. | Per la configurazione dell'attestazione, vedere le [proprietà aggiuntive](#additional-properties-of-optional-claims) seguenti. Richiede `profile` l'ambito.|

### <a name="additional-properties-of-optional-claims"></a>Proprietà aggiuntive delle attestazioni facoltative

Alcuni attestazioni facoltative possono essere configurate per modificare il modo in cui che viene restituita l'attestazione. Queste proprietà aggiuntive vengono usate principalmente per facilitare la migrazione di applicazioni locali con previsioni di dati diverse, ad esempio `include_externally_authenticated_upn_without_hash` consente l'uso di client che non possono gestire cancelletti (`#`) nell'UPN.

**Tabella 4: Valori per la configurazione delle attestazioni facoltative**

| Nome proprietà  | Nome proprietà aggiuntiva | Descrizione |
|----------------|--------------------------|-------------|
| `upn`          |                          | Utilizzabile per le risposte SAML e JWT e per i token v1.0 e v2.0. |
|                | `include_externally_authenticated_upn`  | Include l'UPN guest così come è archiviato nel tenant della risorsa. Ad esempio, usare `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Come sopra, ad eccezione`#`del fatto che i`_`segni hash ( ) vengono sostituiti con caratteri di sottolineatura ( ), ad esempio`foo_hometenant.com_EXT_@resourcetenant.com` |

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

Questo oggetto OptionalClaims fa sì che il token ID restituito al client includa un'attestazione upn con le informazioni aggiuntive sul tenant principale e sul tenant delle risorse. L'attestazione `upn` viene modificata nel token solo se l'utente è guest nel tenant (che utilizza un IDP diverso per l'autenticazione). 

## <a name="configuring-optional-claims"></a>Configurazione di attestazioni facoltative

> [!IMPORTANT]
> I token di accesso vengono **sempre** generati usando il manifesto della risorsa, non il client.  Quindi nella `...scope=https://graph.microsoft.com/user.read...` richiesta la risorsa è l'API Microsoft Graph.  Di conseguenza, il token di accesso viene creato utilizzando il manifesto dell'API Microsoft Graph, non il manifesto del client.  La modifica del manifesto per l'applicazione non farà mai sì che i token per l'API Microsoft Graph abbiano un aspetto diverso.  Per verificare che `accessToken` le modifiche siano attive, richiedere un token per l'applicazione, non un'altra app.  


È possibile configurare attestazioni facoltative per l'applicazione tramite l'interfaccia utente o il manifesto dell'applicazione.

1. Passare al [portale di Azure](https://portal.azure.com). Cercare e selezionare **Azure Active Directory**.
1. Nella sezione **Gestisci** selezionare **Registrazioni app**.
1. Selezionare l'applicazione per cui si desidera configurare attestazioni facoltative nell'elenco.

**Configurazione delle attestazioni facoltative tramite l'interfaccia utente:Configuring optional claims through the UI:**

[![Viene illustrato come configurare attestazioni facoltative tramite l'interfaccia utente](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. Nella sezione **Gestisci** selezionare **Configurazione token (anteprima).**
2. Selezionare **Aggiungi attestazione facoltativa**.
3. Selezionare il tipo di token che si desidera configurare.
4. Selezionare le attestazioni facoltative da aggiungere.
5. Fare clic su **Aggiungi**.

**Configurazione delle attestazioni facoltative tramite il manifesto dell'applicazione:Configuring optional claims through the application manifest:**

[![Mostra come configurare attestazioni facoltative usando il manifesto dell'app](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. Nella sezione **Gestisci** selezionare **Manifesto**. Viene aperto un editor di manifesto basato sul Web, che consente di modificare il manifesto. Facoltativamente è possibile selezionare **Scarica**, modificare il manifesto in locale e quindi usare **Carica** per riapplicarlo all'applicazione. Per altre informazioni sul manifesto dell'applicazione, vedere [l'articolo Informazioni sul manifesto dell'applicazione di Azure AD.](reference-app-manifest.md)

    La seguente voce del manifesto dell'applicazione aggiunge le attestazioni facoltative auth_time, ipaddr e upn a i token ID, access e SAML.

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

2. Al termine fare clic su **Salva**. Ora le attestazioni facoltative specificate verranno incluse nei token per l'applicazione.    


### <a name="optionalclaims-type"></a>Tipo OptionalClaims

Dichiara le attestazioni facoltative richieste da un'applicazione. Un'applicazione può configurare le attestazioni facoltative da restituire in ognuno dei tre tipi di token (token ID, token di accesso, token SAML 2) che può ricevere dal servizio token di sicurezza. L'applicazione può configurare un set di attestazioni facoltative diverso da restituire in ogni tipo di token. La proprietà OptionalClaims dell'entità applicazione è un oggetto OptionalClaims.

**Tabella 5: proprietà di tipo OptionalClaims**

| Nome        | Type                       | Descrizione                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Raccolta (OptionalClaim) | Attestazioni facoltative restituite nel token ID JWT. |
| `accessToken` | Raccolta (OptionalClaim) | Attestazioni facoltative restituite nel token di accesso JWT. |
| `saml2Token`  | Raccolta (OptionalClaim) | Attestazioni facoltative restituite nel token SAML.   |

### <a name="optionalclaim-type"></a>Tipo OptionalClaim

Contiene un'attestazione facoltativa associata a un'applicazione o a un'entità servizio. Le proprietà idToken, accessToken e saml2Token del tipo [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) sono una raccolta di OptionalClaim.
Se supportato da un'attestazione specifica, è inoltre possibile modificare il comportamento di OptionalClaim usando il campo AdditionalProperties.

**Tabella 6: proprietà del tipo OptionalClaim**

| Nome                 | Type                    | Descrizione                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Nome dell'attestazione facoltativa.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | Origine (oggetto directory) dell'attestazione. Sono presenti attestazioni predefinite e attestazioni definite dall'utente dalla proprietà delle estensioni. Se il valore di origine è Null, l'attestazione è un'attestazione facoltativa predefinita. Se il valore di origine è user, il valore della proprietà name è la proprietà dell'estensione dall'oggetto utente. |
| `essential`            | Edm.Boolean             | Se il valore è True, l'attestazione specificata dal client è necessaria per garantire un'esperienza di autorizzazione uniforme per l'attività specifica richiesta dall'utente finale. Il valore predefinito è false.                                                                                                             |
| `additionalProperties` | Raccolta (Edm.String) | Proprietà aggiuntive dell'attestazione. Se esiste una proprietà in questa raccolta, modificherà il comportamento dell'attestazione facoltativa specificata nella proprietà name.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Configurazione delle attestazioni facoltative dell'estensione di directoryConfiguring directory extension optional claims

Oltre al set di attestazioni facoltative standard, è anche possibile configurare i token per includere le estensioni. Per altre info, vedi la documentazione relativa [all'estensione di Microsoft GraphProperty:](https://docs.microsoft.com/graph/api/resources/extensionproperty?view=graph-rest-1.0) tieni presente che lo schema e le estensioni aperte non sono supportati da attestazioni facoltative, ma solo dalle estensioni di directory in stile AAD-Graph.For more info, see the Microsoft Graph extensionProperty documentation - note that schema and open extensions are not supported by optional claims, only the AAD-Graph style directory extensions. Questa funzionalità è utile per il collegamento di altre informazioni sull'utente utilizzabili dall'app, ad esempio un identificatore aggiuntivo o un'opzione di configurazione importante impostata dall'utente. Vedere la parte inferiore di questa pagina per un esempio.

> [!NOTE]
> - Le estensioni dello schema di directory sono una funzionalità solo di Azure AD, pertanto se il manifesto dell'applicazione richiede un'estensione personalizzata e un utente MSA accede all'app, queste estensioni non verranno restituite.

### <a name="directory-extension-formatting"></a>Formattazione dell'estensione di directory

Quando si configurano attestazioni facoltative di estensione di directory utilizzando il `extension_<appid>_<attributename>`manifesto dell'applicazione, utilizzare il nome completo dell'estensione (nel formato: ). Deve `<appid>` corrispondere all'ID dell'applicazione che richiede l'attestazione. 

Nei token JWT queste attestazioni verranno emesse con il formato del nome seguente: `extn.<attributename>`.

Nei token SAML queste attestazioni verranno emesse con il formato URI seguente: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`.

## <a name="configuring-groups-optional-claims"></a>Configurazione delle attestazioni facoltative dei gruppiConfiguring groups optional claims

   > [!NOTE]
   > La possibilità di generare nomi di gruppo per utenti e gruppi sincronizzati da locale è Anteprima pubblica.

In questa sezione vengono illustrate le opzioni di configurazione in attestazioni facoltative per la modifica degli attributi di gruppo utilizzati nelle attestazioni di gruppo dall'objectID di gruppo predefinito agli attributi sincronizzati da Windows Active Directory locale. È possibile configurare le attestazioni facoltative di gruppo per l'applicazione tramite l'interfaccia utente o il manifesto dell'applicazione.

> [!IMPORTANT]
> Per altre informazioni sull'inclusione di importanti avvertenze per l'anteprima pubblica delle attestazioni di gruppo dagli attributi locali, vedere [Configurare le attestazioni](../hybrid/how-to-connect-fed-group-claims.md)di gruppo per le applicazioni con Azure AD.

**Configurazione delle attestazioni facoltative di gruppi tramite l'interfaccia utente:Configuring groups optional claims through the UI:**
1. Accedere al portale di [AzureSign](https://portal.azure.com) in to the Azure portal
1. Dopo aver eseguito l'autenticazione, scegliere il tenant di Azure AD selezionandolo nell'angolo superiore destro della pagina
1. Selezionare **Azure Active Directory** dal menu a sinistra
1. Nella sezione **Gestisci,** seleziona **Registrazioni app**
1. Selezionare l'applicazione per cui si desidera configurare attestazioni facoltative nell'elenco
1. Nella sezione **Gestisci,** seleziona **Configurazione token (anteprima)**
2. Selezionare **Aggiungi attestazione di gruppi**
3. Selezionare i tipi di gruppo da restituire (**Tutti i gruppi**, **GruppoSecurity**o **RuoloDirectory**). L'opzione **Tutti i gruppi** include **SecurityGroup**, **DirectoryRole**e **DistributionList**
4. Facoltativo: fare clic sulle proprietà del tipo di token specifico per modificare il valore dell'attestazione dei gruppi in modo che contenga gli attributi del gruppo locale o per modificare il tipo di attestazione in un ruoloOptional: click on the specific token type properties to modify the groups claim value to contain on premises group attributes or to change the claim type to a role
5. Fare clic su **Save**.

**Configurazione delle attestazioni facoltative dei gruppi tramite il manifesto dell'applicazione:Configuring groups optional claims through the application manifest:**
1. Accedere al portale di [AzureSign](https://portal.azure.com) in to the Azure portal
1. Dopo aver eseguito l'autenticazione, scegliere il tenant di Azure AD selezionandolo nell'angolo superiore destro della pagina
1. Selezionare **Azure Active Directory** dal menu a sinistra
1. Selezionare l'applicazione per cui si desidera configurare attestazioni facoltative nell'elenco
1. Nella sezione **Gestisci** selezionare **Manifesto**
3. Aggiungere la voce seguente utilizzando l'editor del manifesto:

   I valori validi sono:

   - "All" (questa opzione include SecurityGroup, DirectoryRole e DistributionList)
   - "Gruppo di sicurezza"
   - "RuoloDirectory"

   Ad esempio:

    ```json
        "groupMembershipClaims": "SecurityGroup"
    ```

   Per impostazione predefinita, gli OggettiOggetti gruppo verranno generati nel valore dell'attestazione di gruppo.  Per modificare il valore dell'attestazione in modo che contenga gli attributi del gruppo locale o per modificare il tipo di attestazione in ruolo, usare la configurazione OptionalClaims nel modo seguente:To modify the claim value to contain on premises group attributes, or to change the claim type to role, use OptionalClaims configuration as follows:

3. Impostare attestazioni facoltative di configurazione nome gruppo.

   Se si desidera che i gruppi nel token contengano gli attributi del gruppo di Active Directory locale nella sezione relativa alle attestazioni facoltative, specificare a quale attestazione facoltativa del tipo di token deve essere applicata, il nome dell'attestazione facoltativa richiesta e le eventuali proprietà aggiuntive desiderate.  È possibile elencare più tipi di token:Multiple token types can be listed:

   - idToken per il token ID OIDC
   - accessToken per il token di accesso OAuth
   - Saml2Token per i token SAML.

   > [!NOTE]
   > Il tipo Saml2Token si applica ai token di formato SAML1.1 e SAML2.0

   Per ogni tipo di token pertinente, modificare l'attestazione dei gruppi per usare la sezione OptionalClaims nel manifesto. Lo schema OptionalClaims è il seguente:The OptionalClaims schema is as follows:

    ```json
       {
       "name": "groups",
       "source": null,
       "essential": false,
       "additionalProperties": []
       }
    ```

   | Schema delle attestazioni facoltativoOptional claims schema | valore |
   |----------|-------------|
   | **Nome:** | Devono essere "gruppi" |
   | **fonte:** | Non usato. Omettere o specificare null |
   | **Essenziale:** | Non usato. Omettere o specificare false |
   | **additionalProperties:** | Elenco di proprietà aggiuntive.  Le opzioni valide sono "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   In additionalProperties sono necessari solo uno dei "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Se ne è presente più di uno, viene utilizzato il primo e tutti gli altri vengono ignorati.

   Alcune applicazioni richiedono informazioni di gruppo sull'utente nell'attestazione ruolo.  Per modificare il tipo di attestazione da un'attestazione di gruppo a un'attestazione di ruolo, aggiungere "emit_as_roles" a proprietà aggiuntive.  I valori di gruppo verranno generati nell'attestazione del ruolo.

   > [!NOTE]
   > Se viene utilizzato "emit_as_roles" qualsiasi ruolo applicazione configurato che l'utente è assegnato non verrà visualizzato nell'attestazione di ruolo

**Esempi:**

1) Emettere gruppi come nomi di gruppo nei token di accesso OAuth nel formato dnsDomainName/sAMAccountName

    
    **Configurazione dell'interfaccia utente:**

    [![Viene illustrato come configurare attestazioni facoltative tramite l'interfaccia utente](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Voce del manifesto dell'applicazione:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) Generare i nomi dei gruppi da restituire nel formato netbiosDomain-sAMAccountName come attestazione dei ruoli in SAML e token ID OIDC

    **Configurazione dell'interfaccia utente:**

    [![Viene illustrato come configurare attestazioni facoltative tramite l'interfaccia utente](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

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
-    È possibile utilizzare l'interfaccia utente di **configurazione del token (anteprima)** (vedere l'esempio seguente)You can use the Token configuration (preview) UI (see example below)
-    È possibile utilizzare il **manifesto** (vedere l'esempio seguente). Leggere prima il documento [Informazioni sul manifesto dell'applicazione di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) per un'introduzione al manifesto.
-   È anche possibile scrivere un'applicazione che utilizza [l'API Microsoft Graph](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2F1.0&view=graph-rest-1.0) per aggiornare l'applicazione. Il tipo [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) nella guida di riferimento all'API Microsoft Graph può essere utile per configurare le attestazioni facoltative.

**Esempio:** Nell'esempio seguente si useranno l'interfaccia utente di configurazione del **token (anteprima)** e il **manifesto** per aggiungere attestazioni facoltative ai token di accesso, ID e SAML destinati all'applicazione. A ogni tipo di token che l'applicazione può ricevere verranno aggiunte attestazioni facoltative diverse:Different optional claims will be added to each type of token that the application can receive:
-    I token ID conterranno ora l'UPN per gli utenti federati nel formato esteso (`<upn>_<homedomain>#EXT#@<resourcedomain>`).
-    I token di accesso che altri client richiedono per questa applicazione includeranno ora l'attestazione di auth_time
-    I token SAML conterranno ora l'estensione dello schema della directory skypeId (in questo esempio l'ID per l'app è ab603c56068041afb2f6832e2a17e237). I token SAML esporranno l'ID Skype come `extension_skypeId`.

**Configurazione dell'interfaccia utente:**

1. Accedere al portale di [AzureSign](https://portal.azure.com) in to the Azure portal

1. Dopo aver eseguito l'autenticazione, scegliere il tenant di Azure AD selezionandolo nell'angolo superiore destro della pagina.

1. Selezionare **Azure Active Directory** dal menu a sinistra.

1. Nella sezione **Gestisci** selezionare **Registrazioni app**.

1. Trovare l'applicazione per cui si vogliono configurare le attestazioni facoltative e fare clic sul nome.

1. Nella sezione **Gestisci** fare clic su **Configurazione token (anteprima).**

1. Selezionare **Aggiungi attestazione facoltativa**, selezionare il tipo di token **ID,** selezionare **upn** dall'elenco di attestazioni e quindi fare clic su **Aggiungi**.

1. Selezionare **Aggiungi attestazione facoltativa**, selezionare il tipo di token di **accesso,** selezionare **auth_time** dall'elenco di attestazioni, quindi fare clic su **Aggiungi**.

1. Nella schermata Panoramica configurazione token fare clic sull'icona a matita accanto a **upn**, fare clic sull'interruttore **Autenticazione esternae** e quindi su **Salva**.

1. Selezionare **Aggiungi attestazione facoltativa**, selezionare il tipo di token **SAML,** selezionare **extn.skypeID** dall'elenco di attestazioni (applicabile solo se è stato creato un oggetto utente di Azure AD denominato skypeID) e quindi fare clic su **Aggiungi**.

    [![Viene illustrato come configurare attestazioni facoltative tramite l'interfaccia utente](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Configurazione del manifesto:**
1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Dopo aver eseguito l'autenticazione, scegliere il tenant di Azure AD selezionandolo nell'angolo superiore destro della pagina.
1. Selezionare **Azure Active Directory** dal menu a sinistra.
1. Trovare l'applicazione per cui si vogliono configurare le attestazioni facoltative e fare clic sul nome.
1. Nella sezione **Gestisci** fare clic su **Manifesto** per aprire l'editor del manifesto inline.
1. È possibile modificare direttamente il manifesto usando l'editor. Il manifesto segue lo schema per [l'entità Application](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)e formatta automaticamente il manifesto una volta salvato. Alla proprietà `OptionalClaims` verranno aggiunti nuovi elementi.

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
