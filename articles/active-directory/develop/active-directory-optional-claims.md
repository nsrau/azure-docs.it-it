---
title: Fornire attestazioni facoltative alle app Azure AD
titleSuffix: Microsoft identity platform
description: Come aggiungere attestazioni personalizzate o aggiuntive ai token SAML 2.0 e JSON Web (JWT) rilasciati da Azure Active Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/22/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 59f42f7c1fcdfef29becfb4a046753650ae9d14f
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83737555"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Procedura: Fornire attestazioni facoltative alle app Azure AD

Gli sviluppatori di applicazioni possono usare attestazioni facoltative nelle rispettive applicazioni di Azure AD per specificare quali attestazioni vogliono ottenere nei token inviati all'applicazione.

Le attestazioni facoltative possono essere usate per:

- Selezionare attestazioni aggiuntive da includere nei token per l'applicazione.
- Modificare il comportamento di determinate attestazioni che Azure AD restituisce nei token.
- Aggiungere e accedere ad attestazioni personalizzate per l'applicazione.

Per gli elenchi di attestazioni standard, vedere la documentazione relativa alle attestazioni [token di accesso](access-tokens.md) e [id_token](id-tokens.md).

Mentre le attestazioni facoltative sono supportate sia nei token di formato v1.0 e v2.0, oltre che nei token SAML, l'utilizzo risulta particolarmente vantaggioso passando dalla versione 1.0 alla versione 2.0. Uno degli obiettivi dell'[endpoint Microsoft Identity Platform v2.0](active-directory-appmodel-v2-overview.md) è quello di ottenere token di dimensioni minori in modo da garantire prestazioni ottimali da parte dei client. Di conseguenza, diverse attestazioni incluse in precedenza nei token ID e di accesso non sono più presenti nei token v2.0 e devono essere richieste espressamente per ogni applicazione.

**Tabella 1: applicabilità**

| Tipo di account               | Token v1.0 | Token v2.0 |
|----------------------------|-------------|-------------|
| Account Microsoft personale | N/D         | Supportato   |
| Account Azure AD           | Supportato   | Supportato   |

## <a name="v10-and-v20-optional-claims-set"></a>Set di attestazioni facoltative v1.0 e v2.0

Il set di attestazioni facoltative disponibili per impostazione predefinita per l'uso da parte delle applicazioni è riportato di seguito. Per aggiungere attestazioni facoltative personalizzate per l'applicazione, vedere le [estensioni della directory](#configuring-directory-extension-optional-claims) più avanti in questo articolo. Si noti che quando si aggiungono attestazioni al **token di accesso**, le attestazioni si applicano ai token di accesso richiesti *per* l'applicazione (un'API Web), non alle attestazioni richieste *dall*'applicazione. Indipendentemente dalla modalità di accesso del client all'API, i dati corretti sono presenti nel token di accesso usato per l'autenticazione con l'API.

> [!NOTE]
> La maggior parte di queste attestazioni può essere inclusa in token JWT per v1.0 e v2.0, ma non in token SAML, salvo dove diversamente indicato nella colonna Tipo di token. Gli account personali supportano un subset di tali attestazioni, come indicato nella colonna "Tipo utente".  Molte delle attestazioni elencate non si applicano agli utenti consumer (non hanno tenant, quindi `tenant_ctry` non presenta alcun valore).

**Tabella 2: set di attestazioni facoltative v1.0 e v2.0**

| Nome                       |  Descrizione   | Tipo di token | Tipo di utente | Note  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Ora dell'ultima autenticazione dell'utente. Vedere la specifica di OpenID Connect.| Token JSON Web        |           |  |
| `tenant_region_scope`      | Area del tenant della risorsa. | Token JSON Web        |           | |
| `home_oid`                 | Per gli utenti guest, l'ID oggetto dell'utente nel tenant principale dell'utente.| Token JSON Web        |           | |
| `sid`                      | ID sessione, usato per la disconnessione utente per sessione. | Token JSON Web        |  Account personali e Azure AD.   |         |
| `platf`                    | Piattaforma del dispositivo.    | Token JSON Web        |           | Limitato ai dispositivi gestiti che possono verificare il tipo di dispositivo.|
| `verified_primary_email`   | Originato dall'indirizzo di posta elettronica PrimaryAuthoritativeEmail dell'utente      | Token JSON Web        |           |         |
| `verified_secondary_email` | Originato dall'indirizzo di posta elettronica SecondaryAuthoritativeEmail dell'utente   | Token JSON Web        |           |        |
| `enfpolids`                | ID criteri applicati. Elenco degli ID criteri valutati per l'utente corrente. | Token JSON Web |  |  |
| `vnet`                     | Informazioni sull'identificatore di rete virtuale. | Token JSON Web        |           |      |
| `fwd`                      | Indirizzo IP.| Token JSON Web    |   | Aggiunge l'indirizzo IPv4 originale del client richiedente (quando si trova in una rete virtuale). |
| `ctry`                     | Paese/Area geografica dell'utente | Token JSON Web |  | Azure AD restituisce l'attestazione facoltativa `ctry` se presente e il valore dell'attestazione è un codice paese/area geografica a due lettere standard, ad esempio FR, JP, SZ e così via. |
| `tenant_ctry`              | Paese/Area geografica del tenant della risorsa | Token JSON Web | | |
| `xms_pdl`             | Posizione dei dati preferita   | Token JSON Web | | Per i tenant multi-geografici, la posizione dei dati preferita è il codice a tre lettere che mostra l'area geografica in cui si trova l'utente. Per altre informazioni, vedere la [documentazione di Azure AD Connect sulla posizione dei dati preferita](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Ad esempio: `APC` per Asia Pacifico. |
| `xms_pl`                   | Lingua preferita dell'utente  | Token JSON Web ||Lingua preferita dell'utente, se impostata. Originato dal proprio tenant principale, negli scenari di accesso guest. Nel formato LL-PP ("it-it"). |
| `xms_tpl`                  | Lingua preferita del tenant| Token JSON Web | | Lingua preferita del tenant delle risorse, se impostata. Nel formato LL ("it"). |
| `ztdid`                    | ID distribuzione completamente automatico | Token JSON Web | | L'identità del dispositivo usata per [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Indirizzo di posta elettronica di riferimento, se l'utente ne ha uno.  | JWT, SAML | Account del servizio gestito, Azure AD | Questo valore è incluso per impostazione predefinita se l'utente è un ospite nel tenant.  Per gli utenti gestiti (gli utenti all'interno del tenant) deve essere richiesto tramite questa attestazione facoltativa oppure, solo per la versione 2.0, con l'ambito OpenID.  Per gli utenti gestiti, l'indirizzo di posta elettronica deve essere impostato nel [portale di amministrazione di Office](https://portal.office.com/adminportal/home#/users).|
| `groups`| Formattazione facoltativa per le attestazioni di gruppo |JWT, SAML| |Usato in combinazione con l'impostazione GroupMembershipClaims nel [manifesto dell'applicazione](reference-app-manifest.md), che deve anch'esso essere impostato. Per informazioni dettagliate, vedere [Attestazioni di gruppo](#configuring-groups-optional-claims). Per altre informazioni sulle attestazioni di gruppo, vedere [Come configurare le attestazioni di gruppo](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`                | Stato dell'account degli utenti nel tenant. | JWT, SAML | | Se l'utente è membro del tenant, il valore è `0`. Se si tratta di un utente guest, il valore è `1`. |
| `upn`                      | Attestazione UserPrincipalName. | JWT, SAML  |           | Benché questa attestazione sia inclusa automaticamente, è possibile specificarla come attestazione facoltativa per collegare proprietà aggiuntive in modo da modificarne il comportamento nel caso dell'utente guest.  |

## <a name="v20-specific-optional-claims-set"></a>Set di attestazioni facoltative specifiche per v2.0

Queste attestazioni sono sempre incluse nei token di Azure AD v1.0, ma non sono incluse nei token v2.0 a meno che non sia richiesto. Queste attestazioni sono applicabili solo per i token JWT (token ID e token di accesso).

**Tabella 3: attestazioni facoltative specifiche per v2.0**

| Attestazione JWT     | Nome                            | Descrizione                                | Note |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Indirizzo IP                      | Indirizzo IP da cui il client ha effettuato l'accesso.   |       |
| `onprem_sid`  | ID di sicurezza locale |                                             |       |
| `pwd_exp`     | Ora di scadenza della password        | Data e ora in cui scade la password. |       |
| `pwd_url`     | URL per la modifica della password             | URL che l'utente può visitare per cambiare la password.   |   |
| `in_corp`     | All'interno della rete aziendale        | Segnala se il client sta effettuando l'accesso dalla rete aziendale. In caso contrario, l'attestazione non è inclusa.   |  In base alle impostazioni degli [indirizzi IP attendibili](../authentication/howto-mfa-mfasettings.md#trusted-ips) nell'autenticazione a più fattori.    |
| `nickname`    | Nome alternativo                        | Nome aggiuntivo per l'utente. Il nome alternativo è separato dal nome o dal cognome. Richiede l'ambito `profile`.|
| `family_name` | Cognome                       | Fornisce il cognome dell'utente, come definito nell'oggetto utente. <br>"family_name":"Miller" | Supportato nell'account del servizio gestito e in Azure AD. Richiede l'ambito `profile`.   |
| `given_name`  | Nome                      | Fornisce il nome dell'utente, come impostato nell'oggetto utente.<br>"given_name": "Frank"                   | Supportato nell'account del servizio gestito e in Azure AD.  Richiede l'ambito `profile`. |
| `upn`         | Nome entità utente | Identificatore dell'utente che può essere usato con il parametro username_hint.  Non si tratta di un identificatore permanente per l'utente, pertanto non deve essere usato per inserire dati. | Per la configurazione dell'attestazione, vedere le [proprietà aggiuntive](#additional-properties-of-optional-claims) seguenti. Richiede l'ambito `profile`.|

### <a name="additional-properties-of-optional-claims"></a>Proprietà aggiuntive delle attestazioni facoltative

Alcuni attestazioni facoltative possono essere configurate per modificare il modo in cui che viene restituita l'attestazione. Queste proprietà aggiuntive vengono usate principalmente per facilitare la migrazione di applicazioni locali con previsioni di dati diverse, ad esempio `include_externally_authenticated_upn_without_hash` consente l'uso di client che non possono gestire cancelletti (`#`) nell'UPN.

**Tabella 4: valori per la configurazione di attestazioni facoltative**

| Nome proprietà  | Nome proprietà aggiuntiva | Descrizione |
|----------------|--------------------------|-------------|
| `upn`          |                          | Utilizzabile per le risposte SAML e JWT e per i token v1.0 e v2.0. |
|                | `include_externally_authenticated_upn`  | Include l'UPN guest così come è archiviato nel tenant della risorsa. Ad esempio, usare `foo_hometenant.com#EXT#@resourcetenant.com` |
|                | `include_externally_authenticated_upn_without_hash` | Come sopra, tranne che i cancelletti (`#`) vengono sostituiti da caratteri di sottolineatura (`_`), ad esempio `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Esempio di proprietà aggiuntive

```json
"optionalClaims": {
    "idToken": [
        {
            "name": "upn",
            "essential": false,
            "additionalProperties": [
                "include_externally_authenticated_upn"
            ]
        }
    ]
}
```

Questo oggetto OptionalClaims fa in modo che il token ID restituito al client includa un'attestazione del nome dell'entità utente con informazioni aggiuntive sul tenant principale e sul tenant delle risorse. L'attestazione `upn` viene modificata nel token solo se l'utente è un utente guest nel tenant (che usa un provider di identità diverso per l'autenticazione).

## <a name="configuring-optional-claims"></a>Configurazione di attestazioni facoltative

> [!IMPORTANT]
> I token di accesso sono **sempre** generati usando il manifesto della risorsa, non il client.  Di conseguenza, nella richiesta `...scope=https://graph.microsoft.com/user.read...` la risorsa è l'API Microsoft Graph.  Il token di accesso viene quindi creato usando il manifesto dell'API Microsoft Graph, non il manifesto del client.  La modifica del manifesto per l'applicazione non comporterà una modifica dell'aspetto dei token per l'API Microsoft Graph.  Per verificare che le modifiche apportate a `accessToken` siano state applicate, richiedere un token per l'applicazione, non un'altra app.

È possibile configurare attestazioni facoltative per l'applicazione tramite l'interfaccia utente o il manifesto dell'applicazione.

1. Accedere al [portale di Azure](https://portal.azure.com). Cercare e selezionare **Azure Active Directory**.
1. Nella sezione **Gestisci** selezionare **Registrazioni app**.
1. Selezionare l'applicazione per cui si vogliono configurare le attestazioni facoltative nell'elenco.

**Configurazione di attestazioni facoltative tramite l'interfaccia utente:**

[![Mostra come configurare le attestazioni facoltative tramite l'interfaccia utente](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. Nella sezione **Gestisci** selezionare **Configurazione del token**.
1. Selezionare **Aggiungi un'attestazione facoltativa**.
1. Selezionare il tipo di token da configurare.
1. Selezionare le attestazioni facoltative da aggiungere.
1. Selezionare **Aggiungi**.

**Configurazione di attestazioni facoltative tramite il manifesto dell'applicazione:**

[![Mostra come configurare le attestazioni facoltative tramite il manifesto dell'app](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. Nella sezione **Gestisci** selezionare **Manifesto**. Verrà aperto un editor manifesto basato sul Web che consente di modificare il manifesto. Facoltativamente è possibile selezionare **Scarica**, modificare il manifesto in locale e quindi usare **Carica** per riapplicarlo all'applicazione. Per altre informazioni sul manifesto dell'applicazione, vedere l'articolo [Informazioni sul manifesto dell'applicazione Azure AD](reference-app-manifest.md).

    La voce del manifesto dell'applicazione seguente aggiunge le attestazioni facoltative auth_time, ipaddr e upn ai token ID, di accesso e SAML.

    ```json
    "optionalClaims": {
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

2. Al termine, selezionare **Salva**. A questo punto, le attestazioni facoltative specificate verranno incluse nei token per l'applicazione.

### <a name="optionalclaims-type"></a>Tipo OptionalClaims

Dichiara le attestazioni facoltative richieste da un'applicazione. Un'applicazione può configurare le attestazioni facoltative da restituire in ognuno dei tre tipi di token (token ID, token di accesso, token SAML 2) che può ricevere dal servizio token di sicurezza. L'applicazione può configurare un set di attestazioni facoltative diverso da restituire in ogni tipo di token. La proprietà OptionalClaims dell'entità applicazione è un oggetto OptionalClaims.

**Tabella 5: proprietà del tipo OptionalClaims**

| Nome          | Type                       | Descrizione                                           |
|---------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Raccolta (OptionalClaim) | Attestazioni facoltative restituite nel token ID JWT.     |
| `accessToken` | Raccolta (OptionalClaim) | Attestazioni facoltative restituite nel token di accesso JWT. |
| `saml2Token`  | Raccolta (OptionalClaim) | Attestazioni facoltative restituite nel token SAML.       |

### <a name="optionalclaim-type"></a>Tipo OptionalClaim

Contiene un'attestazione facoltativa associata a un'applicazione o a un'entità servizio. Le proprietà idToken, accessToken e saml2Token del tipo [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) sono una raccolta di OptionalClaim.
Se supportato da un'attestazione specifica, è inoltre possibile modificare il comportamento di OptionalClaim usando il campo AdditionalProperties.

**Tabella 6: proprietà del tipo OptionalClaim**

| Nome                   | Type                    | Descrizione                                                                                                                                                                                                                                                                                                   |
|------------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Nome dell'attestazione facoltativa.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | Origine (oggetto directory) dell'attestazione. Sono presenti attestazioni predefinite e attestazioni definite dall'utente dalla proprietà delle estensioni. Se il valore di origine è Null, l'attestazione è un'attestazione facoltativa predefinita. Se il valore di origine è user, il valore della proprietà name è la proprietà dell'estensione dall'oggetto utente. |
| `essential`            | Edm.Boolean             | Se il valore è True, l'attestazione specificata dal client è necessaria per garantire un'esperienza di autorizzazione uniforme per l'attività specifica richiesta dall'utente finale. Il valore predefinito è false.                                                                                                                 |
| `additionalProperties` | Raccolta (Edm.String) | Proprietà aggiuntive dell'attestazione. Se esiste una proprietà in questa raccolta, modificherà il comportamento dell'attestazione facoltativa specificata nella proprietà name.                                                                                                                                                   |

## <a name="configuring-directory-extension-optional-claims"></a>Configurazione delle attestazioni facoltative dell'estensione della directory

Oltre al set di attestazioni facoltative standard, è anche possibile configurare i token per includere le estensioni. Per altre informazioni, vedere la [documentazione di extensionProperty di Microsoft Graph](https://docs.microsoft.com/graph/api/resources/extensionproperty?view=graph-rest-1.0).

Le estensioni aperte e dello schema non sono supportate dalle attestazioni facoltative, ma solo dalle estensioni della directory di tipo AAD-Graph. Questa funzionalità è utile per il collegamento di altre informazioni sull'utente utilizzabili dall'app, ad esempio un identificatore aggiuntivo o un'opzione di configurazione importante impostata dall'utente. Per un esempio, vedere la parte inferiore di questa pagina.

> [!NOTE]
> Le estensioni dello schema della directory sono una funzionalità esclusiva di Azure AD. Se il manifesto dell'applicazione richiede un'estensione personalizzata e un utente con account del servizio gestito accede all'app, queste estensioni non verranno restituite.

### <a name="directory-extension-formatting"></a>Formattazione dell'estensione della directory

Quando si configurano le attestazioni facoltative dell'estensione della directory usando il manifesto dell'applicazione, usare il nome completo dell'estensione, nel formato: `extension_<appid>_<attributename>`. `<appid>` deve corrispondere all'ID dell'applicazione che richiede l'attestazione.

Nei token JWT queste attestazioni verranno emesse con il formato del nome seguente: `extn.<attributename>`.

Nei token SAML queste attestazioni verranno emesse con il formato URI seguente: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`.

## <a name="configuring-groups-optional-claims"></a>Configurazione di attestazioni di gruppo facoltative

   > [!NOTE]
   > La funzionalità di creazione di nomi di gruppo per gli utenti e i gruppi sincronizzati dall'ambiente locale è in anteprima pubblica.

In questa sezione vengono illustrate le opzioni di configurazione delle attestazioni facoltative per la modifica degli attributi di gruppo usati nelle attestazioni di gruppo dal gruppo predefinito objectID agli attributi sincronizzati da Windows Active Directory locale. È possibile configurare attestazioni facoltative dei gruppi per l'applicazione tramite l'interfaccia utente o il manifesto dell'applicazione.

> [!IMPORTANT]
> Per informazioni dettagliate, ad esempio importanti avvertenze per l'anteprima pubblica delle attestazioni di gruppo dagli attributi locali, vedere [Configurare attestazioni di gruppo per le applicazioni con Azure AD](../hybrid/how-to-connect-fed-group-claims.md).

**Configurazione di attestazioni facoltative dei gruppi tramite l'interfaccia utente:**

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Dopo aver completato l'autenticazione, scegliere il tenant di Azure AD selezionandolo nell'angolo superiore destro della pagina
1. Selezionare **Azure Active Directory** dal menu a sinistra
1. Nella sezione **Gestisci** selezionare **Registrazioni app**
1. Selezionare l'applicazione per cui si vogliono configurare le attestazioni facoltative nell'elenco
1. Nella sezione **Gestisci** selezionare **Configurazione del token**
1. Selezionare **Aggiungi un'attestazione basata su gruppi**
1. Selezionare i tipi di gruppo da restituire (**Tutti i gruppi**, **SecurityGroup** o **DirectoryRole**). L'opzione **Tutti i gruppi** include **SecurityGroup**, **DirectoryRole** e **DistributionList**
1. Facoltativo: selezionare le proprietà specifiche del tipo di token per modificare il valore dell'attestazione dei gruppi in modo che contenga gli attributi del gruppo locale o per modificare il tipo di attestazione in un ruolo
1. Selezionare **Salva**

**Configurazione di attestazioni facoltative dei gruppi tramite il manifesto dell'applicazione:**

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Dopo aver completato l'autenticazione, scegliere il tenant di Azure AD selezionandolo nell'angolo superiore destro della pagina
1. Selezionare **Azure Active Directory** dal menu a sinistra
1. Selezionare l'applicazione per cui si vogliono configurare le attestazioni facoltative nell'elenco
1. Nella sezione **Gestisci** selezionare **Manifesto**
1. Aggiungere la voce seguente usando l'editor del manifesto:

   I valori validi sono:

   - "Tutti" (questa opzione include SecurityGroup, DirectoryRole e DistributionList)
   - "SecurityGroup"
   - "DirectoryRole"

   Ad esempio:

    ```json
    "groupMembershipClaims": "SecurityGroup"
    ```

   Per impostazione predefinita, verrà restituito il gruppo ObjectID nel valore dell'attestazione del gruppo.  Per modificare il valore dell'attestazione in modo che contenga gli attributi del gruppo locale o per modificare il tipo di attestazione in un ruolo, usare la configurazione OptionalClaims come indicato di seguito:

1. Impostare le attestazioni facoltative della configurazione del nome del gruppo.

   Affinché i gruppi nel token contengano gli attributi del gruppo Active Directory locale, nella sezione delle attestazioni facoltative specificare a quale tipo di token deve essere applicata l'attestazione facoltativa, il nome dell'attestazione facoltativa richiesta ed eventuali proprietà aggiuntive desiderate.  È possibile elencare più tipi di token:

   - idToken per il token ID OIDC
   - accessToken per il token di accesso OAuth
   - Saml2Token per i token SAML.

   > [!NOTE]
   > Il tipo Saml2Token si applica ai token nel formato SAML1.1 e SAML2.0

   Per ogni tipo di token pertinente, modificare l'attestazione dei gruppi affinché usi la sezione OptionalClaims nel manifesto. Lo schema OptionalClaims è il seguente:

    ```json
    {
        "name": "groups",
        "source": null,
        "essential": false,
        "additionalProperties": []
    }
    ```

   | Schema delle attestazioni facoltative | valore |
   |----------|-------------|
   | **name:** | Deve essere "groups" |
   | **source:** | Non usato. Omettere o specificare null |
   | **essential:** | Non usato. Omettere o specificare false |
   | **additionalProperties:** | Elenco di proprietà aggiuntive.  Le opzioni valide sono "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   In additionalProperties è necessario specificare solo una tra le proprietà "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Se sono presenti più proprietà, viene usata la prima e tutte le altre vengono ignorate.

   Per alcune applicazioni sono necessarie informazioni sul gruppo relative all'utente nell'attestazione del ruolo.  Per modificare il tipo di attestazione da un'attestazione di gruppo a un'attestazione di ruolo, aggiungere "emit_as_roles" alle proprietà aggiuntive.  I valori del gruppo verranno restituiti nell'attestazione del ruolo.

   > [!NOTE]
   > Se viene usata la proprietà "emit_as_roles", i ruoli applicazione configurati a cui l'utente è assegnato non verranno visualizzati nell'attestazione del ruolo

**Esempi:**

1) Restituire i gruppi come nomi di gruppo nei token di accesso OAuth nel formato dnsDomainName\sAMAccountName

    **Configurazione dell'interfaccia utente:**

    [![Mostra come configurare le attestazioni facoltative tramite l'interfaccia utente](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)

    **Voce del manifesto dell'applicazione:**

    ```json
    "optionalClaims": {
        "accessToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "dns_domain_and_sam_account_name"
                ]
            }
        ]
    }
    ```

2) Restituire i nomi dei gruppi nel formato netbiosDomain\sAMAccountName come attestazione dei ruoli nei token ID OIDC e SAML

    **Configurazione dell'interfaccia utente:**

    [![Mostra come configurare le attestazioni facoltative tramite l'interfaccia utente](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Voce del manifesto dell'applicazione:**

    ```json
    "optionalClaims": {
        "saml2Token": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ],
        "idToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ]
    }
    ```

## <a name="optional-claims-example"></a>Esempio di attestazioni facoltative

In questa sezione è riportato uno scenario che mostra come usare la funzionalità delle attestazioni facoltative per l'applicazione.
Sono disponibili più opzioni per l'aggiornamento delle proprietà in una configurazione di identità dell'applicazione per abilitare e configurare le attestazioni facoltative:

- È possibile usare l'interfaccia utente di **Configurazione del token** (vedere l'esempio sotto)
- È possibile usare il **Manifesto** (vedere l'esempio sotto). Leggere prima il documento [Informazioni sul manifesto dell'applicazione di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) per un'introduzione al manifesto.
- È anche possibile scrivere un'applicazione che usa l'[API Microsoft Graph](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2F1.0&view=graph-rest-1.0) per aggiornare l'applicazione. Il tipo [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) nella guida di riferimento sull'API Microsoft Graph consente di configurare le attestazioni facoltative.

**Esempio:**

Nell'esempio seguente si userà l'interfaccia utente di **Configurazione del token** e il **Manifesto** per aggiungere attestazioni facoltative ai token di accesso, ID e SAML destinati all'applicazione. Verranno aggiunte attestazioni facoltative diverse a ogni tipo di token che l'applicazione può ricevere:

- I token ID conterranno ora l'UPN per gli utenti federati nel formato esteso (`<upn>_<homedomain>#EXT#@<resourcedomain>`).
- I token di accesso che altri client richiedono per questa applicazione includono ora l'attestazione auth_time
- I token SAML conterranno ora l'estensione dello schema della directory skypeId (in questo esempio l'ID per l'app è ab603c56068041afb2f6832e2a17e237). I token SAML esporranno l'ID Skype come `extension_skypeId`.

**Configurazione dell'interfaccia utente:**

1. Accedere al [portale di Azure](https://portal.azure.com)

1. Dopo aver completato l'autenticazione, scegliere il tenant di Azure AD selezionandolo nell'angolo superiore destro della pagina.

1. Selezionare **Azure Active Directory** dal menu a sinistra.

1. Nella sezione **Gestisci** selezionare **Registrazioni app**.

1. Trovare l'applicazione per cui si vogliono configurare le attestazioni facoltative nell'elenco e selezionarla.

1. Nella sezione **Gestisci** selezionare **Configurazione del token**.

1. Selezionare **Aggiungi un'attestazione facoltativa**, selezionare il tipo di token **ID**, selezionare **upn** nell'elenco di attestazioni e infine fare clic su **Aggiungi**.

1. Selezionare **Aggiungi un'attestazione facoltativa**, selezionare il tipo di token **Accesso**, selezionare **auth_time** nell'elenco di attestazioni e infine fare clic su **Aggiungi**.

1. Nella schermata della panoramica di Configurazione del token selezionare l'icona della matita accanto a **upn**, selezionare l'interruttore **Con autenticazione esterna** e infine fare clic su **Salva**.

1. Selezionare **Aggiungi un'attestazione facoltativa**, selezionare il tipo di token **SAML**, selezionare **extn.skypeID** dall'elenco di attestazioni (applicabile solo se è stato creato un oggetto utente Azure AD denominato skypeID) e infine fare clic su **Aggiungi**.

    [![Mostra come configurare le attestazioni facoltative tramite l'interfaccia utente](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Configurazione del manifesto:**

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Dopo aver completato l'autenticazione, scegliere il tenant di Azure AD selezionandolo nell'angolo superiore destro della pagina.
1. Selezionare **Azure Active Directory** dal menu a sinistra.
1. Trovare l'applicazione per cui si vogliono configurare le attestazioni facoltative nell'elenco e selezionarla.
1. Nella sezione **Gestisci** selezionare **Manifesto** per aprire l'editor manifesto incorporato.
1. È possibile modificare direttamente il manifesto usando l'editor. Il manifesto segue lo schema per l'[entità applicazione](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) e viene automaticamente formattato dopo essere stato salvato. Alla proprietà `OptionalClaims` verranno aggiunti nuovi elementi.

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "upn",
                "essential": false,
                "additionalProperties": [
                    "include_externally_authenticated_upn"
                ]
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
    }
    ```

1. Dopo avere terminato l'aggiornamento del manifesto, fare clic su **Salva** per salvare il manifesto.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle attestazioni standard fornite da Azure AD.

- [Token ID](id-tokens.md)
- [Token di accesso](access-tokens.md)
