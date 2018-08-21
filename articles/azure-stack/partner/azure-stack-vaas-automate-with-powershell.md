---
title: Automatizzare la convalida di Azure Stack con PowerShell | Microsoft Docs
description: È possibile automatizzare la convalida di Azure Stack con PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 1cb4b1a7cfd72ea302676244a53af58e77215aa9
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235347"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Automatizzare la convalida di Azure Stack con PowerShell 

Convalida come servizio (VaaS) offre la possibilità di automatizzare l'avvio del test usando il **LaunchVaaSTests.ps1** script.

È possibile usare PowerShell per il flusso di lavoro seguente:

- Flusso di lavoro di superamento test

Questo script include i quattro elementi di un flusso di lavoro:

- Installa i prerequisiti.
- Installa e avvia l'agente locale.
- Avvia una categoria di test, ad esempio l'integrazione, funzionale, affidabilità.
- Generazione del file report ogni test passare risultato per il monitoraggio e report.

## <a name="launch-the-test-pass-workflow"></a>Avviare il flusso di lavoro di passaggi di test

1. Aprire un prompt di PowerShell con privilegi elevato.

2. Eseguire lo script seguente per scaricare lo script di automazione:

    ````PowerShell  
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://vaastestpacksprodeastus.blob.core.windows.net/packages/Microsoft.VaaS.Scripts.3.0.0.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ````

3. Eseguire lo script seguente con i propri valori:

    ````PowerShell  
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>"  -AsPlainText -Force)
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<ServiceAdminUser>", (ConvertTo-SecureString "<ServiceAdminPassword>" -AsPlainText -Force)
    $TenantAdminCreds = New-Object System.Management.Automation.PSCredential "<TenantAdminUser>", (ConvertTo-SecureString "<TenantAdminPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
        -VaaSAccountTenantId <VaaSAccountTenantId> `
        -VaaSSolutionName <VaaSSolutionName> `
        -VaaSTestPassName <VaaSTestPassName> `
        -VaaSTestCategories Integration,Functional `
        -MaxScriptWaitTimeInHours 12 `
        -ServiceAdminCreds $ServiceAdminCreds `
    ````

    **Parameters**

    | Parametro | Description |
    | --- | --- |
    | VaaSUserld | L'ID utente di VaaS. | 
    | VaaSUserPassword | La password VaaS. |
    | ServiceAdminUser | L'account di amministratore del servizio di Azure Stack.  |
    | ServiceAdminPassword | La password di servizio di Azure Stack.  |
    | TenantAdminUser | L'amministratore per il tenant primario.  |
    | TenantAdminPassword | La password per il tenant primario.  |
    | FunctionalCategory| Integrazione, funzionale, o. Affidabilità. Se si usano più valori, separare i valori da una virgola.  |

4. Esaminare i risultati del test. Per informazioni sulla lettura dei risultati dei test, vedere [monitorare i test](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Passaggi successivi

 - Per altre informazioni sulle [convalida Azure Stack come servizio](https://docs.microsoft.com/azure/azure-stack/partner).
 - Per altre informazioni su PowerShell in Azure Stack, vedere la [modulo di Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) riferimento.