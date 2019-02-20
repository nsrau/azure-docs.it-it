---
title: Gestire l'autenticazione in Mappe di Azure | Microsoft Docs
description: È possibile usare il portale di Azure per gestire l'autenticazione in Mappe di Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 68c3c8ac39f5803e01ee1038ec85ddb96ac80b30
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242686"
---
# <a name="manage-authentication-in-azure-maps"></a>Gestire l'autenticazione in Mappe di Azure

Dopo aver creato un account Mappe di Azure, vengono creati l'ID client e le chiavi per supportare l'autenticazione con chiave condivisa o di Azure Active Directory (Azure AD).

È possibile trovare i dettagli di autenticazione passando alla pagina **Autenticazione** in **Impostazioni** nel portale di Azure. Passare all'account personale. Selezionare **Autenticazione** dal menu.


## <a name="view-authentication-details"></a>Visualizzare i dettagli di autenticazione

Per visualizzare i dettagli di autenticazione, passare all'opzione **Autenticazione** nel menu delle impostazioni.

![Visualizzare l'autenticazione](./media/how-to-manage-authentication/how-to-view-auth.png)

 Per altre informazioni su Mappe di Azure e l'autenticazione, vedere [Autenticazione con Mappe di Azure](https://aka.ms/amauth).


## <a name="configure-azure-ad-app-registration"></a>Configurare la registrazione dell'app Azure AD

Dopo aver creato un account Mappe di Azure, è necessario un collegamento tra il tenant di Azure AD e la risorsa di Azure Mappe di Azure. 

1. Passare al pannello Azure AD e creare una registrazione di app con un nome e l'URL di accesso corrispondente alla home page dell'app o API Web, ad esempio "https://localhost/". Se si ha già un'app registrata, procedere al passaggio 2.

    ![Registrazione delle app](./media/how-to-manage-authentication/app-registration.png)

    ![Registrazione delle app](./media/how-to-manage-authentication/app-create.png)

2. Assegnare autorizzazioni API delegate a Mappe di Azure passando all'applicazione in Registrazioni app e facendo clic su **Impostazioni**.  Selezionare **Autorizzazioni necessarie** e quindi **Aggiungi**. Cercare e quindi selezionare Mappe di Azure in **Selezionare un'API** e fare clic su **Seleziona**.

    ![Autorizzazioni per l'API dell'app](./media/how-to-manage-authentication/app-permissions.png)

3. Infine, in Selezionare le autorizzazioni scegliere Access Azure Maps (Accesso a Mappe di Azure) e fare clic su Seleziona.

    ![Selezionare le autorizzazioni per l'API dell'app](./media/how-to-manage-authentication/select-app-permissions.png)

4. Seguire il passaggio a o b, in base all'implementazione dell'autenticazione.

    1. Se l'applicazione userà l'autenticazione basata su token utente con Web SDK per Mappe di Azure, è necessario abilitare `oauthEnableImplicitFlow` impostandolo su true nella sezione Manifesto della pagina dei dettagli della registrazione dell'app.
    
       ![Manifesto dell'app](./media/how-to-manage-authentication/app-manifest.png)

    2. Se l'applicazione usa l'autenticazione server/applicazione, passare al pannello **Chiavi** all'interno della registrazione dell'app e creare una password o caricare un certificato di chiave pubblica nella registrazione dell'app. Se si crea una password, dopo aver salvato,****  copiare la password per usarla in seguito e archiviarla in modo sicuro. Verrà usata usato per acquisire token da Azure AD.

       ![Chiavi dell'app](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Concedere il controllo degli accessi in base al ruolo a Mappe di Azure

Una volta associato un account Mappe di Azure al tenant di Azure AD, è possibile concedere il controllo degli accessi assegnando l'utente o l'applicazione ai ruoli del controllo degli accessi di Mappe di Azure disponibili.

1. Passare all'opzione **Controllo di accesso**, fare clic su **Assegnazioni di ruolo** e quindi su **Aggiungi un'assegnazione di ruolo**.

    ![Concedere il controllo degli accessi in base al ruolo](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Nella finestra popup Assegnazioni di ruolo selezionare il **Ruolo** Lettore di dati per Mappe di Azure (anteprima), in **Assegna accesso a**: selezionare Utente, gruppo o entità servizio di Azure AD, quindi selezionare**** Utente o Applicazione dall'elenco a discesa e fare clic su **Salva**.

    ![Aggiungi un'assegnazione di ruolo](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Visualizzare i ruoli Controllo degli accessi in base al ruolo di Mappe di Azure disponibili

Per visualizzare i ruoli Controllo degli accessi in base al ruolo disponibili per Mappe di Azure a cui è possibile concedere l'accesso, passare all'opzione **Controllo di accesso (IAM)**, fare clic su **Ruoli** e cercare i ruoli che contengono **Mappe di Azure**.

![Visualizzare i ruoli disponibili](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-role-based-access-control-rbac"></a>Visualizzare il controllo degli accessi in base al ruolo di Mappe di Azure

Azure AD consente il controllo granulare degli accessi tramite il controllo degli accessi in base al ruolo. 

Per visualizzare gli utenti o le app a cui è stato concesso il controllo degli accessi in base al ruolo per Mappe Azure, passare all'opzione **Controllo di accesso (IAM)**, selezionare **Assegnazioni di ruolo** e filtrare in base a **Mappe di Azure**. Di seguito compariranno tutti i ruoli disponibili.

![Visualizzare il controllo degli accessi in base al ruolo](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Richiedere token per Mappe di Azure

Una volta registrata e associata l'app con Mappe di Azure, è possibile richiedere i token di accesso.

* Se l'applicazione userà l'autenticazione basata su token utente con Web SDK per Mappe di Azure, è necessario configurare la pagina html con l'ID Client di Mappe di Azure e l'ID app di Azure AD.

* Per le applicazioni che usano l'autenticazione server/applicazione, richiedere un token dall'endpoint di accesso di Azure AD `https://login.microsoftonline.com` con ID risorsa di Azure AD `https://atlas.microsoft.com/`, ID client Mappe di Azure, ID app Azure AD e certificato o password della registrazione dell'app Azure AD.

Per altre informazioni sulla richiesta di token di accesso da Azure AD per utenti ed entità servizio, vedere [Scenari di autenticazione per Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'autenticazione di Azure AD e Web SDK per Mappe di Azure, vedere [Usare il controllo mappa di Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).