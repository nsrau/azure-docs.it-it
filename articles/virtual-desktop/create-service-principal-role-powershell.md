---
title: Creare entità servizio e assegnazioni di ruolo con PowerShell nell'anteprima di Desktop virtuale Windows - Azure
description: Come creare entità servizio e assegnare ruoli con PowerShell nell'anteprima di Desktop virtuale Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 93725fc9d77552d779378d0c14294a5bbb11c926
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146138"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell"></a>Esercitazione: Creare entità servizio e assegnazioni di ruolo con PowerShell

Le entità servizio sono identità che è possibile creare in Azure Active Directory per assegnare ruoli e autorizzazioni per uno scopo specifico. Nell'anteprima di Desktop virtuale Windows è possibile creare un'entità servizio per:

- Automatizzare specifiche attività di gestione di Desktop virtuale Windows
- Usarle come credenziali al posto degli utenti con autenticazione MFA obbligatoria quando si esegue qualsiasi modello di Azure Resource Manager in Desktop virtuale Windows

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'entità servizio in Azure Active Directory
> * Creare un'assegnazione di ruolo in Desktop virtuale Windows
> * Accedere a Desktop virtuale Windows con l'entità servizio

## <a name="prerequisites"></a>Prerequisiti

Prima di creare entità servizio e assegnazioni di ruolo, è necessario eseguire tre operazioni:

1. Installare il modulo AzureAD. Per installare il modulo, eseguire PowerShell come amministratore ed eseguire il cmdlet seguente:

    ```powershell
    Install-Module AzureAD
    ```

2. Eseguire i cmdlet seguenti con i valori tra virgolette sostituiti dai valori appropriati per la sessione.

    ```powershell
    $myTenantGroupName = "<my-tenant-group-name>"
    $myTenantName = "<my-tenant-name>"
    ```

3. Seguire tutte le istruzioni di questo articolo nella stessa sessione di PowerShell. Se si chiude la finestra e si torna in seguito, la procedura potrebbe non funzionare.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Creare un'entità servizio in Azure Active Directory

Dopo aver soddisfatto i prerequisiti della sessione di PowerShell, eseguire i cmdlet di PowerShell seguenti per creare un'entità servizio multi-tenant in Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Creare un'assegnazione di ruolo nell'anteprima di Desktop virtuale Windows

Dopo aver creato un'entità servizio, è possibile usarla per accedere a Desktop virtuale Windows. Assicurarsi di accedere con un account che abbia le autorizzazioni per creare l'assegnazione di ruolo.

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto.

Eseguire i cmdlet di PowerShell seguenti per connettersi a Desktop virtuale Windows e creare un'assegnazione di ruolo per l'entità servizio.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsContext -TenantGroupName $myTenantGroupName
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantGroupName $myTenantGroupName -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Accedere con l'entità servizio

Dopo aver creato un'assegnazione di ruolo per l'entità servizio, è necessario verificare che l'entità servizio sia in grado di accedere a Desktop virtuale Windows eseguendo il cmdlet seguente:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Dopo aver eseguito l'accesso, testare alcuni cmdlet di PowerShell per Desktop virtuale Windows con l'entità servizio per assicurarsi che tutto funzioni correttamente.

## <a name="view-your-credentials-in-powershell"></a>Visualizzare le credenziali in PowerShell

Prima di terminare la sessione di PowerShell, è necessario visualizzare le credenziali e prenderne nota per riferimento futuro. La password è particolarmente importante perché non sarà possibile recuperarla dopo aver chiuso questa sessione di PowerShell.

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

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come creare un'entità servizio da usare per accedere a Desktop virtuale Windows. Per altre informazioni su come accedere a Desktop virtuale Windows, continuare con le procedure per connettersi a Desktop virtuale Windows.

- [Connettersi da Windows 10 o Windows 7](connect-windows-7-and-10.md)
- [Connettersi da un Web browser](connect-web.md)
