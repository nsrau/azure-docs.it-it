---
title: Mantenere il provider di risorse SQL nello Stack di Azure | Documenti Microsoft
description: Informazioni su come è possibile gestire il servizio provider di risorse SQL nello Stack di Azure.
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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: ad899739dab1dc51d64368d2136ab87f73f6f3a0
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300911"
---
# <a name="sql-resource-provider-maintenance-operations"></a>Operazioni di manutenzione del provider di risorse SQL

Il provider di risorse SQL viene eseguito in una macchina virtuale bloccata. Per abilitare le operazioni di manutenzione, è necessario aggiornare la protezione della macchina virtuale. A tale scopo di utilizzo del principio di privilegio minimo, è possibile utilizzare [PowerShell Just Enough Administration (JEA)](https://docs.microsoft.com/en-us/powershell/jea/overview) endpoint *DBAdapterMaintenance*. Il pacchetto di installazione di provider di risorse include uno script per questa operazione.

## <a name="patching-and-updating"></a>L'applicazione di patch e aggiornamento

Il provider di risorse SQL non viene gestito come parte dello Stack di Azure perché è un componente aggiuntivo. Microsoft include gli aggiornamenti al provider di risorse SQL in base alle esigenze. Quando viene rilasciato un adapter SQL aggiornato, viene fornito uno script per applicare l'aggiornamento. Questo script crea un nuovo provider di risorse macchina virtuale, eseguire la migrazione lo stato del vecchio provider macchina virtuale per la nuova macchina virtuale. Per altre informazioni, vedere [aggiornare il provider di risorse SQL](azure-stack-sql-resource-provider-update.md).

### <a name="provider-virtual-machine"></a>Macchina virtuale del provider

Poiché il provider di risorse viene eseguito su un *utente* macchina virtuale, è necessario applicare le patch e aggiornamenti quando sono rilasciati. È possibile utilizzare i pacchetti di aggiornamento di Windows che vengono forniti come parte del ciclo di patch e aggiornamento per applicare gli aggiornamenti per la macchina virtuale.

## <a name="backuprestoredisaster-recovery"></a>Backup/Restore/ripristino di emergenza

 Poiché si tratta di un componente aggiuntivo, il provider di risorse SQL non viene eseguito il backup come parte di un processo di Azure Stack Business continuità Disaster Recovery (BCDR). Gli script verranno forniti per le operazioni seguenti:

- Backup delle informazioni di stato (archiviate in un account di archiviazione Azure Stack).
- Se è necessario un ripristino completo dello stack, il ripristino del provider di risorse.

>[!NOTE]
>Se è necessario eseguire un ripristino, i server di database devono essere recuperati prima che venga ripristinato il provider di risorse.

## <a name="updating-sql-credentials"></a>Aggiornamento delle credenziali SQL

Si è responsabili della creazione e gestione di account sysadmin in SQL Server. Il provider di risorse è necessario un account con i privilegi per gestire i database per gli utenti, ma non è necessario l'accesso ai dati degli utenti. Se è necessario aggiornare le password sysadmin di SQL Server, è possibile utilizzare l'interfaccia di amministrazione del provider di risorse per modificare una password archiviata. Queste password sono archiviate in un insieme di credenziali chiave sull'istanza dello Stack di Azure.

Per modificare le impostazioni, selezionare **esplorare** &gt; **risorse amministrative** &gt; **istanze di SQL Server che ospita** &gt; **Account di accesso SQL** e selezionare un nome utente. La modifica deve essere apportata nell'istanza di SQL prima (e tutte le repliche, se necessario). Sotto **impostazioni**, selezionare **Password**.

![Aggiornare la password dell'amministratore](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Rotazione di segreti

*Queste istruzioni si applicano solo a Azure Stack integrata sistemi versione 1804 e versioni successive. Non tentare di ruotare i segreti nelle versioni di pre-1804 Azure dello Stack.*

Quando tramite i provider di risorse MySQL e SQL Server con lo Stack di Azure integrati sistemi, è possibile ruotare i segreti dell'infrastruttura (distribuzione) seguenti:

- Certificato SSL esterno [fornito durante la distribuzione](azure-stack-pki-certs.md).
- La risorsa provider VM password di amministratore locale fornite durante la distribuzione.
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

**Modificare la password dell'account amministratore locale macchina virtuale.**

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

### <a name="secretrotationsqlproviderps1-parameters"></a>Parametri SecretRotationSQLProvider.ps1

|Parametro|DESCRIZIONE|
|-----|-----|
|AzCredential|Credenziale dell'account di amministrazione di Stack dei servizi Azure.|
|CloudAdminCredential|Azure Stack cloud admin dominio credenziale dell'account.|
|PrivilegedEndpoint|Endpoint dei privilegi necessari per l'accesso AzureStackStampInformation Get.|
|DiagnosticsUserPassword|Password dell'account utente diagnostica.|
|VMLocalCredential|Account di amministratore locale nella VM MySQLAdapter.|
|DefaultSSLCertificatePassword|Certificato SSL predefinito (* pfx) la password.|
|DependencyFilesLocalPath|Percorso locale i file di dipendenza.|
|     |     |

### <a name="known-issues"></a>Problemi noti

**Problema**: i registri di rotazione dei segreti.<br>
I log per la rotazione dei segreti non vengono raccolti automaticamente se lo script personalizzato rotazione secret ha esito negativo quando viene eseguito.

**Soluzione alternativa**:<br>
Usare il cmdlet Get-AzsDBAdapterLogs per raccogliere tutti i registri di provider di risorse, tra cui AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, salvato in c:\Logs.

## <a name="update-the-virtual-machine-operating-system"></a>Aggiornare il sistema operativo della macchina virtuale

Utilizzare uno dei metodi seguenti per aggiornare il sistema operativo della macchina virtuale.

- Installare il pacchetto di provider di risorse più recente usando un'immagine di Windows Server 2016 Core attualmente con patch.
- Installa un pacchetto di Windows Update durante l'installazione di, o aggiornare il provider di risorse.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Aggiornare le definizioni di Windows Defender di macchina virtuale

Per aggiornare le definizioni di Windows Defender:

1. Download di aggiornare le definizioni di Windows Defender [Windows Defender definizione](https://www.microsoft.com/en-us/wdsi/definitions).

   Sulle definizioni di aggiornamento pagina, scorrere verso il basso "Scaricare e installare manualmente le definizioni". Scaricare il file a 64 bit "Antivirus Windows Defender per Windows 8.1 e Windows 10".

   In alternativa, usare [questo collegamento diretto](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) al download/eseguire il file fpam fe.exe.

2. Creare una sessione di PowerShell all'endpoint di manutenzione SQL resource provider scheda della macchina virtuale.

3. Copiare il file di aggiornamento delle definizioni per la macchina virtuale mediante la sessione di endpoint di manutenzione.

4. Nella sessione di PowerShell di manutenzione, eseguire la *aggiornamento DBAdapterWindowsDefenderDefinitions* comando.

5. Dopo aver installato le definizioni, è consigliabile eliminare il file di aggiornamento di definizioni utilizzando il *Remove-ItemOnUserDrive* comando.

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

Per raccogliere i log dalla macchina virtuale bloccata, è possibile utilizzare l'endpoint di PowerShell Just Enough Administration (JEA) *DBAdapterDiagnostics*. Questo endpoint vengono forniti i comandi seguenti:

- **Get-AzsDBAdapterLog**. Questo comando crea un pacchetto zip dei log di diagnostica del provider di risorse e Salva il file nell'unità utente della sessione. È possibile eseguire questo comando senza parametri e le ultime quattro ore dei log sono raccolti.
- **Remove-AzsDBAdapterLog**. Questo comando rimuove i pacchetti di log esistenti nel provider di risorse macchina virtuale.

### <a name="endpoint-requirements-and-process"></a>Processo e i requisiti di endpoint

Quando un provider di risorse viene installato o aggiornato, il **dbadapterdiag** viene creato l'account utente. Si utilizzerà questo account per raccogliere i log di diagnostica.

>[!NOTE]
>La password dell'account dbadapterdiag equivale la password utilizzata per l'amministratore locale nella macchina virtuale creata durante un aggiornamento o la distribuzione del provider.

Usare la *DBAdapterDiagnostics* comandi, creare una sessione remota di PowerShell per la macchina virtuale del provider di risorse ed eseguire il **Get-AzsDBAdapterLog** comando.

Impostare l'intervallo di tempo per la raccolta di log utilizzando il **FromDate** e **ToDate** parametri. Se non si specifica uno o entrambi questi parametri, vengono utilizzati i valori predefiniti seguenti:

- FromDate corrisponde a quattro ore prima dell'ora corrente.
- ToDate è l'ora corrente.

**Esempio di script di PowerShell per la raccolta dei log.**

Lo script seguente viene illustrato come raccogliere i log di diagnostica dai provider di risorse macchina virtuale.

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

[Aggiungere i server di hosting SQL Server](azure-stack-sql-resource-provider-hosting-servers.md)
