---
title: Prerequisiti per le API-Azure Marketplace
description: Prerequisiti per l'uso delle API portale Cloud Partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: dsindona
ms.author: dsindona
ms.date: 07/14/2020
ms.openlocfilehash: b672afcd8f52d8293b1bb81e30097062e2ee49c3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87053586"
---
<a name="api-prerequisites"></a>Prerequisiti per API
================

> [!NOTE]
> Le API portale Cloud Partner sono integrate con e continueranno a funzionare nel centro per i partner. La transizione introduce piccole modifiche. Esaminare le modifiche elencate in [portale cloud partner riferimento API](./cloud-partner-portal-api-overview.md) per assicurarsi che il codice continui a funzionare dopo la transizione al centro per i partner. Le API CPP devono essere usate solo per i prodotti esistenti già integrati prima della transizione al centro per i partner; i nuovi prodotti devono usare le API di invio del centro per i partner.

Esistono due risorse programmatiche necessarie per usare le API del portale Cloud Partner: un'entità servizio e un token di accesso Azure Active Directory (Azure AD).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Creare un'entità servizio nel tenant di Azure Active Directory
----------------------------------------------------------------

In primo luogo, creare un'entità servizio nel tenant di Azure AD. A questo tenant verrà assegnato un proprio set di autorizzazioni nel portale Cloud Partner. Il codice chiamerà le API usando il tenant invece delle credenziali personali.  Per una spiegazione completa della creazione di un'entità servizio, vedere [procedura: usare il portale per creare un'applicazione Azure ad e un'entità servizio che possano accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md).


<a name="add-the-service-principal-to-your-account"></a>Aggiungere l'entità servizio all'account
-----------------------------------------

Ora che è stato creata l'entità servizio nel tenant, aggiungerla come utente all'account del portale Cloud Partner. Esattamente come un utente, l'entità servizio può agire come proprietario o come collaboratore del portale.

Usare la procedura seguente per aggiungere l'entità servizio:

1. Accedere al portale Cloud Partner. 
2. Fare clic su **Utenti** nella barra dei menu a sinistra e scegliere **Aggiungi utente**.

   ![Aggiungere un utente al portale](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Selezionare **Entità servizio** dall'elenco a discesa **Tipo** e aggiungere i dettagli seguenti:

-   Un **Nome descrittivo** per l'entità servizio, ad esempio `spAccount`.
-   L'**ID applicazione**. Per trovare questo identificatore, passare alla [portale di Azure](https://portal.azure.com), fare clic su **Azure Active Directory**, scegliere **registrazioni app**, quindi fare clic sull'app.
-   L'**ID tenant**, noto anche come **ID directory**, per il tenant di Azure AD. È possibile trovare questo identificatore nella pagina di Azure Active Directory nel [portale di Azure](https://portal.azure.com), in **Proprietà**.
-   L'**ID oggetto** per l'oggetto entità servizio. È possibile ottenere questo identificatore dal portale di Azure. Passare ad **Azure Active Directory**, scegliere **Registrazioni app**, fare clic sull'app e poi sul nome dell'app in **Applicazione gestita nella directory locale**. Passare quindi alla pagina **Proprietà**, per trovare l'ID oggetto. Assicurarsi di non acquisire l'ID oggetto iniziale sull'app, bensì quello presente all'interno dell'applicazione gestita.
-   Il **Ruolo** associato all'account, viene usato per il Controllo degli accessi in base al ruolo.

     ![Aggiungere un'app gestita al portale](./media/cloud-partner-portal-api-prerequisites/managed-app.png)

1. Fare clic su **Aggiungi** per aggiungere l'entità servizio all'account.

   ![Aggiungere un'entità servizio](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Ottenere un token di accesso di Azure AD
----------------------------

Le API del portale Cloud Partner usano le risorse e i protocolli seguenti durante l'autenticazione:

- Un bearer token Web JSON (JWT) per richiedere l'accesso alle risorse
- Il protocollo[OpenID Connect](https://openid.net/connect/) (OIDC) per verificare l'identità
- [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) come autorità di identità

Esistono due principali approcci per l'acquisizione a livello di codice di un token JWT:

- L'utilizzo di Microsoft Authentication Library (MSAL) per .NET ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Questo approccio di livello superiore è consigliato per gli sviluppatori .NET. 
- La creazione di una richiesta **HTTP POST** all'endpoint Azure AD oauth **token** che si presenta come:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

A questo punto, trasmettere questo token come parte dell'intestazione di autorizzazione per le richieste API.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Per tutte le API in questo riferimento, l'intestazione dell'autorizzazione si presuppone sempre come trasmessa, quindi non esplicitamente menzionata.

Se si verificano errori di autenticazione nella richiesta, vedere [Risoluzione dei problemi relativi a errori di autenticazione](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
