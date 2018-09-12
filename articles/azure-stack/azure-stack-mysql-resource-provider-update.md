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
ms.date: 09/04/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: fb9f022f0af821d81e5b61b99ecb52b7f7151b5f
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391556"
---
# <a name="update-the-mysql-resource-provider"></a>Aggiornare il provider di risorse MySQL 

*Si applica a: i sistemi integrati di Azure Stack.*

Una nuova scheda del provider di risorse SQL potrebbe essere rilasciata quando vengono aggiornate le compilazioni di Azure Stack. Mentre la scheda esistente continua a funzionare, è consigliabile aggiornare alla build più recente quanto prima. 

>[!IMPORTANT]
>È necessario installare aggiornamenti nell'ordine in che cui vengono rilasciate. Non è possibile ignorare le versioni. Vedere l'elenco di versioni nel [distribuisce i prerequisiti di provider di risorse](.\azure-stack-mysql-resource-provider-deploy.md#prerequisites).

## <a name="update-the-mysql-resource-provider-adapter-integrated-systems-only"></a>Aggiornare l'adattatore di provider di risorse MySQL (solo sistemi integrati)

Una nuova scheda del provider di risorse SQL potrebbe essere rilasciata quando vengono aggiornate le compilazioni di Azure Stack. Mentre la scheda esistente continua a funzionare, è consigliabile aggiornare alla build più recente quanto prima.  
 
Per l'aggiornamento del provider di risorse è usare il **UpdateMySQLProvider.ps1** script. Il processo è simile a quello usato per installare un provider di risorse, come descritto nel [distribuire il provider di risorse](#deploy-the-resource-provider) sezione di questo articolo. Lo script è incluso nel download del provider di risorse. 

Il **UpdateMySQLProvider.ps1** script crea una nuova macchina virtuale con il codice di provider di risorse più recente e consente di migrare le impostazioni dalla macchina virtuale precedente alla nuova VM. Le impostazioni di cui eseguire la migrazione includono database e le informazioni sul server di hosting, e registra il DNS necessario. 

>[!NOTE]
>Si consiglia di scaricare l'immagine di Windows Server 2016 Core più recente dalla gestione di Marketplace. Se è necessario installare un aggiornamento, è possibile inserire un **singolo** nel percorso della dipendenza locale il pacchetto MSU. Lo script avrà esito negativo se è presente più di un file MSU in questa posizione.

>[!NOTE]  
> 

Lo script richiede l'uso degli stessi argomenti che sono descritte per lo script DeployMySqlProvider.ps1. Specificare anche il certificato nel portale.  

Seguito è riportato un esempio del *UpdateMySQLProvider.ps1* script che è possibile eseguire dal prompt di PowerShell. Assicurarsi di modificare le informazioni sull'account e password in base alle esigenze:  

> [!NOTE] 
> Il processo di aggiornamento si applica solo ai sistemi integrati. 

```powershell 
# Install the AzureRM.Bootstrapper module and set the profile. 
Install-Module -Name AzureRm.BootStrapper -Force 
Use-AzureRmProfile -Profile 2017-03-09-profile 

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS virtual machines 
$privilegedEndpoint = "AzS-ERCS01" 

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
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
``` 
 
### <a name="updatemysqlproviderps1-parameters"></a>Parametri UpdateMySQLProvider.ps1 
È possibile specificare questi parametri nella riga di comando. In caso contrario, o se ha esito negativo qualsiasi convalida dei parametri, viene chiesto di fornire i parametri obbligatori. 

| Nome parametro | DESCRIZIONE | Commento o il valore predefinito | 
| --- | --- | --- | 
| **CloudAdminCredential** | Le credenziali per l'amministratore del cloud, necessaria per l'accesso all'endpoint con privilegi. | _Obbligatorio_ | 
| **AzCredential** | Le credenziali per l'account di amministratore del servizio di Azure Stack. Usare le stesse credenziali di avere usato per la distribuzione di Azure Stack. | _Obbligatorio_ | 
| **VMLocalCredential** |Le credenziali per l'account amministratore locale del provider di risorse SQL macchina virtuale. | _Obbligatorio_ | 
| **PrivilegedEndpoint** | L'indirizzo IP o nome DNS dell'endpoint con privilegi. |  _Obbligatorio_ | 
| **AzureEnvironment** | Ambiente di azure dell'account di amministratore del servizio che usato per la distribuzione di Azure Stack. Obbligatorio solo se non è ad FS. I nomi di ambiente supportati sono **AzureCloud**, **AzureUSGovernment**, o se si usa un China Azure Active Directory **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Il file con estensione pfx del certificato deve trovarsi in questa directory. | _Facoltativo_ (_obbligatorio_ a nodi multipli) | 
| **DefaultSSLCertificatePassword** | La password per il certificato con estensione pfx. | _Obbligatorio_ | 
| **MaxRetryCount** | Il numero di volte in cui che si desidera ripetere ogni operazione se si verifica un errore.| 2 | 
| **RetryDuration** | L'intervallo di timeout tra i tentativi, in secondi. | 120 | 
| **Disinstallare** | Rimuovere il provider di risorse e tutte le risorse associate (vedere le note seguenti). | No  | 
| **DebugMode** | Impedisce la pulizia automatica in caso di errore. | No  | 
| **AcceptLicense** | Ignora la richiesta di accettazione della licenza GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 
 

## <a name="next-steps"></a>Passaggi successivi
[Gestisci provider di risorse MySQL](azure-stack-mysql-resource-provider-maintain.md)
