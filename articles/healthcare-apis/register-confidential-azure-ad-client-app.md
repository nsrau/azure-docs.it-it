---
title: Registrare un'app client riservata nell'API Azure AD-Azure per FHIR
description: Registrare un'applicazione client riservata in Azure Active Directory che esegue l'autenticazione per conto di un utente e richiede l'accesso alle applicazioni delle risorse.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: e74271119b581b2bb291b1a9ddd74ad0781855e6
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629139"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registrare un'applicazione client riservata in Azure Active Directory

In questa esercitazione si apprenderà come registrare un'applicazione client riservata in Azure Active Directory. 

La registrazione di un'applicazione client è una rappresentazione Azure Active Directory di un'applicazione che può essere usata per eseguire l'autenticazione per conto di un utente e richiedere l'accesso alle [applicazioni delle risorse](register-resource-azure-ad-client-app.md). Un'applicazione client riservata è un'applicazione che può essere considerata attendibile per contenere un segreto e presentare il segreto quando si richiedono token di accesso. Esempi di applicazioni riservate sono applicazioni lato server.

Per registrare una nuova applicazione riservata nel portale, seguire questa procedura.

## <a name="register-a-new-application"></a>Registrare una nuova applicazione

1. Nella [portale di Azure](https://portal.azure.com)passare a **Azure Active Directory**.

1. Selezionare **Registrazioni per l'app**.

    ![portale di Azure. Nuova registrazione dell'app.](media/how-to-aad/portal-aad-new-app-registration.png)

1. Selezionare **Nuova registrazione**.

1. Assegnare all'applicazione un nome visualizzato.

1. Fornire un URL di risposta. Questi dettagli possono essere modificati in un secondo momento, ma se si conosce l'URL di risposta dell'applicazione, immetterlo ora.

    ![Nuova registrazione di app client riservate.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)
1. Selezionare **Registra**.

## <a name="api-permissions"></a>Autorizzazioni delle API

Ora che l'applicazione è stata registrata, è necessario selezionare le autorizzazioni API che l'applicazione deve essere in grado di richiedere per conto degli utenti:

1. Selezionare **Autorizzazioni API**.

    ![Client riservato. Autorizzazioni delle API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

1. Selezionare **Aggiungi un'autorizzazione**.

    Se si usa l'API di Azure per FHIR, si aggiungerà un'autorizzazione alle API di Azure Healthcare cercando le **API di Azure Healthcare** in **API**usate dall'organizzazione. 

    Se si fa riferimento a un'applicazione di risorse diversa, selezionare la [registrazione dell'applicazione della risorsa API FHIR](register-resource-azure-ad-client-app.md) creata in precedenza in **API personali**.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Client riservato. API dell'organizzazione" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

3. Selezionare gli ambiti (autorizzazioni) che l'applicazione riservata deve essere in grado di richiedere per conto di un utente:

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Client riservato. API dell'organizzazione":::

## <a name="application-secret"></a>Segreto dell'applicazione

1. Selezionare **certificati & segreti**.
1. Selezionare **Nuovo segreto client**. 

    ![Client riservato. Segreto applicazione](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Fornire una descrizione e la durata del segreto (1 anno, 2 anni o mai).

3. Una volta generato, verrà visualizzato nel portale una sola volta. Prendere nota di questo e archiviarlo in modo sicuro.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come registrare un'applicazione client riservata in Azure Active Directory. A questo punto è possibile distribuire l' [API di Azure per FHIR](fhir-paas-powershell-quickstart.md).

Dopo aver distribuito l'API di Azure per FHIR, è possibile esaminare le altre impostazioni disponibili.
 
>[!div class="nextstepaction"]
>[Distribuire l'API di Azure per FHIR](fhir-paas-powershell-quickstart.md)
