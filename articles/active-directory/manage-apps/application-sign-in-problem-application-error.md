---
title: Il messaggio di errore viene visualizzato nella pagina dell'app dopo l'accesso Documenti Microsoft
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
ms.openlocfilehash: 9b8d20b31e96973a492355f0515d0532deea0ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185484"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Una pagina dell'app mostra un messaggio di errore dopo l'accesso dell'utente

In questo scenario, Azure Active Directory (Azure AD) accede all'utente. Ma l'applicazione visualizza un messaggio di errore e non consente all'utente di completare il flusso di accesso. Il problema è che l'app non ha accettato la risposta rilasciata da Azure AD.

Esistono diversi motivi possibili per cui l'app non ha accettato la risposta da Azure AD. Se il messaggio di errore non identifica chiaramente ciò che non è presente nella risposta, provare a eseguire le operazioni seguenti:

-   Se l'app è la raccolta di Azure AD, verificare di aver seguito la procedura descritta in Come eseguire il [debug dell'accesso Single Sign-On basato su SAML alle applicazioni in Azure AD.](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)

-   Usa uno strumento come [Fiddler](https://www.telerik.com/fiddler) per acquisire la richiesta, la risposta e il token SAML.

-   Invia la risposta SAML al fornitore dell'app e chiedi loro cosa manca.

## <a name="attributes-are-missing-from-the-saml-response"></a>Gli attributi non sono presenti nella risposta SAML

Per aggiungere un attributo nella configurazione di Azure AD che verrà inviata nella risposta di Azure AD, eseguire la procedura seguente:To add an attribute in the Azure AD configuration that will be sent in the Azure AD response, follow these steps:

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come amministratore globale o coamministratore.

2. Nella parte superiore del riquadro di spostamento sul lato sinistro selezionare **Tutti i servizi** per aprire l'estensione di Azure AD.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare Azure Active **Directory**.

4. Selezionare **Applicazioni aziendali** nel riquadro di spostamento di Azure AD.

5. Selezionare **Tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE]
   > Se l'app desiderata non è visualizzata, utilizzare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni.** Impostare l'opzione **Mostra su** "Tutte le applicazioni".

6. Selezionare l'applicazione che si desidera configurare per l'accesso Single Sign-On.

7. Dopo il caricamento dell'app, seleziona **Single Sign-On** nel riquadro di spostamento.

8. Nella sezione **Attributi utente** selezionare Visualizza e modifica tutti gli altri **attributi utente.** Qui puoi modificare gli attributi da inviare all'app nel token SAML quando gli utenti accedono.

   Per aggiungere un attributo:

   1. Selezionare **Aggiungi attributo**. Immettere il **Nome**e selezionare il **Valore** dall'elenco a discesa.

   1.  Selezionare **Salva**. Vedrai il nuovo attributo nella tabella.

9. Salvare la configurazione.

   Al successivo accesso dell'utente all'app, Azure AD invierà il nuovo attributo nella risposta SAML.

## <a name="the-app-doesnt-identify-the-user"></a>L'app non identifica l'utente

L'accesso all'app non riesce perché nella risposta SAML manca un attributo, ad esempio un ruolo. Oppure non riesce perché l'app prevede un formato o un valore diverso per l'attributo **NameID** (User Identifier).

Se si usa il [provisioning automatico degli utenti](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) di Azure AD per creare, gestire e rimuovere utenti nell'app, verificare che sia stato eseguito il provisioning dell'utente nell'app SaaS. Per altre informazioni, vedere Nessun utente di cui viene eseguito il [provisioning in un'applicazione Raccolta Azure AD.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Aggiungere un attributo alla configurazione dell'app Azure ADAdd an attribute to the Azure AD app configuration

Per modificare il valore dell'ID utente, seguire questa procedura:

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come amministratore globale o coamministratore.

2. Selezionare **Tutti i servizi** nella parte superiore del riquadro di spostamento sul lato sinistro per aprire l'estensione di Azure AD.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare Azure Active **Directory**.

4. Selezionare **Applicazioni aziendali** nel riquadro di spostamento di Azure AD.

5. Selezionare **Tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE]
   > Se l'app desiderata non è visualizzata, utilizzare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni.** Impostare l'opzione **Mostra su** "Tutte le applicazioni".

6. Selezionare l'app che si desidera configurare per SSO.

7. Dopo il caricamento dell'app, seleziona **Single Sign-On** nel riquadro di spostamento.

8. In **Attributi utente**selezionare l'identificatore univoco per l'utente dall'elenco a discesa **Identificatore utente.**

## <a name="change-the-nameid-format"></a>Modificare il formato NameID

Se l'applicazione prevede un altro formato per l'attributo **NameID** (User Identifier), vedere [Modifica di nameID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid) per modificare il formato NameID.

Azure AD seleziona il formato per l'attributo **NameID** (User Identifier) in base al valore selezionato o al formato richiesto dall'app in SAML AuthRequest. Per ulteriori informazioni, vedere la sezione "NameIDPolicy" del [protocollo SAML Single Sign-On](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>L'app prevede un metodo di firma diverso per la risposta SAML

Per modificare le parti del token SAML firmate digitalmente da Azure AD, attenersi alla seguente procedura:

1. Aprire il portale di [Azure](https://portal.azure.com/) e accedere come amministratore globale o coamministratore.

2. Selezionare **Tutti i servizi** nella parte superiore del riquadro di spostamento sul lato sinistro per aprire l'estensione di Azure AD.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare Azure Active **Directory**.

4. Selezionare **Applicazioni aziendali** nel riquadro di spostamento di Azure AD.

5. Selezionare **Tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE]
   > Se l'applicazione desiderata non è visualizzata, utilizzare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni.** Impostare l'opzione **Mostra su** "Tutte le applicazioni".

6. Selezionare l'applicazione che si desidera configurare per l'accesso Single Sign-On.

7. Dopo il caricamento dell'applicazione, selezionare **Single Sign-On** nel riquadro di spostamento.

8. In **Certificato di firma SAML**selezionare Mostra impostazioni avanzate di firma del **certificato**.

9. Selezionare l'opzione di firma prevista dall'app tra le opzioni seguenti:Select the **Signing Option** that the app expects among these options:

   * **Firma risposta SAML**
   * **Firma risposta SAML e asserzione**
   * **Firma asserzione SAML**

   Al successivo accesso dell'app all'app, Azure AD firmerà la parte della risposta SAML selezionata.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>L'app prevede l'algoritmo di firma SHA-1

Per impostazione predefinita, Azure AD firma il token SAML usando l'algoritmo più sicuro. Si consiglia di non modificare l'algoritmo di firma in *SHA-1* a meno che l'app non richieda SHA-1.

Per modificare l'algoritmo di firma, seguire questa procedura:

1. Aprire il portale di [Azure](https://portal.azure.com/) e accedere come amministratore globale o coamministratore.

2. Selezionare **Tutti i servizi** nella parte superiore del riquadro di spostamento sul lato sinistro per aprire l'estensione di Azure AD.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare Azure Active **Directory**.

4. Selezionare **Applicazioni aziendali** nel riquadro di spostamento di Azure AD.

5. Selezionare **Tutte le applicazioni** per visualizzare un elenco delle applicazioni.

   > [!NOTE]
   > Se l'applicazione desiderata non è visualizzata, utilizzare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni.** Impostare l'opzione **Mostra su** "Tutte le applicazioni".

6. Selezionare l'app che si desidera configurare per l'accesso Single Sign-On.

7. Dopo il caricamento dell'app, seleziona **Single Sign-On** dal riquadro di spostamento sul lato sinistro dell'app.

8. In **Certificato di firma SAML**selezionare Mostra impostazioni avanzate di firma del **certificato**.

9. Selezionare **SHA-1** come **algoritmo**di firma .

   Al successivo accesso dell'utente all'app, Azure AD firmerà il token SAML usando l'algoritmo SHA-1.

## <a name="next-steps"></a>Passaggi successivi
[Come eseguire il debug dell'accesso Single Sign-On basato su SAML alle applicazioni in Azure AD.](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
