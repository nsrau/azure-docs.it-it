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
ms.openlocfilehash: 2f4a3d791e6b5d6ff20c09408d1a0bf5995c32fd
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756566"
---
# <a name="manage-authentication-in-azure-maps"></a>Gestire l'autenticazione in Mappe di Azure

Dopo aver creato un account Azure Maps, vengono creati un ID client e le chiavi per supportare la Azure Active Directory (Azure AD) o l'autenticazione con chiave condivisa.

## <a name="view-authentication-details"></a>Visualizzare i dettagli di autenticazione

È possibile visualizzare i dettagli di autenticazione nel portale di Azure. Passare all'account e selezionare **autenticazione** dal menu **Impostazioni** .

![Dettagli di autenticazione](./media/how-to-manage-authentication/how-to-view-auth.png)

 Per altre informazioni, vedere [autenticazione con mappe di Azure](https://aka.ms/amauth).


## <a name="set-up-azure-ad-app-registration"></a>Configurare la registrazione dell'app Azure AD

Dopo aver creato un account Azure Maps, è necessario stabilire un collegamento tra il tenant di Azure AD e la risorsa mappe di Azure.

1. Passare al pannello Azure AD e creare una registrazione per l'app. Consente di specificare un nome per la registrazione. Nella casella **URL di accesso** specificare il Home page dell'app Web/API, ad esempio https: \//localhost/. Se si dispone già di un'app registrata, andare al passaggio 2.

    ![Registrazione delle app](./media/how-to-manage-authentication/app-registration.png)

    ![Dettagli registrazione app](./media/how-to-manage-authentication/app-create.png)

2. Per assegnare le autorizzazioni per le API delegate a mappe di Azure, passare all'applicazione in **registrazioni app**, quindi selezionare **Impostazioni**.  Selezionare **autorizzazioni necessarie**e quindi selezionare **Aggiungi**. Cercare e selezionare **mappe di Azure** in **selezionare un'API**e quindi selezionare il pulsante **Seleziona** .

    ![Autorizzazioni API app](./media/how-to-manage-authentication/app-permissions.png)

3. In **Seleziona autorizzazioni**selezionare **Accedi ad Azure Maps**, quindi selezionare il pulsante **Seleziona** .

    ![Selezionare le autorizzazioni dell'API per le app](./media/how-to-manage-authentication/select-app-permissions.png)

4. Completare il passaggio a o b, a seconda del metodo di autenticazione.

    1. Se l'applicazione usa l'autenticazione basata su token utente con Azure Maps Web SDK, abilitare `oauthEnableImplicitFlow` impostando il valore su true nella sezione manifesto della pagina dei dettagli di registrazione dell'app.
    
       ![Manifesto dell'app](./media/how-to-manage-authentication/app-manifest.png)

    2. Se l'applicazione usa l'autenticazione server/applicazione, passare al pannello **chiavi** nella registrazione dell'app e creare una password o caricare un certificato di chiave pubblica nella registrazione dell'app. Se si crea una password, dopo aver selezionato **Salva**, copiare la password per un momento successivo e archiviarla in modo sicuro. Questa password verrà usata per acquisire i token da Azure AD.

       ![Chiavi dell'app](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Concedere il controllo degli accessi in base al ruolo a Mappe di Azure

Dopo aver associato un account Azure Maps al tenant di Azure AD, è possibile concedere il controllo degli accessi assegnando un utente, un gruppo o un'applicazione a uno o più ruoli di controllo di accesso di Azure maps.

1. Passare a **controllo di accesso (IAM)** , selezionare **assegnazioni di ruolo**, quindi selezionare **Aggiungi assegnazione ruolo**.

    ![Concedere il controllo degli accessi in base al ruolo](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Nella finestra **Aggiungi assegnazione ruolo** , in **ruolo**, selezionare **lettore di data maps di Azure (anteprima)** . In **Assegna accesso a** selezionare **Utente, gruppo o entità servizio di Azure AD**. In **Seleziona**selezionare l'utente o l'applicazione. Selezionare **Salva**.

    ![Aggiungi un'assegnazione di ruolo](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Visualizzare i ruoli Controllo degli accessi in base al ruolo di Mappe di Azure disponibili

Per visualizzare i ruoli del controllo degli accessi in base al ruolo (RBAC) disponibili per le mappe di Azure, passare a **controllo di accesso (IAM)** , selezionare **ruoli**e quindi cercare i ruoli a partire da **mappe di Azure**. Questi sono i ruoli a cui è possibile concedere l'accesso.

![Visualizzare i ruoli disponibili](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Visualizza RBAC di mappe di Azure

RBAC fornisce il controllo di accesso granulare.

Per visualizzare gli utenti e le app a cui è stato concesso il controllo degli accessi in base al ruolo per le mappe di Azure, passare a **controllo di accesso (IAM)** , selezionare **assegnazioni di ruolo**e quindi filtrare in base alle **mappe**

![Visualizza utenti e app concessi RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Richiedere token per Mappe di Azure

Dopo aver registrato l'app e averla associata a Maps di Azure, è possibile richiedere i token di accesso.

* Se l'applicazione usa l'autenticazione basata su token utente con Azure Maps Web SDK, è necessario configurare la pagina HTML con l'ID client Azure Maps e l'ID app Azure AD.

* Se l'applicazione usa l'autenticazione del server o dell'applicazione, è necessario richiedere un token da Azure AD endpoint di accesso `https://login.microsoftonline.com` con l'ID di risorsa Azure AD `https://atlas.microsoft.com/`, l'ID client Azure Maps, l'ID app Azure AD e la password di registrazione dell'app Azure AD o certificato.

Per ulteriori informazioni sulla richiesta di token di accesso da Azure AD per gli utenti e le entità servizio, vedere [scenari di autenticazione per Azure ad](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'autenticazione di Azure AD e Web SDK per Mappe di Azure, vedere [Usare il controllo mappa di Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Informazioni su come visualizzare le metriche di utilizzo delle API per l'account Mappe di Azure:
> [!div class="nextstepaction"] 
> [Visualizzare le metriche di utilizzo](how-to-view-api-usage.md)

Per un elenco di esempi che illustrano come integrare Azure Active Directory (AAD) con mappe di Azure, vedere:

> [!div class="nextstepaction"]
> [Esempi di autenticazione di AAD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)