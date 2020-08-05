---
title: Personalizzare le attestazioni del token SAML dell'app
titleSuffix: Microsoft identity platform
description: Informazioni su come personalizzare le attestazioni rilasciate dalla piattaforma di identità Microsoft nel token SAML per le applicazioni aziendali.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 10/22/2019
ms.author: kenwith
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: f35e5971374f54940396f602a23ffa0ae3abd015
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552833"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Procedura: Personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali

Microsoft Identity Platform, attualmente, supporta Single Sign-On (SSO) con la maggior parte delle applicazioni aziendali, incluse entrambe le applicazioni preintegrate nella raccolta di app Azure AD e nelle applicazioni personalizzate. Quando un utente esegue l'autenticazione in un'applicazione tramite la piattaforma Microsoft Identity usando il protocollo SAML 2,0, la piattaforma Microsoft Identity Invia un token all'applicazione (tramite un HTTP POST). che l'applicazione convalida e usa per l'accesso dell'utente anziché richiedere l'immissione di nome utente e password. Questi token SAML contengono informazioni sull'utente, note come *attestazioni*.

Un'*attestazione* è un insieme di informazioni relative ad un utente dichiarate da un provider di identità all'interno del token rilasciato per tale utente. Nel [token SAML](https://en.wikipedia.org/wiki/SAML_2.0) questi dati sono in genere contenuti nell'istruzione degli attributi SAML. L'ID univoco dell'utente viene in genere rappresentato nel soggetto SAML definito anche identificatore del nome.

Per impostazione predefinita, Microsoft Identity Platform rilascia un token SAML all'applicazione che contiene un' `NameIdentifier` attestazione con un valore del nome utente dell'utente (noto anche come nome dell'entità utente) in Azure ad, che può identificare in modo univoco l'utente. Il token SAML contiene inoltre ulteriori attestazioni contenenti indirizzo di posta elettronica, nome e cognome dell'utente.

Per visualizzare o modificare le attestazioni generate nel token SAML per l'applicazione, aprire l'applicazione nel portale di Azure. Quindi aprire la sezione **Attributi utente e attestazioni**.

![Aprire la sezione Attributi utente e attestazioni nel portale di Azure](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

I due possibili motivi per cui potrebbe essere necessario modificare le attestazioni rilasciate nel token SAML sono i seguenti:

* L'applicazione richiede che l'attestazione `NameIdentifier` o NameID sia diversa dal nome utente, o dal nome dell'entità utente, archiviato in Azure AD.
* L'applicazione è stata scritta per richiedere un set di URI attestazione o di valori attestazione diverso.

## <a name="editing-nameid"></a>Modifica di NameID

Per modificare NameID (valore dell'identificatore del nome):

1. Aprire la pagina **Valore identificatore nome**.
1. Selezionare l'attributo o la trasformazione da applicarvi. Facoltativamente, è possibile specificare il formato da impostare per l'attestazione NameID.

   ![Modificare il valore di NameID (identificatore del nome)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Formato di NameID

Se la richiesta SAML contiene l'elemento NameIDPolicy con un formato specifico, la piattaforma di identità Microsoft rispetterà il formato nella richiesta.

Se la richiesta SAML non contiene un elemento per NameIDPolicy, la piattaforma Microsoft Identity NameID rilascerà il formato specificato dall'utente. Se non viene specificato alcun formato, Microsoft Identity Platform utilizzerà il formato di origine predefinito associato all'origine dell'attestazione selezionata.

Nell'elenco a discesa **Scegliere il formato per l'identificatore del nome** è possibile selezionare una delle opzioni seguenti.

| Formato di NameID | Descrizione |
|---------------|-------------|
| **Default** | La piattaforma Microsoft Identity utilizzerà il formato di origine predefinito. |
| **Persistente** | La piattaforma Microsoft Identity utilizzerà il formato persistente come NameID. |
| **EmailAddress** | Microsoft Identity Platform utilizzerà EmailAddress come formato NameID. |
| **Non specificato** | La piattaforma Microsoft Identity utilizzerà il formato NameID non specificato. |
| **Nome completo dominio Windows** | La piattaforma Microsoft Identity utilizzerà WindowsDomainQualifiedName come formato NameID. |

È supportato anche un formato temporaneo di NameID, ma non è disponibile nell'elenco a discesa e non può essere configurato sul lato di Azure. Per alter informazioni sull'attributo NameIDPolicy, vedere [Protocollo SAML per Single Sign-On](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Attributes

Selezionare l'origine desiderata per l'attestazione `NameIdentifier` (o NameID). Si può scegliere fra le opzioni seguenti.

| Nome | Descrizione |
|------|-------------|
| Email | Indirizzo di posta elettronica dell'utente |
| userprincipalName | Il nome dell'entità utente (UPN) dell'utente |
| onpremisessamaccount | Il nome dell'account SAM che è stato sincronizzato da Azure AD locale |
| objectid | Il valore objectid dell'utente in Azure AD |
| employeeid | L'ID dipendente dell'utente |
| Estensioni della directory | Estensioni di directory [sincronizzate da Active Directory locale mediante la sincronizzazione di Azure AD Connect](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Attributi di estensione 1-15 | Attributi di estensione utilizzati per estendere lo schema di Azure AD locale |

Per altre informazioni, vedere [Tabella 3: Valori di ID validi per origine](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

È anche possibile assegnare qualsiasi valore costante (statico) a tutte le attestazioni definite in Azure AD. Per assegnare un valore costante, seguire questa procedura:

1. Nella sezione **Attributi utente e attestazioni** del [portale di Azure](https://portal.azure.com/) fare clic sull'icona **Modifica** per modificare le attestazioni.

1. Fare clic sull'attestazione richiesta da modificare.

1. In **Attributo di origine** immettere il valore costante senza virgolette in base ai requisiti dell'organizzazione e fare clic su **Salva**.

    ![Aprire la sezione Attributi utente e attestazioni nel portale di Azure](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. Il valore costante verrà visualizzato come indicato di seguito.

    ![Aprire la sezione Attributi utente e attestazioni nel portale di Azure](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Attestazioni speciali - Trasformazioni

È anche possibile usare le funzioni di trasformazione delle attestazioni.

| Funzione | Descrizione |
|----------|-------------|
| **ExtractMailPrefix()** | Rimuove il suffisso del dominio dall'indirizzo di posta elettronica o dal nome dell'entità utente. In questo modo viene estratta solo la prima parte del nome utente passata, ad esempio "joe_smith" anziché joe_smith@contoso.com. |
| **Join()** | Aggiunge un attributo a un dominio verificato. Se il valore dell'ID utente selezionato ha un dominio, estrarrà il nome utente per accodare il dominio verificato selezionato. Ad esempio, se si seleziona l'indirizzo e-mail (joe_smith@contoso.com) come valore dell'ID utente e si seleziona contoso.onmicrosoft.com come dominio verificato, si avrà come risultato joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Converte i caratteri dell'attributo selezionato in minuscole. |
| **ToUpper()** | Converte i caratteri dell'attributo selezionato in maiuscole. |

## <a name="adding-application-specific-claims"></a>Aggiunta di attestazioni specifiche dell'applicazione

Per aggiungere attestazioni specifiche dell'applicazione:

1. In **Attributi utente e intestazioni** selezionare **Aggiungi nuova attestazione** per aprire la pagina **Gestisci attestazioni utente**.
1. Immettere il **nome** delle attestazioni. Il valore non deve necessariamente seguire un modello di URI, in base alla specifica SAML. Se è necessario un modello di URI, è possibile inserirlo nel campo **Spazio dei nomi**.
1. Selezionare l'opzione di **Origine** da cui l'attestazione recupererà il valore. È possibile selezionare un attributo utente nell'elenco a discesa degli attributi di origine oppure applicare una trasformazione all'attributo utente prima di crearlo come attestazione.

### <a name="claim-transformations"></a>Trasformazioni delle attestazioni

Per applicare una trasformazione a un attributo utente:

1. In **Gestisci l'attestazione**selezionare *Trasformazione* come origine dell'attestazione per aprire la pagina **Gestisci la trasformazione**.
2. Selezionare la funzione nell'elenco a discesa Trasformazione. A seconda della funzione selezionata, sarà necessario specificare i parametri e un valore costante da valutare nella trasformazione. Per altre informazioni sulle funzioni disponibili, vedere la tabella seguente.
3. Per applicare più trasformazioni, fare clic su **Aggiungi trasformazione**. È possibile applicare al massimo due trasformazioni a un'attestazione. È ad esempio possibile estrarre prima il prefisso dell'indirizzo di posta elettronica da `user.mail`. Quindi, impostare la stringa in maiuscolo.

   ![Modificare il valore di NameID (identificatore del nome)](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

Per trasformare le attestazioni, è possibile usare le funzioni seguenti.

| Funzione | Descrizione |
|----------|-------------|
| **ExtractMailPrefix()** | Rimuove il suffisso del dominio dall'indirizzo di posta elettronica o dal nome dell'entità utente. In questo modo viene estratta solo la prima parte del nome utente passata, ad esempio "joe_smith" anziché joe_smith@contoso.com. |
| **Join()** | Crea un nuovo valore creando un join tra due attributi. Facoltativamente, è possibile usare un separatore tra i due attributi. Per la trasformazione dell'attestazione NameID, il join è limitato solo a un dominio verificato. Se il valore dell'ID utente selezionato ha un dominio, estrarrà il nome utente per accodare il dominio verificato selezionato. Ad esempio, se si seleziona l'indirizzo e-mail (joe_smith@contoso.com) come valore dell'ID utente e si seleziona contoso.onmicrosoft.com come dominio verificato, si avrà come risultato joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Converte i caratteri dell'attributo selezionato in minuscole. |
| **ToUpper()** | Converte i caratteri dell'attributo selezionato in maiuscole. |
| **Contains()** | Restituisce un attributo o una costante se l'input corrisponde al valore specificato. In caso contrario, se non esistono corrispondenze, è possibile specificare un altro output.<br/>Ad esempio, è possibile scegliere che venga creata un'attestazione in cui il valore corrisponde all'indirizzo di posta elettronica dell'utente se contiene il dominio "@contoso.com" e in caso contrario che venga restituito il nome dell'entità utente. A questo scopo, configurare i valori seguenti:<br/>*Parametro 1 (input)* : user.email<br/>*Valore*: "@contoso.com"<br/>Parametro 2 (output): user.email<br/>Parametro 3 (output se non esistono corrispondenze): user.userprincipalname |
| **EndWith()** | Restituisce un attributo o una costante se l'input termina con il valore specificato. In caso contrario, se non esistono corrispondenze, è possibile specificare un altro output.<br/>Ad esempio, è possibile scegliere che venga creata un'attestazione in cui il valore corrisponde all'ID dipendente dell'utente se termina con "000" e in caso contrario che venga restituito un attributo di estensione. A questo scopo, configurare i valori seguenti:<br/>*Parametro 1 (input)* : user.employeeid<br/>*Value*: "000"<br/>Parametro 2 (output): user.employeeid<br/>Parametro 3 (output se non esistono corrispondenze): user.extensionattribute1 |
| **StartWith()** | Restituisce un attributo o una costante se l'input inizia con il valore specificato. In caso contrario, se non esistono corrispondenze, è possibile specificare un altro output.<br/>Ad esempio, è possibile scegliere che venga creata un'attestazione in cui il valore corrisponde all'ID dipendente dell'utente se il paese/area geografica inizia con "US" e in caso contrario che venga restituito un attributo di estensione. A questo scopo, configurare i valori seguenti:<br/>*Parametro 1 (input)* : user.country<br/>*Value*: "US"<br/>Parametro 2 (output): user.employeeid<br/>Parametro 3 (output se non esistono corrispondenze): user.extensionattribute1 |
| **Extract() - Dopo la corrispondenza** | Restituisce la sottostringa dopo aver trovato una corrispondenza con il valore specificato.<br/>Ad esempio, se il valore di input è "Finance_BSimon", il valore corrispondente è "Finance_", allora l'output dell'attestazione è "BSimon". |
| **Extract() - Prima della corrispondenza** | Restituisce la sottostringa finché non trova una corrispondenza con il valore specificato.<br/>Ad esempio, se il valore di input è "BSimon_US", il valore corrispondente è "_US", allora l'output dell'attestazione è "BSimon". |
| **Extract() - Tra corrispondenze** | Restituisce la sottostringa finché non trova una corrispondenza con il valore specificato.<br/>Ad esempio, se il valore di input è "Finance_BSimon_US", il primo valore corrispondente è "Finance_", il secondo valore corrispondente è "_US", allora l'output dell'attestazione è "BSimon". |
| **ExtractAlpha() - Prefisso** | Restituisce la parte alfabetica del prefisso della stringa.<br/>Ad esempio, se il valore di input è "BSimon_123", restituisce "BSimon". |
| **ExtractAlpha() - Suffisso** | Restituisce la parte alfabetica del suffisso della stringa.<br/>Ad esempio, se il valore di input è "123_Simon", restituisce "Simon". |
| **ExtractNumeric() - Prefisso** | Restituisce la parte numerica del prefisso della stringa.<br/>Ad esempio, se il valore di input è "123_BSimon", restituisce "123". |
| **ExtractNumeric() - Suffisso** | Restituisce la parte numerica del suffisso della stringa.<br/>Ad esempio, se il valore dell'input è "BSimon_123", restituisce "123". |
| **IfEmpty()** | Restituisce un attributo o una costante se l'input è Null o vuoto.<br/>Ad esempio, si può scegliere che venga restituito un attributo archiviato in extensionattribute se l'ID dipendente di uno specifico utente è vuoto. A questo scopo, configurare i valori seguenti:<br/>Parametro 1 (input): user.employeeid<br/>Parametro 2 (output): user.extensionattribute1<br/>Parametro 3 (output se non esistono corrispondenze): user.employeeid |
| **IfNotEmpty()** | Restituisce un attributo o una costante se l'input non è Null o vuoto.<br/>Ad esempio, si può scegliere che venga restituito un attributo archiviato in extensionattribute se l'ID dipendente di uno specifico utente non è vuoto. A questo scopo, configurare i valori seguenti:<br/>Parametro 1 (input): user.employeeid<br/>Parametro 2 (output): user.extensionattribute1 |

Se sono necessarie altre trasformazioni, inviare il suggerimento al [forum di feedback di Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) nella categoria delle *applicazioni SaaS*.

## <a name="emitting-claims-based-on-conditions"></a>Creazione di attestazioni in base a condizioni

È possibile specificare l'origine di un'attestazione in base al tipo di utente e al gruppo a cui appartiene. 

Il tipo di utente può essere:
- **Qualsiasi**: tutti gli utenti sono autorizzati ad accedere all'applicazione.
- **Membri**: membro nativo del tenant
- **Tutti gli utenti guest**: l'utente proviene da un'organizzazione esterna con o senza Azure AD.
- **Utenti guest di AAD**: l'utente guest appartiene a un'altra organizzazione che usa Azure AD.
- **Utenti guest esterni**: l'utente guest appartiene a un'organizzazione esterna che non ha Azure AD.


Questa opzione è utile, ad esempio, negli scenari in cui l'origine di un'attestazione è diversa per un utente guest e per un dipendente che accedono a un'applicazione. È possibile specificare che, se l'utente è un dipendente, l'origine di NameID deve essere user.email, mentre se è un utente guest deve essere user.extensionattribute1.

Per aggiungere una condizione per l'attestazione:

1. In **Gestisci l'attestazione** espandere Condizioni dell'attestazione.
2. Selezionare il tipo di utente.
3. Selezionare il gruppo o i gruppi a cui l'utente deve appartenere. È possibile selezionare fino a 50 gruppi univoci in tutte le attestazioni per una determinata applicazione. 
4. Selezionare l'opzione di **Origine** da cui l'attestazione recupererà il valore. È possibile selezionare un attributo utente nell'elenco a discesa degli attributi di origine oppure applicare una trasformazione all'attributo utente prima di crearlo come attestazione.

L'ordine in cui si aggiungono le condizioni è importante. Azure AD valuta le condizioni dall'alto verso il basso per decidere quale valore generare nell'attestazione. 

Britta Simon, ad esempio, è un utente guest nel tenant di Contoso. Appartiene a un'altra organizzazione che usa Azure AD. Considerata la configurazione seguente per l'applicazione Fabrikam, quando Britta tenta di accedere a Fabrikam, Microsoft Identity Platform valuterà le condizioni come segue.

Per prima cosa, la piattaforma di identità Microsoft verifica se il tipo di utente di Britta è `All guests` . Poiché questo è vero, la piattaforma Microsoft Identity assegna l'origine per l'attestazione a `user.extensionattribute1` . In secondo luogo, la piattaforma di identità Microsoft verifica se il tipo di utente di Britta è `AAD guests` , poiché questo è vero anche se la piattaforma Microsoft Identity assegna l'origine per l'attestazione a `user.mail` . Infine, l'attestazione viene creata con il valore `user.mail` per Britta.

![Configurazione condizionale delle attestazioni](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Passaggi successivi

* [Gestione di applicazioni in Azure AD](../manage-apps/what-is-application-management.md)
* [Configurare l'accesso Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Risolvere i problemi relativi all'accesso Single Sign-On basato su SAML](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
