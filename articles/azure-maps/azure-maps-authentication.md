---
title: Metodi di autenticazione | Mappe Microsoft Azure
description: In questo articolo si apprenderà come Azure Active Directory (Azure AD) e l'autenticazione con chiave condivisa. Entrambi vengono usati per i servizi di Microsoft Azure maps. Informazioni su come ottenere la chiave di sottoscrizione di Azure maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 2bcc2d4c92e903b723bffa8461a8a1a10534d3e4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025623"
---
# <a name="authentication-with-azure-maps"></a>Autenticazione con Mappe di Azure

Azure Maps supporta due modi per autenticare le richieste: autenticazione con chiave condivisa e autenticazione Azure Active Directory. Questo articolo illustra questi metodi di autenticazione per guidare il processo di implementazione.

## <a name="shared-key-authentication"></a>Autenticazione con chiave condivisa

L'autenticazione con chiave condivisa passa le chiavi generate da un account Azure Maps a ogni richiesta a Maps di Azure. Per ogni richiesta ai servizi di Azure Maps, la *chiave di sottoscrizione* deve essere aggiunta come parametro all'URL. Le chiavi primarie e secondarie vengono generate dopo la creazione dell'account Azure maps. Si consiglia di usare la chiave primaria come chiave di sottoscrizione quando si chiama Azure Maps usando l'autenticazione con chiave condivisa. La chiave secondaria può essere usata in scenari come le modifiche delle chiavi in sequenza.  

Per informazioni sulla visualizzazione delle chiavi nell'portale di Azure, vedere [Manage Authentication](https://aka.ms/amauthdetails).

> [!Tip]
> È consigliabile rigenerare le chiavi regolarmente. Vengono fornite due chiavi, in modo che sia possibile mantenere le connessioni con una chiave durante la rigenerazione dell'altra. Quando si rigenerano le chiavi, è necessario aggiornare tutte le applicazioni che accedono all'account con le nuove chiavi.



## <a name="authentication-with-azure-active-directory-preview"></a>Autenticazione con Azure Active Directory (anteprima)

Azure Maps offre ora richieste di autenticazione per i servizi di Azure Maps usando [Azure Active Directory (Azure ad)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Azure AD fornisce l'autenticazione basata sull'identità, incluso il [controllo degli accessi in base al ruolo (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). Il controllo degli accessi in base al ruolo viene usato per concedere l'accesso a livello di utente, a livello di gruppo o di applicazione alle risorse di Azure maps. Le sezioni successive consentono di comprendere i concetti e i componenti dell'integrazione di Azure Maps con Azure AD.
## <a name="authentication-with-oauth-access-tokens"></a>Autenticazione con token di accesso OAuth

Mappe di Azure accetta token di accesso **OAuth 2.0** per i tenant di Azure AD associati a una sottoscrizione di Azure contenente un account di Mappe di Azure. Mappe di Azure accetta token per:

* Utenti Azure AD
* Applicazioni partner che usano le autorizzazioni delegate dagli utenti
* Identità gestite per le risorse di Azure

Mappe di Azure genera un *identificatore univoco (ID client)* per ogni account Mappe di Azure. È possibile richiedere token da Azure AD quando si combina questo ID client con parametri aggiuntivi. Per richiedere un token, è necessario specificare i valori nella tabella seguente in base all'ambiente di Azure.

| Ambiente Azure   | Endpoint token Azure AD |
| --------------------|-------------------------|
| Azure Public        | https://login.microsoftonline.com |
| Azure per enti pubblici    | https://login.microsoftonline.us |


Per altre informazioni su come configurare Azure AD e richiedere i token per Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Per informazioni generali su come richiedere token da Azure AD, vedere [Informazioni sull'autenticazione](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Richiedere risorse di Mappe di Azure con token OAuth

Dopo la ricezione di un token da Azure AD, viene inviata una richiesta a Maps di Azure con il seguente set di intestazioni di richiesta obbligatorie:

| Intestazione della richiesta    |    Valore    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Autorizzazione     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` è il GUID basato sull'account di Mappe di Azure che compare nella pagina di autenticazione di Mappe di Azure.

Ecco un esempio di instradamento delle richieste di Mappe di Azure che usa un token OAuth:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Per informazioni sulla visualizzazione dell'ID client, vedere [Visualizzare i dettagli di autenticazione](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Controllare l'accesso con il controllo degli accessi in base al ruolo

In Azure AD usare RBAC per controllare l'accesso alle risorse protette. Configurare l'account Azure Maps e registrare le mappe di Azure Azure AD TENANT. Azure Maps supporta il controllo di accesso in lettura per singoli utenti, gruppi, applicazioni, risorse di Azure e servizi di Azure di Azure AD tramite identità gestite per le risorse di Azure. Nella pagina del portale mappe di Azure è possibile configurare il controllo degli accessi in base al ruolo per i ruoli desiderati.

![Lettore di dati per Mappe di Azure (anteprima)](./media/azure-maps-authentication/concept.png)

Per informazioni su come visualizzare le impostazioni del controllo degli accessi in base al ruolo, vedere [Come configurare il controllo degli accessi in base al ruolo per Mappe di Azure](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Identità gestite per risorse di Azure e Mappe di Azure

[Le identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) offrono servizi di Azure con un'identità gestita automaticamente, che può essere autorizzata ad accedere ai servizi di Azure maps. Alcuni esempi di identità gestite includono: servizio app Azure, funzioni di Azure e macchine virtuali di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'autenticazione di un'applicazione con Azure AD e Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Per altre informazioni sull'autenticazione del controllo mappa di Mappe di Azure e di Azure AD, vedere [Usare il controllo mappa di Mappe di Azure](https://aka.ms/amaadmc).
