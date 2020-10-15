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
ms.openlocfilehash: f39fb5766965e3881068bd6d2fd3a8142f9eb2ac
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975910"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Registrare un'applicazione client pubblica in Azure Active Directory

Questo articolo illustra come registrare un'applicazione pubblica in Azure Active Directory.  

Le registrazioni delle applicazioni client sono Azure Active Directory rappresentazioni di applicazioni in grado di autenticare e richiedere autorizzazioni API per conto di un utente. I client pubblici sono applicazioni come applicazioni per dispositivi mobili e applicazioni JavaScript a pagina singola che non possono rimanere riservati ai segreti. La procedura è simile alla [registrazione di un client riservato](register-confidential-azure-ad-client-app.md), ma poiché i client pubblici non possono essere considerati attendibili per contenere un segreto dell'applicazione, non è necessario aggiungerne uno.

La Guida introduttiva fornisce informazioni generali su come [registrare un'applicazione con la piattaforma di identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

## <a name="app-registrations-in-azure-portal"></a>Registrazioni app in portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel pannello di spostamento a sinistra.

2. Nel pannello **Azure Active Directory** fare clic su **registrazioni app**:

    ![portale di Azure. Nuova registrazione dell'app.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Fare clic sulla **nuova registrazione**.

## <a name="application-registration-overview"></a>Panoramica della registrazione dell'applicazione

1. Assegnare all'applicazione un nome visualizzato.

2. Fornire un URL di risposta. L'URL di risposta è il punto in cui i codici di autenticazione verranno restituiti all'applicazione client. È possibile aggiungere altri URL di risposta e modificare quelli esistenti in un secondo momento.

    ![portale di Azure. Nuova registrazione dell'app pubblica.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)


Per configurare un'applicazione [Desktop](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), per [dispositivi mobili](https://docs.microsoft.com/azure/active-directory/develop/scenario-mobile-app-registration) o a [singola pagina](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) come applicazione pubblica:

1. Nel [portale di Azure](https://portal.azure.com), in **registrazioni app**Selezionare l'app e quindi selezionare **autenticazione**.

2. Selezionare **Impostazioni avanzate**  >  **tipo di client predefinito**. Per **considera applicazione come client pubblico**, selezionare **Sì**.

3. Per un'applicazione a singola pagina, selezionare **token di accesso** e **token ID** per abilitare il flusso implicito.

   - Se l'applicazione consente l'accesso agli utenti, selezionare **Token ID**.
   - Se l'applicazione deve anche chiamare un'API Web protetta, selezionare **Token di accesso**.

## <a name="api-permissions"></a>Autorizzazioni delle API

Analogamente all' [applicazione client riservata](register-confidential-azure-ad-client-app.md), è necessario selezionare le autorizzazioni API che l'applicazione deve essere in grado di richiedere per conto degli utenti:

1. Aprire le **autorizzazioni dell'API**.

    Se si usa l'API di Azure per FHIR, si aggiungerà un'autorizzazione alle API di Azure Healthcare cercando le API di Azure Healthcare in **API**usate dall'organizzazione. Questa operazione sarà possibile solo se è già stata [distribuita l'API di Azure per FHIR](fhir-paas-powershell-quickstart.md).

    
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
