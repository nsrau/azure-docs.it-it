---
title: Librerie di gestione di Hub eventi di Azure | Microsoft Docs
description: Gestire entità e spazi dei nomi di Hub eventi da .NET
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 06/13/2018
ms.author: shvija
ms.openlocfilehash: 1b158fd3ac3f36b71a76c149e8b6815f576be1c5
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "40007303"
---
# <a name="event-hubs-management-libraries"></a>Librerie di gestione di Hub eventi

È possibile usare le librerie di gestione di Hub eventi per effettuare il provisioning dinamico di entità e spazi dei nomi di Hub eventi. Questa modalità dinamica consente distribuzioni complesse e scenari di messaggistica e permette di determinare a livello di codice le entità di cui effettuare il provisioning. Queste librerie sono attualmente disponibili per .NET.

## <a name="supported-functionality"></a>Funzionalità supportate

* Creazione, aggiornamento, eliminazione di spazi dei nomi
* Creazione, aggiornamento, eliminazione di Hub eventi
* Creazione, aggiornamento, eliminazione di gruppi di consumer

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a usare le librerie di gestione di Hub eventi, è necessario eseguire l'autenticazione con Azure Active Directory (AAD). AAD richiede l'autenticazione come entità servizio, che fornisce l'accesso alle risorse di Azure in uso. Per informazioni su come creare un'entità servizio, vedere uno di questi articoli:  

* [Usare il portale di Azure per creare un'applicazione Active Directory e un'entità servizio che accedono alle risorse](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Usare Azure PowerShell per creare un'entità servizio per accedere alle risorse](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Usare l'interfaccia della riga di comando di Azure per creare un'entità servizio per accedere alle risorse](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Nel corso di queste esercitazioni vengono forniti un `AppId` (ID client), un `TenantId` e un `ClientSecret` (chiave di autenticazione) che sono usati per l'autenticazione da parte delle librerie di gestione. È necessario avere autorizzazioni di **Proprietario** per il gruppo di risorse in cui verranno eseguite le librerie.

## <a name="programming-pattern"></a>Modello di programmazione

Il modello di modifica delle risorse di Hub eventi segue un protocollo comune:

1. Ottenere un token da AAD usando la libreria `Microsoft.IdentityModel.Clients.ActiveDirectory`.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Creare l'oggetto `EventHubManagementClient`.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Impostare i parametri `CreateOrUpdate` sui valori specificati.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Effettuare la chiamata.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Passaggi successivi
* [Esempio di gestione .NET](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Riferimento di Microsoft.Azure.Management.EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 
