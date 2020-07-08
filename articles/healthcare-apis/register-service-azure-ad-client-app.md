---
title: Registrare un'app di servizio in Azure AD-API di Azure per FHIR
description: Informazioni su come registrare un'applicazione client del servizio in Azure Active Directory che può essere usata per autenticare e ottenere i token.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 83514ea13ca51e6980a1b4b60d4555974db5a240
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871081"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Registrare un'applicazione client del servizio in Azure Active Directory

Questo articolo illustra come registrare un'applicazione client del servizio in Azure Active Directory. Le registrazioni delle applicazioni client sono Azure Active Directory rappresentazioni di applicazioni che possono essere usate per autenticare e ottenere i token. Un client del servizio è progettato per essere usato da un'applicazione per ottenere un token di accesso senza l'autenticazione interattiva di un utente. Con determinate autorizzazioni per l'applicazione e l'uso di un segreto dell'applicazione (password) quando si ottengono i token di accesso.

Attenersi alla procedura seguente per creare un nuovo client del servizio.

## <a name="app-registrations-in-azure-portal"></a>Registrazioni app in portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel pannello di spostamento a sinistra.

2. Nel pannello **Azure Active Directory** fare clic su **registrazioni app**:

    ![portale di Azure. Nuova registrazione dell'app.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Fare clic su **Nuova registrazione**.

## <a name="service-client-application-details"></a>Dettagli dell'applicazione client del servizio

* Il client del servizio necessita di un nome visualizzato ed è anche possibile specificare un URL di risposta, ma in genere non verrà usato.

    ![portale di Azure. Nuova registrazione dell'app client del servizio.](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>Autorizzazioni delle API

Sarà necessario concedere i ruoli applicazione client del servizio. 

1. Aprire le **autorizzazioni API** e selezionare la [registrazione dell'applicazione per le risorse dell'API FHIR](register-resource-azure-ad-client-app.md). Se si usa l'API di Azure per FHIR, si aggiungerà un'autorizzazione alle API di Azure Healthcare cercando le API di Azure Healthcare in **API**usate dall'organizzazione.

    ![portale di Azure. Autorizzazioni API client del servizio](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. Selezionare i ruoli applicazione da quelli definiti nell'applicazione della risorsa:

    ![portale di Azure. Autorizzazioni applicazione client servizio](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. Concedere il consenso all'applicazione. Se non si dispone delle autorizzazioni necessarie, rivolgersi all'amministratore Azure Active Directory:

    ![portale di Azure. Consenso dell'amministratore del client del servizio](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>Segreto dell'applicazione

Il client del servizio necessita di un segreto (password), che sarà usato per ottenere i token.

1. Fare clic su **certificati &amp; segreti**

2. Fare clic su **Nuovo segreto client**

    ![portale di Azure. Segreto client del servizio](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Specificare la durata del segreto.

4. Una volta generato, verrà visualizzato una sola volta nel portale. Annotarlo e archiviarlo in modo sicuro.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come registrare un'applicazione client del servizio in Azure Active Directory. Distribuire quindi un'API FHIR in Azure.
 
>[!div class="nextstepaction"]
>[Distribuire il server FHIR Open Source](fhir-oss-powershell-quickstart.md)