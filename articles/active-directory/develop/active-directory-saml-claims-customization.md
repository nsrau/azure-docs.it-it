---
title: Personalizzazione delle attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure Active Directory | Microsoft Docs
description: Informazioni su come personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure Active Directory
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 73f63300e65e1f6bc25edf7f28ec2769acbb7c9e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="customizing-claims-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Personalizzazione delle attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure Active Directory
Oggi Azure Active Directory supporta l'accesso single sign on con la maggior parte delle applicazioni aziendali comprese le applicazioni pre-integrate nella raccolta di app di Azure AD e le applicazioni personalizzate. Quando un utente esegue l'autenticazione in un'applicazione con Azure AD usando il protocollo SAML 2.0, Azure AD invia un token all'applicazione (via HTTP POST). che l'applicazione convalida e usa per l'accesso dell'utente anziché richiedere l'immissione di nome utente e password. Questi token SAML contengono informazioni sull'utente denominate "attestazioni".

Per quanto riguarda le identità, un'"attestazione" è un insieme di informazioni relative ad un utente dichiarate da un provider di identità all'interno del token rilasciato per tale utente. Nel [token SAML](http://en.wikipedia.org/wiki/SAML_2.0) questi dati sono in genere contenuti nell'istruzione degli attributi SAML. L'ID univoco dell'utente viene in genere rappresentato nel soggetto SAML definito anche identificatore del nome.

Per impostazione predefinita, Azure Active Directory genera per l'applicazione un token SAML che contiene un'attestazione NameIdentifier, il cui valore nome utente, o nome dell'entità utente, è quello dell'utente in Azure AD. Tale valore identifica in modo univoco l'utente. Il token SAML contiene inoltre ulteriori attestazioni contenenti indirizzo di posta elettronica, nome e cognome dell'utente.

Per visualizzare o modificare le attestazioni generate nel token SAML per l'applicazione, aprire l'applicazione nel portale di Azure. Selezionare la casella di controllo **Visualizza e modifica tutti gli altri attributi utente** nella sezione **Attributi utente** dell'applicazione.

![Sezione Attributi utente][1]

I due possibili motivi per cui potrebbe essere necessario modificare le attestazioni rilasciate nel token SAML sono i seguenti:
* L'applicazione è stata scritta per richiedere un set di URI attestazione o di valori attestazione diverso.
* L'applicazione è stata distribuita in modo da richiedere un'attestazione NameIdentifier diversa dal nome utente, o nome dell'entità utente, archiviato in Azure Active Directory.

Per modificare i valori di attestazione predefiniti, selezionare la riga dell'attestazione nella tabella degli attributi del token SAML. Viene visualizzata la sezione **Modifica attributo** dove è possibile modificare il nome e il valore di attestazione nonché lo spazio dei nomi associato all'attestazione.

![Modifica attributo utente][2]

È anche possibile rimuovere le attestazioni (ad eccezione di NameIdentifier) usando il menu di scelta rapida, che si apre facendo clic sull'icona **...**.  Si possono inoltre aggiungere nuove attestazioni usando il pulsante **Aggiungi attributo**.

![Modifica attributo utente][3]

## <a name="editing-the-nameidentifier-claim"></a>Modifica dell'attestazione NameIdentifier
Per risolvere il problema relativo alla distribuzione dell'applicazione con un nome utente diverso, fare clic sull'elenco a discesa **Identificatore utente** nella sezione **Attributi utente**. Verrà visualizzata una finestra di dialogo con diverse opzioni:

![Modifica attributo utente][4]

Nell'elenco a discesa selezionare **user.mail** per impostare l'attestazione NameIdentifier in modo che corrisponda all'indirizzo e-mail dell'utente nella directory. In alternativa, selezionare **user.onpremisessamaccountname** per impostare il nome account SAM dell'utente sincronizzato da Azure AD in locale.

È anche possibile usare la funzione speciale **ExtractMailPrefix()** per rimuovere il suffisso del dominio dall'indirizzo e-mail, dal nome account SAM o dal nome dell'entità utente. In questo modo viene estratta solo la prima parte del nome utente passata, ad esempio "joe_smith" anziché joe_smith@contoso.com.

![Modifica attributo utente][5]

È stata anche aggiunta la funzione **join()** per unire il dominio verificato con il valore dell'identificatore utente. Quando si seleziona la funzione join() in **Identificatore utente** selezionare prima l'identificatore utente come indirizzo e-mail o nome dell'entità utente e selezionare il dominio verificato nel secondo elenco a discesa. Se si seleziona l'indirizzo e-mail con il dominio verificato, AD Azure estrae il nome utente dal primo valore joe_smith di joe_smith@contoso.com e lo aggiunge a contoso.onmicrosoft.com. Vedere l'esempio seguente:

![Modifica attributo utente][6]

## <a name="adding-claims"></a>Aggiunta di attestazioni
Quando si aggiunge un'attestazione, è possibile specificare il nome dell'attributo (che non deve necessariamente seguire un modello di URI secondo la specifica SAML). Impostare il valore su qualsiasi attributo utente archiviato nella directory.

![Aggiungi attributo utente][7]

Se ad esempio è necessario inviare il reparto dell'organizzazione cui appartiene l'utente come attestazione (si supponga il reparto Vendite), Immettere il nome dell'attestazione come previsto dall'applicazione e quindi selezionare **user.department** come valore.

> [!NOTE]
> Se per un determinato utente non è stato archiviato alcun valore per un attributo selezionato, l'attestazione non verrà rilasciata nel token.

> [!TIP]
> **user.onpremisesecurityidentifier** e **user.onpremisesamaccountname** sono supportati solo se si esegue la sincronizzazione dei dati utente da Active Directory in locale usando lo [strumento Azure AD Connect](../active-directory-aadconnect.md).

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
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](../active-directory-apps-index.md)
* [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](../active-directory-saas-custom-apps.md)
* [Risoluzione dei problemi dell'accesso Single Sign-On basato su SAML](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
