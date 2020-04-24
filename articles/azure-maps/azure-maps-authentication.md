---
title: Metodi di autenticazione | Mappe Microsoft Azure
description: In questo articolo si apprenderà come Azure Active Directory (Azure AD) e l'autenticazione con chiave condivisa. Entrambi vengono usati per i servizi di Microsoft Azure maps. Informazioni su come ottenere la chiave di sottoscrizione di Azure maps.
author: philmea
ms.author: philmea
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 21d29cba85adfc147ec9deb6ab362a5da943bf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335709"
---
# <a name="authentication-with-azure-maps"></a>Autenticazione con Mappe di Azure

Azure Maps supporta due modi per autenticare le richieste: autenticazione con chiave condivisa e autenticazione Azure Active Directory. Questo articolo illustra questi metodi di autenticazione per semplificare l'implementazione dei servizi di Azure maps.

> [!NOTE]
> Per migliorare la comunicazione protetta con le mappe di Azure, è ora supportato Transport Layer Security (TLS) 1,2 e il supporto per TLS 1,0 e 1,1 è in fase di ritiro. Per evitare interruzioni del servizio, **aggiornare i server e le applicazioni per l'uso di TLS 1,2 prima del 2 aprile 2020**.  Se attualmente si usa TLS 1. x, valutare la conformità di TLS 1,2 e sviluppare un piano di migrazione con i test descritti in [risoluzione del problema tls 1,0](https://docs.microsoft.com/security/solving-tls1-problem).

## <a name="shared-key-authentication"></a>Autenticazione con chiave condivisa

 Le chiavi primarie e secondarie vengono generate dopo la creazione dell'account Azure maps. Si consiglia di usare la chiave primaria come chiave di sottoscrizione quando si chiamano le mappe di Azure usando l'autenticazione con chiave condivisa. L'autenticazione con chiave condivisa passa una chiave generata da un account Azure Maps a un servizio Maps di Azure. Per ogni richiesta ai servizi di Azure Maps, aggiungere la *chiave di sottoscrizione* come parametro all'URL. La chiave secondaria può essere usata in scenari come le modifiche delle chiavi in sequenza.  

Per informazioni sulla visualizzazione delle chiavi nell'portale di Azure, vedere [Manage Authentication](https://aka.ms/amauthdetails).

> [!Tip]
> È consigliabile rigenerare le chiavi regolarmente. Vengono fornite due chiavi, in modo che sia possibile mantenere le connessioni con una chiave durante la rigenerazione dell'altra. Quando si rigenerano le chiavi, è necessario aggiornare tutte le applicazioni che accedono all'account con le nuove chiavi.

## <a name="authentication-with-azure-active-directory-preview"></a>Autenticazione con Azure Active Directory (anteprima)

Azure Maps offre ora richieste di autenticazione per i servizi di Azure Maps usando [Azure Active Directory (Azure ad)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Azure AD fornisce l'autenticazione basata sull'identità, incluso il [controllo degli accessi in base al ruolo (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). Il controllo degli accessi in base al ruolo viene usato per concedere l'accesso a livello di utente, a livello di gruppo o di applicazione alle risorse di Azure maps. Le sezioni successive illustrano i concetti e i componenti dell'integrazione di Azure Maps con Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Autenticazione con token di accesso OAuth

Mappe di Azure accetta token di accesso **OAuth 2.0** per i tenant di Azure AD associati a una sottoscrizione di Azure contenente un account di Mappe di Azure. Azure Maps accetta anche i token per:

* Utenti Azure AD
* Applicazioni partner che usano le autorizzazioni delegate dagli utenti
* Identità gestite per le risorse di Azure

Mappe di Azure genera un *identificatore univoco (ID client)* per ogni account Mappe di Azure. È possibile richiedere token da Azure AD quando si combina questo ID client con parametri aggiuntivi. Per richiedere un token, specificare i valori nella tabella seguente in base all'ambiente di Azure.

| Ambiente Azure   | Endpoint token Azure AD |
| --------------------|-------------------------|
| Azure Public        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Per altre informazioni su come configurare Azure AD e richiedere i token per Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Per informazioni generali su come richiedere token da Azure AD, vedere [Informazioni sull'autenticazione](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Richiedere risorse di Mappe di Azure con token OAuth

Dopo che Azure AD riceve un token, Azure Maps invia una richiesta con il seguente set di intestazioni di richiesta obbligatorie:

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

In Azure AD usare RBAC per controllare l'accesso alle risorse protette. Configurare l'account Azure Maps e registrare le mappe di Azure Azure AD tenant. Azure Maps supporta il controllo di accesso in lettura per singoli utenti, gruppi, applicazioni, risorse di Azure e servizi di Azure di Azure AD tramite identità gestite per le risorse di Azure. Nella pagina del portale mappe di Azure è possibile configurare il controllo degli accessi in base al ruolo per i ruoli scelti.

![Lettore di dati per Mappe di Azure (anteprima)](./media/azure-maps-authentication/concept.png)

Per informazioni su come visualizzare le impostazioni del controllo degli accessi in base al ruolo, vedere [Come configurare il controllo degli accessi in base al ruolo per Mappe di Azure](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Identità gestite per risorse di Azure e Mappe di Azure

[Le identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) offrono servizi di Azure con un'identità gestita automaticamente, che può essere autorizzata ad accedere ai servizi di Azure maps. Alcuni esempi di identità gestite includono: servizio app Azure, funzioni di Azure e macchine virtuali di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'autenticazione di un'applicazione con Azure AD e Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Per altre informazioni sull'autenticazione del controllo mappa di Mappe di Azure e di Azure AD, vedere [Usare il controllo mappa di Mappe di Azure](https://aka.ms/amaadmc).
