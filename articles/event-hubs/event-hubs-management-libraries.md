---
title: Librerie di gestione di Hub eventi di Azure | Documentazione Microsoft
description: "Gestire entità e spazi dei nomi di Hub eventi da .NET"
services: event-hubs
cloud: na
documentationcenter: na
author: jtaubensee
manager: timlt
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 1/6/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: dfd1ae52cc56a4d4b4c7ee3f69f0c454be607401
ms.openlocfilehash: 84075b60074b0607c14787db72c8dff8b701a8ea


---

# <a name="event-hubs-management-libraries"></a>Librerie di gestione di Hub eventi

Le librerie di gestione di Hub eventi possono effettuare il provisioning di entità e spazi dei nomi di Hub eventi in modo dinamico, per agevolare distribuzioni complesse e scenari di messaggistica e consentire di determinare a livello di codice le entità di cui effettuare il provisioning. Queste librerie sono attualmente disponibili per .NET.

## <a name="supported-functionality"></a>Funzionalità supportate

* Creazione, aggiornamento, eliminazione di spazi dei nomi
* Creazione, aggiornamento, eliminazione di Hub eventi
* Creazione, aggiornamento, eliminazione di gruppi di consumer

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a usare le librerie di gestione di Hub eventi, è necessario eseguire l'autenticazione con Azure Active Directory (AAD). AAD richiede l'autenticazione come entità servizio, che fornisce l'accesso alle risorse di Azure in uso. Per informazioni su come creare un'entità servizio, vedere uno di questi articoli:  

* [Usare il portale di Azure per creare un'applicazione Active Directory e un'entità servizio che accedono alle risorse](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Usare Azure PowerShell per creare un'entità servizio per accedere alle risorse](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Usare l'interfaccia della riga di comando di Azure per creare un'entità servizio per accedere alle risorse](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Nel corso di queste esercitazioni verranno forniti un `AppId` (ID client), un `TenantId` e un `ClientSecret` (chiave di autenticazione) che verranno usati per l'autenticazione da parte delle librerie di gestione. È necessario disporre delle autorizzazioni di 'Proprietario' per il gruppo di risorse in cui verranno eseguite le librerie.

## <a name="programming-pattern"></a>Modello di programmazione

Il modello di modifica delle risorse di Hub eventi segue un protocollo comune:

1. Ottenere un token dall'istanza di Azure Active Directory che usa la libreria `Microsoft.IdentityModel.Clients.ActiveDirectory`.
    ```csharp
    var context = new AuthenticationContext($"https://login.windows.net/{tenantId}");

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

1. Impostare i parametri CreateOrUpdate sui valori specificati.
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



<!--HONumber=Jan17_HO3-->


