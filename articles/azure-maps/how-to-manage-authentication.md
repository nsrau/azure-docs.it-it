---
title: Gestire l'autenticazione Mappe di Microsoft Azure
description: Usare il portale di Azure per gestire l'autenticazione in Microsoft Azure Maps.Use the Azure portal to manage authentication in Microsoft Azure Maps.
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

Dopo aver creato un account di Azure Maps, vengono creati un ID client e chiavi per supportare l'autenticazione di Azure Active Directory (Azure AD) e l'autenticazione con chiave condivisa.

## <a name="view-authentication-details"></a>Visualizzare i dettagli di autenticazione

Dopo aver creato un account di Azure Maps, vengono generate le chiavi primarie e secondarie. È consigliabile usare una chiave primaria come chiave di sottoscrizione quando si [usa l'autenticazione con chiave condivisa per chiamare Azure Maps.](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication) È possibile utilizzare una chiave secondaria in scenari quali il rollforward delle modifiche della chiave. Per altre informazioni, vedere Autenticazione in Mappe di Azure.For more information, see [Authentication in Azure Maps.](https://aka.ms/amauth)

È possibile visualizzare i dettagli di autenticazione nel portale di Azure.You can view your authentication details in the Azure portal. Nel tuo account, nel menu **Impostazioni,** seleziona **Autenticazione**.

![Dettagli di autenticazione](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Registrare e configurare un'app di Azure ADRegister and configure an Azure AD app

1. Nell'elenco dei servizi di Azure del portale di Azure selezionare > **Registrazioni nuove registrazioni****dell'app** **Azure Active Directory.** >   

    ![Registrazione delle app](./media/how-to-manage-authentication/app-registration.png)

1. Se hai già registrato l'app, vai al passaggio successivo. Se l'app non è stata registrata, immettere un **Nome**, scegliere un tipo di account di **supporto**e quindi selezionare **Registra**.  

    ![Dettagli di registrazione dell'app](./media/how-to-manage-authentication/app-create.png)

1. Per assegnare autorizzazioni API delegate ad Azure Maps, passare all'applicazione. In **Registrazioni app**selezionare **Autorizzazioni** > API**Aggiungere un'autorizzazione**. In **API utilizzate dall'organizzazione**cercare e selezionare Mappe di **Azure.**

    ![Aggiungere le autorizzazioni dell'API dell'appAdd app API permissions](./media/how-to-manage-authentication/app-permissions.png)

1. Selezionare la casella di controllo accanto a Mappe di **Access Azure**e quindi selezionare **Aggiungi autorizzazioni**.

    ![Selezionare le autorizzazioni dell'API dell'app](./media/how-to-manage-authentication/select-app-permissions.png)

1. Completare uno dei passaggi seguenti, a seconda del metodo di autenticazione. 

    * Se l'applicazione utilizza l'autenticazione del token `oauth2AllowImplicitFlow`utente con Azure Maps Web SDK, abilitare . Per abilitarla, nella sezione **Manifesto** della `oauth2AllowImplicitFlow` registrazione dell'app, impostare su true. 
    
       ![Manifesto dell'app](./media/how-to-manage-authentication/app-manifest.png)

    * Se l'applicazione usa l'autenticazione del server o dell'applicazione, nella pagina di registrazione dell'app passare a **Certificati & segreti**. Caricare quindi un certificato di chiave pubblica o creare una password selezionando **Nuovo segreto client**. 
    
       ![Creare un segreto client](./media/how-to-manage-authentication/app-keys.png)

        Se si crea una password, dopo aver selezionato **Aggiungi**, copiare la password e archiviarla in modo sicuro. Questa password verrà usata per ottenere token da Azure AD.

       ![Aggiungere un segreto client](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Concedere il controllo degli accessi in base al ruolo ad Azure MapsGrant role-based access control to Azure Maps

Dopo aver associato un account di Azure Maps al tenant di Azure AD, è possibile concedere il controllo di accesso. Il controllo degli accessi in base al ruolo viene concesso assegnando un utente, un gruppo o un'applicazione a uno o più ruoli di controllo di accesso di Azure Maps.You *grant role-based access control* (RBAC) by assigning a user, group, or application to one or more Azure Maps access control roles. 

1. Passare **all'account di Azure Maps**. Selezionare**Assegnazione ruolo** **controllo di accesso (IAM).** > 

    ![Concedere il controllo degli accessi in base al ruolo](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. Nella scheda **Assegnazioni ruolo,** in **Ruolo,** selezionare **Lettore data di Azure Maps (anteprima)**. In **Assegna accesso a** selezionare **Utente, gruppo o entità servizio di Azure AD**. Selezionare l'utente o l'applicazione. Quindi selezionare **Salva**.

    ![Aggiungi un'assegnazione di ruolo](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Visualizzare i ruoli Controllo degli accessi in base al ruolo di Mappe di Azure disponibili

Per visualizzare i ruoli RBAC disponibili per Azure Maps, passare a **Controllo di accesso (IAM)**. Selezionare **Ruoli**e quindi cercare i ruoli che iniziano con *Azure Maps.* Questi ruoli di Mappe di Azure sono i ruoli a cui è possibile concedere l'accesso.

![Visualizzare i ruoli disponibili](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Visualizzare il controllo degli accessi in base al ruolo di Azure MapsView

Il controllo degli accessi in base al ruolo fornisce un controllo granulare degli accessi.

Per visualizzare gli utenti e le app a cui è stato concesso il controllo degli accessi in base al ruolo per Azure Maps, passare a **Controllo di accesso (IAM).** Selezionare **Assegnazioni di**ruolo e quindi filtrare in base a Mappe di **Azure**.

![Visualizzare gli utenti e le app a cui è stato concesso il controllo degli accessi in base al ruolo](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Richiedere token per Mappe di Azure

Dopo aver registrato l'app e averla associata ad Azure Maps, è possibile richiedere i token di accesso.

Se l'applicazione usa l'autenticazione con token utente con Azure Maps Web SDK, configurare la pagina HTML con l'ID client di Azure Maps e l'ID app di Azure AD.

Se l'applicazione usa l'autenticazione del server o `https://login.microsoftonline.com`dell'applicazione, richiedere un token dall'endpoint del token di Azure AD. Nella tua richiesta, utilizza i seguenti dettagli: 

* Azure AD resource ID`https://atlas.microsoft.com/`
* Azure Maps client ID
* ID dell'app di Azure AD
* Password o certificato di registrazione dell'app Azure ADAzure AD app registration password or certificate

| Ambiente di AzureAzure environment   | Endpoint token Azure ADAzure AD token endpoint | ID risorsa di AzureAzure resource ID |
| --------------------|-------------------------|-------------------|
| Cloud pubblico di AzureAzure public cloud        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Cloud di Azure per enti pubbliciAzure Government cloud   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Per altre informazioni sulla richiesta di token di accesso da Azure AD per utenti ed entità servizio, vedere Scenari di [autenticazione per Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Azure AD e Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Trovare le metriche di utilizzo delle API per l'account Di Azure Maps:Find the API usage metrics for your Azure Maps account:
> [!div class="nextstepaction"] 
> [Visualizzare le metriche di utilizzo](how-to-view-api-usage.md)

Esplorare esempi che illustrano come integrare Azure AD con Azure Maps:Explore samples that show how to integrate Azure AD with Azure Maps:

> [!div class="nextstepaction"]
> [Esempi di autenticazione di Azure ADAzure AD authentication samples](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
