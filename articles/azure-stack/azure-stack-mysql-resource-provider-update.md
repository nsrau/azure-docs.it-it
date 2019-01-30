---
title: L'aggiornamento del provider di risorse MySQL di Azure Stack | Microsoft Docs
description: Informazioni su come è possibile aggiornare il provider di risorse MySQL di Azure Stack.
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
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: f3129c27d123aa448f8293f7e1827dc3ebc5c82e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239078"
---
# <a name="update-the-mysql-resource-provider"></a>Aggiornare il provider di risorse MySQL 

*Si applica a: Sistemi integrati di Azure Stack.*

Vengono aggiornate un MySQL nuovi adapter del provider di risorse potrebbe essere rilasciato quando si basa Azure Stack. Mentre la scheda esistente continua a funzionare, è consigliabile aggiornare alla build più recente quanto prima. 

Iniziando con il rilascio della versione 1.1.33.0 MySQL resource provider, gli aggiornamenti sono cumulativi e non devono essere installati nell'ordine in cui vengono rilasciate; fino a quando si è a partire dalla versione 1.1.24.0 o versione successiva. Ad esempio, se si esegue la versione 1.1.24.0 del provider di risorse MySQL, quindi è possibile aggiornare alla versione 1.1.33.0 o in un secondo momento senza dover prima installare versione 1.1.30.0. Per esaminare le versioni del provider delle risorse disponibili e la versione di Azure Stack sono supportati in, vedere l'elenco di versioni nel [distribuisce i prerequisiti di provider di risorse](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Per l'aggiornamento del provider di risorse è usare il **UpdateMySQLProvider.ps1** script. Il processo è simile a quello usato per installare un provider di risorse, come descritto nel [distribuire il provider di risorse](#deploy-the-resource-provider) sezione di questo articolo. Lo script è incluso nel download del provider di risorse. 

 > [!IMPORTANT]
 > Prima di aggiornare il provider di risorse, esaminare le note sulla versione per informazioni sulle nuove funzionalità, correzioni e i problemi noti che potrebbero influire sulla distribuzione.

## <a name="update-script-processes"></a>Processi di script di aggiornamento

Il **UpdateMySQLProvider.ps1** script crea una nuova macchina virtuale con il codice di provider di risorse più recente e consente di migrare le impostazioni dalla macchina virtuale precedente alla nuova VM. Le impostazioni di cui eseguire la migrazione includono database e le informazioni sul server di hosting, e registra il DNS necessario. 

>[!NOTE]
>Si consiglia di scaricare l'immagine di Windows Server 2016 Core più recente dalla gestione di Marketplace. Se è necessario installare un aggiornamento, è possibile inserire un **singolo** nel percorso della dipendenza locale il pacchetto MSU. Lo script avrà esito negativo se è presente più di un file MSU in questa posizione.

Lo script richiede l'uso degli stessi argomenti che sono descritte per lo script DeployMySqlProvider.ps1. Specificare anche il certificato nel portale.  


## <a name="update-script-parameters"></a>Aggiornare i parametri dello script 
È possibile specificare i parametri seguenti dalla riga di comando quando si esegue la **UpdateMySQLProvider.ps1** script di PowerShell. In caso contrario, o se ha esito negativo qualsiasi convalida dei parametri, viene chiesto di fornire i parametri obbligatori. 

| Nome parametro | DESCRIZIONE | Commento o il valore predefinito | 
| --- | --- | --- | 
| **CloudAdminCredential** | Le credenziali per l'amministratore del cloud, necessaria per l'accesso all'endpoint con privilegi. | _Obbligatorio_ | 
| **AzCredential** | Le credenziali per l'account di amministratore del servizio di Azure Stack. Usare le stesse credenziali di avere usato per la distribuzione di Azure Stack. | _Obbligatorio_ | 
| **VMLocalCredential** |Le credenziali per l'account amministratore locale del provider di risorse SQL macchina virtuale. | _Obbligatorio_ | 
| **PrivilegedEndpoint** | L'indirizzo IP o nome DNS dell'endpoint con privilegi. |  _Obbligatorio_ | 
| **AzureEnvironment** | Ambiente di Azure dell'account di amministratore del servizio che usato per la distribuzione di Azure Stack. Obbligatorio solo per le distribuzioni di Azure AD. I nomi di ambiente supportati sono **AzureCloud**, **AzureUSGovernment**, o se si usa una Azure AD, Cina **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Il file con estensione pfx del certificato deve trovarsi in questa directory. | _Facoltativo_ (_obbligatorio_ a nodi multipli) | 
| **DefaultSSLCertificatePassword** | La password per il certificato con estensione pfx. | _Obbligatorio_ | 
| **MaxRetryCount** | Il numero di volte in cui che si desidera ripetere ogni operazione se si verifica un errore.| 2 | 
| **RetryDuration** | L'intervallo di timeout tra i tentativi, in secondi. | 120 | 
| **Disinstallare** | Rimuovere il provider di risorse e tutte le risorse associate (vedere le note seguenti). | No  | 
| **DebugMode** | Impedisce la pulizia automatica in caso di errore. | No  | 
| **AcceptLicense** | Ignora la richiesta di accettazione della licenza GPL.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Esempio di script di aggiornamento
Di seguito è riportato un esempio d'uso di *UpdateMySQLProvider.ps1* script che è possibile eseguire da una console di PowerShell con privilegi elevata. Assicurarsi di modificare le informazioni sulle variabili e le password in base alle esigenze:  

> [!NOTE] 
> Il processo di aggiornamento si applica solo ai sistemi integrati. 

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.5.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS virtual machines 
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services). 
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM. 
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access. 
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate. 
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
 
# Change directory to the folder where you extracted the installation files. 
# Then adjust the endpoints. 
$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
```  

## <a name="next-steps"></a>Passaggi successivi
[Gestisci provider di risorse MySQL](azure-stack-mysql-resource-provider-maintain.md)
