---
title: Gestire l'autenticazione | Mappe Microsoft Azure
description: È possibile utilizzare il portale di Azure per gestire l'autenticazione in Microsoft Azure maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1f7f128898089292a8ccd92686af5d68fe328f3c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766142"
---
# <a name="manage-authentication-in-azure-maps"></a>Gestire l'autenticazione in Mappe di Azure

Dopo aver creato un account Azure Maps, vengono creati un ID client e le chiavi per supportare Azure Active Directory (Azure AD) e l'autenticazione con chiave condivisa.

## <a name="view-authentication-details"></a>Visualizzare i dettagli di autenticazione

Dopo la creazione dell'account Azure Maps, vengono generate le chiavi primarie e secondarie. Usare la chiave primaria come chiave di sottoscrizione, a volte questi nomi vengono usati in modo interscambiabile. La chiave secondaria può essere usata in scenari come le modifiche delle chiavi in sequenza. In entrambi i casi, è necessaria una chiave per chiamare Azure maps. Questo processo è denominato [autenticazione con chiave condivisa](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Per altre informazioni sulla chiave condivisa e sull'autenticazione Azure AD, vedere [autenticazione con Azure Maps](https://aka.ms/amauth) .

È possibile visualizzare i dettagli di autenticazione nel portale di Azure. Passare all'account e selezionare **autenticazione** dal menu **Impostazioni** .

![Dettagli di autenticazione](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="set-up-azure-ad-app-registration"></a>Configurare la registrazione dell'app Azure AD

Dopo aver creato un account Azure Maps, è necessario stabilire un collegamento tra il tenant di Azure AD e la risorsa mappe di Azure.

1. Selezionare **Azure Active Directory** dal menu del portale. Consente di specificare un nome per la registrazione. Fare clic su **registrazioni app** quindi fare clic su **nuova registrazione**. Nella casella **URI di reindirizzamento** specificare il Home page dell'app Web. Ad esempio: https://localhost/. Se si dispone già di un'app registrata, andare al passaggio 2.

    ![Registrazione delle app](./media/how-to-manage-authentication/app-registration.png)

    ![Dettagli registrazione app](./media/how-to-manage-authentication/app-create.png)

2. Per assegnare le autorizzazioni per le API delegate a mappe di Azure, passare all'applicazione in **registrazioni app**e quindi selezionare **autorizzazioni API**. Selezionare **Aggiungi autorizzazione**. Cercare e selezionare **mappe di Azure** in **selezionare un'API**.

    ![Autorizzazioni API app](./media/how-to-manage-authentication/app-permissions.png)

3. In **Seleziona autorizzazioni selezionare**la casella per la **rappresentazione utente**, quindi fare clic sul pulsante **Seleziona** nella parte inferiore.

    ![Selezionare le autorizzazioni dell'API per le app](./media/how-to-manage-authentication/select-app-permissions.png)

4. Completare il passaggio a o b, a seconda del metodo di autenticazione.

    1. Se l'applicazione usa l'autenticazione basata su token utente con Azure Maps Web SDK, abilitare `oauth2AllowImplicitFlow` impostando il valore su true nella sezione manifest della registrazione dell'app.
    
       ![Manifesto dell'app](./media/how-to-manage-authentication/app-manifest.png)

    2. Se l'applicazione usa l'autenticazione server/applicazione, passare al pannello **certificati & segreti** nella registrazione dell'app e creare una password o caricare un certificato di chiave pubblica nella registrazione dell'app. Se si crea una password, archiviarla in modo sicuro per un uso successivo. Questa password verrà usata per acquisire i token da Azure AD.

       ![Chiavi dell'app](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-role-based-access-control-rbac-to-azure-maps"></a>Concedere il controllo degli accessi in base al ruolo (RBAC) a mappe di Azure

Dopo aver associato un account Azure Maps al tenant di Azure AD, è possibile concedere il controllo di accesso. Il controllo dell'accesso viene concesso assegnando un utente, un gruppo o un'applicazione a uno o più ruoli di controllo di accesso di Azure maps.

1. Passare all' **account Azure Maps**. Selezionare **controllo di accesso (IAM)** , quindi selezionare **assegnazione ruolo**.

    ![Concedere il controllo degli accessi in base al ruolo](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Nella finestra **assegnazione ruolo** selezionare **lettore di data maps di Azure (anteprima)** in **ruolo**. In **assegnare l'accesso per** selezionare **Azure ad utente, gruppo o principio di servizio**. Selezionare l'utente o l'applicazione. Selezionare **Salva**.

    ![Aggiungi un'assegnazione di ruolo](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Visualizzare i ruoli Controllo degli accessi in base al ruolo di Mappe di Azure disponibili

Per visualizzare i ruoli del controllo degli accessi in base al ruolo (RBAC) disponibili per le mappe di Azure, passare a **controllo di accesso (IAM)** , selezionare **ruoli**e quindi cercare i ruoli a partire da **mappe di Azure**. Questi ruoli sono i ruoli a cui è possibile concedere l'accesso.

![Visualizzare i ruoli disponibili](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Visualizza RBAC di mappe di Azure

RBAC fornisce il controllo di accesso granulare.

Per visualizzare gli utenti e le app a cui è stato concesso il controllo degli accessi in base al ruolo per le mappe di Azure, passare a **controllo di accesso (IAM)** , selezionare **assegnazioni di ruolo**e quindi filtrare in base alle **mappe**

![Visualizza utenti e app concessi RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Richiedere token per Mappe di Azure

Dopo aver registrato l'app e averla associata a Maps di Azure, è possibile richiedere i token di accesso.

* Se l'applicazione usa l'autenticazione basata su token utente con Azure Maps Web SDK, è necessario configurare la pagina HTML con l'ID client Azure Maps e l'ID app Azure AD.

* Se l'applicazione usa l'autenticazione del server o dell'applicazione, è necessario richiedere un token da Azure AD endpoint token `https://login.microsoftonline.com` con l'ID di risorsa Azure AD `https://atlas.microsoft.com/`, l'ID client di Azure Maps, l'ID app Azure AD e la password o il certificato di registrazione dell'app Azure AD.

| Ambiente Azure   | Endpoint token Azure AD | ID risorsa di Azure |
| --------------------|-------------------------|-------------------|
| Azure Public        | https://login.microsoftonline.com | https://atlas.microsoft.com/ |
| Azure per enti pubblici    | https://login.microsoftonline.us  | https://atlas.microsoft.com/ | 

Per ulteriori informazioni sulla richiesta di token di accesso da Azure AD, per gli utenti e le entità servizio, vedere [scenari di autenticazione per Azure ad](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'autenticazione di Azure AD e Web SDK per Mappe di Azure, vedere [Usare il controllo mappa di Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Informazioni su come visualizzare le metriche di utilizzo delle API per l'account Mappe di Azure:
> [!div class="nextstepaction"] 
> [Visualizzare le metriche di utilizzo](how-to-view-api-usage.md)

Per un elenco di esempi che illustrano come integrare Azure Active Directory (AAD) con mappe di Azure, vedere:

> [!div class="nextstepaction"]
> [Esempi di autenticazione Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
