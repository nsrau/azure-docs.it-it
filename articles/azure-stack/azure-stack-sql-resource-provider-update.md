---
title: Utilizzo di database SQL Azure stack | Documenti Microsoft
description: Informazioni su come distribuire i database SQL come servizio in Azure Stack e la procedura per distribuire l'adapter di provider di risorse di SQL Server.
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 9154f509f9019c28515970869678aa6633d16163
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2018
---
# <a name="update-the-sql-resource-provider-adapter"></a>Aggiornare l'adapter di provider di risorse SQL
Un nuovo adattatore di provider di risorse SQL potrebbe essere rilasciato quando vengono aggiornate le compilazioni di Stack di Azure. Mentre l'adapter esistente continua a funzionare, si consiglia di aggiornare appena possibile per la build più recente. Gli aggiornamenti devono essere installati nell'ordine: non è possibile ignorare le versioni (vedere l'elenco di versioni nel [distribuire i prerequisiti di provider di risorse](.\azure-stack-sql-resource-provider-deploy.md#prerequisites)).

Per l'aggiornamento del provider di risorse a cui si utilizza il *UpdateSQLProvider.ps1* script. Il processo è simile a quello utilizzato per installare un provider di risorse, come descritto nel [distribuire il provider di risorse](.\azure-stack-sql-resource-provider-deploy.md) articolo. Lo script è incluso con il download del provider di risorse.

Il *UpdateSQLProvider.ps1* script crea una nuova macchina virtuale con il codice di provider di risorse più recente e consente di migrare le impostazioni dalla macchina virtuale precedente per la nuova macchina virtuale. Le impostazioni di cui eseguire la migrazione includono database e le informazioni sul server di hosting e registra il DNS necessario.

Lo script richiede l'utilizzo degli stessi argomenti descritti per lo script DeploySqlProvider.ps1. Specificare anche il certificato. 

Si consiglia di scaricare l'immagine di Windows Server 2016 Core più recente dalla gestione Marketplace. Se è necessario installare un aggiornamento, è possibile inserire un singolo. Pacchetto MSU nel percorso locale. Se più oggetti. È possibile trovare il file MSU, lo script avrà esito negativo.

Di seguito è riportato un esempio del *UpdateSQLProvider.ps1* script che è possibile eseguire dal prompt di PowerShell. Assicurarsi di modificare le informazioni sull'account e password in base alle esigenze: 

> [!NOTE]
> Il processo di aggiornamento si applica solo ai sistemi integrati.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
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
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

## <a name="updatesqlproviderps1-parameters"></a>Parametri UpdateSQLProvider.ps1
È possibile specificare questi parametri nella riga di comando. In caso contrario, o qualsiasi parametro di convalida non riesce, viene chiesto di fornire i parametri richiesti.

| Nome parametro | DESCRIZIONE | Commento o il valore predefinito |
| --- | --- | --- |
| **CloudAdminCredential** | Le credenziali per l'amministratore del cloud, necessaria per l'accesso all'endpoint con privilegi. | _Obbligatorio_ |
| **AzCredential** | Le credenziali per l'account amministratore del servizio Azure Stack. Utilizzare le stesse credenziali utilizzate per la distribuzione dello Stack di Azure. | _Obbligatorio_ |
| **VMLocalCredential** | Le credenziali per l'account amministratore locale del provider di risorse SQL macchina virtuale. | _Obbligatorio_ |
| **PrivilegedEndpoint** | L'indirizzo IP o nome DNS dell'endpoint con privilegi. |  _Obbligatorio_ |
| **DependencyFilesLocalPath** | Il file pfx del certificato deve trovarsi in questa directory. | _Parametro facoltativo_ (_obbligatorio_ a nodi multipli) |
| **DefaultSSLCertificatePassword** | La password per il certificato con estensione pfx. | _obbligatorio_ |
| **MaxRetryCount** | Il numero di volte in cui che si desidera ripetere ogni operazione se si è verificato un errore.| 2 |
| **RetryDuration** |L'intervallo di timeout tra due tentativi, in secondi. | 120 |
| **Disinstallare** | Rimuove il provider di risorse e tutte le risorse associate (vedere le note seguenti). | No  |
| **DebugMode** | Impedisce la pulizia automatica in caso di errore. | No  |


## <a name="next-steps"></a>Passaggi successivi

[Gestire il provider di risorse SQL](azure-stack-sql-resource-provider-maintain.md)
