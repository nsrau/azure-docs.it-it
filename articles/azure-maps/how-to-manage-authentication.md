---
title: Gestire l'autenticazione | Mappe Microsoft Azure
description: Usare il portale di Azure per gestire l'autenticazione in mappe Microsoft Azure.
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: dfe73971f29ea362fdd0ddd654e705b622ab1866
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335535"
---
# <a name="manage-authentication-in-azure-maps"></a>Gestire l'autenticazione in Mappe di Azure

Dopo aver creato un account Azure Maps, vengono creati un ID client e le chiavi per supportare l'autenticazione Azure Active Directory (Azure AD) e l'autenticazione con chiave condivisa.

## <a name="view-authentication-details"></a>Visualizzare i dettagli di autenticazione

Dopo aver creato un account Azure Maps, vengono generate le chiavi primarie e secondarie. Si consiglia di usare una chiave primaria come chiave di sottoscrizione quando si [Usa l'autenticazione con chiave condivisa per chiamare Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). È possibile usare una chiave secondaria in scenari quali le modifiche delle chiavi in sequenza. Per altre informazioni, vedere [autenticazione in mappe di Azure](https://aka.ms/amauth).

È possibile visualizzare i dettagli di autenticazione nel portale di Azure. Nell'account scegliere **autenticazione**dal menu **Impostazioni** .

![Dettagli di autenticazione](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Registrare e configurare un'app Azure AD

1. Nell'elenco dei servizi di Azure portale di Azure selezionare **Azure Active Directory** > **registrazioni app** > **nuova registrazione**.  

    ![Registrazione delle app](./media/how-to-manage-authentication/app-registration.png)

1. Se l'app è già stata registrata, continuare con il passaggio successivo. Se l'app non è stata registrata, immettere un **nome**, scegliere un **tipo di account di supporto**e quindi selezionare **registra**.  

    ![Dettagli registrazione app](./media/how-to-manage-authentication/app-create.png)

1. Per assegnare le autorizzazioni per le API delegate a mappe di Azure, passare all'applicazione. In **registrazioni app**selezionare **autorizzazioni** > API**Aggiungi un'autorizzazione**. In **API l'organizzazione USA**, cercare e selezionare **mappe di Azure**.

    ![Aggiungi autorizzazioni API app](./media/how-to-manage-authentication/app-permissions.png)

1. Selezionare la casella di controllo accanto a **Accedi a mappe di Azure**e quindi selezionare **Aggiungi autorizzazioni**.

    ![Selezionare le autorizzazioni dell'API per le app](./media/how-to-manage-authentication/select-app-permissions.png)

1. Completare uno dei passaggi seguenti, a seconda del metodo di autenticazione. 

    * Se l'applicazione usa l'autenticazione basata su token utente con Azure Maps Web SDK, abilitare `oauth2AllowImplicitFlow`. Per abilitarla, nella sezione **manifesto** della registrazione dell'app impostare `oauth2AllowImplicitFlow` su true. 
    
       ![Manifesto dell'app](./media/how-to-manage-authentication/app-manifest.png)

    * Se l'applicazione usa l'autenticazione del server o dell'applicazione, nella pagina di registrazione dell'app passare a **certificati & segreti**. Caricare quindi un certificato di chiave pubblica o creare una password selezionando **nuovo segreto client**. 
    
       ![Creare un segreto client](./media/how-to-manage-authentication/app-keys.png)

        Se si crea una password, dopo aver selezionato **Aggiungi**, copiare la password e archiviarla in modo sicuro. Questa password verrà usata per ottenere i token da Azure AD.

       ![Aggiungere un segreto client](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Concedere il controllo degli accessi in base al ruolo a Maps di Azure

Dopo aver associato un account Azure Maps al tenant di Azure AD, è possibile concedere il controllo di accesso. Si concede il *controllo degli accessi in base al ruolo* assegnando un utente, un gruppo o un'applicazione a uno o più ruoli di controllo di accesso di Azure maps. 

1. Passare all' **account Azure Maps**. Selezionare l'**assegnazione di ruolo** **controllo di accesso (IAM)** > .

    ![Concedere il controllo degli accessi in base al ruolo](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. Nella scheda **assegnazioni di ruolo** , in **ruolo**, selezionare **lettore di data maps di Azure (anteprima)**. In **Assegna accesso a** selezionare **Utente, gruppo o entità servizio di Azure AD**. Selezionare l'utente o l'applicazione. Quindi selezionare **Salva**.

    ![Aggiungi un'assegnazione di ruolo](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Visualizzare i ruoli Controllo degli accessi in base al ruolo di Mappe di Azure disponibili

Per visualizzare i ruoli RBAC disponibili per le mappe di Azure, passare a **controllo di accesso (IAM)**. Selezionare **Roles (ruoli**) e quindi cercare i ruoli che iniziano con *Maps di Azure*. Questi ruoli di Azure Maps sono i ruoli a cui è possibile concedere l'accesso.

![Visualizzare i ruoli disponibili](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Visualizza RBAC di mappe di Azure

RBAC fornisce il controllo di accesso granulare.

Per visualizzare gli utenti e le app a cui è stato concesso il **controllo degli accessi**in base al ruolo per le mappe di Azure, vedere controllo di accesso. Selezionare **assegnazioni di ruolo**, quindi filtrare in base a **Maps di Azure**.

![Visualizza gli utenti e le app a cui è stato concesso RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Richiedere token per Mappe di Azure

Dopo aver registrato l'app e averla associata a Maps di Azure, è possibile richiedere i token di accesso.

Se l'applicazione usa l'autenticazione basata su token utente con Azure Maps Web SDK, configurare la pagina HTML con l'ID client di Azure Maps e l'ID app Azure AD.

Se l'applicazione usa l'autenticazione del server o dell'applicazione, richiedere un token dall'endpoint `https://login.microsoftonline.com`del token Azure ad. Nella richiesta usare i dettagli seguenti: 

* ID risorsa Azure AD`https://atlas.microsoft.com/`
* ID client di mappe di Azure
* ID dell'app di Azure AD
* Password o certificato per la registrazione dell'app Azure AD

| Ambiente Azure   | Endpoint token Azure AD | ID risorsa di Azure |
| --------------------|-------------------------|-------------------|
| Cloud pubblico di Azure        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Cloud di Azure per enti pubblici   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Per ulteriori informazioni sulla richiesta di token di accesso da Azure AD per gli utenti e le entità servizio, vedere [scenari di autenticazione per Azure ad](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Azure ad e Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Trovare le metriche di utilizzo dell'API per l'account Azure Maps:
> [!div class="nextstepaction"] 
> [Visualizzare le metriche di utilizzo](how-to-view-api-usage.md)

Scopri gli esempi che illustrano come integrare Azure AD con mappe di Azure:

> [!div class="nextstepaction"]
> [Esempi di autenticazione Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
