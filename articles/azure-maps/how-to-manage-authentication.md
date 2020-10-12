---
title: Gestire l'autenticazione
titleSuffix: Azure Maps
description: Acquisire familiarità con l'autenticazione di Azure maps. Vedere quale approccio funziona meglio nello scenario. Informazioni su come usare il portale per visualizzare le impostazioni di autenticazione.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 3383971b9984e6f565feebc3bdfb968fb77cbe85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91311104"
---
# <a name="manage-authentication-in-azure-maps"></a>Gestire l'autenticazione in Mappe di Azure

Dopo aver creato un account Azure Maps, vengono creati un ID client e le chiavi per supportare l'autenticazione Azure Active Directory (Azure AD) e l'autenticazione con chiave condivisa.

## <a name="view-authentication-details"></a>Visualizzare i dettagli di autenticazione

Dopo aver creato un account Azure Maps, vengono generate le chiavi primarie e secondarie. Si consiglia di usare una chiave primaria come chiave di sottoscrizione quando si [Usa l'autenticazione con chiave condivisa per chiamare Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). È possibile usare una chiave secondaria in scenari quali le modifiche delle chiavi in sequenza. Per altre informazioni, vedere [autenticazione in mappe di Azure](https://aka.ms/amauth).

È possibile visualizzare i dettagli di autenticazione nel portale di Azure. Nell'account scegliere **autenticazione**dal menu **Impostazioni** .

> [!div class="mx-imgBorder"]
> ![Dettagli autenticazione](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>Individua categoria e scenario

A seconda delle esigenze dell'applicazione, esistono percorsi specifici per la protezione dell'applicazione. Azure AD definisce le categorie per supportare un'ampia gamma di flussi di autenticazione. Vedere [categorie di applicazioni](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios#application-categories) per comprendere la categoria in cui l'applicazione è più idonea.

> [!NOTE]
> Anche se si usa l'autenticazione con chiave condivisa, le informazioni sulle categorie e sugli scenari consentono di proteggere l'applicazione.

## <a name="determine-authentication-and-authorization"></a>Determinare l'autenticazione e l'autorizzazione

La tabella seguente descrive gli scenari di autenticazione e autorizzazione comuni nelle mappe di Azure. La tabella fornisce un confronto tra i tipi di protezione offerti da ogni scenario.

> [!IMPORTANT]
> Microsoft consiglia di implementare Azure Active Directory (Azure AD) con il controllo degli accessi in base al ruolo (RBAC) per le applicazioni di produzione.

| Scenario                                                                                    | Authentication | Autorizzazione | Lavoro di sviluppo | Lavoro operativo |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [Daemon attendibile/applicazione client non interattiva](./how-to-secure-daemon-app.md)        | Chiave condivisa     | N/D           | Media             | Alto               |
| [Daemon attendibile/applicazione client non interattiva](./how-to-secure-daemon-app.md)        | Azure AD       | Alto          | Basso                | Media             |
| [Applicazione Web a pagina singola con accesso Single Sign-on interattivo](./how-to-secure-spa-users.md) | Azure AD       | Alto          | Medio             | Livello medio             |
| [Applicazione Web a pagina singola con accesso non interattivo](./how-to-secure-spa-app.md)      | Azure AD       | Alto          | Medio             | Livello medio             |
| [Applicazione Web con accesso Single Sign-on interattivo](./how-to-secure-webapp-users.md)          | Azure AD       | Alto          | Alto               | Medio             |
| [Dispositivo a cui è vincolato il dispositivo/input](./how-to-secure-device-code.md)                     | Azure AD       | Alto          | Medio             | Livello medio             |

I collegamenti nella tabella consentono di ottenere informazioni dettagliate sulla configurazione per ogni scenario.

## <a name="view-role-definitions"></a>Visualizzare le definizioni dei ruoli

Per visualizzare i ruoli di Azure disponibili per le mappe di Azure, passare a **controllo di accesso (IAM)**. Selezionare **Roles (ruoli**) e quindi cercare i ruoli che iniziano con *Maps di Azure*. Questi ruoli di Azure Maps sono i ruoli a cui è possibile concedere l'accesso.

> [!div class="mx-imgBorder"]
> ![Visualizzare i ruoli disponibili](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>Visualizzare le assegnazioni di ruolo

Per visualizzare gli utenti e le app a cui è stato concesso il **controllo degli accessi**in base al ruolo per le mappe di Azure, vedere controllo di accesso. Selezionare **assegnazioni di ruolo**, quindi filtrare in base a **Maps di Azure**.

> [!div class="mx-imgBorder"]
> ![Visualizza gli utenti e le app a cui è stato concesso RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Richiedere token per Mappe di Azure

Richiedere un token dall'endpoint del token Azure AD. Nella richiesta Azure AD usare i dettagli seguenti:

| Ambiente Azure      | Endpoint token Azure AD             | ID risorsa di Azure              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Cloud pubblico di Azure     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Cloud di Azure per enti pubblici | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Per ulteriori informazioni sulla richiesta di token di accesso da Azure AD per gli utenti e le entità servizio, vedere scenari di [autenticazione per Azure ad](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios) e visualizzare scenari specifici nella tabella degli [scenari](./how-to-manage-authentication.md#determine-authentication-and-authorization).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Azure ad e Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Trovare le metriche di utilizzo dell'API per l'account Azure Maps:
> [!div class="nextstepaction"]
> [Visualizzare le metriche di utilizzo](how-to-view-api-usage.md)

Scopri gli esempi che illustrano come integrare Azure AD con mappe di Azure:

> [!div class="nextstepaction"]
> [Esempi di autenticazione Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
