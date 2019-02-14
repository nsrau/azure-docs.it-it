---
title: Personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure AD | Microsoft Docs
description: Informazioni su come personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2018
ms.author: celested
ms.reviewer: luleon, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9396fbc470f25e3cf6fad883ab525af1f96e96a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188750"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Procedura: Personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali

Oggi Azure Active Directory (Azure AD) supporta l'accesso single sign on con la maggior parte delle applicazioni aziendali comprese le applicazioni pre-integrate nella raccolta di app di Azure AD e le applicazioni personalizzate. Quando un utente esegue l'autenticazione in un'applicazione con Azure AD usando il protocollo SAML 2.0, Azure AD invia un token all'applicazione (via HTTP POST). che l'applicazione convalida e usa per l'accesso dell'utente anziché richiedere l'immissione di nome utente e password. Questi token SAML contengono informazioni sull'utente denominate "attestazioni".

Un'*attestazione* è un insieme di informazioni relative ad un utente dichiarate da un provider di identità all'interno del token rilasciato per tale utente. Nel [token SAML](https://en.wikipedia.org/wiki/SAML_2.0) questi dati sono in genere contenuti nell'istruzione degli attributi SAML. L'ID univoco dell'utente viene in genere rappresentato nel soggetto SAML definito anche identificatore del nome.

Per impostazione predefinita, Azure AD genera per l'applicazione un token SAML che contiene un'attestazione NameIdentifier, il cui valore nome utente, o nome dell'entità utente, è quello dell'utente in Azure AD. Tale valore identifica in modo univoco l'utente. Il token SAML contiene inoltre ulteriori attestazioni contenenti indirizzo di posta elettronica, nome e cognome dell'utente.

Per visualizzare o modificare le attestazioni generate nel token SAML per l'applicazione, aprire l'applicazione nel portale di Azure. Selezionare la casella di controllo **Visualizza e modifica tutti gli altri attributi utente** nella sezione **Attributi utente** dell'applicazione.

![Sezione Attributi utente][1]

I due possibili motivi per cui potrebbe essere necessario modificare le attestazioni rilasciate nel token SAML sono i seguenti:
* L'applicazione è stata scritta per richiedere un set di URI attestazione o di valori attestazione diverso.
* L'applicazione è stata distribuita in modo da richiedere un'attestazione NameIdentifier diversa dal nome utente, o nome dell'entità utente, archiviato in Azure AD.

Per modificare i valori di attestazione predefiniti, selezionare la riga dell'attestazione nella tabella degli attributi del token SAML. Viene visualizzata la sezione **Modifica attributo** dove è possibile modificare il nome e il valore di attestazione nonché lo spazio dei nomi associato all'attestazione.

![Modifica attributo utente][2]

È anche possibile rimuovere le attestazioni (ad eccezione di NameIdentifier) usando il menu di scelta rapida, che si apre facendo clic sull'icona **...**. Si possono inoltre aggiungere nuove attestazioni usando il pulsante **Aggiungi attributo**.

![Modifica attributo utente][3]

## <a name="editing-the-nameidentifier-claim"></a>Modifica dell'attestazione NameIdentifier

Per risolvere il problema relativo alla distribuzione dell'applicazione con un nome utente diverso, selezionare un valore nell'elenco a discesa **Identificatore utente** nella sezione **Attributi utente**. Verrà visualizzata una finestra di dialogo con diverse opzioni:

![Modifica attributo utente][4]

### <a name="attributes"></a>Attributi

Selezionare l'origine desiderata per l'attestazione `NameIdentifier` (o NameID). Si può scegliere fra le opzioni seguenti.

| NOME | DESCRIZIONE |
|------|-------------|
| Email | L'indirizzo e-mail dell'utente |
| userprincipalName | Il nome dell'entità utente (UPN) dell'utente |
| onpremisessamaccount | Il nome dell'account SAM che è stato sincronizzato da Azure AD locale |
| objectID | Il valore objectID dell'utente in Azure AD |
| EmployeeID | Il valore EmployeeID dell'utente |
| Estensioni della directory | Estensioni di directory [sincronizzate da Active Directory locale mediante la sincronizzazione di Azure AD Connect](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Attributi di estensione 1-15 | Attributi di estensione utilizzati per estendere lo schema di Azure AD locale |

### <a name="transformations"></a>Trasformazioni

È possibile anche usare le funzioni speciali di trasformazione delle attestazioni.

| Funzione | DESCRIZIONE |
|----------|-------------|
| **ExtractMailPrefix()** | Rimuove il suffisso del dominio dall'indirizzo e-mail, il nome dell'account SAM o il nome dell'entità utente. In questo modo viene estratta solo la prima parte del nome utente passata, ad esempio "joe_smith" anziché joe_smith@contoso.com. |
| **join()** | Aggiunge un attributo a un dominio verificato. Se il valore dell'ID utente selezionato ha un dominio, estrarrà il nome utente per accodare il dominio verificato selezionato. Ad esempio, se si seleziona l'indirizzo e-mail (joe_smith@contoso.com) come valore dell'ID utente e si seleziona contoso.onmicrosoft.com come dominio verificato, si avrà come risultato joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Converte i caratteri dell'attributo selezionato in minuscole. |
| **ToUpper()** | Converte i caratteri dell'attributo selezionato in maiuscole. |

## <a name="adding-claims"></a>Aggiunta di attestazioni

Quando si aggiunge un'attestazione, è possibile specificare il nome dell'attributo (che non deve necessariamente seguire un modello di URI secondo la specifica SAML). Impostare il valore su qualsiasi attributo utente archiviato nella directory o usare un valore costante come voce statica per tutti gli utenti nell'organizzazione.

![Aggiungi attributo utente][7]

Se ad esempio è necessario inviare il reparto dell'organizzazione cui appartiene l'utente come attestazione (si supponga il reparto Vendite), Immettere il nome dell'attestazione come previsto dall'applicazione e quindi selezionare **user.department** come valore.

> [!NOTE]
> Se per un determinato utente non è stato archiviato alcun valore per un attributo selezionato, l'attestazione non verrà rilasciata nel token.

> [!TIP]
> **user.onpremisesecurityidentifier** e **user.onpremisesamaccountname** sono supportati solo se si esegue la sincronizzazione dei dati utente da Active Directory in locale usando lo [strumento Azure AD Connect](../hybrid/whatis-hybrid-identity.md).

## <a name="restricted-claims"></a>Attestazioni con restrizioni

Esistono alcune attestazioni con restrizioni in SAML. Se si aggiungono queste attestazioni, Azure AD non le invia. Di seguito sono riportate le attestazioni SAML con restrizioni:

    | Tipo di attestazione (URI) |
    | ------------------- |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expired |
    | http://schemas.microsoft.com/identity/claims/accesstoken |
    | http://schemas.microsoft.com/identity/claims/openid2_id |
    | http://schemas.microsoft.com/identity/claims/identityprovider |
    | http://schemas.microsoft.com/identity/claims/objectidentifier |
    | http://schemas.microsoft.com/identity/claims/puid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
    | http://schemas.microsoft.com/identity/claims/tenantid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod |
    | http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groups |
    | http://schemas.microsoft.com/claims/groups.link |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/role |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/wids |
    | http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant |
    | http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown |
    | http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged |
    | http://schemas.microsoft.com/2014/03/psso |
    | http://schemas.microsoft.com/claims/authnmethodsreferences |
    | http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier |
    | http://schemas.microsoft.com/identity/claims/scope |

## <a name="next-steps"></a>Passaggi successivi

* [Gestione di applicazioni in Azure AD](../manage-apps/what-is-application-management.md)
* [Configurare l'accesso Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Risolvere i problemi relativi all'accesso Single Sign-On basato su SAML](howto-v1-debug-saml-sso-issues.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
