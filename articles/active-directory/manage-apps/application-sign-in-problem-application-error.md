---
title: Messaggio di errore viene visualizzato nella pagina dell'app dopo l'accesso | Microsoft Docs
description: Come risolvere i problemi di accesso ad Azure AD quando l'app restituisce un messaggio di errore.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: d41ec1f510b028a2ffe2554bfcbd77bc439c4e79
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272959"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Una pagina dell'app Visualizza un messaggio di errore dopo l'accesso dell'utente

In questo scenario, Azure Active Directory (Azure AD) esegue l'accesso all'utente. Tuttavia, l'applicazione viene visualizzato un messaggio di errore e non consente all'utente di completare il flusso di accesso. Il problema è che l'app non ha accettato la risposta che ha rilasciato Azure AD.

Esistono varie ragioni per cui l'app non ha accettato la risposta da Azure AD. Se il messaggio di errore non viene identificato in modo chiaro cosa manca dalla risposta, procedere come segue:

-   Se l'app è una raccolta di Azure AD, verificare di aver seguito i passaggi descritti in [come eseguire il debug basato su SAML single sign-on alle applicazioni in Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Usare uno strumento come [Fiddler](https://www.telerik.com/fiddler) per acquisire la richiesta, risposta e token SAML.

-   Inviare la risposta SAML il fornitore dell'app e chiedere di cosa manca.

## <a name="attributes-are-missing-from-the-saml-response"></a>Gli attributi non sono presenti la risposta SAML

Per aggiungere un attributo nella configurazione di Azure AD che verrà inviata nella risposta di Azure AD, seguire questa procedura:

1. Aprire il [ **portale di Azure** ](https://portal.azure.com/) e accedere come un amministratore globale o un coamministratore.

2. Nella parte superiore del riquadro di spostamento a sinistra, selezionare **tutti i servizi** per aprire l'estensione Azure AD.

3. Tipo di **Azure Active Directory** nella casella di ricerca filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** nel riquadro di spostamento di Azure AD.

5. Selezionare **tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE]
   > Se non viene visualizzata l'app che vuole, usare il **filtro** nella parte superiore del controllo il **elenco tutte le applicazioni**. Impostare il **mostrare** opzione per "Tutte le applicazioni."

6. Selezionare l'applicazione che si desidera configurare per il single sign-on.

7. Una volta caricata l'app, selezionare **l'accesso Single sign-on** nel riquadro di spostamento.

8. Nel **attributi utente** sezione, selezionare **visualizzazione e modifica tutti gli altri attributi utente**. Qui è possibile modificare gli attributi da inviare all'app nel token SAML all'accesso degli utenti.

   Per aggiungere un attributo:

   1. Selezionare **Aggiungi attributo**. Immettere il **Name**e selezionare il **valore** nell'elenco a discesa.

   1.  Selezionare **Salva**. Si noterà il nuovo attributo nella tabella.

9. Salvare la configurazione.

   La volta successiva che l'utente accede all'app, Azure AD invierà il nuovo attributo nella risposta SAML.

## <a name="the-app-doesnt-identify-the-user"></a>L'app non identifica l'utente

Accesso alle app non riesce perché la risposta SAML manca un attributo, ad esempio un ruolo. O non riesce perché l'app si aspetta un formato diverso o un valore per il **NameID** attributo (identificatore utente).

Se si usa [AD Azure il provisioning utenti automatizzato](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) per creare, gestire, rimuovere gli utenti nell'app, verificare che l'utente sia stato predisposto per l'app SaaS. Per altre informazioni, vedere [gli utenti non sono in corso il provisioning di un'applicazione di Azure AD Gallery](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Aggiungere un attributo per la configurazione dell'app Azure AD

Per modificare il valore dell'ID utente, seguire questa procedura:

1. Aprire il [ **portale di Azure** ](https://portal.azure.com/) e accedere come un amministratore globale o un coamministratore.

2. Selezionare **tutti i servizi** nella parte superiore del riquadro di spostamento a sinistra per aprire l'estensione Azure AD.

3. Tipo di **Azure Active Directory** nella casella di ricerca filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** nel riquadro di spostamento di Azure AD.

5. Selezionare **tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE]
   > Se non viene visualizzata l'app che vuole, usare il **filtro** nella parte superiore del controllo il **elenco tutte le applicazioni**. Impostare il **mostrare** opzione per "Tutte le applicazioni."

6. Selezionare l'app che si desidera configurare per SSO.

7. Una volta caricata l'app, selezionare **l'accesso Single sign-on** nel riquadro di spostamento.

8. Sotto **attributi utente**, selezionare l'identificatore univoco per l'utente dalle **identificatore utente** elenco a discesa.

## <a name="change-the-nameid-format"></a>Modificare il formato NameID

Se l'applicazione prevede un altro formato per il **NameID** attributo (identificatore utente), vedere [nameID modifica](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization.md#editing-nameid) per modificare il formato di NameID.

Azure AD seleziona il formato per il **NameID** attributo (identificatore utente) in base al valore che viene selezionato o il formato richiesto dall'app nell'oggetto AuthRequest SAML. Per altre informazioni, vedere la sezione "NameIDPolicy" del [l'accesso Single sign-on di protocollo SAML](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>L'app prevede un metodo di firma diverso per la risposta SAML

Per modificare le parti del token SAML firmate digitalmente da Azure AD, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere come un amministratore globale o un coamministratore.

2. Selezionare **tutti i servizi** nella parte superiore del riquadro di spostamento a sinistra per aprire l'estensione Azure AD.

3. Tipo di **Azure Active Directory** nella casella di ricerca filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** nel riquadro di spostamento di Azure AD.

5. Selezionare **tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE]
   > Se non viene visualizzata l'applicazione che desidera, utilizzare il **filtro** nella parte superiore del controllo il **elenco tutte le applicazioni**. Impostare il **mostrare** opzione per "Tutte le applicazioni."

6. Selezionare l'applicazione che si desidera configurare per il single sign-on.

7. Dopo il caricamento dell'applicazione, selezionare **l'accesso Single sign-on** nel riquadro di spostamento.

8. Sotto **certificato di firma SAML**, selezionare **Mostra impostazioni avanzate certificati firma**.

9. Selezionare il **opzione di firma** che l'app prevede tra queste opzioni:

   * **Firma risposta SAML**
   * **Firma asserzione e risposta SAML**
   * **Firma asserzione SAML**

   La volta successiva che l'utente accede all'app, Azure AD firmerà la parte della risposta SAML selezionata.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>L'app si aspetta che l'algoritmo di firma SHA-1

Per impostazione predefinita, Azure AD firma il token SAML usando l'algoritmo più sicura. È consigliabile non modificare l'algoritmo di firma *SHA-1* , a meno che l'app richiede SHA-1.

Per modificare l'algoritmo di firma, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere come un amministratore globale o un coamministratore.

2. Selezionare **tutti i servizi** nella parte superiore del riquadro di spostamento a sinistra per aprire l'estensione Azure AD.

3. Tipo di **Azure Active Directory** nella casella di ricerca filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** nel riquadro di spostamento di Azure AD.

5. Selezionare **tutte le applicazioni** per visualizzare un elenco delle applicazioni.

   > [!NOTE]
   > Se non viene visualizzata l'applicazione che desidera, utilizzare il **filtro** nella parte superiore del controllo il **elenco tutte le applicazioni**. Impostare il **mostrare** opzione per "Tutte le applicazioni."

6. Selezionare l'app che si desidera configurare per il single sign-on.

7. Una volta caricata l'app, selezionare **l'accesso Single sign-on** dal riquadro di spostamento sul lato sinistro dell'app.

8. Sotto **certificato di firma SAML**, selezionare **Mostra impostazioni avanzate certificati firma**.

9. Selezionare **SHA-1** come la **algoritmo di firma**.

   La volta successiva che l'utente accede all'app, Azure AD firmerà il token SAML usando l'algoritmo SHA-1.

## <a name="next-steps"></a>Passaggi successivi
[Come eseguire il debug basato su SAML single sign-on alle applicazioni in Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).
