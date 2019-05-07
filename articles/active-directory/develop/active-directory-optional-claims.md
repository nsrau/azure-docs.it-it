---
title: Informazioni su come fornire attestazioni facoltative per l'applicazione Azure AD | Microsoft Docs
description: Una guida per l'aggiunta di attestazioni personalizzate o aggiuntive ai token SAML 2.0 e JSON Web (JWT) rilasciati da Azure Active Directory.
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/27/2019
ms.author: celested
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd7b05a5411c03e1324871fbff3c29061ce7b3d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65139234"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Procedura: Fornire attestazioni facoltative per l'app di Azure AD

Questa funzionalità viene usata dagli sviluppatori di applicazioni per specificare le attestazioni da includere nei token inviati all'applicazione. Le attestazioni facoltative possono essere usate per:

- Selezionare attestazioni aggiuntive da includere nei token per l'applicazione.
- Modificare il comportamento di determinate attestazioni che Azure AD restituisce nei token.
- Aggiungere e accedere ad attestazioni personalizzate per l'applicazione.

Per gli elenchi di attestazioni standard, vedere la [token di accesso](access-tokens.md) e [id_token](id-tokens.md) documentazione di attestazioni. 

Attestazioni facoltative sono supportate in sia v1.0 e v2.0 i token di formato, nonché i token SAML, ma offrono la maggior parte del loro valore quando si spostano da v1.0, v2.0. Uno degli obiettivi dell'[endpoint Azure AD v2.0](active-directory-appmodel-v2-overview.md) è quello di ottenere token di dimensioni minori in modo da garantire prestazioni ottimali da parte dei client. Di conseguenza, diverse attestazioni incluse in precedenza nei token ID e di accesso non sono più presenti nei token v2.0 e devono essere richieste espressamente per ogni applicazione.

**Tabella 1: applicabilità**

| Tipo di account | Token v1.0 | Token v2.0  |
|--------------|---------------|----------------|
| Account Microsoft personale  | N/D  | Supportato|
| Account Azure AD      | Supportato | Supportato |

## <a name="v10-and-v20-optional-claims-set"></a>Impostare v1.0 e V2.0 attestazioni facoltative

Il set di attestazioni facoltative disponibili per impostazione predefinita per l'uso da parte delle applicazioni è riportato di seguito. Per aggiungere attestazioni facoltative personalizzate per l'applicazione, vedere le [estensioni della directory](#configuring-directory-extension-optional-claims) più avanti in questo articolo. Quando si aggiungono attestazioni per il **token di accesso**, verranno applicate ai token di accesso richiesto *per* dell'applicazione (un'API web), non quelle *da* l'applicazione. Ciò garantisce che, indipendentemente dal client che accede all'API, il token di accesso usato per l'autenticazione nell'API contenga i dati corretti.

> [!NOTE]
> La maggior parte di queste attestazioni può essere inclusa in token JWT per v1.0 e v2.0, ma non in token SAML, salvo dove diversamente indicato nella colonna Tipo di token. Consumer account supportano un subset di tali attestazioni, contrassegnate nella colonna "Tipo di utente".  Molte delle attestazioni elencate non si applicano agli utenti di consumer (non dispongono di alcun tenant, pertanto `tenant_ctry` non ha alcun valore).  

**Tabella 2: Set di attestazioni di v1.0 e V2.0 facoltativo**

| NOME                       |  DESCRIZIONE   | Tipo di token | Tipo di utente | Note  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Ora dell'ultima autenticazione dell'utente. Vedere la specifica di OpenID Connect.| Token JSON Web        |           |  |
| `tenant_region_scope`      | Area del tenant della risorsa. | Token JSON Web        |           | |
| `home_oid`                 | Per gli utenti guest, l'ID oggetto dell'utente nel tenant home dell'utente.| Token JSON Web        |           | |
| `sid`                      | ID di sessione, usato per l'accesso utente per ogni sessione out. | Token JSON Web        |  Personale e gli account Azure AD.   |         |
| `platf`                    | Piattaforma del dispositivo.    | Token JSON Web        |           | Limitato ai dispositivi gestiti che possono verificare il tipo di dispositivo.|
| `verified_primary_email`   | Originato da PrimaryAuthoritativeEmail dell'utente.      | Token JSON Web        |           |         |
| `verified_secondary_email` | Originato da SecondaryAuthoritativeEmail dell'utente.   | Token JSON Web        |           |        |
| `enfpolids`                | ID criteri applicati. Elenco degli ID criteri valutati per l'utente corrente. | Token JSON Web |  |  |
| `vnet`                     | Informazioni sull'identificatore di rete virtuale. | Token JSON Web        |           |      |
| `fwd`                      | Indirizzo IP.| Token JSON Web    |   | Aggiunge l'indirizzo IPv4 originale del client richiedente (quando si trova in una rete virtuale). |
| `ctry`                     | Paese dell'utente. | Token JSON Web |  | Azure AD restituisce l'attestazione facoltativa `ctry` se presente e il valore dell'attestazione è un codice paese di due lettere standard, ad esempio FR, JP, SZ e così via. |
| `tenant_ctry`              | Paese del tenant della risorsa. | Token JSON Web | | |
| `xms_pdl`          | Posizione dei dati preferita   | Token JSON Web | | Per i tenant di Multi-Geo Capabilities, questo è il codice di 3 lettere che mostra l'area geografica, che l'utente appartiene. Per altre informazioni, vedere la [documentazione di Azure AD Connect sul percorso dati preferito](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Ad esempio: `APC` per Asia Pacifico. |
| `xms_pl`                   | Lingua preferita dell'utente  | Token JSON Web ||La lingua preferita dell'utente, se impostata. Originato dal proprio tenant principale, negli scenari di accesso guest. LL-CC formattato ("en-us"). |
| `xms_tpl`                  | Lingua preferita del tenant| Token JSON Web | | La lingua preferita del tenant risorse, se impostata. LL formattato ("en"). |
| `ztdid`                    | ID distribuzione completamente automatico | Token JSON Web | | L'identità del dispositivo usata per [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Indirizzo di posta elettronica di riferimento, se l'utente ne ha uno.  | JWT, SAML | MSA, AAD | Questo valore è incluso per impostazione predefinita se l'utente è un ospite nel tenant.  Per gli utenti gestiti (quelli all'interno del tenant) deve essere richiesto tramite questa attestazione facoltativa oppure, solo per la versione 2.0, con l'ambito OpenID.  Per gli utenti gestiti, l'indirizzo di posta elettronica deve essere impostato nel [portale di amministrazione di Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Facoltativo di formattazione per le attestazioni di gruppo |JWT, SAML| |Usato in combinazione con l'impostazione GroupMembershipClaims nel [manifesto dell'applicazione](reference-app-manifest.md), che deve essere impostata anche. Per informazioni dettagliate, vedere [attestazioni di gruppo](#Configuring-group-optional claims) sotto. Per altre informazioni sulle attestazioni di gruppo vedere [come configurare le attestazioni di gruppo](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Stato dell'account degli utenti nel tenant. | JWT, SAML | | Se l'utente è membro del tenant, il valore è `0`. Se si tratta di un utente guest, il valore è `1`. |
| `upn`                      | Attestazione UserPrincipalName. | JWT, SAML  |           | Benché questa attestazione sia inclusa automaticamente, è possibile specificarla come attestazione facoltativa per collegare proprietà aggiuntive in modo da modificarne il comportamento nel caso dell'utente guest.  |

### <a name="v20-optional-claims"></a>Attestazioni facoltative v2.0

Queste attestazioni sono sempre incluse nei token di AD Azure v1.0, ma non incluse nei token v2.0 a meno che non richiesto. Queste attestazioni sono applicabili solo per i token Jwt (token ID e token di accesso). 

**Tabella 3: attestazioni facoltative specifiche di V2.0**

| Attestazione JWT     | NOME                            | DESCRIZIONE                                | Note |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Indirizzo IP                      | Indirizzo IP da cui il client ha effettuato l'accesso.   |       |
| `onprem_sid`  | ID di sicurezza locale |                                             |       |
| `pwd_exp`     | Ora di scadenza della password        | Data e ora in cui scade la password. |       |
| `pwd_url`     | URL per la modifica della password             | URL che l'utente può visitare per cambiare la password.   |   |
| `in_corp`     | All'interno della rete aziendale        | Segnala se il client sta effettuando l'accesso dalla rete aziendale. In caso contrario, l'attestazione non è inclusa.   |  In base alle impostazioni degli [indirizzi IP attendibili](../authentication/howto-mfa-mfasettings.md#trusted-ips) nell'autenticazione a più fattori.    |
| `nickname`    | Nome alternativo                        | Nome aggiuntivo per l'utente, distinto dal nome o dal cognome. | 
| `family_name` | Cognome                       | Fornisce l'ultimo nome, cognome o cognome dell'utente, come definito nell'oggetto utente. <br>"family_name":"Miller" | Supportato in AAD e account del servizio gestito   |
| `given_name`  | Nome                      | Fornisce il primo o "base" nome dell'utente, come set nell'oggetto utente.<br>"given_name": "Frank"                   | Supportato in AAD e account del servizio gestito  |
| `upn`         | Nome dell'entità utente | Identificatore dell'utente che può essere usato con il parametro username_hint.  Non si tratta di un identificatore permanente per l'utente, pertanto non deve essere usato per inserire dati. | Per la configurazione dell'attestazione, vedere le [proprietà aggiuntive](#additional-properties-of-optional-claims) seguenti. |

### <a name="additional-properties-of-optional-claims"></a>Proprietà aggiuntive delle attestazioni facoltative

Alcuni attestazioni facoltative possono essere configurate per modificare il modo in cui che viene restituita l'attestazione. Queste proprietà aggiuntive vengono usate principalmente per facilitare la migrazione di applicazioni locali con previsioni di dati diverse, ad esempio `include_externally_authenticated_upn_without_hash` consente l'uso di client che non possono gestire cancelletti (`#`) nell'UPN.

**Tabella 4: valori per la configurazione di attestazioni facoltative**

| Nome proprietà  | Nome proprietà aggiuntiva | DESCRIZIONE |
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

È possibile configurare attestazioni facoltative per l'applicazione modificando il manifesto dell'applicazione (vedere l'esempio riportato di seguito). Per altre informazioni, vedere la [comprendere l'articolo del manifesto dell'applicazione di Azure AD](reference-app-manifest.md).

**Schema di esempio:**

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

### <a name="optionalclaims-type"></a>Tipo OptionalClaims

Dichiara le attestazioni facoltative richieste da un'applicazione. Un'applicazione può configurare le attestazioni facoltative da restituire in ognuno dei tre tipi di token (token ID, token di accesso, token SAML 2) che può ricevere dal servizio token di sicurezza. L'applicazione può configurare un set di attestazioni facoltative diverso da restituire in ogni tipo di token. La proprietà OptionalClaims dell'entità applicazione è un oggetto OptionalClaims.

**Tabella 5: proprietà del tipo OptionalClaims**

| NOME        | Type                       | DESCRIZIONE                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Raccolta (OptionalClaim) | Attestazioni facoltative restituite nel token ID JWT. |
| `accessToken` | Raccolta (OptionalClaim) | Attestazioni facoltative restituite nel token di accesso JWT. |
| `saml2Token`  | Raccolta (OptionalClaim) | Attestazioni facoltative restituite nel token SAML.   |

### <a name="optionalclaim-type"></a>Tipo OptionalClaim

Contiene un'attestazione facoltativa associata a un'applicazione o a un'entità servizio. Le proprietà idToken, accessToken e saml2Token del tipo [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) sono una raccolta di OptionalClaim.
Se supportato da un'attestazione specifica, è inoltre possibile modificare il comportamento di OptionalClaim usando il campo AdditionalProperties.

**Tabella 6: proprietà del tipo OptionalClaim**

| NOME                 | Type                    | DESCRIZIONE                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Nome dell'attestazione facoltativa.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | Origine (oggetto directory) dell'attestazione. Sono presenti attestazioni predefinite e attestazioni definite dall'utente dalla proprietà delle estensioni. Se il valore di origine è Null, l'attestazione è un'attestazione facoltativa predefinita. Se il valore di origine è user, il valore della proprietà name è la proprietà dell'estensione dall'oggetto utente. |
| `essential`            | Edm.Boolean             | Se il valore è True, l'attestazione specificata dal client è necessaria per garantire un'esperienza di autorizzazione uniforme per l'attività specifica richiesta dall'utente finale. Il valore predefinito è False.                                                                                                             |
| `additionalProperties` | Raccolta (Edm.String) | Proprietà aggiuntive dell'attestazione. Se esiste una proprietà in questa raccolta, modificherà il comportamento dell'attestazione facoltativa specificata nella proprietà name.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Configurazione di attestazioni facoltative di estensioni di directory

Oltre al set di attestazioni facoltative standard, è anche possibile configurare i token in modo da includere le estensioni dello schema di directory. Per altre informazioni, vedi [estensioni dello schema di Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). Questa funzionalità è utile per il collegamento di altre informazioni sull'utente utilizzabili dall'app, ad esempio un identificatore aggiuntivo o un'opzione di configurazione importante impostata dall'utente. 

> [!Note]
> Le estensioni dello schema della directory sono una funzionalità esclusiva di AAD, pertanto se il manifesto dell'applicazione richiede un'estensione personalizzata e un utente dell'account del servizio gestito accede all'app, queste estensioni non verranno restituite.

### <a name="directory-extension-formatting"></a>Estensione di directory di formattazione

Per gli attributi dell'estensione, usare il nome completo dell'estensione (nel formato `extension_<appid>_<attributename>`) nel manifesto dell'applicazione. Il `<appid>` deve corrispondere all'ID dell'applicazione che richiede l'attestazione. 

Nei token JWT queste attestazioni verranno emesse con il formato del nome seguente: `extn.<attributename>`.

Nei token SAML queste attestazioni verranno emesse con il formato URI seguente: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`.

## <a name="configuring-group-optional-claims"></a>Configurazione di attestazioni facoltative di gruppo

   > [!NOTE]
   > La possibilità di generare i nomi dei gruppi di utenti e gruppi sincronizzati dall'istanza locale è l'anteprima pubblica

Questa sezione descrive le opzioni di configurazione in attestazioni facoltative per la modifica degli attributi di gruppo usati nelle attestazioni di gruppo dall'objectID del gruppo predefinito di attributi sincronizzati da Active Directory di Windows in locale
> [!IMPORTANT]
> Visualizzare [configurare le attestazioni di gruppo per le applicazioni con Azure Active Directory](../hybrid/how-to-connect-fed-group-claims.md) per altri dettagli, incluse importanti indicazioni per l'anteprima pubblica di attestazioni di gruppo di attributi in locale.

1. Nel portale -> Azure Active Directory -> applicazione le registrazioni -> selezionare applicazione -> manifesto

2. Abilitare le attestazioni di appartenenza al gruppo modificando il groupMembershipClaim

   I valori validi sono:

   - "Tutti"
   - "SecurityGroup"
   - "DistributionList"
   - "DirectoryRole"

   Ad esempio: 

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Per impostazione predefinita che verranno emesse ObjectIDs gruppo nel gruppo valore di attestazione.  Per modificare il valore dell'attestazione per contenere gli attributi di gruppo locale o per modificare il tipo di attestazione ruolo, usare configurazione OptionalClaims come segue:

3. Set di attestazioni facoltative configurazione nome di gruppo.

   Se si desidera gruppi nel token per contenere gli attributi di gruppo AD nella sezione attestazioni facoltative specificano quale attestazione facoltativa di tipo di token deve essere applicato a locale, il nome dell'attestazione facoltativa richiesto ed eventuali proprietà aggiuntive desiderate.  È possibile specificare più tipi di token:

   - idToken per il token ID OIDC
   - per il token di accesso di OAuth/OIDC accessToken
   - Saml2Token per i token SAML.

   > [!NOTE]
   > Il tipo Saml2Token si applica SAML1.1 sia SAML2.0 i token di formato

   Per ogni tipo di token pertinente, modificare l'attestazione relativa ai gruppi per usare la sezione OptionalClaims nel manifesto. Come indicato di seguito è riportato lo schema OptionalClaims:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Schema di attestazioni facoltative | Value |
   |----------|-------------|
   | **Nome:** | Deve essere "groups" |
   | **source:** | Non usato. Omettere o specificare null |
   | **essential:** | Non usato. Omettere oppure specificare false |
   | **additionalProperties:** | Elenco di proprietà aggiuntive.  Le opzioni valide sono "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   In additionalProperties solo uno dei "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" sono obbligatori.  Se è presentano più di uno, viene usato il primo e tutte le altre ignorato.

   Alcune applicazioni richiedono informazioni sul gruppo sull'utente nell'attestazione basata su ruolo.  Per modificare il tipo di attestazione da un gruppo a un'attestazione di ruolo, aggiungere "emit_as_roles" alle proprietà aggiuntive.  I valori del gruppo verranno generati nell'attestazione basata su ruolo.

   > [!NOTE]
   > Se viene usato "emit_as_roles" tutti i ruoli applicazione configurato che l'utente sia assegnata will non visualizzata nell'attestazione basata su ruolo

**Esempi:** Creare i gruppi come i nomi dei gruppi nei token di accesso di OAuth nel formato dnsDomainName\sAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Per generare nomi di gruppo venga restituito nel formato netbiosDomain\sAMAccountName come i ruoli di attestazione nel token ID di OIDC e SAML:

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
 }

 ```

## <a name="optional-claims-example"></a>Esempio di attestazioni facoltative

In questa sezione è riportato uno scenario che mostra come usare la funzionalità delle attestazioni facoltative per l'applicazione.
Sono disponibili più opzioni per l'aggiornamento delle proprietà di configurazione di un'identità di applicazione per abilitare e configurare le attestazioni facoltative:
-   È possibile modificare il manifesto dell'applicazione. L'esempio seguente userà questo metodo per eseguire la configurazione. Leggere prima il documento [Informazioni sul manifesto dell'applicazione di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) per un'introduzione al manifesto.
-   È anche possibile scrivere un'applicazione che usa l'[API Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) per aggiornare l'applicazione. Il [riferimento per entità e tipi complessi](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) nella guida di riferimento sull'API Graph consente di configurare le attestazioni facoltative.

**Esempio:** nell'esempio seguente si modificherà il manifesto di un'applicazione per aggiungere attestazioni ai token di accesso, ID e SAML specifici per l'applicazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Dopo essersi autenticati, scegliere il tenant di Azure AD selezionandolo nell'angolo superiore destro della pagina.
1. Selezionare **Registrazioni per l'app** dal lato sinistro.
1. Trovare l'applicazione per cui si vogliono configurare le attestazioni facoltative e fare clic sul nome.
1. Nella pagina dell'applicazione fare clic su **Manifesto** per aprire l'editor manifesto incorporato. 
1. È possibile modificare direttamente il manifesto usando l'editor. Il manifesto segue lo schema per l'[entità applicazione](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) e viene automaticamente formattato dopo essere stato salvato. Alla proprietà `OptionalClaims` verranno aggiunti nuovi elementi.

    ```json
      "optionalClaims": 
      {
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
      }

    ```

    In questo caso, sono state aggiunte attestazioni facoltative differenti a ogni tipo di token che l'applicazione può ricevere. I token ID conterranno ora l'UPN per gli utenti federati nel formato esteso (`<upn>_<homedomain>#EXT#@<resourcedomain>`). I token di accesso che altri client richiedono per questa applicazione includono ora l'attestazione auth_time. I token SAML conterranno ora l'estensione dello schema della directory skypeId (in questo esempio l'ID per l'app è ab603c56068041afb2f6832e2a17e237). I token SAML esporranno l'ID Skype come `extension_skypeId`.

1. Dopo avere terminato l'aggiornamento del manifesto, fare clic su **Salva** per salvare il manifesto.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle attestazioni standard fornite da Azure AD.

- [Token ID](id-tokens.md)
- [Token di accesso](access-tokens.md)
