---
title: Errore nella pagina di un'applicazione dopo l'accesso | Microsoft Docs
description: Come risolvere i problemi di accesso ad Azure AD quando l'applicazione stessa genera un errore
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: bd86d4b13c8f61f278589e5c1d705ad91b3e3d4c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="error-on-an-applications-page-after-signing-in"></a>Errore nella pagina di un'applicazione dopo l'accesso

In questo scenario l'utente ha eseguito l'accesso ad Azure AD, ma l'applicazione visualizza un errore che non consente all'utente di completare correttamente il flusso di accesso. In questo scenario l'applicazione non accetta la risposta di Azure AD.

Ci sono diversi motivi per cui l'applicazione non ha accettato la risposta di Azure AD. Se l'errore nell'applicazione non è sufficientemente chiaro per capire cosa manca nella risposta, procedere come segue:

-   Se l'applicazione è la raccolta di Azure AD, verificare di aver seguito tutti i passaggi nell'articolo [Come eseguire il debug di single sign-on basato su SAML per applicazioni in Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Usare uno strumento come [Fiddler](http://www.telerik.com/fiddler) per acquisire la richiesta SAML, la risposta SAML e il token SAML.

-   Condividere la risposta SAML con il fornitore dell'applicazione per capire cosa manca.

## <a name="missing-attributes-in-the-saml-response"></a>Attributi mancanti nella risposta SAML

Per aggiungere un attributo nella configurazione di Azure AD da inviare nella risposta di Azure AD, seguire questa procedura:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di navigazione a sinistra dell'applicazione.

8.  Fare clic su **Visualizza e modifica tutti gli altri attributi utente** nella sezione **Attributi utente** per modificare gli attributi da inviare all'applicazione nel token SAML quando l'utente esegue l'accesso.

   Per aggiungere un attributo:

   * Fare clic su **Aggiungi attributo**. Immettere il **Nome** e selezionare il **Valore** nell'elenco a discesa.

   * Fare clic su **Salva**. Il nuovo attributo verrà visualizzato nella tabella.

9.  Salvare la configurazione.

Al successivo accesso dell'utente all'applicazione, Azure AD invia il nuovo attributo nella risposta SAML.

## <a name="the-application-expects-a-different-user-identifier-value-or-format"></a>L'applicazione prevede un formato o un valore dell'ID utente diverso

L'accesso all'applicazione non riesce perché nella risposta SAML mancano attributi come i ruoli o perché l'applicazione prevede un formato diverso per l'attributo EntityID.

## <a name="add-an-attribute-in-the-azure-ad-application-configuration"></a>Aggiungere un attributo nella configurazione dell'applicazione Azure AD:

Per modificare il valore dell'ID utente, seguire questa procedura:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di navigazione a sinistra dell'applicazione.

8.  In **Attributi utente** selezionare l'identificatore univoco per gli utenti nel menu a discesa **Identificatore utente**.

## <a name="change-entityid-user-identifier-format"></a>Modificare il formato di EntityID (ID utente)

Se l'applicazione prevede un altro formato per l'attributo EntityID, non sarà possibile selezionare il formato di EntityID (ID utente) che Azure AD invia all'applicazione nella risposta dopo l'autenticazione utente.

Azure AD seleziona il formato per l'attributo NameID (ID utente) in base al valore selezionato o al formato richiesto dall'applicazione nell'oggetto AuthRequest SAML. Per altre informazioni, vedere la sezione NameIDPolicy dell'articolo [Protocollo SAML per Single Sign-On](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest).

## <a name="the-application-expects-a-different-signature-method-for-the-saml-response"></a>L'applicazione prevede un metodo di firma diverso per la risposta SAML

Per modificare le parti del token SAML con firma digitale di Azure Active Directory. Attenersi ai passaggi indicati di seguito:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di navigazione a sinistra dell'applicazione.

8.  Fare clic su **Mostra impostazioni avanzate per la firma di certificati** nella sezione **Certificato di firma SAML**.

9.  Selezionare un valore appropriato per **Opzione di firma** in base a quanto previsto dall'applicazione:

  * Firma risposta SAML

  * Firma asserzione e risposta SAML

  * Firma asserzione SAML

Al successivo accesso dell'utente all'applicazione, Azure AD firma la parte della risposta SAML selezionata.

## <a name="the-application-expects-the-signing-algorithm-to-be-sha-1"></a>L'applicazione prevede che l'algoritmo di firma sia SHA-1

Per impostazione predefinita, Azure AD firma il token SAML usando l'algoritmo più sicuro. Non è consigliabile modificare l'algoritmo di firma scegliendo SHA-1, a meno che non sia richiesto dall'applicazione.

Per modificare l'algoritmo di firma, seguire questa procedura:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di navigazione a sinistra dell'applicazione.

8.  Fare clic su **Mostra impostazioni avanzate per la firma di certificati** nella sezione **Certificato di firma SAML**.

9.  Selezionare SHA-1 in **Algoritmo di firma**.

Al successivo accesso dell'utente all'applicazione, Azure AD firma il token SAML usando l'algoritmo SHA-1.

## <a name="next-steps"></a>Passaggi successivi
[Come eseguire il debug di single sign-on basato su SAML per applicazioni in Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
