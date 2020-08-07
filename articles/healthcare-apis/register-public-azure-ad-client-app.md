---
title: Registrare un'app client pubblica in Azure AD-API di Azure per FHIR
description: Questo articolo illustra come registrare un'applicazione client pubblica in Azure Active Directory, in preparazione per la distribuzione dell'API FHIR in Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: df1e6159baafc11c4b73c33feaf936784c05469e
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853042"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Registrare un'applicazione client pubblica in Azure Active Directory

Questo articolo illustra come registrare un'applicazione pubblica in Azure Active Directory.  

Le registrazioni delle applicazioni client sono Azure Active Directory rappresentazioni di applicazioni in grado di autenticare e richiedere autorizzazioni API per conto di un utente. I client pubblici sono applicazioni come applicazioni per dispositivi mobili e applicazioni JavaScript a pagina singola che non possono rimanere riservati ai segreti. La procedura è simile alla [registrazione di un client riservato](register-confidential-azure-ad-client-app.md), ma poiché i client pubblici non possono essere considerati attendibili per contenere un segreto dell'applicazione, non è necessario aggiungerne uno.

## <a name="app-registrations-in-azure-portal"></a>Registrazioni app in portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel pannello di spostamento a sinistra.

2. Nel pannello **Azure Active Directory** fare clic su **registrazioni app**:

    ![portale di Azure. Nuova registrazione dell'app.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Fare clic sulla **nuova registrazione**.

## <a name="application-registration-overview"></a>Panoramica della registrazione dell'applicazione

1. Assegnare all'applicazione un nome visualizzato.

2. Fornire un URL di risposta. L'URL di risposta è il punto in cui i codici di autenticazione verranno restituiti all'applicazione client. È possibile aggiungere altri URL di risposta e modificare quelli esistenti in un secondo momento.

    ![portale di Azure. Nuova registrazione dell'app pubblica.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)

## <a name="api-permissions"></a>Autorizzazioni delle API

Analogamente all' [applicazione client riservata](register-confidential-azure-ad-client-app.md), è necessario selezionare le autorizzazioni API che l'applicazione deve essere in grado di richiedere per conto degli utenti:

1. Aprire le **autorizzazioni dell'API**.

    Se si usa l'API di Azure per FHIR, si aggiungerà un'autorizzazione alle API di Azure Healthcare cercando le API di Azure Healthcare in **API** usate dall'organizzazione (immagine seguente).
    
    Se si fa riferimento a un'applicazione di risorse diversa, selezionare la [registrazione dell'applicazione della risorsa API FHIR](register-resource-azure-ad-client-app.md) creata in precedenza in **API personali**:

    ![portale di Azure. Nuove autorizzazioni per le API pubbliche-API di Azure per FHIR predefinita](media/public-client-app/api-permissions.png)


2. Selezionare le autorizzazioni che si desidera che l'applicazione possa richiedere: ![ portale di Azure. Autorizzazioni delle app](media/public-client-app/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>Convalida autorità server FHIR
Se l'applicazione registrata in questo articolo e il server FHIR si trovano nello stesso tenant di Azure AD, è opportuno procedere con i passaggi successivi.

Se si configura l'applicazione client in un tenant di Azure AD diverso dal server FHIR, sarà necessario aggiornare l' **autorità**. Nell'API di Azure per FHIR è possibile impostare l'autorità in impostazioni--> l'autenticazione. Impostare l'autorità su **https://login.microsoftonline.com/\<TENANT-ID>** .

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come registrare un'applicazione client pubblica in Azure Active Directory. Successivamente, testare l'accesso al server FHIR con il post.
 
>[!div class="nextstepaction"]
>[Accedere all'API di Azure per FHIR con Postman](access-fhir-postman-tutorial.md)
