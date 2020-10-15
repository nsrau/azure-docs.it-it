---
title: Registrare un'app di servizio in Azure AD-API di Azure per FHIR
description: Informazioni su come registrare un'applicazione client del servizio in Azure Active Directory.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 6f7bf122b292ca144eac406957f19a13c7ba6662
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975825"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Registrare un'applicazione client del servizio in Azure Active Directory

Questo articolo illustra come registrare un'applicazione client del servizio in Azure Active Directory. Le registrazioni delle applicazioni client sono Azure Active Directory rappresentazioni di applicazioni che possono essere usate per autenticare e ottenere i token. Un client del servizio è progettato per essere usato da un'applicazione per ottenere un token di accesso senza l'autenticazione interattiva di un utente. Con determinate autorizzazioni per l'applicazione e l'uso di un segreto dell'applicazione (password) quando si ottengono i token di accesso.

Per creare un nuovo client del servizio, attenersi alla seguente procedura.

## <a name="app-registrations-in-azure-portal"></a>Registrazioni app in portale di Azure

1. Nella [portale di Azure](https://portal.azure.com)passare a **Azure Active Directory**.

2. Selezionare **Registrazioni per l'app**.

    ![portale di Azure. Nuova registrazione dell'app.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Selezionare **Nuova registrazione**.

4. Fornire al client del servizio un nome visualizzato. Le applicazioni client del servizio in genere non usano un URL di risposta.

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="portale di Azure. Nuova registrazione dell'app client del servizio.":::

5. Selezionare **Registra**.

## <a name="api-permissions"></a>Autorizzazioni delle API

Ora che l'applicazione è stata registrata, è necessario selezionare le autorizzazioni API che l'applicazione deve essere in grado di richiedere per conto degli utenti:

1. Selezionare **Autorizzazioni API**.
1. Selezionare **Aggiungi un'autorizzazione**.

    Se si usa l'API di Azure per FHIR, si aggiungerà un'autorizzazione alle API di Azure Healthcare cercando le **API di Azure Healthcare** in **API**usate dall'organizzazione. 

    Se si fa riferimento a un'applicazione di risorse diversa, selezionare la [registrazione dell'applicazione della risorsa API FHIR](register-resource-azure-ad-client-app.md) creata in precedenza in **API personali**.

    :::image type="content" source="media/service-client-app/service-client-org-api.png" alt-text="portale di Azure. Nuova registrazione dell'app client del servizio." lightbox="media/service-client-app/service-client-org-api-expanded.png":::

1. Selezionare gli ambiti (autorizzazioni) che l'applicazione riservata deve essere in grado di richiedere per conto di un utente:

    :::image type="content" source="media/service-client-app/service-client-add-permission.png" alt-text="portale di Azure. Nuova registrazione dell'app client del servizio.":::

1. Concedere il consenso all'applicazione. Se non si dispone delle autorizzazioni necessarie, rivolgersi all'amministratore Azure Active Directory:

    :::image type="content" source="media/service-client-app/service-client-grant-permission.png" alt-text="portale di Azure. Nuova registrazione dell'app client del servizio.":::

## <a name="application-secret"></a>Segreto dell'applicazione

Il client del servizio necessita di un segreto (password) per ottenere un token.

1. Selezionare **Certificati e segreti**.
2. Selezionare **Nuovo segreto client**.

    ![portale di Azure. Segreto client del servizio](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Fornire una descrizione e la durata del segreto (1 anno, 2 anni o mai).

4. Una volta generato, il segreto verrà visualizzato una sola volta nel portale. Annotarlo e archiviarlo in modo sicuro.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come registrare un'applicazione client del servizio in Azure Active Directory. Successivamente, testare l'accesso al server FHIR con il post.
 
>[!div class="nextstepaction"]
>[Accedere all'API di Azure per FHIR con Postman](access-fhir-postman-tutorial.md)
