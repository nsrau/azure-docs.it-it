---
title: Automatizzare la convalida di Azure Stack con PowerShell | Microsoft Docs
description: È possibile automatizzare la convalida dello Stack di Azure con PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7f9a4ce4f1e16f69a1d8998e24c1bfe955d17d92
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767108"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Automatizzare la convalida di Azure Stack con PowerShell

Convalida come servizio (VaaS) offre la possibilità di automatizzare l'avvio del test usando il **LaunchVaaSTests.ps1** script.

> [!NOTE]  
> Automazione è disponibile solo per il flusso di lavoro di superamento Test. I flussi di lavoro di convalida del pacchetto e la convalida di soluzioni sono supportate solo tramite il portale VaaS.

Questo script può essere usato per:

> [!div class="checklist"]
> * Installare i componenti prerequisiti
> * Installare e avviare l'agente locale
> * Avviare una categoria di test, ad esempio *integration*, *funzionale*, *affidabilità*
> * Segnalare i risultati di test

## <a name="launch-the-test-pass-workflow"></a>Avviare il flusso di lavoro di superamento Test

1. Aprire un prompt di PowerShell con privilegi elevato.

2. Eseguire lo script seguente per scaricare lo script di automazione:

    ```PowerShell
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ```

3. Eseguire lo script seguente con i valori dei parametri appropriati:

    ```PowerShell
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
                          -VaaSAccountTenantId <VaaSAccountTenantId> `
                          -VaaSSolutionName <VaaSSolutionName> `
                          -VaaSTestPassName <VaaSTestPassName> `
                          -VaaSTestCategories Integration,Functional `
                          -MaxScriptWaitTimeInHours 12 `
                          -ServiceAdminUserName <AzSServiceAdminUser> `
                          -ServiceAdminUserPassword <AzSServiceAdminPassword> `
                          -TenantAdminUserName <AzSTenantAdminUser> `
                          -TenantAdminUserPassword <AzSTenantAdminPassword> `
                          -CloudAdminUserName <AzSCloudAdminUser> `
                          -CloudAdminUserPassword <AzSCloudAdminPassword>
    ```

    **Parameters**

    | Parametro | DESCRIZIONE |
    | --- | --- |
    | VaaSUserId | L'ID utente di VaaS. |
    | VaaSUserPassword | La password VaaS. |
    | VaaSAccountTenantId | Il GUID del tenant VaaS. |
    | VaaSSolutionName | Verrà eseguito il nome della soluzione VaaS in cui il test viene superato. |
    | VaaSTestPassName | Il nome del test VaaS passare flusso di lavoro da creare. |
    | VaaSTestCategories | `Integration`, `Functional`, o. `Reliability`. Se si usano più valori, separare i valori da una virgola.  |
    | ServiceAdminUserName | L'account di amministratore del servizio di Azure Stack.  |
    | ServiceAdminPassword | La password di servizio di Azure Stack.  |
    | TenantAdminUserName | L'amministratore per il tenant primario.  |
    | TenantAdminPassword | La password per il tenant primario.  |
    | CloudAdminUserName | Il nome utente amministratore cloud.  |
    | CloudAdminPassword | La password per l'amministratore del cloud.  |

4. Esaminare i risultati del test. Per altre opzioni, vedere [monitorare e gestire i test nel portale di VaaS](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su PowerShell in Azure Stack, esaminare i moduli più recenti.

> [!div class="nextstepaction"]
> [Modulo di Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
