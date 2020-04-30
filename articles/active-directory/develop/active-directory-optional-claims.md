---
title: Fornire attestazioni facoltative alle app Azure AD
titleSuffix: Microsoft identity platform
description: Come aggiungere attestazioni personalizzate o aggiuntive ai token SAML 2,0 e JSON Web Tokens (JWT) rilasciati da Azure Active Directory.
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
ms.openlocfilehash: 515ac034158b821968e2d7b2be9514a3f7c20866
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82099078"
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

| Tipo di account               | Token v1.0 | Token v2.0 |
|----------------------------|-------------|-------------|
| Account Microsoft personale | N/D         | Supportato   |
| Account di Azure AD           | Supportato   | Supportato   |

## <a name="v10-and-v20-optional-claims-set"></a>set di attestazioni facoltative v 1.0 e v 2.0

Il set di attestazioni facoltative disponibili per impostazione predefinita per l'uso da parte delle applicazioni è riportato di seguito. Per aggiungere attestazioni facoltative personalizzate per l'applicazione, vedere le [estensioni della directory](#configuring-directory-extension-optional-claims) più avanti in questo articolo. Quando si aggiungono le attestazioni al **token di accesso**, le attestazioni si applicano ai token di accesso richiesti *per* l'applicazione (un'API Web), non alle attestazioni richieste *dall'* applicazione. Indipendentemente dal modo in cui il client accede all'API, i dati corretti sono presenti nel token di accesso usato per l'autenticazione nell'API.

> [!NOTE]
> La maggior parte di queste attestazioni può essere inclusa in token JWT per v1.0 e v2.0, ma non in token SAML, salvo dove diversamente indicato nella colonna Tipo di token. Gli account utente supportano un subset di tali attestazioni, contrassegnato nella colonna "tipo di utente".  Molte delle attestazioni elencate non si applicano agli utenti consumer (non hanno tenant, `tenant_ctry` quindi non ha alcun valore).

**Tabella 2: set di attestazioni facoltativo v 1.0 e v 2.0**

| Name                       |  Descrizione   | Tipo di token | Tipo di utente | Note  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Ora dell'ultima autenticazione dell'utente. Vedere la specifica di OpenID Connect.| Token JSON Web        |           |  |
| `tenant_region_scope`      | Area del tenant della risorsa. | Token JSON Web        |           | |
| `home_oid`                 | Per gli utenti guest, l'ID oggetto dell'utente nel tenant home dell'utente.| Token JSON Web        |           | |
| `sid`                      | ID sessione, usato per la disconnessione utente per sessione. | Token JSON Web        |  Account personali e Azure AD.   |         |
| `platf`                    | Piattaforma per i dispositivi    | Token JSON Web        |           | Limitato ai dispositivi gestiti che possono verificare il tipo di dispositivo.|
| `verified_primary_email`   | Originato da PrimaryAuthoritativeEmail dell'utente.      | Token JSON Web        |           |         |
| `verified_secondary_email` | Originato da SecondaryAuthoritativeEmail dell'utente.   | Token JSON Web        |           |        |
| `enfpolids`                | ID criteri applicati. Elenco degli ID criteri valutati per l'utente corrente. | Token JSON Web |  |  |
| `vnet`                     | Informazioni sull'identificatore di rete virtuale. | Token JSON Web        |           |      |
| `fwd`                      | Indirizzo IP.| Token JSON Web    |   | Aggiunge l'indirizzo IPv4 originale del client richiedente (quando si trova in una rete virtuale). |
| `ctry`                     | Paese dell'utente. | Token JSON Web |  | Azure AD restituisce l'attestazione facoltativa `ctry` se presente e il valore dell'attestazione è un codice paese di due lettere standard, ad esempio FR, JP, SZ e così via. |
| `tenant_ctry`              | Paese del tenant della risorsa. | Token JSON Web | | |
| `xms_pdl`             | Posizione dei dati preferita   | Token JSON Web | | Per i tenant multi-Geo, la posizione dei dati preferita è il codice di tre lettere che mostra l'area geografica in cui si trova l'utente. Per ulteriori informazioni, vedere la [documentazione Azure ad Connect sul percorso dati preferito](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Ad esempio: `APC` per Asia Pacifico. |
| `xms_pl`                   | Lingua preferita dell'utente  | Token JSON Web ||La lingua preferita dell'utente, se impostata. Originato dal proprio tenant principale, negli scenari di accesso guest. Formattato LL-CC ("en-US"). |
| `xms_tpl`                  | Lingua preferita del tenant| Token JSON Web | | La lingua preferita del tenant risorse, se impostata. Formattato LL ("en"). |
| `ztdid`                    | ID distribuzione completamente automatico | Token JSON Web | | L'identità del dispositivo usata per [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Indirizzo di posta elettronica di riferimento, se l'utente ne ha uno.  | JWT, SAML | MSA, Azure AD | Questo valore è incluso per impostazione predefinita se l'utente è un ospite nel tenant.  Per gli utenti gestiti (gli utenti all'interno del tenant), questo deve essere richiesto tramite questa attestazione facoltativa o, solo in versione 2.0, con l'ambito OpenID.  Per gli utenti gestiti, l'indirizzo di posta elettronica deve essere impostato nel [portale di amministrazione di Office](https://portal.office.com/adminportal/home#/users).|
| `groups`| Formattazione facoltativa per le attestazioni di gruppo |JWT, SAML| |Usato in combinazione con l'impostazione GroupMembershipClaims nel [manifesto dell'applicazione](reference-app-manifest.md), che è necessario impostare anche. Per informazioni dettagliate, vedere il [gruppo Claims](#configuring-groups-optional-claims) below. Per ulteriori informazioni sulle attestazioni di gruppo, vedere [How to configure Group Claims](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`                | Stato dell'account degli utenti nel tenant. | JWT, SAML | | Se l'utente è membro del tenant, il valore è `0`. Se si tratta di un utente guest, il valore è `1`. |
| `upn`                      | Attestazione UserPrincipalName. | JWT, SAML  |           | Benché questa attestazione sia inclusa automaticamente, è possibile specificarla come attestazione facoltativa per collegare proprietà aggiuntive in modo da modificarne il comportamento nel caso dell'utente guest.  |

## <a name="v20-specific-optional-claims-set"></a>set di attestazioni facoltative specifiche v 2.0

Queste attestazioni sono sempre incluse nei token di Azure AD v 1.0, ma non sono inclusi nei token v 2.0, a meno che non vengano richiesti. Queste attestazioni sono applicabili solo a token JWT (token ID e token di accesso).

**Tabella 3: attestazioni facoltative solo v 2.0**

| Attestazione JWT     | Name                            | Descrizione                                | Note |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Indirizzo IP                      | Indirizzo IP da cui il client ha effettuato l'accesso.   |       |
| `onprem_sid`  | ID di sicurezza locale |                                             |       |
| `pwd_exp`     | Ora di scadenza della password        | Data e ora in cui scade la password. |       |
| `pwd_url`     | URL per la modifica della password             | URL che l'utente può visitare per cambiare la password.   |   |
| `in_corp`     | All'interno della rete aziendale        | Segnala se il client sta effettuando l'accesso dalla rete aziendale. In caso contrario, l'attestazione non viene inclusa.   |  In base alle impostazioni degli [indirizzi IP attendibili](../authentication/howto-mfa-mfasettings.md#trusted-ips) nell'autenticazione a più fattori.    |
| `nickname`    | Nome alternativo                        | Nome aggiuntivo per l'utente. Il nome alternativo è separato dal primo o dal cognome. Richiede l' `profile` ambito.|
| `family_name` | Cognome                       | Fornisce il cognome, il cognome o il nome della famiglia dell'utente come definito nell'oggetto utente. <br>"family_name":"Miller" | Supportato in MSA e Azure AD. Richiede l' `profile` ambito.   |
| `given_name`  | Nome                      | Fornisce il primo o il nome "specificato" dell'utente, come impostato nell'oggetto utente.<br>"given_name": "Frank"                   | Supportato in MSA e Azure AD.  Richiede l' `profile` ambito. |
| `upn`         | Nome entità utente | Identificatore dell'utente che può essere usato con il parametro username_hint.  Non si tratta di un identificatore permanente per l'utente, pertanto non deve essere usato per inserire dati. | Per la configurazione dell'attestazione, vedere le [proprietà aggiuntive](#additional-properties-of-optional-claims) seguenti. Richiede l' `profile` ambito.|

### <a name="additional-properties-of-optional-claims"></a>Proprietà aggiuntive delle attestazioni facoltative

Alcuni attestazioni facoltative possono essere configurate per modificare il modo in cui che viene restituita l'attestazione. Queste proprietà aggiuntive vengono usate principalmente per facilitare la migrazione di applicazioni locali con previsioni di dati diverse, ad esempio `include_externally_authenticated_upn_without_hash` consente l'uso di client che non possono gestire cancelletti (`#`) nell'UPN.

**Tabella 4: valori per la configurazione di attestazioni facoltative**

| Nome proprietà  | Nome proprietà aggiuntiva | Descrizione |
|----------------|--------------------------|-------------|
| `upn`          |                          | Utilizzabile per le risposte SAML e JWT e per i token v1.0 e v2.0. |
|                | `include_externally_authenticated_upn`  | Include l'UPN guest così come è archiviato nel tenant della risorsa. Ad esempio, usare `foo_hometenant.com#EXT#@resourcetenant.com` |
|                | `include_externally_authenticated_upn_without_hash` | Come sopra, con la differenza che i segni di`#`cancelletto () vengono sostituiti con`_`caratteri di sottolineatura (), ad esempio`foo_hometenant.com_EXT_@resourcetenant.com` |

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

Questo oggetto OptionalClaims fa sì che il token ID restituito al client includa un'attestazione UPN con le informazioni aggiuntive sul tenant della risorsa e sul tenant Home. L' `upn` attestazione viene modificata nel token solo se l'utente è un Guest nel tenant (che usa un IDP diverso per l'autenticazione).

## <a name="configuring-optional-claims"></a>Configurazione di attestazioni facoltative

> [!IMPORTANT]
> I token di accesso vengono **sempre** generati usando il manifesto della risorsa, non il client.  Quindi, nella richiesta `...scope=https://graph.microsoft.com/user.read...` , la risorsa è l'API Microsoft Graph.  Il token di accesso viene quindi creato usando il manifesto dell'API Microsoft Graph, non il manifesto del client.  Se si modifica il manifesto per l'applicazione, i token per l'API Microsoft Graph non verranno mai diversi.  Per verificare che le `accessToken` modifiche siano attive, richiedere un token per l'applicazione, non un'altra app.

È possibile configurare attestazioni facoltative per l'applicazione tramite l'interfaccia utente o il manifesto dell'applicazione.

1. Accedere al [portale di Azure](https://portal.azure.com). Cercare e selezionare **Azure Active Directory**.
1. Nella sezione **Gestisci** selezionare **registrazioni app**.
1. Selezionare nell'elenco l'applicazione per la quale si desidera configurare attestazioni facoltative.

**Configurazione di attestazioni facoltative tramite l'interfaccia utente:**

[![Mostra come configurare attestazioni facoltative tramite l'interfaccia utente](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. Nella sezione **Gestisci** selezionare **configurazione del token**.
1. Selezionare **Aggiungi attestazione facoltativa**.
1. Selezionare il tipo di token che si desidera configurare.
1. Selezionare le attestazioni facoltative da aggiungere.
1. Selezionare **Aggiungi**.

**Configurazione di attestazioni facoltative tramite il manifesto dell'applicazione:**

[![Mostra come configurare attestazioni facoltative usando il manifesto dell'app](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. Nella sezione **Gestisci** selezionare **manifesto**. Si apre un editor manifesto basato sul Web che consente di modificare il manifesto. Facoltativamente è possibile selezionare **Scarica**, modificare il manifesto in locale e quindi usare **Carica** per riapplicarlo all'applicazione. Per ulteriori informazioni sul manifesto dell'applicazione, vedere l' [articolo informazioni sul manifesto dell'applicazione Azure ad](reference-app-manifest.md).

    La seguente voce del manifesto dell'applicazione aggiunge le attestazioni facoltative auth_time, IPADDR e UPN ai token ID, Access e SAML.

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

| Nome          | Type                       | Description                                           |
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

## <a name="configuring-directory-extension-optional-claims"></a>Configurazione delle attestazioni facoltative dell'estensione di directory

Oltre al set di attestazioni facoltative standard, è anche possibile configurare i token per includere le estensioni. Per ulteriori informazioni, vedere [la documentazione di Microsoft Graph extensionProperty](https://docs.microsoft.com/graph/api/resources/extensionproperty?view=graph-rest-1.0).

Le estensioni schema e Open non sono supportate dalle attestazioni facoltative, ma solo dalle estensioni di directory di tipo grafico AAD. Questa funzionalità è utile per il collegamento di altre informazioni sull'utente utilizzabili dall'app, ad esempio un identificatore aggiuntivo o un'opzione di configurazione importante impostata dall'utente. Per un esempio, vedere la parte inferiore di questa pagina.

> [!NOTE]
> Le estensioni dello schema di directory sono una funzionalità di solo Azure AD. Se il manifesto dell'applicazione richiede un'estensione personalizzata e un utente di MSA accede all'app, queste estensioni non vengono restituite.

### <a name="directory-extension-formatting"></a>Formattazione estensione directory

Quando si configurano le attestazioni facoltative dell'estensione di directory usando il manifesto dell'applicazione, usare il nome `extension_<appid>_<attributename>`completo dell'estensione, nel formato:. `<appid>` Deve corrispondere all'ID dell'applicazione che richiede l'attestazione.

Nei token JWT queste attestazioni verranno emesse con il formato del nome seguente: `extn.<attributename>`.

Nei token SAML queste attestazioni verranno emesse con il formato URI seguente: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`.

## <a name="configuring-groups-optional-claims"></a>Configurazione di gruppi attestazioni facoltative

   > [!NOTE]
   > La possibilità di creare nomi di gruppi per gli utenti e i gruppi sincronizzati dall'ambiente locale è un'anteprima pubblica.

In questa sezione vengono illustrate le opzioni di configurazione in attestazioni facoltative per la modifica degli attributi di gruppo utilizzati nelle attestazioni del gruppo dal gruppo predefinito objectID agli attributi sincronizzati da Active Directory Windows locale. È possibile configurare i gruppi attestazioni facoltative per l'applicazione tramite l'interfaccia utente o il manifesto dell'applicazione.

> [!IMPORTANT]
> Per altri dettagli, tra cui importanti avvertenze per l'anteprima pubblica delle attestazioni del gruppo dagli attributi locali, vedere [configurare attestazioni di gruppo per le applicazioni con Azure ad](../hybrid/how-to-connect-fed-group-claims.md).

**Configurazione di gruppi attestazioni facoltative tramite l'interfaccia utente:**

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Dopo aver eseguito l'autenticazione, scegliere il tenant Azure AD selezionandola dall'angolo in alto a destra della pagina
1. Selezionare **Azure Active Directory** dal menu a sinistra.
1. Nella sezione **Gestisci** selezionare **registrazioni app**
1. Selezionare nell'elenco l'applicazione per la quale si desidera configurare le attestazioni facoltative
1. Nella sezione **Gestisci** selezionare **configurazione del token**
1. Selezionare l' **attestazione Aggiungi gruppi**
1. Selezionare i tipi di gruppo da restituire (**tutti i gruppi**, **SecurityGroup**o **DirectoryRole**). L'opzione **tutti i gruppi** include **SecurityGroup**, **DirectoryRole**e **Distribution** .
1. Facoltativo: selezionare le proprietà specifiche del tipo di token per modificare il valore dell'attestazione dei gruppi in modo che contenga gli attributi del gruppo locale o per modificare il tipo di attestazione in un ruolo
1. Selezionare **Salva**

**Configurazione di gruppi attestazioni facoltative tramite il manifesto dell'applicazione:**

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Dopo aver eseguito l'autenticazione, scegliere il tenant Azure AD selezionandola dall'angolo in alto a destra della pagina
1. Selezionare **Azure Active Directory** dal menu a sinistra.
1. Selezionare nell'elenco l'applicazione per la quale si desidera configurare le attestazioni facoltative
1. Nella sezione **Gestisci** selezionare **manifesto**
1. Aggiungere la voce seguente usando l'editor del manifesto:

   I valori validi sono:

   - "All" (questa opzione include SecurityGroup, DirectoryRole e DistributionName)
   - SecurityGroup
   - DirectoryRole

   Ad esempio:

    ```json
    "groupMembershipClaims": "SecurityGroup"
    ```

   Per impostazione predefinita, il gruppo ObjectID verrà emesso nel valore dell'attestazione del gruppo.  Per modificare il valore dell'attestazione in modo che contenga gli attributi del gruppo locale o per modificare il tipo di attestazione in role, usare la configurazione di OptionalClaims come indicato di seguito:

1. Imposta la configurazione del nome del gruppo attestazioni facoltative.

   Se si vuole raggruppare nel token gli attributi di gruppo AD locali nella sezione attestazioni facoltative, specificare a quale tipo di token deve essere applicata l'attestazione facoltativa, il nome dell'attestazione facoltativa richiesta ed eventuali proprietà aggiuntive desiderate.  È possibile elencare più tipi di token:

   - idToken per il token ID OIDC
   - accessToken per il token di accesso OAuth
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

   | Schema delle attestazioni facoltativo | valore |
   |----------|-------------|
   | **nome** | Deve essere "groups" |
   | **origine** | Non usato. Omettere o specificare null |
   | **essenziale** | Non usato. Omettere o specificare false |
   | **AdditionalProperties** | Elenco di proprietà aggiuntive.  Le opzioni valide sono "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

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

2) Creare i nomi dei gruppi da restituire nel formato netbiosDomain\sAMAccountName come attestazione dei ruoli nei token ID SAML e OIDC

    **Configurazione interfaccia utente:**

    [![Mostra come configurare attestazioni facoltative tramite l'interfaccia utente](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

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
Sono disponibili più opzioni per l'aggiornamento delle proprietà di configurazione di un'identità di applicazione per abilitare e configurare le attestazioni facoltative:

- È possibile usare l'interfaccia utente di **configurazione del token** (vedere l'esempio seguente)
- È possibile usare il **manifesto** (vedere l'esempio seguente). Leggere prima il documento [Informazioni sul manifesto dell'applicazione di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) per un'introduzione al manifesto.
- È anche possibile scrivere un'applicazione che usa l' [API Microsoft Graph](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2F1.0&view=graph-rest-1.0) per aggiornare l'applicazione. Il tipo [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) nella Guida di riferimento dell'API Microsoft Graph può essere utile per la configurazione delle attestazioni facoltative.

**Esempio:**

Nell'esempio seguente si userà l'interfaccia utente di **configurazione del token** e il **manifesto** per aggiungere attestazioni facoltative ai token di accesso, ID e SAML destinati all'applicazione. Verranno aggiunte diverse attestazioni facoltative a ogni tipo di token che l'applicazione può ricevere:

- I token ID conterranno ora l'UPN per gli utenti federati nel formato esteso (`<upn>_<homedomain>#EXT#@<resourcedomain>`).
- I token di accesso richiesti da altri client per l'applicazione includeranno ora l'attestazione auth_time
- I token SAML conterranno ora l'estensione dello schema della directory skypeId (in questo esempio l'ID per l'app è ab603c56068041afb2f6832e2a17e237). I token SAML esporranno l'ID Skype come `extension_skypeId`.

**Configurazione interfaccia utente:**

1. Accedere al [portale di Azure](https://portal.azure.com)

1. Dopo aver eseguito l'autenticazione, scegliere il tenant Azure AD selezionandola dall'angolo in alto a destra della pagina.

1. Selezionare **Azure Active Directory** dal menu a sinistra.

1. Nella sezione **Gestisci** selezionare **registrazioni app**.

1. Individuare l'applicazione per cui si desidera configurare le attestazioni facoltative nell'elenco e selezionarla.

1. Nella sezione **Gestisci** selezionare **configurazione del token**.

1. Selezionare **Aggiungi attestazione facoltativa**, selezionare il tipo di token **ID** , selezionare **UPN** nell'elenco di attestazioni e quindi selezionare **Aggiungi**.

1. Selezionare **Aggiungi attestazione facoltativa**, selezionare il tipo di token di **accesso** , selezionare **auth_time** dall'elenco di attestazioni, quindi selezionare **Aggiungi**.

1. Dalla schermata Panoramica della configurazione del token selezionare l'icona della matita accanto a **UPN**, selezionare l'interruttore **autenticato esternamente** e quindi fare clic su **Salva**.

1. Selezionare **Aggiungi attestazione facoltativa**, selezionare il tipo di token **SAML** , selezionare **Extn. skypeID** dall'elenco di attestazioni (applicabile solo se è stato creato un oggetto utente Azure ad denominato skypeID), quindi selezionare **Aggiungi**.

    [![Mostra come configurare attestazioni facoltative tramite l'interfaccia utente](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Configurazione del manifesto:**

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Dopo aver eseguito l'autenticazione, scegliere il tenant Azure AD selezionandola dall'angolo in alto a destra della pagina.
1. Selezionare **Azure Active Directory** dal menu a sinistra.
1. Individuare l'applicazione per cui si desidera configurare le attestazioni facoltative nell'elenco e selezionarla.
1. Nella sezione **Gestisci** selezionare **manifesto** per aprire l'editor manifesto inline.
1. È possibile modificare direttamente il manifesto usando l'editor. Il manifesto segue lo schema per l' [entità applicazione](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)e formatta automaticamente il manifesto dopo essere stato salvato. Alla proprietà `OptionalClaims` verranno aggiunti nuovi elementi.

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

1. Al termine dell'aggiornamento del manifesto, selezionare **Salva** per salvare il manifesto.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle attestazioni standard fornite da Azure AD.

- [Token ID](id-tokens.md)
- [Token di accesso](access-tokens.md)
