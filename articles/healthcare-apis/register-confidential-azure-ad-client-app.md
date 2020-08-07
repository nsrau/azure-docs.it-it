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
ms.openlocfilehash: 756645d2df22f1222c3004a44e5a46c7a3bc1a2f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852549"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registrare un'applicazione client riservata in Azure Active Directory

In questa esercitazione si apprenderà come registrare un'applicazione client riservata in Azure Active Directory. 

La registrazione di un'applicazione client è una rappresentazione Azure Active Directory di un'applicazione che può essere usata per eseguire l'autenticazione per conto di un utente e richiedere l'accesso alle [applicazioni delle risorse](register-resource-azure-ad-client-app.md). Un'applicazione client riservata è un'applicazione che può essere considerata attendibile per contenere un segreto e presentare il segreto quando si richiedono token di accesso. Esempi di applicazioni riservate sono applicazioni lato server.

Per registrare una nuova applicazione riservata nel portale, seguire questa procedura.

## <a name="app-registrations-in-azure-portal"></a>Registrazioni app in portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel pannello di spostamento a sinistra.

2. Nel pannello **Azure Active Directory** fare clic su **registrazioni app**:

    ![portale di Azure. Nuova registrazione dell'app.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Fare clic sulla **nuova registrazione**.

## <a name="register-a-new-application"></a>Registrare una nuova applicazione

1. Assegnare all'applicazione un nome visualizzato.

2. Fornire un URL di risposta. Questi dettagli possono essere modificati in un secondo momento, ma se si conosce l'URL di risposta dell'applicazione, immetterlo ora.

    ![Nuova registrazione di app client riservate.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>Autorizzazioni delle API

Aggiungere le autorizzazioni API seguenti:

1. Aprire le **autorizzazioni API**:

    ![Client riservato. Autorizzazioni API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. Fare clic su **Aggiungi un'autorizzazione**

3. Selezionare l'API risorse appropriata:

    Per l'API di Azure per FHIR (servizio gestito), fare clic su **API utilizzate dall'organizzazione** e cercare "API di Azure Healthcare". Per il server FHIR open source per Azure, selezionare la [registrazione dell'applicazione della risorsa API FHIR](register-resource-azure-ad-client-app.md):

    ![Client riservato. API personali](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. Selezionare gli ambiti (autorizzazioni) che l'applicazione riservata deve essere in grado di richiedere per conto di un utente:

    ![Client riservato. Autorizzazioni delegate](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>Segreto dell'applicazione

1. Creare un segreto dell'applicazione (segreto client):

    ![Client riservato. Segreto applicazione](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Fornire una descrizione e la durata del segreto.

3. Una volta generato, verrà visualizzato nel portale una sola volta. Prendere nota di questo e archiviarlo in modo sicuro.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come registrare un'applicazione client riservata in Azure Active Directory. A questo punto è possibile distribuire l' [API di Azure per FHIR](fhir-paas-powershell-quickstart.md).

Dopo aver distribuito l'API di Azure per FHIR, è possibile esaminare le altre impostazioni disponibili.
 
>[!div class="nextstepaction"]
>[Distribuire l'API di Azure per FHIR](fhir-paas-powershell-quickstart.md)