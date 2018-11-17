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
ms.date: 11/15/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: ad1a89c69d34dbb48b87a75778e18c9a995f749a
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853558"
---
# <a name="update-the-sql-resource-provider"></a>Aggiornare il provider di risorse SQL

*Si applica a: i sistemi integrati di Azure Stack.*

Un nuovo provider di risorse SQL potrebbe essere rilasciato quando Azure Stack viene aggiornato a una nuova compilazione. Anche se la scheda esistente continua a funzionare, è consigliabile aggiornare quanto prima della compilazione più recente.

> [!IMPORTANT]
> È necessario installare aggiornamenti nell'ordine in che cui vengono rilasciate. Non è possibile ignorare le versioni. Vedere l'elenco di versioni nel [distribuisce i prerequisiti di provider di risorse](.\azure-stack-sql-resource-provider-deploy.md#prerequisites).

## <a name="overview"></a>Panoramica

Per aggiornare il provider di risorse, usare il *UpdateSQLProvider.ps1* script. Questo script è incluso nel download del nuovo provider di risorse SQL. È simile a quello usato per il processo di aggiornamento [distribuire il provider di risorse](.\azure-stack-sql-resource-provider-deploy.md). Lo script di aggiornamento Usa gli stessi argomenti dello script DeploySqlProvider.ps1 e sarà necessario fornire le informazioni sul certificato.

### <a name="update-script-processes"></a>Processi di script di aggiornamento

Il *UpdateSQLProvider.ps1* script crea una nuova macchina virtuale (VM) con il codice del provider di risorse più recente.

> [!NOTE]
> Si consiglia di scaricare l'immagine di Windows Server 2016 Core più recente dalla gestione di Marketplace. Se è necessario installare un aggiornamento, è possibile inserire un **singolo** nel percorso della dipendenza locale il pacchetto MSU. Lo script avrà esito negativo se è presente più di un file MSU in questa posizione.

Dopo il *UpdateSQLProvider.ps1* lo script crea una nuova macchina virtuale, lo script viene eseguita la migrazione le impostazioni seguenti dal vecchio provider macchina virtuale:

* informazioni sul database
* informazioni sul server di hosting
* record DNS necessario

### <a name="update-script-powershell-example"></a>Aggiornare l'esempio di script di PowerShell

È possibile modificare ed eseguire lo script seguente da un esempio di PowerShell con privilegi elevati ISE. 

Ricordarsi di modificare le informazioni sull'account e le password in base alle necessità per l'ambiente.

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

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
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

## <a name="updatesqlproviderps1-parameters"></a>Parametri UpdateSQLProvider.ps1

È possibile specificare i parametri seguenti dalla riga di comando quando si esegue lo script. In caso contrario, o se ha esito negativo qualsiasi convalida dei parametri, viene chiesto di fornire i parametri obbligatori.

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

## <a name="next-steps"></a>Passaggi successivi

[Gestire il provider di risorse SQL](azure-stack-sql-resource-provider-maintain.md)
