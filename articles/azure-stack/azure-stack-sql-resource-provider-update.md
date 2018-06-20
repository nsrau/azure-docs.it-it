---
title: L'aggiornamento del provider di risorse di SQL Azure Stack | Documenti Microsoft
description: Informazioni su come è possibile aggiornare il provider di risorse di SQL Azure dello Stack.
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
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: ac5073d1abc32b7598a869750f9c5a801559e9e6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264078"
---
# <a name="update-the-sql-resource-provider"></a>Aggiornare il provider di risorse SQL

*Si applica a: Azure Stack integrati sistemi.*

Un nuovo provider di risorse SQL potrebbero essere rilasciati quando Azure Stack viene aggiornato a una nuova compilazione. Anche se l'adapter esistente continua a funzionare, si consiglia di aggiornare appena possibile per la build più recente.

>[!IMPORTANT]
>È necessario installare gli aggiornamenti nell'ordine che vengano rilasciati. Non è possibile ignorare le versioni. Vedere l'elenco di versioni nel [distribuire i prerequisiti di provider di risorse](.\azure-stack-sql-resource-provider-deploy.md#prerequisites).

## <a name="overview"></a>Panoramica

Per aggiornare il provider di risorse, usare il *UpdateSQLProvider.ps1* script. Questo script è incluso con il download del nuovo provider di risorse SQL. È simile a quello utilizzato per il processo di aggiornamento [distribuire il provider di risorse](.\azure-stack-sql-resource-provider-deploy.md). Lo script di aggiornamento utilizza gli stessi argomenti come lo script DeploySqlProvider.ps1 e sarà necessario fornire le informazioni sul certificato.

### <a name="update-script-processes"></a>Processi di script di aggiornamento

Il *UpdateSQLProvider.ps1* script crea una nuova macchina virtuale (VM) con il codice di provider di risorse più recente.

>[!NOTE]
>Si consiglia di scaricare l'immagine di Windows Server 2016 Core più recente dalla gestione Marketplace. Se è necessario installare un aggiornamento, è possibile inserire un **singolo** pacchetto MSU nel percorso locale. Lo script avrà esito negativo se non esiste più di un file MSU in questa posizione.

Dopo il *UpdateSQLProvider.ps1* script crea una nuova macchina virtuale, lo script viene eseguita la migrazione le impostazioni seguenti dal vecchio provider VM:

* informazioni sul database
* le informazioni sul server di hosting
* record DNS necessario

### <a name="update-script-powershell-example"></a>Aggiornare script PowerShell di esempio

È possibile modificare ed eseguire lo script seguente da un con privilegi elevati di PowerShell ISE. Ricordarsi di modificare le informazioni sull'account e password in base alle esigenze per l'ambiente.

> [!NOTE]
> Questo processo di aggiornamento si applica solo ai sistemi Azure Stack integrato.

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

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
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `

 ```

## <a name="updatesqlproviderps1-parameters"></a>Parametri UpdateSQLProvider.ps1

È possibile specificare i seguenti parametri dalla riga di comando quando si esegue lo script. Se non sono presenti o se si verifica un errore di convalida qualsiasi parametro, viene chiesto di fornire i parametri obbligatori.

| Nome parametro | DESCRIZIONE | Commento o il valore predefinito |
| --- | --- | --- |
| **CloudAdminCredential** | Le credenziali per l'amministratore del cloud, necessaria per l'accesso all'endpoint con privilegi. | _Obbligatorio_ |
| **AzCredential** | Le credenziali per l'account amministratore del servizio Azure Stack. Utilizzare le stesse credenziali utilizzate per la distribuzione dello Stack di Azure. | _Obbligatorio_ |
| **VMLocalCredential** | Le credenziali per l'account amministratore locale del provider di risorse SQL macchina virtuale. | _Obbligatorio_ |
| **PrivilegedEndpoint** | L'indirizzo IP o nome DNS dell'endpoint con privilegi. |  _Obbligatorio_ |
| **DependencyFilesLocalPath** | È inoltre necessario inserire il file pfx del certificato in questa directory. | _Facoltativo per singolo nodo, ma obbligatoria a nodi multipli_ |
| **DefaultSSLCertificatePassword** | La password per il certificato con estensione pfx. | _Obbligatorio_ |
| **MaxRetryCount** | Il numero di volte in cui che si desidera ripetere ogni operazione se si verifica un errore.| 2 |
| **RetryDuration** |L'intervallo di timeout tra due tentativi, in secondi. | 120 |
| **Disinstallare** | Rimuove il provider di risorse e tutte le risorse associate. | No  |
| **DebugMode** | Impedisce la pulizia automatica in caso di errore. | No  |

## <a name="next-steps"></a>Passaggi successivi

[Gestire il provider di risorse SQL](azure-stack-sql-resource-provider-maintain.md)
