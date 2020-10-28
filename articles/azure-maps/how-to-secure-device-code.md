---
title: Come proteggere il dispositivo vincolato di input con Azure AD e le API REST di Azure Maps
titleSuffix: Azure Maps
description: Come configurare un'applicazione senza browser che supporta l'accesso a Azure AD e chiama le API REST di Azure maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 3833cbfd0802f334e482203d269984eb0e299797
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895631"
---
# <a name="secure-an-input-constrained-device-with-azure-ad-and-azure-maps-rest-apis"></a>Proteggere un dispositivo vincolato di input con Azure AD e le API REST di Azure Maps

Questa guida illustra come proteggere le applicazioni pubbliche o i dispositivi che non possono archiviare in modo sicuro i segreti o accettare l'input del browser. Questi tipi di applicazioni rientrano nella categoria delle cose o Internet delle cose. Alcuni esempi di queste applicazioni possono includere: dispositivi Smart TV o dati dei sensori che emettono applicazioni. 

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Creare una registrazione dell'applicazione in Azure AD

> [!NOTE]
> * **Lettura dei prerequisiti:** [scenario: app desktop che chiama le API Web](../active-directory/develop/scenario-desktop-overview.md)
> * Lo scenario seguente usa il flusso del codice del dispositivo, che non prevede l'acquisizione di un token da parte di un Web browser.

Creare l'applicazione basata su dispositivo in Azure AD per abilitare Azure AD l'accesso. A questa applicazione verrà concesso l'accesso alle API REST di Azure maps.

1. Nell'elenco dei servizi di Azure portale di Azure selezionare **Azure Active Directory**  >  **registrazioni app**  >  **nuova registrazione** .  

    > [!div class="mx-imgBorder"]
    > ![Registrazione delle app](./media/how-to-manage-authentication/app-registration.png)

2. Immettere un **nome** e scegliere **account in questa directory aziendale solo** come **tipo di account supportato** . In **URI di reindirizzamento** specificare **client pubblico/nativo (mobile & desktop)** e quindi aggiungere `https://login.microsoftonline.com/common/oauth2/nativeclient` al valore. Per altri dettagli, vedere Azure AD [app desktop che chiama le API Web: registrazione dell'app](../active-directory/develop/scenario-desktop-app-registration.md). Quindi **registrare** l'applicazione.

    > [!div class="mx-imgBorder"]
    > ![Aggiungere i dettagli di registrazione dell'app per il nome e l'URI di Reindirizzamento](./media/azure-maps-authentication/devicecode-app-registration.png)

3. Passare a **autenticazione** e abilitare **considera applicazione come client pubblico** . In questo modo verrà abilitata l'autenticazione del codice dispositivo con Azure AD.
    
    > [!div class="mx-imgBorder"]
    > ![Abilitare la registrazione dell'app come client pubblico](./media/azure-maps-authentication/devicecode-public-client.png)

4.  Per assegnare le autorizzazioni per le API delegate a mappe di Azure, passare all'applicazione. Quindi selezionare **autorizzazioni API**  >  **Aggiungi un'autorizzazione** . In **API l'organizzazione USA** , cercare e selezionare **mappe di Azure** .

    > [!div class="mx-imgBorder"]
    > ![Aggiungi autorizzazioni API app](./media/how-to-manage-authentication/app-permissions.png)

5. Selezionare la casella di controllo accanto a **Accedi a mappe di Azure** e quindi selezionare **Aggiungi autorizzazioni** .

    > [!div class="mx-imgBorder"]
    > ![Selezionare le autorizzazioni dell'API per le app](./media/how-to-manage-authentication/select-app-permissions.png)

6. Configurare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per utenti o gruppi. Vedere [concedere l'accesso basato sui ruoli per gli utenti ad Azure Maps](#grant-role-based-access-for-users-to-azure-maps).

7. Aggiungere il codice per l'acquisizione del flusso di token nell'applicazione. per informazioni dettagliate sull'implementazione, vedere [flusso del codice del dispositivo](../active-directory/develop/scenario-desktop-acquire-token.md#device-code-flow). Quando si acquisiscono i token, fare riferimento all'ambito: `user_impersonation` selezionato nei passaggi precedenti.

> [!Tip]
> Usare Microsoft Authentication Library (MSAL) per acquisire i token di accesso. Vedere le raccomandazioni sull' [app desktop che chiama le API Web: configurazione del codice](../active-directory/develop/scenario-desktop-app-configuration.md)

8. Comporre la richiesta HTTP con il token acquisito da Azure AD e inviare la richiesta con un client HTTP valido.

### <a name="sample-request"></a>Richiesta di esempio
Di seguito è riportato un esempio di corpo della richiesta per il caricamento di una semplice geometria rappresentata come geometria del cerchio usando un punto centrale e un raggio.

```http
POST /mapData/upload?api-version=1.0&dataFormat=geojson
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

 Il corpo della richiesta di esempio seguente è in GeoJSON:
```json
{
    "type": "FeatureCollection",
    "features": [{
        "type": "Feature",
        "geometry": {
            "type": "Point",
            "coordinates": [-122.126986, 47.639754]
        },
        "properties": {
            "geometryId": "001",
            "radius": 500
        }
    }]
}
```

### <a name="sample-response"></a>Risposta di esempio:

Headers:
```http
Location: https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0
Access-Control-Expose-Headers: Location
```

Corpo:
```json
{
  "operationId": "{operationId}",
  "status": "Succeeded",
  "created": "2020-01-02 1:02:03 AM +00:00",
  "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{resourceId}?api-version=1.0"
}
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Passaggi successivi

Trovare le metriche di utilizzo dell'API per l'account Azure Maps:
> [!div class="nextstepaction"]
> [Visualizzare le metriche di utilizzo](how-to-view-api-usage.md)