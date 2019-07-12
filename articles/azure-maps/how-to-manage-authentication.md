---
title: Gestire l'autenticazione in Mappe di Azure | Microsoft Docs
description: È possibile usare il portale di Azure per gestire l'autenticazione in Mappe di Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 07/11/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1dc63a2c2350ad4f1d02d0c1b22050293d7b866c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67837807"
---
# <a name="manage-authentication-in-azure-maps"></a>Gestire l'autenticazione in Mappe di Azure

Dopo aver creato un account mappe di Azure, un ID client e le chiavi vengono create per supportare l'autenticazione chiave condivisa o Azure Active Directory (Azure AD).

## <a name="view-authentication-details"></a>Visualizzare i dettagli di autenticazione

È possibile visualizzare i dettagli di autenticazione nel portale di Azure. Passare al proprio account e selezionare **Authentication** nel **impostazioni** menu.

![Dettagli di autenticazione](./media/how-to-manage-authentication/how-to-view-auth.png)

 Per altre informazioni, vedere [l'autenticazione con le mappe di Azure](https://aka.ms/amauth).


## <a name="set-up-azure-ad-app-registration"></a>Configurare la registrazione app di Azure AD

Dopo aver creato un account mappe di Azure, è necessario stabilire un collegamento tra il tenant di Azure AD e la risorsa di mappe di Azure.

1. Passare al pannello Azure AD e creare una registrazione dell'app. Specificare un nome per la registrazione. Nel **URL Sign-on** immettere la home page dell'app web / API (ad esempio, https:\//localhost/). Se si dispone già di un'app registrata, andare al passaggio 2.

    ![Registrazione delle app](./media/how-to-manage-authentication/app-registration.png)

    ![Dettagli di registrazione di App](./media/how-to-manage-authentication/app-create.png)

2. Per assegnare le autorizzazioni delegate di API per le mappe di Azure, passare all'applicazione sottoposta **registrazioni per l'App**, quindi selezionare **impostazioni**.  Selezionare **autorizzazioni necessarie**, quindi selezionare **Add**. Cercare e selezionare **mappe di Azure** sotto **selezionare un'API**, quindi selezionare il **selezionare** pulsante.

    ![Autorizzazioni di App per le API](./media/how-to-manage-authentication/app-permissions.png)

3. Sotto **selezionare le autorizzazioni**, selezionare **le mappe di Azure di accedere**e quindi selezionare il **selezionare** pulsante.

    ![Selezionare le autorizzazioni di app per le API](./media/how-to-manage-authentication/select-app-permissions.png)

4. Completare il passaggio una a o b, a seconda del metodo di autenticazione.

    1. Se l'applicazione usa l'autenticazione token dell'utente con mappe di Azure SDK per Web, abilitare `oauthEnableImplicitFlow` impostando su true nella sezione manifesto della pagina di dettaglio registrazione dell'app.
    
       ![Manifesto dell'app](./media/how-to-manage-authentication/app-manifest.png)

    2. Se l'applicazione usa l'autenticazione server / dell'applicazione, passare al **chiavi** pannello nella registrazione dell'app e creare una password o caricare un certificato di chiave pubblica per la registrazione dell'app. Se si crea una password, dopo aver selezionato **salvare**, copiare la password per un momento successivo e archiviarlo in modo sicuro. Userai questa password per acquisire i token da Azure AD.

       ![Chiavi dell'app](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Concedere il controllo degli accessi in base al ruolo a Mappe di Azure

Dopo aver associato un account mappe di Azure con il tenant di Azure AD, è possibile concedere il controllo di accesso tramite l'assegnazione di un utente, gruppo o un'applicazione a uno o più ruoli di controllo di accesso di mappe di Azure.

1. Passare a **controllo di accesso (IAM)** , selezionare **le assegnazioni di ruolo**, quindi selezionare **aggiungere un'assegnazione di ruolo**.

    ![Concedere il controllo degli accessi in base al ruolo](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Nel **aggiungere un'assegnazione di ruolo** finestra, sotto **ruolo**, selezionare **lettura data mappe di Azure (anteprima)** . In **Assegna accesso a** selezionare **Utente, gruppo o entità servizio di Azure AD**. Sotto **seleziona**, selezionare l'utente o applicazione. Selezionare **Salva**.

    ![Aggiungi un'assegnazione di ruolo](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Visualizzare i ruoli Controllo degli accessi in base al ruolo di Mappe di Azure disponibili

Per visualizzare i ruoli controllo degli accessi in base al ruolo di accesso disponibili per le mappe di Azure, passare a **controllo di accesso (IAM)** , selezionare **ruoli**, e quindi cercare i ruoli che iniziano con **mappediAzure**. Questi sono i ruoli che è possibile concedere l'accesso a.

![Visualizzare i ruoli disponibili](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Visualizzare mappe di Azure RBAC

RBAC fornisce controllo di accesso granulare.

Per visualizzare gli utenti e le app che sono stati concessi RBAC per le mappe di Azure, passare a **controllo di accesso (IAM)** , selezionare **le assegnazioni di ruolo**e quindi filtrare in base **mappe di Azure**.

![Visualizzare gli utenti e le app concesse RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Richiedere token per Mappe di Azure

Dopo aver registrato l'app e ad essa associato con le mappe di Azure, è possibile richiedere i token di accesso.

* Se l'applicazione usa l'autenticazione token dell'utente con mappe di Azure SDK per Web, è necessario configurare la pagina HTML con l'ID client di mappe di Azure e l'ID dell'app Azure AD.

* Se l'applicazione usa l'autenticazione server / dell'applicazione, è necessario richiedere un token dall'endpoint di accesso di Azure AD `https://login.microsoftonline.com` con l'ID di risorsa di Azure AD `https://atlas.microsoft.com/`, l'ID client di mappe di Azure, l'ID di app di Azure AD e la registrazione dell'app Azure AD password o certificato.

Per altre informazioni su come richiedere i token di accesso da Azure AD per gli utenti ed entità servizio, vedere [scenari di autenticazione per Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'autenticazione di Azure AD e Web SDK per Mappe di Azure, vedere [Usare il controllo mappa di Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Informazioni su come visualizzare le metriche di utilizzo delle API per l'account Mappe di Azure:
> [!div class="nextstepaction"] 
> [Visualizzare le metriche di utilizzo](how-to-view-api-usage.md)