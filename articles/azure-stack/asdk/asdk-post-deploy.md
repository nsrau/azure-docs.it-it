---
title: Registra le configurazioni di distribuzione per Azure Stack Development Kit (ASDK) | Documenti Microsoft
description: Vengono descritte le modifiche di configurazione consigliata per rendere dopo aver installato il Kit di sviluppo dello Stack di Azure (ASDK).
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 2183576e87aa2fb31f8be8f676a5aee7d52f68df
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="post-asdk-installation-configuration-tasks"></a>Attività post-installazione ASDK configurazione
Dopo aver [installando il ASDK](asdk-install.md), esistono essere apportate alcune modifiche di configurazione post-installazione consigliata. 

## <a name="install-azure-stack-powershell"></a>Installare PowerShell per Azure Stack 
Moduli di Azure PowerShell compatibili Stack Azure necessarie per lavorare con lo Stack di Azure.

I comandi di PowerShell per lo Stack di Azure vengono installati tramite PowerShell Gallery. Per registrare il repository PSGallery, aprire una sessione di PowerShell con privilegi elevata ed eseguire il comando seguente:

``` Powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

 Moduli di Azure Resource Manager compatibili Stack Azure vengono installati tramite i profili della versione API. Stack di Azure, è necessario il profilo di versione API 2017-03-09-profilo, che è disponibile per l'installazione del modulo AzureRM.Bootstrapper. 
 
 È possibile installare Azure PowerShell Stack con o senza connettività a internet per il computer host ASDK:

- **Con una connessione internet** dal computer host ASDK. Eseguire lo script di PowerShell seguente per installare questi moduli nell'installazione kit sviluppo:

  ``` PowerShell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```
  Se l'installazione ha esito positivo, i moduli di Azure Resource Manager e AzureStack vengono visualizzati nell'output.

- **Senza una connessione internet** dal computer host ASDK. In uno scenario disconnesso, è necessario prima scaricare i moduli di PowerShell in un computer con connettività internet usando i seguenti comandi PowerShell:

  ```PowerShell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet `
    -Source https://www.powershellgallery.com/api/v2 `
    -Name AzureRM `
    -Path $Path `
    -Force `
    -RequiredVersion 1.2.11

  Save-Package `
    -ProviderName NuGet `
    -Source https://www.powershellgallery.com/api/v2 `
    -Name AzureStack `
    -Path $Path `
    -Force `
    -RequiredVersion 1.2.11
  ```
  Successivamente, copiare i pacchetti scaricati nel computer ASDK e registrare il percorso come il repository predefinito e installare i moduli AzureRM e AzureStack da questo repository:

    ```PowerShell
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository `
      -Name $RepoName `
      -SourceLocation $SourceLocation `
      -InstallationPolicy Trusted

    Install-Module AzureRM `
      -Repository $RepoName

    Install-Module AzureStack `
      -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>Scaricare gli strumenti di Azure Stack
[Strumenti di AzureStack](https://github.com/Azure/AzureStack-Tools) è un repository di GitHub che ospita i moduli di PowerShell per la gestione e distribuzione delle risorse allo Stack di Azure. Per ottenere questi strumenti, clonare il repository GitHub o scaricare la cartella Strumenti di AzureStack eseguendo lo script seguente:

  ```PowerShell
  # Change directory to the root directory. 
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip `
    -DestinationPath . `
    -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>Convalidare l'installazione ASDK
Per garantire che la distribuzione ASDK avuto esito positivo, è possibile utilizzare il cmdlet Test-AzureStack attenendosi alla procedura seguente:

1. Accedere come AzureStack\CloudAdmin nel computer host ASDK.
2. Aprire PowerShell come amministratore (non PowerShell ISE).
3. Eseguire: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Eseguire: `Test-AzureStack`

I test richiedere alcuni minuti. Se l'installazione ha esito positivo, l'output simile al seguente:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Se si è verificato un errore, seguire i passaggi di risoluzione dei problemi per ottenere la Guida.

## <a name="activate-the-administrator-and-tenant-portals"></a>Attivare i portali tenant e di amministratore
Dopo le distribuzioni che usano Azure AD, è necessario attivare entrambi i Azure Stack amministratore portali tenant e. Questa attivazione consente che il portale di Azure Stack e Azure Resource Manager le autorizzazioni corrette (elencate nella pagina di consenso) per tutti gli utenti della directory.

- Per il portale dell'amministratore, passare a https://adminportal.local.azurestack.external/guest/signup, leggere le informazioni e quindi fare clic su **Accept**. Dopo avere accettato, è possibile aggiungere gli amministratori del servizio non sono anche amministratori di tenant di directory.

- Per il portale tenant, passare a https://portal.local.azurestack.external/guest/signup, leggere le informazioni e quindi fare clic su **Accept**. Dopo avere accettato, gli utenti nella directory possono accedere al portale tenant. 

> [!NOTE] 
> Se non sono attivati i portali, solo l'amministratore di directory può accedere e utilizzare i portali. Se un altro utente accede, essi verrà visualizzato un errore che informa che l'amministratore non dispone di autorizzazioni ad altri utenti. Quando l'amministratore in modo nativo appartiene alla directory registrato in Azure Stack, la directory di Azure Stack deve essere aggiunto all'URL di attivazione. Ad esempio, se Azure Stack è registrato con fabrikam.onmicrosoft.com e l'utente amministratore è admin@contoso.com, passare a https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com per attivare il portale. 

## <a name="reset-the-password-expiration-policy"></a>Reimpostare i criteri di scadenza password 
Per assicurarsi che la password per l'host del kit di sviluppo non scada prima la fine del periodo di valutazione, seguire questi passaggi dopo aver distribuito il ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Per modificare i criteri di scadenza della password da Powershell:
In una console di Powershell con privilegi elevata, eseguire il comando:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Per modificare manualmente i criteri di scadenza delle password:
1. Nell'host del kit di sviluppo, aprire **Gestione criteri di gruppo** (Gestione criteri di gruppo. MMC) e passare a **Gestione criteri di gruppo** – **foresta: azurestack.local** – **domini** – **azurestack.local**.
2. Fare doppio clic su **criterio dominio predefinito** e fare clic su **modificare**.
3. In Editor Gestione criteri di gruppo, passare a **configurazione Computer** : **criteri** : **impostazioni di Windows** – **le impostazioni di sicurezza**– **Criteri di account** – **criteri Password**.
4. Nel riquadro destro fare doppio clic su **validità massima password**.
5. Nel **validità massima password proprietà** della finestra di dialogo Modifica il **Password scadrà** valore **180**e quindi fare clic su **OK**.

![Console Gestione criteri di gruppo](media/asdk-post-deploy/gpmc.png)


## <a name="next-steps"></a>Passaggi successivi
[Registrare il ASDK con Azure](asdk-register.md)
