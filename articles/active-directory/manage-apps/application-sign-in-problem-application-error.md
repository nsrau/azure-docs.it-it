---
title: Il messaggio di errore viene visualizzato nella pagina dell'app dopo l'accesso | Microsoft Docs
description: Come risolvere i problemi relativi all'accesso Azure AD quando l'app restituisce un messaggio di errore.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c826a679c1c64e113beb6b2cc5ffd29f82b55a3b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84759539"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Una pagina dell'app mostra un messaggio di errore dopo l'accesso dell'utente

In questo scenario, Azure Active Directory (Azure AD) consente di firmare l'utente in. Tuttavia, l'applicazione visualizza un messaggio di errore e non consente all'utente di completare il flusso di accesso. Il problema è che l'app non ha accettato la risposta che Azure AD emesso.

Esistono diversi motivi possibili per cui l'app non ha accettato la risposta da Azure AD. Se il messaggio di errore non identifica chiaramente i dati mancanti nella risposta, provare a eseguire le operazioni seguenti:

-   Se l'app è la raccolta di Azure AD, verificare di aver seguito i passaggi descritti in [come eseguire il debug di Single Sign-on basati su SAML per le applicazioni in Azure ad](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Usare uno strumento come [Fiddler](https://www.telerik.com/fiddler) per acquisire la richiesta, la risposta e il token SAML.

-   Inviare la risposta SAML al fornitore dell'app e chiedere gli elementi mancanti.

## <a name="attributes-are-missing-from-the-saml-response"></a>Attributi mancanti nella risposta SAML

Per aggiungere un attributo nella configurazione di Azure AD che verrà inviata nella risposta Azure AD, attenersi alla seguente procedura:

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come amministratore globale o coamministratore.

2. Nella parte superiore del riquadro di spostamento sul lato sinistro selezionare tutti i **Servizi** per aprire l'estensione Azure ad.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** nel riquadro di spostamento Azure ad.

5. Selezionare **tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE]
   > Se non viene visualizzata l'app desiderata, usare il controllo **filtro** all'inizio dell' **elenco tutte le applicazioni**. Impostare l'opzione **Mostra** su "tutte le applicazioni".

6. Selezionare l'applicazione che si desidera configurare per Single Sign-On.

7. Al termine del caricamento dell'app, selezionare **Single Sign-on** nel riquadro di spostamento.

8. Nella sezione **attributi utente** selezionare **Visualizza e modifica tutti gli altri attributi utente**. Qui è possibile modificare gli attributi da inviare all'app nel token SAML quando l'utente esegue l'accesso.

   Per aggiungere un attributo:

   1. Selezionare **Aggiungi attributo**. Immettere il **nome**e selezionare il **valore** dall'elenco a discesa.

   1.  Selezionare **Salva**. Il nuovo attributo verrà visualizzato nella tabella.

9. Salvare la configurazione.

   La volta successiva che l'utente accede all'app, Azure AD invierà il nuovo attributo nella risposta SAML.

## <a name="the-app-doesnt-identify-the-user"></a>L'app non identifica l'utente

L'accesso all'app ha esito negativo perché nella risposta SAML manca un attributo, ad esempio un ruolo. In alternativa, si verifica un errore perché l'app prevede un formato o un valore diverso per l'attributo **NameID** (ID utente).

Se si usa [Azure ad provisioning utenti automatizzato](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) per creare, gestire e rimuovere gli utenti nell'app, verificare che sia stato effettuato il provisioning dell'utente per l'app Saas. Per altre informazioni, vedere [non è in corso il provisioning degli utenti in un'applicazione della raccolta Azure ad](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Aggiungere un attributo alla configurazione dell'app Azure AD

Per modificare il valore dell'ID utente, seguire questa procedura:

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come amministratore globale o coamministratore.

2. Selezionare **tutti i servizi** nella parte superiore del riquadro di spostamento sul lato sinistro per aprire l'estensione Azure ad.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** nel riquadro di spostamento Azure ad.

5. Selezionare **tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE]
   > Se non viene visualizzata l'app desiderata, usare il controllo **filtro** all'inizio dell' **elenco tutte le applicazioni**. Impostare l'opzione **Mostra** su "tutte le applicazioni".

6. Selezionare l'app che si vuole configurare per SSO.

7. Al termine del caricamento dell'app, selezionare **Single Sign-on** nel riquadro di spostamento.

8. In **attributi utente**selezionare l'identificatore univoco per l'utente nell'elenco a discesa **identificatore utente** .

## <a name="change-the-nameid-format"></a>Modificare il formato NameID

Se l'applicazione prevede un altro formato per l'attributo **NameID** (ID utente), vedere [modifica di NameID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid) per modificare il formato NameID.

Azure AD seleziona il formato per l'attributo **NameID** (identificatore utente) in base al valore selezionato o al formato richiesto dall'app nel oggetto authrequest SAML. Per ulteriori informazioni, vedere la sezione "NameIDPolicy" del [protocollo SAML per Single Sign-on](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>L'app prevede un metodo di firma diverso per la risposta SAML

Per modificare le parti del token SAML con firma digitale mediante Azure AD, attenersi alla seguente procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere come amministratore globale o coamministratore.

2. Selezionare **tutti i servizi** nella parte superiore del riquadro di spostamento sul lato sinistro per aprire l'estensione Azure ad.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** nel riquadro di spostamento Azure ad.

5. Selezionare **tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE]
   > Se non viene visualizzata l'applicazione desiderata, usare il controllo **filtro** nella parte superiore dell' **elenco tutte le applicazioni**. Impostare l'opzione **Mostra** su "tutte le applicazioni".

6. Selezionare l'applicazione che si desidera configurare per Single Sign-On.

7. Al termine del caricamento dell'applicazione, selezionare **Single Sign-on** nel riquadro di spostamento.

8. In **certificato di firma SAML**selezionare **Mostra impostazioni avanzate**per la firma di certificati.

9. Selezionare l' **opzione di firma** che l'app prevede tra le seguenti opzioni:

   * **Firma risposta SAML**
   * **Firma asserzione e risposta SAML**
   * **Firma asserzione SAML**

   La volta successiva che l'utente accede all'app, Azure AD firmerà la parte della risposta SAML selezionata.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>L'app prevede l'algoritmo di firma SHA-1

Per impostazione predefinita, Azure AD firma il token SAML usando l'algoritmo più sicuro. Si consiglia di non modificare l'algoritmo di firma in *SHA-1* , a meno che l'app non richieda SHA-1.

Per modificare l'algoritmo di firma, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere come amministratore globale o coamministratore.

2. Selezionare **tutti i servizi** nella parte superiore del riquadro di spostamento sul lato sinistro per aprire l'estensione Azure ad.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** nel riquadro di spostamento Azure ad.

5. Selezionare **tutte le applicazioni** per visualizzare un elenco delle applicazioni.

   > [!NOTE]
   > Se non viene visualizzata l'applicazione desiderata, usare il controllo **filtro** nella parte superiore dell' **elenco tutte le applicazioni**. Impostare l'opzione **Mostra** su "tutte le applicazioni".

6. Selezionare l'app che si vuole configurare per Single Sign-On.

7. Al termine del caricamento dell'app, selezionare **Single Sign-on** nel riquadro di spostamento sul lato sinistro dell'app.

8. In **certificato di firma SAML**selezionare **Mostra impostazioni avanzate**per la firma di certificati.

9. Selezionare **SHA-1** come **algoritmo di firma**.

   La volta successiva che l'utente accede all'app, Azure AD firma il token SAML usando l'algoritmo SHA-1.

## <a name="next-steps"></a>Passaggi successivi
[Come eseguire il debug di Single Sign-on basate su SAML per le applicazioni Azure ad](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).
