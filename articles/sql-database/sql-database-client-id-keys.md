---
title: Ottenere valori per l'autenticazione delle app - Database SQL di Azure | Microsoft Docs
description: Creare un'entità servizio per l'accesso al database SQL dal codice.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: ''
manager: digimobile
origin.date: 03/12/2019
ms.date: 04/08/2019
ms.openlocfilehash: 1d60e875b12f02c957ebd6259eb0e7267f23ee51
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66150207"
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Ottenere i valori richiesti per l'autenticazione di un'applicazione per l'accesso al database SQL dal codice

Per creare e gestire database SQL dal codice è necessario registrare l'app nel dominio di Azure Active Directory (AAD) nella sottoscrizione in cui sono state create le risorse di Azure.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Creare un'entità servizio per accedere alle risorse da un'applicazione

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure PowerShell per Resource Manager è ancora supportato dal Database SQL di Azure, ma i progetti di sviluppo future è per il modulo Az.Sql. Per questi cmdlet, vedere [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli AzureRm sono sostanzialmente identici.

Lo script di PowerShell seguente permette di creare l'applicazione Active Directory (AD) e l'entità servizio necessarie per autenticare l'app C#. Lo script restituisce i valori necessari per l'esempio di C# precedente. Per informazioni dettagliate, vedere [Usare Azure PowerShell per creare un'entità servizio per accedere alle risorse](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

    # Sign in to Azure.
    Connect-AzAccount -EnvironmentName AzureChinaCloud

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create an AAD app
    $azureAdApplication = New-AzADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>Vedere anche 
* [Usare C# per creare un database SQL](sql-database-get-started-csharp.md)
* [Connettersi al Database SQL utilizzando l’autenticazione di Azure Active Directory](sql-database-aad-authentication.md)

