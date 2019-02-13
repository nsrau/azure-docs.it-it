---
title: Registra le configurazioni di distribuzione per Azure Stack Development Kit (ASDK) | Microsoft Docs
description: Descrive le modifiche alla configurazione consigliata per rendere dopo l'installazione di Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/10/2018
ms.openlocfilehash: 45eadf5e049d56c8f558596eb197eb010b1fbb14
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56196995"
---
# <a name="post-asdk-installation-configuration-tasks"></a>Attività post-installazione ASDK configurazione

Dopo aver [installazione di Azure Stack Development Kit (ASDK)](asdk-install.md), è necessario apportare alcune modifiche di configurazione post-installazione consigliata mentre si è connessi come AzureStack\AzureStackAdmin nel computer host ASDK. 

## <a name="install-azure-stack-powershell"></a>Installare PowerShell per Azure Stack

Azure Stack compatibile con i moduli Azure PowerShell sono necessari per lavorare con Azure Stack.

I comandi di PowerShell per Azure Stack vengono installati tramite PowerShell Gallery. Per registrare il repository PSGallery, aprire una sessione di PowerShell con privilegi elevata ed eseguire il comando seguente:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

È possibile usare i profili delle versioni API per specificare i moduli AzureRM compatibile con Azure Stack.  I profili delle versioni API forniscono un modo per gestire le differenze di versione tra Azure e Azure Stack. Un profilo di versione API è un set di moduli AzureRM di PowerShell con le versioni API specifiche. Il **AzureRM.Bootstrapper** modulo che è disponibile tramite la raccolta di PowerShell fornisce cmdlet di PowerShell necessari per lavorare con i profili della versione API.

È possibile installare il modulo di PowerShell per Azure Stack più recente con o senza connettività Internet per il computer host ASDK:

> [!IMPORTANT]
> Prima di installare la versione richiesta, assicurarsi che si [disinstallare tutti i moduli Azure PowerShell esistenti](../azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules).

- **Con una connessione internet** dal computer host ASDK. Eseguire lo script di PowerShell seguente per installare i moduli nell'installazione del kit di sviluppo:

  - Azure Stack 1811 o versione successivo:

    ``` PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.6.0.
    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

  - Azure Stack 1808 o versione successivo:

    ``` PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.5.0.
    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

  Se l'installazione ha esito positivo, i moduli AzureRM e AzureStack vengono visualizzati nell'output.

- **Senza una connessione internet** dal computer host ASDK. In uno scenario disconnesso, è necessario prima scaricare i moduli di PowerShell in un computer che abbia la connettività internet usando i comandi PowerShell seguenti:

  ```PowerShell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  Successivamente, copiare i pacchetti scaricati nel computer ASDK e registrare il percorso come il repository predefinito e installare i moduli AzureRM e AzureStack da questo repository:

    ```PowerShell  
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

    Install-Module AzureRM -Repository $RepoName

    Install-Module AzureStack -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>Scaricare gli strumenti di Azure Stack

[Gli strumenti di AzureStack](https://github.com/Azure/AzureStack-Tools) è un repository di GitHub che ospita i moduli di PowerShell per la gestione e distribuzione delle risorse in Azure Stack. Per ottenere questi strumenti, clonare il repository GitHub o scaricare la cartella Strumenti di AzureStack eseguendo lo script seguente:

  ```PowerShell
  # Change directory to the root directory.
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip -DestinationPath . -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>Convalidare l'installazione ASDK

Per assicurarsi che la distribuzione ASDK avuto esito positivo, è possibile usare il cmdlet Test-AzureStack seguendo questa procedura:

1. Accedere come AzureStack\AzureStackAdmin nel computer host ASDK.
2. Aprire PowerShell come amministratore (non PowerShell ISE).
3. Eseguire: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Eseguire: `Test-AzureStack`

Il test richiedere alcuni minuti. Se l'installazione ha esito positivo, l'output simile al seguente:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Se si è verificato un errore, seguire i passaggi di risoluzione dei problemi per ottenere assistenza.

## <a name="reset-the-password-expiration-policy"></a>Reimpostare i criteri di scadenza password 

Per assicurarsi che la password per l'host del kit di sviluppo non scada prima della scadenza periodo della valutazione, seguire questi passaggi dopo aver distribuito il ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Per modificare i criteri di scadenza della password da Powershell

Da una console di Powershell con privilegi elevata, eseguire il comando:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Per modificare manualmente i criteri di scadenza password

1. Nell'host del kit di sviluppo, aprire **Gestione criteri di gruppo** (GPMC. MMC) e passare a **Gestione criteri di gruppo** – **foresta: con azurestack. Local** – **domini** – **con azurestack. Local**.
2. Fare doppio clic su **criterio dominio predefinito** e fare clic su **modificare**.
3. In Editor Gestione criteri di gruppo, passare a **configurazione Computer** – **criteri** – **impostazioni di Windows** – **impostazionidisicurezza**– **Criteri di account** – **criteri Password**.
4. Nel riquadro di destra, fare doppio clic su **validità massima password**.
5. Nel **validità massima password proprietà** della finestra di dialogo Modifica il **la Password scadrà** valore **180**e quindi fare clic su **OK**.

![Console Gestione criteri di gruppo](media/asdk-post-deploy/gpmc.png)

## <a name="enable-multi-tenancy"></a>Abilitare il multi-tenancy

Per le distribuzioni usando Azure AD, è necessario [abilitare multi-tenancy](../azure-stack-enable-multitenancy.md#enable-multi-tenancy) per l'installazione ASDK.

> [!NOTE]  
> Quando gli account utente o amministratore da domini diversi da quello usato per registrare Azure Stack vengono usati per accedere a un portale di Azure Stack, il nome di dominio usato per registrare Azure Stack deve essere aggiunto al portale di url. Ad esempio, se Azure Stack è stato registrato con fabrikam.onmicrosoft.com e l'account utente di accesso viene admin@contoso.com, l'url da usare per accedere al portale per gli utenti sarà: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Passaggi successivi

[Registrare il ASDK con Azure](asdk-register.md)
