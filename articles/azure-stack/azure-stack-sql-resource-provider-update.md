---
title: L'aggiornamento del provider di risorse SQL di Azure Stack | Microsoft Docs
description: Informazioni su come è possibile aggiornare il provider di risorse SQL di Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.openlocfilehash: 00a7644663b4628d20dbe598def158bc120a7aee
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245464"
---
# <a name="update-the-sql-resource-provider"></a>Aggiornare il provider di risorse SQL

*Si applica a: Sistemi integrati di Azure Stack.*

Un nuovo provider di risorse SQL potrebbe essere rilasciato quando Azure Stack viene aggiornato a una nuova compilazione. Anche se il provider di risorse esistente continuino a funzionare, è consigliabile aggiornare quanto prima della compilazione più recente. 

Iniziando con il rilascio della versione 1.1.33.0 SQL resource provider, gli aggiornamenti sono cumulativi e non devono essere installati nell'ordine in cui vengono rilasciate; fino a quando si è a partire dalla versione 1.1.24.0 o versione successiva. Ad esempio, se si esegue la versione 1.1.24.0 del provider di risorse SQL, quindi è possibile aggiornare alla versione 1.1.33.0 o in un secondo momento senza dover prima installare versione 1.1.30.0. Per esaminare le versioni del provider delle risorse disponibili e la versione di Azure Stack sono supportati in, vedere l'elenco di versioni nel [distribuisce i prerequisiti di provider di risorse](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Per aggiornare il provider di risorse, usare il *UpdateSQLProvider.ps1* script. Questo script è incluso nel download del nuovo provider di risorse SQL. È simile a quello usato per il processo di aggiornamento [distribuire il provider di risorse](./azure-stack-sql-resource-provider-deploy.md). Lo script di aggiornamento Usa gli stessi argomenti dello script DeploySqlProvider.ps1 e sarà necessario fornire le informazioni sul certificato.

 > [!IMPORTANT]
 > Prima di aggiornare il provider di risorse, esaminare le note sulla versione per informazioni sulle nuove funzionalità, correzioni e i problemi noti che potrebbero influire sulla distribuzione.

## <a name="update-script-processes"></a>Processi di script di aggiornamento

Il *UpdateSQLProvider.ps1* script crea una nuova macchina virtuale (VM) con il codice del provider di risorse più recente.

> [!NOTE]
> Si consiglia di scaricare l'immagine di Windows Server 2016 Core più recente dalla gestione di Marketplace. Se è necessario installare un aggiornamento, è possibile inserire un **singolo** nel percorso della dipendenza locale il pacchetto MSU. Lo script avrà esito negativo se è presente più di un file MSU in questa posizione.

Dopo il *UpdateSQLProvider.ps1* lo script crea una nuova macchina virtuale, lo script viene eseguita la migrazione le impostazioni seguenti dal vecchio provider macchina virtuale:

* informazioni sul database
* informazioni sul server di hosting
* record DNS necessario

## <a name="update-script-parameters"></a>Aggiornare i parametri dello script

È possibile specificare i parametri seguenti dalla riga di comando quando si esegue la **UpdateSQLProvider.ps1** script di PowerShell. In caso contrario, o se ha esito negativo qualsiasi convalida dei parametri, viene chiesto di fornire i parametri obbligatori.

| Nome parametro | DESCRIZIONE | Commento o il valore predefinito |
| --- | --- | --- |
| **CloudAdminCredential** | Le credenziali per l'amministratore del cloud, necessaria per l'accesso all'endpoint con privilegi. | _Obbligatorio_ |
| **AzCredential** | Le credenziali per l'account di amministratore del servizio di Azure Stack. Usare le stesse credenziali usate per la distribuzione di Azure Stack. | _Obbligatorio_ |
| **VMLocalCredential** | Le credenziali per l'account amministratore locale del provider di risorse SQL macchina virtuale. | _Obbligatorio_ |
| **PrivilegedEndpoint** | L'indirizzo IP o nome DNS dell'endpoint con privilegi. |  _Obbligatorio_ |
| **AzureEnvironment** | Ambiente di Azure dell'account di amministratore del servizio che usato per la distribuzione di Azure Stack. Obbligatorio solo per le distribuzioni di Azure AD. I nomi di ambiente supportati sono **AzureCloud**, **AzureUSGovernment**, o se si usa una Azure AD, Cina **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | È anche necessario inserire il file con estensione pfx del certificato in questa directory. | _Facoltativo per singolo nodo, ma obbligatorio per multi-nodo_ |
| **DefaultSSLCertificatePassword** | La password per il certificato con estensione pfx. | _Obbligatorio_ |
| **MaxRetryCount** | Il numero di volte in cui che si desidera ripetere ogni operazione se si verifica un errore.| 2 |
| **RetryDuration** |L'intervallo di timeout tra i tentativi, in secondi. | 120 |
| **Disinstallare** | Rimuove il provider di risorse e tutte le risorse associate. | No  |
| **DebugMode** | Impedisce la pulizia automatica in caso di errore. | No  |

## <a name="update-script-powershell-example"></a>Aggiornare l'esempio di script di PowerShell
Di seguito è riportato un esempio d'uso di *UpdateSQLProvider.ps1* script che è possibile eseguire da una console di PowerShell con privilegi elevata. Assicurarsi di modificare le informazioni sulle variabili e le password in base alle esigenze:  

> [!NOTE]
> Questo processo di aggiornamento si applica solo ai sistemi integrati di Azure Stack.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.5.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service administrator account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -AzureEnvironment $AzureEnvironment `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `

 ```

## <a name="next-steps"></a>Passaggi successivi

[Gestire il provider di risorse SQL](azure-stack-sql-resource-provider-maintain.md)
