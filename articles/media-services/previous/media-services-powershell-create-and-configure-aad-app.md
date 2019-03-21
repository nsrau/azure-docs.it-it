---
title: Usare PowerShell per creare un'app Azure AD per l'accesso all'API Servizi multimediali di Azure | Microsoft Docs
description: Informazioni su come usare PowerShell per creare un'app Azure Active Directory (Azure AD) e configurarla per accedere all'API Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 1da963f8aaf356988df2a7c9bf9923aafa186cca
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259332"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Usare PowerShell per creare un'app Azure AD da usare con l'API Servizi multimediali di Azure

Informazioni su come usare uno script di PowerShell per creare un'applicazione e un'entità servizio di Azure Active Directory (Azure AD) per accedere alle risorse di Servizi multimediali di Azure.  

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Un account Azure. Se non si dispone di un account, iniziare con una [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Account di Servizi multimediali. Per altre informazioni, vedere [Creare un account Servizi multimediali di Azure nel portale di Azure](media-services-portal-create-account.md).

- Azure PowerShell. Per altre informazioni, vedere [Come usare Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Creare un'app Azure AD usando PowerShell  

```powershell
Connect-AzAccount
Import-Module Az.Resources
Set-AzContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

Per altre informazioni, vedere gli articoli seguenti:

- [Usare Azure PowerShell per creare un'entità servizio per accedere alle risorse](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Gestire il controllo degli accessi in base al ruolo usando Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Come configurare manualmente le app daemon usando i certificati](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>Passaggi successivi

Introduzione al [caricamento di file nell'account](media-services-portal-upload-files.md).
