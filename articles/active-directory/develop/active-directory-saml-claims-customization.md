---
title: Personalizzare le attestazioni del token SAML per le app aziendali in Azure AD | Microsoft Docs
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
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c0625a233b3b4a949feff2e289361a26fc8dc5a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835350"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Procedura: Personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali

Attualmente, Azure Active Directory (Azure AD) supporta l'accesso Single Sign-on (SSO) con la maggior parte delle applicazioni aziendali, incluse le applicazioni preintegrate nella raccolta di app di Azure AD e applicazioni personalizzate. Quando un utente esegue l'autenticazione in un'applicazione con Azure AD usando il protocollo SAML 2.0, Azure AD invia un token all'applicazione (via HTTP POST). che l'applicazione convalida e usa per l'accesso dell'utente anziché richiedere l'immissione di nome utente e password. Questi token SAML contengono informazioni sull'utente note come *attestazioni*.

Un'*attestazione* è un insieme di informazioni relative ad un utente dichiarate da un provider di identità all'interno del token rilasciato per tale utente. Nel [token SAML](https://en.wikipedia.org/wiki/SAML_2.0) questi dati sono in genere contenuti nell'istruzione degli attributi SAML. L'ID univoco dell'utente viene in genere rappresentato nel soggetto SAML definito anche identificatore del nome.

Per impostazione predefinita, Azure ad rilascia un token SAML all'applicazione che contiene un' `NameIdentifier` attestazione con un valore del nome utente dell'utente (noto anche come nome dell'entità utente) in Azure ad, che può identificare in modo univoco l'utente. Il token SAML contiene inoltre ulteriori attestazioni contenenti indirizzo di posta elettronica, nome e cognome dell'utente.

Per visualizzare o modificare le attestazioni generate nel token SAML per l'applicazione, aprire l'applicazione nel portale di Azure. Aprire quindi la sezione **attributi utente & attestazioni** .

![Aprire la sezione attributi utente & attestazioni nella portale di Azure](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

I due possibili motivi per cui potrebbe essere necessario modificare le attestazioni rilasciate nel token SAML sono i seguenti:

* L'applicazione richiede che `NameIdentifier` l'attestazione o NameID sia un valore diverso dal nome utente (o nome dell'entità utente) archiviato in Azure ad.
* L'applicazione è stata scritta per richiedere un set di URI attestazione o di valori attestazione diverso.

## <a name="editing-nameid"></a>Modifica di NameID

Per modificare il valore di NameID (nome identificatore):

1. Aprire la pagina **valore identificatore nome** .
1. Consente di selezionare l'attributo o la trasformazione che si desidera applicare all'attributo. Facoltativamente, è possibile specificare il formato desiderato per l'attestazione NameID.

   ![Modificare il valore di NameID (nome identificatore)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Formato NameID

Se la richiesta SAML contiene l'elemento NameIDPolicy con un formato specifico, Azure AD rispetterà il formato nella richiesta.

Se la richiesta SAML non contiene un elemento per NameIDPolicy, Azure AD emetterà il NameID con il formato specificato. Se non viene specificato alcun formato Azure AD utilizzerà il formato di origine predefinito associato all'origine dell'attestazione selezionata.

Dall'elenco a discesa **Scegli formato identificatore nome** è possibile selezionare una delle opzioni seguenti.

| Formato NameID | DESCRIZIONE |
|---------------|-------------|
| **Default** | Azure AD utilizzerà il formato di origine predefinito. |
| **Persistente** | Azure AD utilizzerà il formato persistente come NameID. |
| **EmailAddress** | Azure AD utilizzerà EmailAddress come formato NameID. |
| **Unspecified** | Azure AD utilizzerà il formato NameID non specificato. |
| **Transitoria** | Azure AD utilizzerà il formato temporaneo come NameID. |

Per altre informazioni sull'attributo NameIDPolicy, vedere [protocollo SAML per Single Sign-on](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Attributi

Selezionare l'origine desiderata per l'attestazione `NameIdentifier` (o NameID). Si può scegliere fra le opzioni seguenti.

| Name | Descrizione |
|------|-------------|
| Email | Indirizzo di posta elettronica dell'utente |
| userprincipalName | Nome dell'entità utente (UPN) dell'utente |
| onpremisessamaccount | Il nome dell'account SAM che è stato sincronizzato da Azure AD locale |
| objectId | ObjectId dell'utente in Azure AD |
| employeeid | EmployeeID dell'utente |
| Estensioni della directory | Estensioni di directory [sincronizzate da Active Directory locale mediante la sincronizzazione di Azure AD Connect](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Attributi di estensione 1-15 | Attributi di estensione utilizzati per estendere lo schema di Azure AD locale |

Per ulteriori informazioni, vedere [la tabella 3: Valori ID validi per origine](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

### <a name="special-claims---transformations"></a>Attestazioni speciali-trasformazioni

È inoltre possibile utilizzare le funzioni di trasformazioni delle attestazioni.

| Funzione | DESCRIZIONE |
|----------|-------------|
| **ExtractMailPrefix()** | Rimuove il suffisso di dominio dall'indirizzo di posta elettronica o dal nome dell'entità utente. In questo modo viene estratta solo la prima parte del nome utente passata, ad esempio "joe_smith" anziché joe_smith@contoso.com. |
| **Join()** | Aggiunge un attributo a un dominio verificato. Se il valore dell'ID utente selezionato ha un dominio, estrarrà il nome utente per accodare il dominio verificato selezionato. Ad esempio, se si seleziona l'indirizzo e-mail (joe_smith@contoso.com) come valore dell'ID utente e si seleziona contoso.onmicrosoft.com come dominio verificato, si avrà come risultato joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Converte i caratteri dell'attributo selezionato in minuscole. |
| **ToUpper()** | Converte i caratteri dell'attributo selezionato in maiuscole. |

## <a name="adding-application-specific-claims"></a>Aggiunta di attestazioni specifiche dell'applicazione

Per aggiungere attestazioni specifiche dell'applicazione:

1. In **attributi utente & attestazioni**selezionare **Aggiungi nuova attestazione** per aprire la pagina **Gestisci attestazioni utente** .
1. Immettere il **nome** delle attestazioni. Il valore non deve necessariamente seguire un modello di URI, in base alla specifica SAML. Se è necessario un modello URI, è possibile inserirlo nel campo **spazio dei nomi** .
1. Consente di selezionare l' **origine** in cui l'attestazione recupererà il valore. È possibile selezionare un attributo utente dall'elenco a discesa attributo di origine o applicare una trasformazione all'attributo utente prima di emetterlo come attestazione.

### <a name="application-specific-claims---transformations"></a>Attestazioni specifiche dell'applicazione-trasformazioni

È inoltre possibile utilizzare le funzioni di trasformazioni delle attestazioni.

| Funzione | DESCRIZIONE |
|----------|-------------|
| **ExtractMailPrefix()** | Rimuove il suffisso di dominio dall'indirizzo di posta elettronica o dal nome dell'entità utente. In questo modo viene estratta solo la prima parte del nome utente passata, ad esempio "joe_smith" anziché joe_smith@contoso.com. |
| **Join()** | Crea un nuovo valore creando un join tra due attributi. Facoltativamente, è possibile utilizzare un separatore tra i due attributi. |
| **ToLower()** | Converte i caratteri dell'attributo selezionato in minuscole. |
| **ToUpper()** | Converte i caratteri dell'attributo selezionato in maiuscole. |
| **Contains()** | Restituisce un attributo o una costante se l'input corrisponde al valore specificato. In caso contrario, è possibile specificare un altro output se non esiste alcuna corrispondenza.<br/>Ad esempio, se si desidera emettere un'attestazione in cui il valore è l'indirizzo di posta elettronica dell'utente se contiene il@contoso.comdominio ""; in caso contrario, si desidera restituire il nome dell'entità utente. A tale scopo, è necessario configurare i valori seguenti:<br/>*Parametro 1 (input)* : User. email<br/>*Valore*: "@contoso.com"<br/>Parametro 2 (output): User. email<br/>Parametro 3 (output se non esiste alcuna corrispondenza): User. userPrincipalName |
| **EndWith()** | Restituisce un attributo o una costante se l'input termina con il valore specificato. In caso contrario, è possibile specificare un altro output se non esiste alcuna corrispondenza.<br/>Ad esempio, se si desidera emettere un'attestazione in cui il valore è il valore EmployeeID dell'utente se EmployeeID termina con "000"; in caso contrario, si desidera restituire un attributo di estensione. A tale scopo, è necessario configurare i valori seguenti:<br/>*Parametro 1 (input)* : User. EmployeeID<br/>*Value*: 000<br/>Parametro 2 (output): User. EmployeeID<br/>Parametro 3 (output se non esiste alcuna corrispondenza): User. extensionAttribute1 |
| **StartWith()** | Restituisce un attributo o una costante se l'input inizia con il valore specificato. In caso contrario, è possibile specificare un altro output se non esiste alcuna corrispondenza.<br/>Ad esempio, se si desidera emettere un'attestazione in cui il valore è EmployeeID dell'utente se il paese inizia con "US"; in caso contrario, si desidera restituire un attributo di estensione. A tale scopo, è necessario configurare i valori seguenti:<br/>*Parametro 1 (input)* : User. Country<br/>*Value*: NOI<br/>Parametro 2 (output): User. EmployeeID<br/>Parametro 3 (output se non esiste alcuna corrispondenza): User. extensionAttribute1 |
| **Estrai ()-dopo la corrispondenza** | Restituisce la sottostringa dopo che corrisponde al valore specificato.<br/>Se, ad esempio, il valore di input è "Finance_BSimon", il valore corrispondente è "Finance_", l'output dell'attestazione è "BSimon". |
| **Estrai ()-prima della corrispondenza** | Restituisce la sottostringa finché non corrisponde al valore specificato.<br/>Se, ad esempio, il valore di input è "BSimon_US", il valore corrispondente è "US", l'output dell'attestazione è "BSimon". |
| **Estrai ()-tra la corrispondenza** | Restituisce la sottostringa finché non corrisponde al valore specificato.<br/>Se, ad esempio, il valore di input è "Finance_BSimon_US", il primo valore corrispondente è "Finance_", il secondo valore corrispondente è "US", quindi l'output dell'attestazione è "BSimon". |
| **ExtractAlpha() - Prefix** | Restituisce la parte alfabetica del prefisso della stringa.<br/>Se, ad esempio, il valore di input è "BSimon_123", viene restituito "BSimon". |
| **ExtractAlpha() - Suffix** | Restituisce la parte alfabetica del suffisso della stringa.<br/>Se, ad esempio, il valore di input è "123_Simon", viene restituito "Simon". |
| **ExtractNumeric ()-prefisso** | Restituisce la parte numerica del prefisso della stringa.<br/>Se, ad esempio, il valore di input è "123_BSimon", viene restituito "123". |
| **ExtractNumeric ()-suffisso** | Restituisce la parte numerica del suffisso della stringa.<br/>Se, ad esempio, il valore di input è "BSimon_123", viene restituito "123". |
| **IfEmpty()** | Restituisce un attributo o una costante se l'input è null o vuoto.<br/>Ad esempio, se si desidera restituire un attributo archiviato in un ExtensionAttribute se EmployeeID per un determinato utente è vuoto. A tale scopo, è necessario configurare i valori seguenti:<br/>Parametro 1 (input): User. EmployeeID<br/>Parametro 2 (output): User. extensionAttribute1<br/>Parametro 3 (output se non esiste alcuna corrispondenza): User. EmployeeID |
| **IfNotEmpty()** | Restituisce un attributo o una costante se l'input non è null o vuoto.<br/>Ad esempio, se si desidera restituire un attributo archiviato in un ExtensionAttribute se EmployeeID per un determinato utente non è vuoto. A tale scopo, è necessario configurare i valori seguenti:<br/>Parametro 1 (input): User. EmployeeID<br/>Parametro 2 (output): User. extensionAttribute1 |

Se sono necessarie altre trasformazioni, inviare un'idea nel [Forum dei commenti e suggerimenti in Azure ad](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) sotto la categoria di *applicazioni SaaS* .

## <a name="next-steps"></a>Passaggi successivi

* [Gestione di applicazioni in Azure AD](../manage-apps/what-is-application-management.md)
* [Configurare l'accesso Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Risolvere i problemi relativi all'accesso Single Sign-On basato su SAML](howto-v1-debug-saml-sso-issues.md)
