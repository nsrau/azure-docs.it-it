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
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 53d74e9979b9f82d7a76d21517f2fd62ac95787a
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54387971"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Automatizzare la convalida di Azure Stack con PowerShell

Convalida come servizio (VaaS) offre la possibilità di automatizzare l'avvio del test usando il **LaunchVaaSTests.ps1** script.

È possibile usare PowerShell per il flusso di lavoro seguente:

- Superamento test

In questa esercitazione descrive come creare uno script che:

> [!div class="checklist"]
> * Installa i prerequisiti
> * Installa e avvia l'agente locale
> * Avvia una categoria di test, ad esempio di integrazione, funzionale, affidabilità
> * I risultati dei test di report

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
