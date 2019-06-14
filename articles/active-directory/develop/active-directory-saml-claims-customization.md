---
title: Personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure AD | Microsoft Docs
description: Informazioni su come personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 622525705979cd6a7a088c606ac167d28f8f6482
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65951009"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Procedura: Personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali

Attualmente, Azure Active Directory (Azure AD) supporta single sign-on (SSO) con la maggior parte delle applicazioni aziendali, comprese le applicazioni preintegrate nella raccolta di app di Azure AD, nonché di applicazioni personalizzate. Quando un utente esegue l'autenticazione in un'applicazione con Azure AD usando il protocollo SAML 2.0, Azure AD invia un token all'applicazione (via HTTP POST). che l'applicazione convalida e usa per l'accesso dell'utente anziché richiedere l'immissione di nome utente e password. Questi token SAML contengono informazioni sull'utente denominate *attestazioni*.

Un'*attestazione* è un insieme di informazioni relative ad un utente dichiarate da un provider di identità all'interno del token rilasciato per tale utente. Nel [token SAML](https://en.wikipedia.org/wiki/SAML_2.0) questi dati sono in genere contenuti nell'istruzione degli attributi SAML. L'ID univoco dell'utente viene in genere rappresentato nel soggetto SAML definito anche identificatore del nome.

Per impostazione predefinita, Azure AD rilascia un token SAML all'applicazione contenente un `NameIdentifier` un'attestazione con un valore del nome utente dell'utente (noto anche come il nome dell'entità utente) in Azure AD, che identifica in modo univoco l'utente. Il token SAML contiene inoltre ulteriori attestazioni contenenti indirizzo di posta elettronica, nome e cognome dell'utente.

Per visualizzare o modificare le attestazioni generate nel token SAML per l'applicazione, aprire l'applicazione nel portale di Azure. Quindi aprire il **attributi utente e le attestazioni** sezione.

![Sezione attributi utente e le attestazioni](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

I due possibili motivi per cui potrebbe essere necessario modificare le attestazioni rilasciate nel token SAML sono i seguenti:

* L'applicazione richiede il `NameIdentifier` o NameID dichiarano di essere qualcosa di diverso dal nome utente (o nome dell'entità utente) archiviato in Azure AD.
* L'applicazione è stata scritta per richiedere un set di URI attestazione o di valori attestazione diverso.

## <a name="editing-nameid"></a>Modifica di NameID

Per modificare l'elemento NameID (valore dell'identificatore nome):

1. Aprire il **denominare il valore dell'ID** pagina.
1. Selezionare l'attributo o la trasformazione da applicare all'attributo. Facoltativamente, è possibile specificare il formato desiderato l'attestazione NameID disporre.

   ![Modificare il valore di NameID (identificatore nome)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Formato NameID

Se la richiesta SAML contiene l'elemento NameIDPolicy con un formato specifico, Azure AD rispetta il formato nella richiesta.

Se la richiesta SAML non contiene un elemento per NameIDPolicy, Azure AD rilascerà l'elemento NameID con il formato specificato. Se viene specificato alcun formato Azure AD Usa il formato di origine predefinito associato all'origine di attestazione selezionato.

Dal **formato identificatore nome scegliere** elenco a discesa, è possibile selezionare una delle opzioni seguenti.

| Formato NameID | Descrizione |
|---------------|-------------|
| **Default** | Azure AD Usa il formato di origine predefinito. |
| **Persistente** | Azure AD userà persistente come formato di NameID. |
| **EmailAddress** | Azure AD userà EmailAddress come formato di NameID. |
| **non è specificato** | Azure AD userà non specificato come formato di NameID. |
| **Temporaneo** | Azure AD userà temporaneo come formato di NameID. |

Per altre informazioni sull'attributo NameIDPolicy, vedere [protocollo SAML per Single Sign-On](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Attributi

Selezionare l'origine desiderata per l'attestazione `NameIdentifier` (o NameID). Si può scegliere fra le opzioni seguenti.

| NOME | Descrizione |
|------|-------------|
| Email | Indirizzo di posta elettronica dell'utente |
| userprincipalName | Nome dell'entità utente (UPN) dell'utente |
| onpremisessamaccount | Il nome dell'account SAM che è stato sincronizzato da Azure AD locale |
| objectId | ObjectID dell'utente in Azure AD |
| employeeid | valore EmployeeID dell'utente |
| Estensioni della directory | Estensioni di directory [sincronizzate da Active Directory locale mediante la sincronizzazione di Azure AD Connect](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Attributi di estensione 1-15 | Attributi di estensione utilizzati per estendere lo schema di Azure AD locale |

Per altre informazioni, vedere [tabella 3: I valori di ID validi per ogni origine](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

### <a name="special-claims---transformations"></a>Attestazioni speciali - trasformazioni

È anche possibile usare le funzioni di trasformazioni di attestazioni.

| Funzione | Descrizione |
|----------|-------------|
| **ExtractMailPrefix()** | Rimuove il suffisso del dominio dall'indirizzo di posta elettronica o il nome dell'entità utente. In questo modo viene estratta solo la prima parte del nome utente passata, ad esempio "joe_smith" anziché joe_smith@contoso.com. |
| **Join()** | Aggiunge un attributo a un dominio verificato. Se il valore dell'ID utente selezionato ha un dominio, estrarrà il nome utente per accodare il dominio verificato selezionato. Ad esempio, se si seleziona l'indirizzo e-mail (joe_smith@contoso.com) come valore dell'ID utente e si seleziona contoso.onmicrosoft.com come dominio verificato, si avrà come risultato joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Converte i caratteri dell'attributo selezionato in minuscole. |
| **ToUpper()** | Converte i caratteri dell'attributo selezionato in maiuscole. |

## <a name="adding-application-specific-claims"></a>Aggiunta di attestazioni specifiche dell'applicazione

Per aggiungere attestazioni specifiche dell'applicazione:

1. Nelle **attributi utente e le attestazioni**, selezionare **Aggiungi nuova attestazione** per aprire il **gestire attestazioni utente** pagina.
1. Immettere il **nome** delle attestazioni. Il valore non deve necessariamente seguire un modello URI, per la specifica SAML. Se è necessario un modello di URI, è possibile inserire che nel **Namespace** campo.
1. Selezionare il **origine** in cui l'attestazione verrà per recuperare il relativo valore. È possibile selezionare un attributo utente nel menu a discesa dell'attributo di origine o applicare una trasformazione per l'attributo utente prima di emettere, come attestazione.

### <a name="application-specific-claims---transformations"></a>Attestazioni specifiche dell'applicazione - trasformazioni

È anche possibile usare le funzioni di trasformazioni di attestazioni.

| Funzione | Descrizione |
|----------|-------------|
| **ExtractMailPrefix()** | Rimuove il suffisso del dominio dall'indirizzo di posta elettronica o il nome dell'entità utente. In questo modo viene estratta solo la prima parte del nome utente passata, ad esempio "joe_smith" anziché joe_smith@contoso.com. |
| **Join()** | Crea un nuovo valore unendo in join due attributi. Facoltativamente, è possibile usare un separatore tra i due attributi. |
| **ToLower()** | Converte i caratteri dell'attributo selezionato in minuscole. |
| **ToUpper()** | Converte i caratteri dell'attributo selezionato in maiuscole. |
| **Contains()** | Se l'input corrisponde al valore specificato, genera un attributo o una costante. In caso contrario, è possibile specificare un altro output se non esiste alcuna corrispondenza.<br/>Ad esempio, se si desidera generare un'attestazione in cui il valore è l'indirizzo di posta elettronica dell'utente, se contiene il dominio "@contoso.com", in caso contrario, si desidera restituire il nome dell'entità utente. A tale scopo, configurare i valori seguenti:<br/>*Parametro 1(input)* : User. email<br/>*Valore*: "@contoso.com"<br/>Il parametro 2 (output): User. email<br/>Parametro 3 (se non esiste alcuna corrispondenza di output): User. userPrincipalName |
| **EndWith()** | Se l'input termina con il valore specificato, genera un attributo o una costante. In caso contrario, è possibile specificare un altro output se non esiste alcuna corrispondenza.<br/>Ad esempio, se si desidera generare un'attestazione in cui il valore è employeeid dell'utente se il valore employeeid termina con "000", in caso contrario, si desidera restituire un attributo di estensione. A tale scopo, configurare i valori seguenti:<br/>*Parametro 1(input)* : User. EmployeeID<br/>*Value*: "000"<br/>Il parametro 2 (output): User. EmployeeID<br/>Parametro 3 (se non esiste alcuna corrispondenza di output): user.extensionattribute1 |
| **StartWith()** | Se l'input inizia con il valore specificato, genera un attributo o una costante. In caso contrario, è possibile specificare un altro output se non esiste alcuna corrispondenza.<br/>Ad esempio, se si desidera generare un'attestazione in cui il valore è employeeid dell'utente se il paese/regione inizia con "US", in caso contrario, si desidera restituire un attributo di estensione. A tale scopo, configurare i valori seguenti:<br/>*Parametro 1(input)* : User. Country<br/>*Value*: "US"<br/>Il parametro 2 (output): User. EmployeeID<br/>Parametro 3 (se non esiste alcuna corrispondenza di output): user.extensionattribute1 |
| **Extract () - dopo la corrispondenza** | Restituisce la sottostringa dopo che corrisponde al valore specificato.<br/>Ad esempio, se il valore dell'input è "Finance_BSimon", il valore corrisponda è "Finance_", quindi l'output dell'attestazione è "BSimon". |
| **Extract () - prima corrispondenza** | Restituisce la sottostringa fino a quando non corrisponde a quello specificato.<br/>Ad esempio, se il valore dell'input è "BSimon_US", il valore corrisponda è "_US", quindi l'output dell'attestazione è "BSimon". |
| **Extract () - tra corrispondenza** | Restituisce la sottostringa fino a quando non corrisponde a quello specificato.<br/>Ad esempio, se il valore dell'input è "Finance_BSimon_US", il primo valore corrisponda è "Finance_", il secondo valore corrisponda è "_US", quindi l'output dell'attestazione è "BSimon". |
| **ExtractAlpha() - Prefix** | Restituisce la parte in ordine alfabetico di prefisso della stringa.<br/>Ad esempio, se il valore dell'input è "BSimon_123", viene restituito "BSimon". |
| **ExtractAlpha() - Suffix** | Restituisce la parte alfabetico suffisso della stringa.<br/>Ad esempio, se il valore dell'input è "123_Simon", viene restituito "Simon". |
| **ExtractNumeric() - Prefix** | Restituisce la parte numerica di prefisso della stringa.<br/>Ad esempio, se il valore dell'input è "123_BSimon", viene restituito "123". |
| **ExtractNumeric() - Suffix** | Restituisce la parte numerica suffisso della stringa.<br/>Ad esempio, se il valore dell'input è "BSimon_123", viene restituito "123". |
| **IfEmpty()** | Restituisce un attributo o una costante se l'input è null o vuoto.<br/>Ad esempio, se si desidera restituire un attributo archiviato in un extensionattribute se il valore employeeid per un determinato utente è vuoto. A tale scopo, configurare i valori seguenti:<br/>Parametro 1(input): User. EmployeeID<br/>Il parametro 2 (output): user.extensionattribute1<br/>Parametro 3 (se non esiste alcuna corrispondenza di output): User. EmployeeID |
| **IfNotEmpty()** | Restituisce un attributo o una costante se l'input non è null o vuoto.<br/>Ad esempio, se si desidera restituire un attributo archiviato in un extensionattribute se il valore employeeid per un determinato utente non è vuoto. A tale scopo, configurare i valori seguenti:<br/>Parametro 1(input): User. EmployeeID<br/>Il parametro 2 (output): user.extensionattribute1 |

Se è necessario ulteriori trasformazioni, invia la tua idea nel [forum dei commenti di Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) sotto il *applicazione SaaS* categoria.

## <a name="next-steps"></a>Passaggi successivi

* [Gestione di applicazioni in Azure AD](../manage-apps/what-is-application-management.md)
* [Configurare l'accesso Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Risolvere i problemi relativi all'accesso Single Sign-On basato su SAML](howto-v1-debug-saml-sso-issues.md)
