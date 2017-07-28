---
title: Problema nella configurazione dell'accesso Single Sign-On federato per un'applicazione nella raccolta di Azure AD | Microsoft Docs
description: Informazioni sui problemi comuni che si possono incontrare durante la configurazione dell'accesso Single Sign-On federato per applicazioni SAML incluse nella raccolta delle applicazioni di Azure AD
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: b8c189865c27566e3265f14eb54cb5ea4a005dc5
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---

# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Problema nella configurazione dell'accesso Single Sign-On federato per un'applicazione nella raccolta di Azure AD

Se si verifica un problema durante la configurazione di un'applicazione. Verificare di aver seguito tutti i passaggi dell'esercitazione per l'applicazione. Durante la configurazione dell'applicazione è disponibile la documentazione in linea relativa a come configurare l'applicazione. Per le istruzioni dettagliate, è possibile accedere all'[Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/).

## <a name="cant-add-another-instance-of-the-application"></a>Impossibile aggiungere un'altra istanza dell'applicazione

Per aggiungere una seconda istanza di un'applicazione, è necessario:

-   Configurare un identificatore univoco per la seconda istanza. Non è possibile configurare lo stesso identificatore usato per la prima istanza.

-   Configurare un certificato diverso da quello usato per la prima istanza.

Se l'applicazione non supporta alcuna delle operazioni sopra indicate, non è possibile configurare una seconda istanza.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Impossibile aggiungere l'identificatore o l'URL di risposta

Se non è possibile configurare l'identificatore o l'URL di risposta, verificare che i valori dell'identificatore e dell'URL di risposta corrispondano ai modelli preconfigurati per l'applicazione.

Per conoscere i modelli preconfigurati per l'applicazione:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**. Andare al passaggio 7. Se si è già nel pannello di configurazione dell'applicazione in Azure AD.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di navigazione a sinistra dell'applicazione.

8.  Selezionare **Accesso basato su SAML** dal menu a discesa **Modalità**.

9.  Passare alla casella di testo **Identificatore** o **URL di risposta** nella sezione **URL e dominio**.

10. Esistono tre modi per conoscere i modelli supportati per l'applicazione:

   * Nella casella di testo è presente il modello supportato come segnaposto, *esempio:* <https://contoso.com>.

   * Se il modello non è supportato, viene visualizzato un punto esclamativo rosso quando si tenta di immettere il valore nella casella di testo. Se si posiziona il mouse sul punto esclamativo rosso, vengono visualizzati i modelli supportati.

   * Nell'esercitazione per l'applicazione sono disponibili anche informazioni sui modelli supportati. Nella sezione **Configurare il Single Sign-On di Azure AD**. Andare al passaggio per configurare i valori nella sezione **URL e dominio**.

Se i valori non corrispondono ai modelli preconfigurati in Azure AD. È possibile:

-   Chiedere al fornitore dell'applicazione valori che corrispondono al modello preconfigurato in Azure AD

-   Contattare, in alternativa, il team di Azure AD all'indirizzo <aadapprequest@microsoft.com> o lasciare un commento nell'esercitazione per richiedere l'aggiornamento dei modelli supportati per l'applicazione

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Dove impostare il formato di EntityID (identificatore utente)

Non è possibile selezionare il formato di EntityID (identificatore utente) che Azure AD invia all'applicazione in risposta all'autenticazione dell'utente.

Azure AD seleziona il formato per l'attributo NameID (identificatore utente) in base al valore selezionato o al formato richiesto dall'applicazione nell'oggetto AuthRequest SAML. Per altre informazioni, vedere l'articolo relativo al [protocollo SAML per Single Sign-On](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) sotto la sezione NameIDPolicy.

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Impossibile trovare i metadati di Azure AD per completare la configurazione con l'applicazione

Per scaricare il certificato o i metadati dell'applicazione da Azure AD, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui è stato configurato l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di navigazione a sinistra dell'applicazione.

8.  Passare alla sezione **Certificato di firma SAML** e quindi fare clic sul valore della colonna **Download**. A seconda di quale applicazione richiede la configurazione dell'accesso Single Sign-On, è visibile l'opzione per scaricare il codice XML dei metadati o l'opzione per scaricare il certificato.

Azure AD non fornisce URL per ottenere i metadati. I metadati possono essere recuperati solo come file XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Informazioni sulla personalizzazione delle attestazioni SAML inviate a un'applicazione

Per informazioni su come personalizzare le attestazioni degli attributi SAML inviate all'applicazione, vedere [Claims mapping in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) (Mapping di attestazioni in Azure Active Directory).

## <a name="next-steps"></a>Passaggi successivi
[Gestione di applicazioni con Azure Active Directory](active-directory-enable-sso-scenario.md)

