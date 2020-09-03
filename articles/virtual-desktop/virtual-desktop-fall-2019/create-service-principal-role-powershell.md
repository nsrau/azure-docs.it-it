---
title: Assegnazione di ruolo dell'entità servizio di Desktop virtuale Windows (versione classica) - Azure
description: Come creare entità servizio e assegnare ruoli usando PowerShell in Desktop virtuale Windows (versione classica).
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: lizross
ms.openlocfilehash: 91bb14a174d5bd5c16b38513825579097e1d6f7f
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078529"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell-in-windows-virtual-desktop-classic"></a>Esercitazione: Creare entità servizio e assegnazioni di ruolo con PowerShell in Desktop virtuale Windows (versione classica)

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows (versione classica), che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows.

Le entità servizio sono identità che è possibile creare in Azure Active Directory per assegnare ruoli e autorizzazioni per uno scopo specifico. In Desktop virtuale Windows è possibile creare un'entità servizio per:

- Automatizzare specifiche attività di gestione di Desktop virtuale Windows.
- Usarle come credenziali al posto degli utenti con autenticazione MFA obbligatoria quando si esegue qualsiasi modello di Azure Resource Manager in Desktop virtuale Windows.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'entità servizio in Azure Active Directory.
> * Creare un'assegnazione di ruolo in Desktop virtuale Windows.
> * Accedere a Desktop virtuale Windows usando l'entità servizio.

## <a name="prerequisites"></a>Prerequisiti

Prima di creare entità servizio e assegnazioni di ruolo, è necessario eseguire tre operazioni:

1. Installare il modulo AzureAD. Per installare il modulo, eseguire PowerShell come amministratore ed eseguire il cmdlet seguente:

    ```powershell
    Install-Module AzureAD
    ```

2. [Scaricare e importare il modulo PowerShell di Desktop virtuale Windows](/powershell/windows-virtual-desktop/overview/).

3. Seguire tutte le istruzioni di questo articolo nella stessa sessione di PowerShell. Il processo potrebbe non funzionare se si interrompe la sessione di PowerShell chiudendo la finestra e riaprendola in un secondo momento.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Creare un'entità servizio in Azure Active Directory

Dopo aver soddisfatto i prerequisiti della sessione di PowerShell, eseguire i cmdlet di PowerShell seguenti per creare un'entità servizio multi-tenant in Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>Visualizzare le credenziali in PowerShell

Prima di creare l'assegnazione di ruolo per l'entità servizio, visualizzare le credenziali e annotarle per riferimento futuro. La password è particolarmente importante perché non sarà possibile recuperarla dopo aver chiuso questa sessione di PowerShell.

Ecco le tre credenziali da annotare e i cmdlet che è necessario eseguire per recuperarle:

- Password:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- ID tenant:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- ID applicazione:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Creare un'assegnazione di ruolo in Desktop virtuale Windows

Successivamente, è necessario creare un'assegnazione di ruolo in modo che l'entità servizio possa accedere a Desktop virtuale Windows. Assicurarsi di accedere con un account che abbia le autorizzazioni per creare assegnazioni di ruolo.

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](/powershell/windows-virtual-desktop/overview/) da usare nella sessione di PowerShell, se non è già stato fatto.

Eseguire i cmdlet di PowerShell seguenti per connettersi a Desktop virtuale Windows e visualizzare i tenant.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

Quando si individua il nome del tenant per cui si vuole creare un'assegnazione di ruolo, usare tale nome nel cmdlet seguente:

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Accedere con l'entità servizio

Dopo aver creato un'assegnazione di ruolo per l'entità servizio, verificare che l'entità servizio sia in grado di accedere a Desktop virtuale Windows eseguendo il cmdlet seguente:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Dopo aver eseguito l'accesso, testare alcuni cmdlet di PowerShell per Desktop virtuale Windows con l'entità servizio per assicurarsi che tutto funzioni correttamente.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato l'entità servizio e aver assegnato un ruolo nel tenant di Desktop virtuale Windows, è possibile usarla per creare un pool di host. Per altre informazioni sui pool di host, continuare con l'esercitazione per la creazione di pool di host in Desktop virtuale Windows.

 > [!div class="nextstepaction"]
 > [Creare un pool di host con Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
