---
title: Registrare un'app per le risorse nell'API Azure AD-Azure per FHIR
description: Registrare un'app di risorsa (o API) in Azure Active Directory, in modo che le applicazioni client possano richiedere l'accesso alla risorsa durante l'autenticazione.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 10b951300b8386b057744a980abd5d847b6b6907
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871091"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Registrare un'applicazione della risorsa in Azure Active Directory

Questo articolo illustra come registrare un'applicazione di risorse (o API) in Azure Active Directory. Un'applicazione della risorsa è una rappresentazione Azure Active Directory dell'API del server FHIR e le applicazioni client possono richiedere l'accesso alla risorsa durante l'autenticazione. L'applicazione della risorsa è nota anche come *destinatari* nel gergo OAuth.

## <a name="azure-api-for-fhir"></a>API di Azure per FHIR

Se si usa l'API di Azure per FHIR, viene creata automaticamente un'applicazione della risorsa quando si distribuisce il servizio. Fino a quando si usa l'API di Azure per FHIR nello stesso tenant di Azure Active Directory in cui si distribuisce l'applicazione, è possibile ignorare questa guida dettagliata e distribuire invece l'API di Azure per FHIR per iniziare.

Se si usa un tenant di Azure Active Directory diverso (non associato alla sottoscrizione), è possibile importare l'applicazione della risorsa API di Azure per FHIR nel tenant con PowerShell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

oppure usare l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>Server FHIR per Azure

Se si usa il server FHIR open source per Azure, attenersi alla procedura seguente per registrare un'applicazione della risorsa.

### <a name="app-registrations-in-azure-portal"></a>Registrazioni app in portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel pannello di spostamento a sinistra.

2. Nel pannello **Azure Active Directory** fare clic su **registrazioni app**:

    ![portale di Azure. Nuova registrazione dell'app.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Fare clic sulla **nuova registrazione**.

### <a name="add-a-new-application-registration"></a>Aggiungere una nuova registrazione dell'applicazione

Inserire i dettagli per la nuova applicazione. Non sono previsti requisiti specifici per il nome visualizzato, ma l'impostazione dell'URI del server FHIR rende più semplice trovare:

![Registrazione nuova applicazione](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

### <a name="set-identifier-uri-and-define-scopes"></a>Impostare l'URI dell'identificatore e definire gli ambiti

Un'applicazione della risorsa ha un URI identificatore (URI ID applicazione), che i client possono usare per richiedere l'accesso alla risorsa. Questo valore compilerà l' `aud` attestazione del token di accesso. È consigliabile impostare questo URI come URI del server FHIR. Per le app intelligenti in FHIR, si presuppone che il gruppo di *destinatari* sia l'URI del server FHIR.

1. Fare clic su **esporre un'API**

2. Fare clic su **imposta** accanto a *URI ID applicazione*.

3. Immettere l'URI dell'identificatore e fare clic su **Salva**. Un URI di identificatore valido è l'URI del server FHIR.

4. Fare clic su **Aggiungi un ambito** e aggiungere eventuali ambiti che si desidera definire per l'API. È necessario aggiungere almeno un ambito per concedere le autorizzazioni per l'applicazione della risorsa in futuro. Se non si dispone di ambiti specifici che si desidera aggiungere, è possibile aggiungere user_impersonation come ambito.

![Destinatari e ambito](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

### <a name="define-application-roles"></a>Definire i ruoli applicazione

L'API di Azure per FHIR e il server OSS FHIR per Azure usano [Azure Active Directory ruoli applicazione](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) per il controllo degli accessi in base al ruolo. Per definire i ruoli che devono essere disponibili per l'API del server FHIR, aprire il [manifesto](https://docs.microsoft.com/azure/active-directory/active-directory-application-manifest/)dell'applicazione della risorsa:

1. Fare clic su **manifesto**:

    ![Ruoli applicazione](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. Nella `appRoles` Proprietà aggiungere i ruoli che si desidera includere per gli utenti o le applicazioni:

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User",
          "Application"
        ],
        "description": "FHIR Server Administrators",
        "displayName": "admin",
        "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
        "isEnabled": true,
        "value": "admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "description": "Users who can read",
        "displayName": "reader",
        "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
        "isEnabled": true,
        "value": "reader"
      }
    ],
    ```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come registrare un'applicazione della risorsa in Azure Active Directory. Distribuire quindi l'API di Azure per FHIR.
 
>[!div class="nextstepaction"]
>[Distribuire l'API di Azure per FHIR](fhir-paas-powershell-quickstart.md)