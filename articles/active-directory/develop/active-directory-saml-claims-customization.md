---
title: Personalizzazione delle attestazioni rilasciate nel token SAML per le applicazioni preintegrate in Azure Active Directory | Microsoft Docs
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
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: e89a06de6232afef579c32d51137ddf577917436
ms.contentlocale: it-it
ms.lasthandoff: 04/27/2017


---
# <a name="customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps-in-azure-active-directory"></a>Personalizzazione delle attestazioni rilasciate nel token SAML per le applicazioni preintegrate in Azure Active Directory
Oggi Azure Active Directory supporta migliaia di applicazioni preintegrate nella raccolta della applicazioni di Azure AD, tra cui oltre 150 che supportano l'accesso single sign-on tramite il protocollo SAML 2.0. Quando un utente esegue l'autenticazione in un'applicazione tramite Azure AD usando il protocollo SAML, Azure AD invia un token all'applicazione (tramite un reindirizzamento HTTP 302) che l'applicazione convalida e usa per l'accesso dell'utente anziché richiedere l'immissione di nome utente e password. Questi token SAML contengono informazioni sull'utente denominate "attestazioni".

Per quanto riguarda le identità, un'"attestazione" è un insieme di informazioni relative ad un utente dichiarate da un provider di identità all'interno del token rilasciato per tale utente. In un [token SAML](http://en.wikipedia.org/wiki/SAML_2.0), questi dati in genere sono contenuti nell'istruzione degli attributi del SAML e l'ID univoco dell'utente viene generalmente rappresentato nel soggetto del SAML.

Per impostazione predefinita, Azure AD emette per l'applicazione un token SAML che contiene un'attestazione NameIdentifier, il cui valore nome utente è quello dell'utente in Azure AD. Tale valore identifica in modo univoco l'utente. Il token SAML contiene inoltre ulteriori attestazioni contenenti indirizzo di posta elettronica, nome e cognome dell'utente.

Per visualizzare o modificare le attestazioni rilasciate nel token SAML per l'applicazione, aprire il record dell'applicazione nel portale di Azure classico e selezionare la scheda **Attributi** sotto l'applicazione.

![Scheda attributi][1]

I due possibili motivi per cui potrebbe essere necessario modificare le attestazioni rilasciate nel token SAML sono i seguenti:
* L'applicazione è stata scritta per richiedere un set di URI attestazione o di valori attestazione diverso 
* L'applicazione è stata distribuita in modo da richiedere un'attestazione NameIdentifier diversa dal nome utente (nome dell'entità utente AKA) archiviato in Azure Active Directory. 

Per modificare i valori di attestazione predefiniti, selezionare l'icona a forma di matita visualizzata a destra ogni volta che si sposta il mouse su una riga della tabella degli attributi token SAML. È possibile anche rimuovere attestazioni (ad eccezione di NameIdentifier) usando l'icona **X** e aggiungere nuove attestazioni tramite il pulsante **Aggiungi attributo utente**.

## <a name="editing-the-nameidentifier-claim"></a>Modifica dell'attestazione NameIdentifier
Per risolvere il problema relativo alla distribuzione dell'applicazione con un nome utente diverso, fare clic sull'icona a forma di matita accanto all'attestazione NameIdentifier. Verrà visualizzata una finestra di dialogo con diverse opzioni:

![Modifica attributo utente][2]

Nel menu **Valore attributo** selezionare **user.mail** per impostare l'attestazione NameIdentifier in modo che corrisponda all'indirizzo di posta elettronica dell'utente nella directory. In alternativa, selezionare **user.onpremisessamaccountname** per impostare il nome account SAM dell'utente sincronizzato da Azure AD in locale.

È anche possibile usare la funzione speciale ExtractMailPrefix() per rimuovere il suffisso del dominio dall'indirizzo di posta elettronica o dal nome dell'entità utente in modo che venga passata solo la prima parte del nome utente (ad esempio "joe_smith" invece di joe_smith@contoso.com).

![Modifica attributo utente][3]

## <a name="adding-claims"></a>Aggiunta di attestazioni
Quando si aggiunge un'attestazione, è possibile specificare il nome dell'attributo (che non deve necessariamente seguire un modello di URI secondo la specifica SAML). Impostare il valore su qualsiasi attributo utente archiviato nella directory.

![Aggiungi attributo utente][4]

Se ad esempio è necessario inviare il reparto dell'organizzazione cui appartiene l'utente come attestazione (si supponga il reparto Vendite), è possibile immettere qualsiasi valore di attestazione previsto dall'applicazione e quindi selezionare **user.department** come valore.

Se per un determinato utente non è stato archiviato alcun valore per un attributo selezionato, l'attestazione non verrà rilasciata nel token.

**Nota:** **user.onpremisesecurityidentifier** e **user.onpremisesamaccountname** sono supportati soltanto quando si effettua la sincronizzazione dei dati utente da Active Directory in locale usando lo strumento [Azure AD Connect](../active-directory-aadconnect.md).

## <a name="next-steps"></a>Passaggi successivi
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](../active-directory-apps-index.md)
* [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](../active-directory-saas-custom-apps.md)
* [Risoluzione dei problemi dell'accesso Single Sign-On basato su SAML](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ../media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ../media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ../media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ../media/active-directory-saml-claims-customization/claimscustomization4.png
