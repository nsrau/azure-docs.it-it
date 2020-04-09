---
title: Personalizzare le attestazioni di token SAML dell'app Azure ADCustomize Azure AD app SAML token claims
titleSuffix: Microsoft identity platform
description: Informazioni su come personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: article
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 87a9632ec2433b8698e3ae3761ba733aa6bc63a5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885685"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Procedura: personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendaliHow to: customize claims issued in the SAML token for enterprise applications

Oggi Azure Active Directory (Azure AD) supporta l'accesso Single Sign-On (SSO) con la maggior parte delle applicazioni aziendali, incluse entrambe le applicazioni preintegrate nella raccolta di app di Azure AD e le applicazioni personalizzate. Quando un utente esegue l'autenticazione in un'applicazione con Azure AD usando il protocollo SAML 2.0, Azure AD invia un token all'applicazione (via HTTP POST). che l'applicazione convalida e usa per l'accesso dell'utente anziché richiedere l'immissione di nome utente e password. Questi token SAML contengono informazioni sull'utente noto come *attestazioni*.

Un'*attestazione* è un insieme di informazioni relative ad un utente dichiarate da un provider di identità all'interno del token rilasciato per tale utente. Nel [token SAML](https://en.wikipedia.org/wiki/SAML_2.0) questi dati sono in genere contenuti nell'istruzione degli attributi SAML. L'ID univoco dell'utente viene in genere rappresentato nel soggetto SAML definito anche identificatore del nome.

Per impostazione predefinita, Azure AD invia un `NameIdentifier` token SAML all'applicazione che contiene un'attestazione con un valore del nome utente dell'utente (noto anche come nome dell'entità utente) in Azure AD, che può identificare in modo univoco l'utente. Il token SAML contiene inoltre ulteriori attestazioni contenenti indirizzo di posta elettronica, nome e cognome dell'utente.

Per visualizzare o modificare le attestazioni generate nel token SAML per l'applicazione, aprire l'applicazione nel portale di Azure. Aprire quindi la sezione **Attributi utente & attestazioni.**

![Aprire la sezione Attributi utente & attestazioni nel portale di AzureOpen the User Attributes & Claims section in the Azure portal](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

I due possibili motivi per cui potrebbe essere necessario modificare le attestazioni rilasciate nel token SAML sono i seguenti:

* L'applicazione `NameIdentifier` richiede che l'attestazione o NameID sia diversa dal nome utente (o nome dell'entità utente) archiviato in Azure AD.
* L'applicazione è stata scritta per richiedere un set di URI attestazione o di valori attestazione diverso.

## <a name="editing-nameid"></a>Modifica nameID

Per modificare nameID (valore identificatore nome):

1. Aprire la pagina del **valore Identificatore nome.**
1. Selezionare l'attributo o la trasformazione che si desidera applicare all'attributo. Facoltativamente, è possibile specificare il formato che si desidera assegnare all'attestazione NameID.

   ![Modificare il valore NameID (name identifier)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Formato NameID

Se la richiesta SAML contiene l'elemento NameIDPolicy con un formato specifico, Azure AD rispetterà il formato nella richiesta.

Se la richiesta SAML non contiene un elemento per NameIDPolicy, Azure AD genererà il NameID con il formato specificato. Se non viene specificato alcun formato, Azure AD utilizzerà il formato di origine predefinito associato all'origine attestazione selezionata.

Dall'elenco a discesa **Scegli formato identificatore nome,** è possibile selezionare una delle opzioni seguenti.

| Formato NameID | Descrizione |
|---------------|-------------|
| **Predefinito** | Azure AD utilizzerà il formato di origine predefinito. |
| **Persistente** | Azure AD utilizzerà Persistent come formato NameID. |
| **EmailAddress** | Azure AD utilizzerà EmailAddress come formato NameID.Azure AD will use EmailAddress as the NameID format. |
| **Specificato** | Azure AD utilizzerà Non specificato come formato NameID.Azure AD will use Unspecified as the NameID format. |
| **Nome completo di dominio Windows** | Azure AD utilizzerà WindowsDomainQualifiedName come formato NameID. |

Anche NameID temporaneo è supportato, ma non è disponibile nell'elenco a discesa e non può essere configurato sul lato di Azure.Transient NameID is also supported, but is not available in the dropdown and cannot be configured on Azure's side. Per ulteriori informazioni sull'attributo NameIDPolicy , vedere [Protocollo SAML Single Sign-On](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Attributes

Selezionare l'origine desiderata per l'attestazione `NameIdentifier` (o NameID). Si può scegliere fra le opzioni seguenti.

| Nome | Descrizione |
|------|-------------|
| Email | Indirizzo di posta elettronica dell'utente |
| userprincipalName | Nome dell'entità utente (UPN) dell'utente |
| onpremisessamaccount | Il nome dell'account SAM che è stato sincronizzato da Azure AD locale |
| objectid | Objectid of the user in Azure AD |
| employeeid | ID dipendente dell'utente |
| Estensioni della directory | Estensioni di directory [sincronizzate da Active Directory locale mediante la sincronizzazione di Azure AD Connect](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Attributi di estensione 1-15 | Attributi di estensione utilizzati per estendere lo schema di Azure AD locale |

Per altre info, vedi [Tabella 3: Valori ID validi per origine](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

È anche possibile assegnare qualsiasi valore costante (statico) a qualsiasi attestazione definita in Azure AD. Attenersi alla seguente procedura per assegnare un valore costante:

1. Nel [portale di Azure](https://portal.azure.com/), nella sezione **Attributi utente & attestazioni,** fare clic sull'icona **Modifica** per modificare le attestazioni.

1. Fare clic sul reclamo richiesto che si desidera modificare.

1. Immettere il valore costante senza virgolette **nell'attributo Origine** in base all'organizzazione e fare clic su **Salva**.

    ![Aprire la sezione Attributi utente & attestazioni nel portale di AzureOpen the User Attributes & Claims section in the Azure portal](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. Il valore costante verrà visualizzato come indicato di seguito.

    ![Aprire la sezione Attributi utente & attestazioni nel portale di AzureOpen the User Attributes & Claims section in the Azure portal](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Attestazioni speciali - trasformazioni

È inoltre possibile utilizzare le funzioni di trasformazione delle attestazioni.

| Funzione | Descrizione |
|----------|-------------|
| **ExtractMailPrefix()** | Rimuove il suffisso di dominio dall'indirizzo di posta elettronica o dal nome dell'entità utente. In questo modo viene estratta solo la prima parte del nome utente passata, ad esempio "joe_smith" anziché joe_smith@contoso.com. |
| **Join()** | Aggiunge un attributo a un dominio verificato. Se il valore dell'ID utente selezionato ha un dominio, estrarrà il nome utente per accodare il dominio verificato selezionato. Ad esempio, se si seleziona l'indirizzo e-mail (joe_smith@contoso.com) come valore dell'ID utente e si seleziona contoso.onmicrosoft.com come dominio verificato, si avrà come risultato joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Converte i caratteri dell'attributo selezionato in minuscole. |
| **ToUpper()** | Converte i caratteri dell'attributo selezionato in maiuscole. |

## <a name="adding-application-specific-claims"></a>Aggiunta di attestazioni specifiche dell'applicazioneAdding application-specific claims

Per aggiungere attestazioni specifiche dell'applicazione:

1. In **Attributi utente & Attestazioni**selezionare Aggiungi nuova **attestazione** per aprire la pagina **Gestisci attestazioni utente.**
1. Immettere il **nome** delle attestazioni. Il valore non deve seguire rigorosamente un modello URI, in base alla specifica SAML. Se è necessario un modello URI, è possibile inserirlo nel campo **Spazio dei nomi.**
1. Selezionare **l'origine** in cui l'attestazione sta per recuperare il valore. È possibile selezionare un attributo utente dall'elenco a discesa dell'attributo di origine o applicare una trasformazione all'attributo utente prima di emetterla come attestazione.

### <a name="claim-transformations"></a>Trasformazioni delle attestazioni

Per applicare una trasformazione a un attributo utente:

1. In **Gestisci attestazione**selezionare Trasformazione come origine *attestazione* per aprire la pagina **Gestisci trasformazione.**
2. Selezionare la funzione dall'elenco a discesa della trasformazione. A seconda della funzione selezionata, sarà necessario fornire parametri e un valore costante da valutare nella trasformazione. Fare riferimento alla tabella seguente per ulteriori informazioni sulle funzioni disponibili.
3. Per applicare più trasformazioni, fare clic su **Aggiungi trasformazione**. È possibile applicare un massimo di due trasformazioni a un'attestazione. Ad esempio, è possibile estrarre prima `user.mail`il prefisso di posta elettronica del file . Quindi, rendere la stringa maiuscola.

   ![Modificare il valore NameID (name identifier)](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

È possibile utilizzare le funzioni seguenti per trasformare le attestazioni.

| Funzione | Descrizione |
|----------|-------------|
| **ExtractMailPrefix()** | Rimuove il suffisso di dominio dall'indirizzo di posta elettronica o dal nome dell'entità utente. In questo modo viene estratta solo la prima parte del nome utente passata, ad esempio "joe_smith" anziché joe_smith@contoso.com. |
| **Join()** | Crea un nuovo valore unendo due attributi. Facoltativamente, è possibile utilizzare un separatore tra i due attributi. Per la trasformazione dell'attestazione NameID, l'aggiunta è limitata a un dominio verificato. Se il valore dell'ID utente selezionato ha un dominio, estrarrà il nome utente per accodare il dominio verificato selezionato. Ad esempio, se si seleziona l'indirizzo e-mail (joe_smith@contoso.com) come valore dell'ID utente e si seleziona contoso.onmicrosoft.com come dominio verificato, si avrà come risultato joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Converte i caratteri dell'attributo selezionato in minuscole. |
| **ToUpper()** | Converte i caratteri dell'attributo selezionato in maiuscole. |
| **Contains()** | Restituisce un attributo o una costante se l'input corrisponde al valore specificato. In caso contrario, è possibile specificare un altro output se non vi è alcuna corrispondenza.<br/>Ad esempio, se si desidera generare un'attestazione in cui il valore@contoso.comè l'indirizzo di posta elettronica dell'utente se contiene il dominio " ", altrimenti si desidera restituire il nome dell'entità utente. A tale scopo, è necessario configurare i seguenti valori:<br/>*Parametro 1(input):* user.email<br/>*Valore*:@contoso.com" "<br/>Parametro 2 (output): user.email<br/>Parametro 3 (output se non corrisponde): user.userprincipalname |
| **EndWith()** | Restituisce un attributo o una costante se l'input termina con il valore specificato. In caso contrario, è possibile specificare un altro output se non vi è alcuna corrispondenza.<br/>Ad esempio, se si desidera generare un'attestazione in cui il valore è l'ID dipendente dell'utente se l'ID dipendente termina con "000", in caso contrario si desidera generare un attributo di estensione. A tale scopo, è necessario configurare i seguenti valori:<br/>*Parametro 1(input)*: user.employeeid<br/>*Valore*: "000"<br/>Parametro 2 (output): user.employeeid<br/>Parametro 3 (output se non c'è corrispondenza): user.extensionattribute1 |
| **StartWith()** | Restituisce un attributo o una costante se l'input inizia con il valore specificato. In caso contrario, è possibile specificare un altro output se non vi è alcuna corrispondenza.<br/>Ad esempio, se si desidera generare un'attestazione in cui il valore è l'ID dipendente dell'utente se il paese inizia con "US", in caso contrario si desidera generare un attributo di estensione. A tale scopo, è necessario configurare i seguenti valori:<br/>*Parametro 1(input)*: user.country<br/>*Valore*: "US"<br/>Parametro 2 (output): user.employeeid<br/>Parametro 3 (output se non c'è corrispondenza): user.extensionattribute1 |
| **Extract() - Dopo la corrispondenza** | Restituisce la sottostringa dopo che corrisponde al valore specificato.<br/>Ad esempio, se il valore dell'input è "Finance_BSimon", il valore corrispondente è "Finance_", l'output dell'attestazione è "BSimon". |
| **Extract() - Prima della corrispondenza** | Restituisce la sottostringa finché non corrisponde al valore specificato.<br/>Ad esempio, se il valore dell'input è "BSimon_US", il valore corrispondente è "_US", l'output dell'attestazione è "BSimon". |
| **Extract() - Tra corrispondenza** | Restituisce la sottostringa finché non corrisponde al valore specificato.<br/>Ad esempio, se il valore dell'input è "Finance_BSimon_US", il primo valore corrispondente è "Finance_", il secondo valore corrispondente è "_US", l'output dell'attestazione è "BSimon". |
| **ExtractAlpha() - Prefisso** | Restituisce la parte alfabetica del prefisso della stringa.<br/>Ad esempio, se il valore dell'input è "BSimon_123", restituisce "BSimon". |
| **ExtractAlpha() - Suffisso** | Restituisce la parte alfabetica del suffisso della stringa.<br/>Ad esempio, se il valore dell'input è "123_Simon", restituisce "Simon". |
| **ExtractNumeric() - Prefisso** | Restituisce la parte numerica del prefisso della stringa.<br/>Ad esempio, se il valore dell'input è "123_BSimon", restituisce "123". |
| **ExtractNumeric() - Suffisso** | Restituisce la parte numerica del suffisso della stringa.<br/>Ad esempio, se il valore dell'input è "BSimon_123", restituisce "123". |
| **IfEmpty()** | Restituisce un attributo o una costante se l'input è null o vuoto.<br/>Ad esempio, se si desidera generare un attributo archiviato in un extensionattribute se l'ID dipendente per un determinato utente è vuoto. A tale scopo, è necessario configurare i seguenti valori:<br/>Parametro 1(input): user.employeeid<br/>Parametro 2 (output): user.extensionattribute1<br/>Parametro 3 (output se non c'è corrispondenza): user.employeeid |
| **IfNotEmpty()** | Restituisce un attributo o una costante se l'input non è null o vuoto.<br/>Ad esempio, se si desidera generare un attributo archiviato in un extensionattribute se l'ID dipendente per un determinato utente non è vuoto. A tale scopo, è necessario configurare i seguenti valori:<br/>Parametro 1(input): user.employeeid<br/>Parametro 2 (output): user.extensionattribute1 |

Se sono necessarie trasformazioni aggiuntive, inviare l'idea nel forum di commenti e suggerimenti in Azure AD nella categoria *dell'applicazione SaaS.If* you need additional transformations, submit your idea in the [feedback forum in Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) under the SaaS application category.

## <a name="emitting-claims-based-on-conditions"></a>Emissione di sinistri in base alle condizioni

È possibile specificare l'origine di un'attestazione in base al tipo di utente e al gruppo a cui appartiene l'utente. 

Il tipo di utente può essere:
- **Qualsiasi**: Tutti gli utenti sono autorizzati ad accedere all'applicazione.
- **Membri**: membro nativo del tenant
- **Tutti gli ospiti:** l'utente viene portato da un'organizzazione esterna con o senza Azure AD.
- **Guest AAD:** l'utente guest appartiene a un'altra organizzazione tramite Azure AD.
- **Guest esterni:** l'utente guest appartiene a un'organizzazione esterna che non dispone di Azure AD.


Uno scenario in cui questo è utile è quando l'origine di un'attestazione è diversa per un guest e un dipendente che accede a un'applicazione. È possibile specificare che se l'utente è un dipendente il NameID proviene da user.email, ma se l'utente è un guest, il NameID proviene da user.extensionattribute1.

Per aggiungere una condizione di attestazione:

1. In **Gestisci attestazione**espandere Condizioni attestazione.
2. Selezionare il tipo di utente.
3. Selezionare i gruppi a cui l'utente deve appartenere. È possibile selezionare fino a 10 gruppi univoci in tutte le attestazioni per una determinata applicazione. 
4. Selezionare **l'origine** in cui l'attestazione sta per recuperare il valore. È possibile selezionare un attributo utente dall'elenco a discesa dell'attributo di origine o applicare una trasformazione all'attributo utente prima di emetterla come attestazione.

L'ordine in cui si aggiungono le condizioni è importante. Azure AD valuta le condizioni dall'alto verso il basso per decidere quale valore generare nell'attestazione. 

Ad esempio, Brita Simon è un utente guest nel tenant Contoso. Appartiene a un'altra organizzazione che usa anche Azure AD. Data la configurazione seguente per l'applicazione Fabrikam, quando Brita tenta di accedere a Fabrikam, Azure AD valuterà le condizioni come segue.

In primo luogo, Azure AD verifica se `All guests`il tipo di utente di Brita è . Poiché, questo è vero, Azure AD assegna `user.extensionattribute1`l'origine per l'attestazione a . In secondo luogo, Azure AD verifica se `AAD guests`il tipo di utente di Brita è , `user.mail`poiché anche questo è true, Azure AD assegna l'origine per l'attestazione a . Infine, l'affermazione viene `user.email` emessa con valore per la Brita.

![Configurazione condizionale delle attestazioni](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Passaggi successivi

* [Gestione di applicazioni in Azure AD](../manage-apps/what-is-application-management.md)
* [Configurare l'accesso Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Risolvere i problemi relativi all'accesso Single Sign-On basato su SAML](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
