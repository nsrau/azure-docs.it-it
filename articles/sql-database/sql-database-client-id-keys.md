---
title: Ottenere valori per l'autenticazione delle app - Database SQL di Azure | Microsoft Docs
description: "Creare un'entità servizio per l'accesso al database SQL dal codice."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
tags: 
ms.assetid: b43e43bb-6660-49e6-b069-abde97eb5770
ms.service: sql-database
ms.custom: develop apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/30/2016
ms.author: sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: ec6256e9c5bb0d9c8d15d0f673cea70b3915eb34
ms.contentlocale: it-it
ms.lasthandoff: 06/22/2017

---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Ottenere i valori richiesti per l'autenticazione di un'applicazione per l'accesso al database SQL dal codice
Per creare e gestire database SQL dal codice è necessario registrare l'app nel dominio di Azure Active Directory (AAD) nella sottoscrizione in cui sono state create le risorse di Azure.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Creare un'entità servizio per accedere alle risorse da un'applicazione
È necessario che sia installata ed eseguita la versione più recente di [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx). Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

Lo script di PowerShell seguente permette di creare l'applicazione Active Directory (AD) e l'entità servizio necessarie per autenticare l'app C#. Lo script restituisce i valori necessari per l'esempio di C# precedente. Per informazioni dettagliate, vedere [Usare Azure PowerShell per creare un'entità servizio per accedere alle risorse](../azure-resource-manager/resource-group-authenticate-service-principal.md).

    # Sign in to Azure.
    Add-AzureRmAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>Vedere anche
* [Usare C# per creare un database SQL](sql-database-get-started-csharp.md)
* [Connettersi al Database SQL utilizzando l’autenticazione di Azure Active Directory](sql-database-aad-authentication.md)


