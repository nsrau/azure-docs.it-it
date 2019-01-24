---
title: Mantenere il provider di risorse SQL in Azure Stack | Microsoft Docs
description: Informazioni su come è possibile gestire il servizio provider di risorse SQL in Azure Stack.
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
ms.openlocfilehash: fa2e834b142d5df4751b46f6dac2db9b3a10a463
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54391321"
---
# <a name="sql-resource-provider-maintenance-operations"></a>Operazioni di manutenzione del provider di risorse SQL

Il provider di risorse SQL viene eseguito in una macchina virtuale bloccata. Per abilitare le operazioni di manutenzione, è necessario aggiornare la protezione della macchina virtuale. Per eseguire questa operazione usando il principio del privilegio minimo, è possibile usare [PowerShell Just Enough Administration (JEA)](https://docs.microsoft.com/powershell/jea/overview) endpoint *DBAdapterMaintenance*. Il pacchetto di installazione di provider di risorse include uno script per questa operazione.

## <a name="patching-and-updating"></a>L'applicazione di patch e aggiornamento

Il provider di risorse SQL non è gestito come parte di Azure Stack perché è un componente aggiuntivo. Microsoft fornisce aggiornamenti per il provider di risorse SQL, se necessario. Quando viene rilasciato un aggiornato adapter SQL, viene fornito uno script per applicare l'aggiornamento. Questo script crea un nuovo provider di risorse della macchina virtuale, eseguire la migrazione lo stato del provider precedente della macchina virtuale per la nuova macchina virtuale. Per altre informazioni, vedere [aggiornare il provider di risorse SQL](azure-stack-sql-resource-provider-update.md).

### <a name="provider-virtual-machine"></a>Macchina virtuale del provider

Poiché il provider di risorse viene eseguito su un *utente* macchina virtuale, è necessario applicare le patch necessarie e gli aggiornamenti quando vengono rilasciate. È possibile usare i pacchetti di aggiornamento di Windows che vengono forniti come parte del ciclo di patch e aggiornamento per applicare gli aggiornamenti per la macchina virtuale.

## <a name="updating-sql-credentials"></a>L'aggiornamento delle credenziali SQL

Ha la responsabilità per la creazione e gestione degli account sysadmin su SQL Server. Il provider di risorse necessita di un account con tali privilegi per gestire i database per gli utenti, ma non è necessario l'accesso ai dati degli utenti. Se è necessario aggiornare le password sysadmin su SQL Server, è possibile utilizzare l'interfaccia di amministrazione del provider di risorse per modificare una password archiviata. Queste password vengono archiviate in un insieme di credenziali delle chiavi nell'istanza di Azure Stack.

Per modificare le impostazioni, selezionare **esplorare** &gt; **risorse amministrative** &gt; **istanze di SQL Server che ospita** &gt; **Account di accesso SQL** e selezionare un nome utente. La modifica deve essere apportata nell'istanza di SQL prima di tutto (e tutte le repliche, se necessario). Sotto **le impostazioni**, selezionare **Password**.

![Aggiornare la password dell'amministratore](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Rotazione dei segreti

*Queste istruzioni si applicano solo per i sistemi integrati di Azure Stack.*

Quando i sistemi integrati di utilizzando il provider di risorse SQL e MySQL con Azure Stack, l'operatore di Azure Stack è responsabile per la rotazione i segreti di infrastruttura del provider di risorse seguenti per assicurarsi che non scadono:

- Certificato SSL esterno [specificato durante la distribuzione](azure-stack-pki-certs.md).
- Resource provider VM account password dell'amministratore locale specificate durante la distribuzione.
- Password utente diagnostica (dbadapterdiag) provider di risorse.

### <a name="powershell-examples-for-rotating-secrets"></a>Esempi di PowerShell per la rotazione dei segreti

**Modificare tutti i segreti nello stesso momento.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds
```

**Modificare la password dell'utente di diagnostica.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd
```

**Modificare la password dell'account amministratore locale della macchina virtuale.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Modificare la password del certificato SSL.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd
```

### <a name="secretrotationsqlproviderps1-parameters"></a>SecretRotationSQLProvider.ps1 parameters

|Parametro|DESCRIZIONE|
|-----|-----|
|AzCredential|Credenziale dell'account di Azure Stack amministratore del servizio.|
|CloudAdminCredential|Azure Stack cloud amministratore dominio credenziale dell'account.|
|PrivilegedEndpoint|Endpoint il privilegio di accesso Get-AzureStackStampInformation.|
|DiagnosticsUserPassword|Password dell'account utente di diagnostica.|
|VMLocalCredential|Account amministratore locale nella VM MySQLAdapter.|
|DefaultSSLCertificatePassword|Certificato SSL predefinito (* pfx) la password.|
|DependencyFilesLocalPath|Percorso locale per i file delle dipendenze.|
|     |     |

### <a name="known-issues"></a>Problemi noti

**Problema**: Log di rotazione dei segreti.<br>
I log per la rotazione dei segreti non vengono raccolti automaticamente se lo script personalizzato di rotazione segreta non riesce quando viene eseguito.

**Soluzione alternativa**:<br>
Usare il cmdlet Get-AzsDBAdapterLogs per raccogliere tutti i log di provider di risorse, ad esempio AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, salvato in C:\Logs.

## <a name="update-the-virtual-machine-operating-system"></a>Aggiornare il sistema operativo della macchina virtuale

Usare uno dei metodi seguenti per aggiornare il sistema operativo della macchina virtuale.

- Installare il pacchetto di provider di risorse più recente usando un'immagine di Windows Server 2016 Core attualmente con patch.
- Installare un pacchetto di Windows Update durante l'installazione di, o l'aggiornamento a provider di risorse.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Aggiornare le definizioni di Windows Defender di macchina virtuale

Per aggiornare le definizioni di Windows Defender:

1. Scaricare le definizioni di Windows Defender Aggiorna da [definizione di Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions).

   Per le definizioni di aggiornamento pagina, scorrere verso il basso "Scaricare e installare manualmente le definizioni di". Scaricare il file a 64 bit "Windows Defender Antivirus per Windows 10 e Windows 8.1".

   In alternativa, usare [questo collegamento diretto](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) download/esecuzione file fpam fe.exe.

2. Creare una sessione di PowerShell all'endpoint di manutenzione SQL resource provider della scheda della macchina virtuale.

3. Copiare il file di aggiornamento delle definizioni per la macchina virtuale tramite la sessione di endpoint di manutenzione.

4. Nella sessione di PowerShell di manutenzione, eseguire la *Update-DBAdapterWindowsDefenderDefinitions* comando.

5. Dopo aver installato le definizioni, è consigliabile eliminare il file di aggiornamento delle definizioni usando il *Remove-ItemOnUserDrive* comando.

**Esempio di script di PowerShell per l'aggiornamento delle definizioni.**

È possibile modificare ed eseguire lo script seguente per aggiornare le definizioni di Defender. Sostituire i valori nello script con i valori dell'ambiente.

```powershell
# Set credentials for local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download the Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy the defender update file to the adapter virtual machine.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"
# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```

## <a name="collect-diagnostic-logs"></a>Raccogliere i log di diagnostica

Per raccogliere i log dalla macchina virtuale bloccata, è possibile usare l'endpoint PowerShell Just Enough Administration (JEA) *DBAdapterDiagnostics*. Questo endpoint fornisce i comandi seguenti:

- **Get-AzsDBAdapterLog**. Questo comando crea un pacchetto con estensione zip dei log di diagnostica del provider di risorse e Salva il file nell'unità utente della sessione. È possibile eseguire questo comando senza parametri e le ultime quattro ore di log vengono raccolti.
- **Remove-AzsDBAdapterLog**. Questo comando rimuove i pacchetti esistenti di log per il provider di risorse della macchina virtuale.

### <a name="endpoint-requirements-and-process"></a>Processo e sui requisiti di endpoint

Quando un provider di risorse viene installato o aggiornato, il **dbadapterdiag** account utente viene creato. Si userà questo account per raccogliere i log di diagnostica.

>[!NOTE]
>La password dell'account dbadapterdiag è uguale a quella usata per l'amministratore locale nella macchina virtuale che viene creata durante un aggiornamento o la distribuzione del provider.

Usare la *DBAdapterDiagnostics* comandi, creare una sessione remota di PowerShell per la macchina virtuale del provider di risorse ed eseguire il **Get-AzsDBAdapterLog** comando.

Impostare l'intervallo di tempo per la raccolta di log utilizzando il **FromDate** e **ToDate** parametri. Se non si specifica uno o entrambi questi parametri, vengono usati i valori predefiniti seguenti:

- FromDate corrisponde a quattro ore prima dell'ora corrente.
- ToDate è l'ora corrente.

**Esempio di script di PowerShell per la raccolta dei log.**

Lo script seguente illustra come raccogliere i log di diagnostica dai provider di risorse della macchina virtuale.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session.
$session | Remove-PSSession
```

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere i server di hosting di SQL Server](azure-stack-sql-resource-provider-hosting-servers.md)
