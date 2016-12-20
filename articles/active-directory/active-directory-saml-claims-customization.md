---
title: Personalizzazione delle attestazioni rilasciate nel token SAML per le applicazioni preintegrate in Azure Active Directory | Documentazione Microsoft
description: Informazioni su come personalizzare le attestazioni rilasciate nel token SAML per le app preintegrate in Azure Active Directory
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 6aa7d045e2472fdf80817b364e76368ef6b053bf


---
# <a name="customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps-in-azure-active-directory"></a>Personalizzazione delle attestazioni rilasciate nel token SAML per le applicazioni preintegrate in Azure Active Directory
Oggi Azure Active Directory supporta migliaia di applicazioni preintegrate nella raccolta della applicazioni di Azure AD, tra cui oltre 150 che supportano l’accesso single sign-on tramite il protocollo SAML 2.0. Quando un utente effettua l’autenticazione in un'applicazione tramite Azure AD utilizzando SAML, Azure AD invia un token all'applicazione (tramite un reindirizzamento HTTP 302) che l'applicazione convalida e utilizza per l'accesso dell'utente anziché richiedere l'immissione di nome utente e password. Questi token SAML contengono informazioni sull'utente note come "attestazioni".

Per quanto riguarda le identità, un’"attestazione" è un’insieme di informazioni relative ad un utente dichiarate da un provider di identità all'interno del token rilasciato per tale utente. In un [token SAML](http://en.wikipedia.org/wiki/SAML_2.0), questi dati in genere sono contenuti nell'istruzione degli attributi del SAML e l'ID univoco dell'utente viene generalmente rappresentato nel soggetto del SAML.

Per impostazione predefinita, Azure AD emette per l’applicazione un token SAML che contiene un'attestazione NameIdentifier, con un valore nome utente dell'utente in Azure AD (questo valore identifica in modo univoco l'utente). Il token SAML contiene inoltre ulteriori attestazioni contenenti indirizzo di posta elettronica, nome e cognome dell’utente.

Per visualizzare o modificare le attestazioni rilasciate nel token SAML per l'applicazione, aprire il record dell'applicazione nel portale di gestione di Azure e selezionare la scheda **Attributi** sotto l'applicazione.

![][1]

Esistono due possibili motivi per i quali potrebbe essere necessario modificare le attestazioni rilasciate nel token SAML: •L’applicazione è stata scritta in modo da richiedere un diverso set di URI dell’attestazione o valori dell’attestazione •L’applicazione è stata distribuita in modo da richiedere un’attestazione NameIdentifier diversa dal nome utente (nome principale utente AKA) archiviato in Azure Active Directory. 

È possibile modificare qualsiasi valore predefinito dell’attestazione selezionando l'icona a forma di matita visualizzata a destra, ogni volta che si sposta il mouse su una delle righe della tabella degli attributi token SAML. È possibile anche rimuovere attestazioni (ad eccezione di NameIdentifier) usando l'icona **X** e aggiungere nuove attestazioni tramite il pulsante **Aggiungi attributo utente**.

## <a name="editing-the-nameidentifier-claim"></a>Modifica dell'attestazione NameIdentifier
Per risolvere il problema relativo alla distribuzione dell'applicazione con un nome utente diverso, fare clic sull'icona a forma di matita accanto all'attestazione NameIdentifier. Si apre una finestra di dialogo con diverse opzioni:

![][2]

Nel menu **Valore attributo** selezionare **user.mail** per impostare l'attestazione NameIdentifier in modo che corrisponda all'indirizzo di posta elettronica dell'utente nella directory o selezionare **user.onpremisessamaccountname** per impostare il nome utente dell'account SAM che è stato sincronizzato da Azure AD in locale. 

È anche possibile usare la funzione specifica ExtractMailPrefix() per rimuovere il suffisso del dominio dall'indirizzo di posta elettronica o dal nome dell'entità utente in modo che venga passata solo la prima parte del nome utente (ad esempio, "joesmith" invece di joesmith@contoso.com).

![][3]

## <a name="adding-claims"></a>Aggiunta di attestazioni
Quando si aggiunge una nuova attestazione, è possibile specificare il nome dell'attributo (che non deve necessariamente seguire un modello di URI secondo la specifica SAML), nonché impostare il valore su qualsiasi attributo utente archiviato nella directory.

![][4]

Ad esempio, se è necessario inviare il reparto dell’organizzazione a cui appartiene l'utente come attestazione (ad esempio vendite), è possibile immettere qualsiasi valore di attestazione previsto dall'applicazione e selezionare **user.department** come valore.

Se per un determinato utente non è stato archiviato alcun valore per un attributo selezionato, l’attestazione non verrà rilasciata nel token.

**Nota:** **user.onpremisesecurityidentifier** e **user.onpremisesamaccountname** sono supportati soltanto quando si effettua la sincronizzazione dei dati utente da Active Directory in locale usando lo strumento [Azure AD Connect](active-directory-aadconnect.md).

## <a name="related-articles"></a>Articoli correlati
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](active-directory-saas-custom-apps.md)
* [Risoluzione dei problemi dell'accesso Single Sign-On basato su SAML](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ./media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ./media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ./media/active-directory-saml-claims-customization/claimscustomization4.png



<!--HONumber=Nov16_HO3-->


