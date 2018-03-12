---
title: Utilizzo di database SQL Azure stack | Documenti Microsoft
description: Informazioni su come distribuire i database SQL come servizio in Azure Stack e la procedura per distribuire l'adapter di provider di risorse di SQL Server.
services: azure-stack
documentationCenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: 4d2a00f04e5b07aeb3585fb3ab6c8966e0de7e19
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Utilizzare i database SQL nello Stack di Microsoft Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Utilizzare l'adapter di provider di risorse di SQL Server per esporre i database SQL come servizio di [Azure Stack](azure-stack-poc.md). Dopo aver installato il provider di risorse e connetterla a uno o più istanze di SQL Server, gli utenti possono creare:
- Database per le app cloud nativo.
- Siti Web basati su SQL.
- Carichi di lavoro basati su SQL.
Non è necessario eseguire il provisioning di una macchina virtuale (VM) che ospita SQL Server ogni volta.

Il provider di risorse non supporta tutte le funzionalità di gestione di database di [Database SQL di Azure](https://azure.microsoft.com/services/sql-database/). Ad esempio, pool di Database elastici e la possibilità di comporre su e giù automaticamente le prestazioni del database non sono disponibili. Tuttavia, risorse provider supportati simile creare, leggere, aggiornare ed eliminazione (CRUD). L'API non è compatibile con il Database SQL.

## <a name="sql-resource-provider-adapter-architecture"></a>Architettura dell'adapter SQL resource provider
Il provider di risorse è costituito da tre componenti:

- **L'adapter di provider di risorse SQL VM**, che è una macchina virtuale di Windows che esegue i servizi di provider.
- **Il provider di risorse stesso**, che elabora le richieste di provisioning ed espone le risorse di database.
- **I server che ospitano SQL Server**, che forniscono capacità per i database denominato server di hosting.

È necessario creare istanze di uno (o più) di SQL Server e/o fornire l'accesso alle istanze di SQL Server esterne.

> [!NOTE]
> I server che vengono installati nello Stack di Azure di hosting sistemi integrati devono essere creati da una sottoscrizione tenant. Non possono essere creati dalla sottoscrizione provider predefinito. Essi devono essere creati dal portale tenant o da una sessione di PowerShell con un accesso appropriato. Tutti i server di hosting sono addebitabile macchine virtuali e devono disporre delle licenze appropriate. L'amministratore del servizio può essere il proprietario della sottoscrizione tenant.

## <a name="deploy-the-resource-provider"></a>Distribuire il provider di risorse

1. Se non è già stato fatto, registrare il kit di sviluppo e scaricare l'immagine di Windows Server 2016 Data Center Core scaricabile tramite la gestione del Marketplace. È necessario utilizzare un'immagine di Windows Server 2016 Core. È inoltre possibile utilizzare uno script per creare un [immagine di Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Assicurarsi di selezionare l'opzione di core.)

2. Accedere a un host che possa accedere all'endpoint con privilegi di macchina virtuale.

    - Le installazioni del Kit di sviluppo dello Stack di Azure, accedere all'host fisico.

    - Nei sistemi a più nodi, l'host deve essere un sistema che possa accedere all'endpoint con privilegi.
    
    >[!NOTE]
    > Il sistema in cui viene eseguito lo script *deve* un sistema di Windows 10 o Windows Server 2016 con la versione più recente del runtime di .NET installata. Installazione non riesce in caso contrario. L'host di Azure SDK Stack soddisfa questo criterio.


3. Scaricare il provider di risorse SQL binario. Quindi eseguire il programma di autoestrazione per estrarre il contenuto in una directory temporanea.

    >[!NOTE] 
    > Il provider di risorse dispone di uno Stack di Azure corrispondente minimo di compilazione. Assicurarsi di scaricare il file binario corretto per la versione dello Stack di Azure che è in esecuzione.

    | Compilazione di Azure Stack | Installazione di provider di risorse SQL |
    | --- | --- |
    | 1802: 1.0.180302.1 | [RP SQL versione 1.1.18.0](https://aka.ms/azurestacksqlrp1802) |
    | 1712: 1.0.180102.3, 1.0.180103.2 o 1.0.180106.1 (a più nodi) | [RP SQL versione 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1711: 1.0.171122.1 | [RP SQL versione 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1710: 1.0.171028.1 | [RP SQL versione 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. Il certificato radice dello Stack di Azure viene recuperato dall'endpoint con privilegi. Per il SDK di Stack di Azure, viene creato un certificato autofirmato come parte di questo processo. Per i sistemi integrati, è necessario fornire un certificato appropriato.

   Per fornire il proprio certificato, inserire un file con estensione pfx nel **DependencyFilesLocalPath** come indicato di seguito:

    - Un certificato con caratteri jolly per \*.dbadapter.\< area\>.\< fqdn esterno\> o un certificato del singolo sito con un nome comune sqladapter.dbadapter.\< area\>.\< fqdn esterno\>.

    - Questo certificato deve essere considerato attendibile. Vale a dire la catena di certificati deve esistere senza richiedere i certificati intermedi.

    - Nella directory a cui puntata il parametro DependencyFilesLocalPath, può esistere solo un singolo file di certificato.

    - Il nome del file non deve contenere caratteri speciali.


5. Aprire un **nuova** console di PowerShell con privilegi elevati (amministratore) e modifica della directory in cui sono stati estratti i file. Per evitare problemi che potrebbero verificarsi dei moduli di PowerShell non corretti che sono già caricati nel sistema, utilizzare una nuova finestra.

6. [Installare Azure PowerShell versione 1.2.11](azure-stack-powershell-install.md).

7. Eseguire lo script DeploySqlProvider.ps1, che consente di eseguire questi passaggi:

    - Carica i certificati e altri elementi di un account di archiviazione nello Stack di Azure.
    - Pubblica i pacchetti di raccolta in modo che è possibile distribuire il database SQL tramite la raccolta.
    - Pubblica un pacchetto di raccolta per la distribuzione di server di hosting.
    - Consente di distribuire una macchina virtuale usando l'immagine di Windows Server 2016 che è stato creato nel passaggio 1 e quindi installa il provider di risorse.
    - Registra un record DNS locale che esegue il mapping al provider di risorse macchina virtuale.
    - Registra il provider di risorse con locale Gestione risorse di Azure (utente e amministratore).
    - Consente di installare, facoltativamente, un singolo aggiornamento di Windows durante l'installazione di relying Party

8. Si consiglia di scaricare l'immagine di Windows Server 2016 Core più recente dalla gestione Marketplace. Se è necessario installare un aggiornamento, è possibile inserire un singolo. Pacchetto MSU nel percorso locale. Se più oggetti. È possibile trovare il file MSU, lo script avrà esito negativo.


Di seguito è riportato un esempio che è possibile eseguire dal prompt di PowerShell. (Assicurarsi di modificare le informazioni sull'account e password in base alle necessità.)

```
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

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>Parametri DeploySqlProvider.ps1
È possibile specificare questi parametri nella riga di comando. In caso contrario, o qualsiasi parametro di convalida non riesce, viene chiesto di fornire i parametri richiesti.

| Nome parametro | DESCRIZIONE | Commento o il valore predefinito |
| --- | --- | --- |
| **CloudAdminCredential** | Le credenziali per l'amministratore del cloud, necessaria per l'accesso all'endpoint con privilegi. | _Obbligatorio_ |
| **AzCredential** | Le credenziali per l'account amministratore del servizio Azure Stack. Utilizzare le stesse credenziali utilizzate per la distribuzione dello Stack di Azure. | _Obbligatorio_ |
| **VMLocalCredential** | Le credenziali per l'account amministratore locale del provider di risorse SQL macchina virtuale. | _Obbligatorio_ |
| **PrivilegedEndpoint** | L'indirizzo IP o nome DNS dell'endpoint con privilegi. |  _Obbligatorio_ |
| **DependencyFilesLocalPath** | Il file pfx del certificato deve trovarsi in questa directory. | _Parametro facoltativo_ (_obbligatorio_ a nodi multipli) |
| **DefaultSSLCertificatePassword** | La password per il certificato con estensione pfx. | _Obbligatorio_ |
| **MaxRetryCount** | Il numero di volte in cui che si desidera ripetere ogni operazione se si è verificato un errore.| 2 |
| **RetryDuration** | L'intervallo di timeout tra due tentativi, in secondi. | 120 |
| **Disinstallare** | Rimuove il provider di risorse e tutte le risorse associate (vedere le note seguenti). | No  |
| **DebugMode** | Impedisce la pulizia automatica in caso di errore. | No  |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Verificare la distribuzione tramite il portale di Azure Stack

> [!NOTE]
>  Al termine dell'esecuzione dello script di installazione, è necessario aggiornare il portale per visualizzare il pannello di amministrazione.


1. Accedere al portale di amministrazione con l'amministratore del servizio.

2. Verificare che la distribuzione ha avuto esito positivo. Passare a **gruppi di risorse**. Selezionare quindi il **system.\< percorso\>.sqladapter** gruppo di risorse. Verificare che tutte le distribuzioni di quattro ha avuto esito positivo.

      ![Verificare la distribuzione del provider di risorse SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Aggiornare l'adapter di provider di risorse SQL (a più nodi solo, le compilazioni 1710 e versioni successive)
Un nuovo adattatore di provider di risorse SQL potrebbe essere rilasciato quando vengono aggiornate le compilazioni di Stack di Azure. Mentre l'adapter esistente continua a funzionare, si consiglia di aggiornare appena possibile per la build più recente. Gli aggiornamenti devono essere installati nell'ordine: non è possibile ignorare le versioni (vedere la tabella nel passaggio 3 di [distribuire il provider di risorse](#deploy-the-resource-provider)).

Per l'aggiornamento del provider di risorse a cui si utilizza il *UpdateSQLProvider.ps1* script. Il processo è simile a quello utilizzato per installare un provider di risorse, come descritto nel [distribuire il provider di risorse](#deploy-the-resource-provider) sezione di questo articolo. Lo script è incluso con il download del provider di risorse.

Il *UpdateSQLProvider.ps1* script crea una nuova macchina virtuale con il codice di provider di risorse più recente e consente di migrare le impostazioni dalla macchina virtuale precedente per la nuova macchina virtuale. Le impostazioni di cui eseguire la migrazione includono database e le informazioni sul server di hosting e registra il DNS necessario.

Lo script richiede l'utilizzo degli stessi argomenti descritti per lo script DeploySqlProvider.ps1. Specificare anche il certificato. 

Si consiglia di scaricare l'immagine di Windows Server 2016 Core più recente dalla gestione Marketplace. Se è necessario installare un aggiornamento, è possibile inserire un singolo. Pacchetto MSU nel percorso locale. Se più oggetti. È possibile trovare il file MSU, lo script avrà esito negativo.

Di seguito è riportato un esempio del *UpdateSQLProvider.ps1* script che è possibile eseguire dal prompt di PowerShell. Assicurarsi di modificare le informazioni sull'account e password in base alle esigenze: 

> [!NOTE]
> Il processo di aggiornamento si applica solo ai sistemi integrati.

```
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

### <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 parameters
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


## <a name="collect-diagnostic-logs"></a>Raccogliere i log di diagnostica
Il provider di risorse SQL è un controllo bloccato inattivo macchina virtuale. Se è necessario raccogliere i log dalla macchina virtuale, un endpoint di PowerShell Just Enough Administration (JEA) _DBAdapterDiagnostics_ viene fornito a tale scopo. Sono disponibili due comandi tramite questo endpoint:

* Get-AzsDBAdapterLog - prepara un pacchetto zip contenente i registri di diagnostica RP e lo inserisce nell'unità sessione utente. Il comando può essere chiamato senza parametri e raccoglierà le ultime quattro ore dei log.
* Remove-AzsDBAdapterLog - pulisce i pacchetti di log esistenti nel provider di risorse macchina virtuale

Un account utente denominato _dbadapterdiag_ viene creato durante la distribuzione di relying Party o aggiornamento per la connessione all'endpoint di diagnostica per l'estrazione dei registri di relying Party. La password dell'account è uguale a quella fornita per l'account amministratore locale durante l'installazione/aggiornamento.

Per utilizzare questi comandi, è necessario creare una sessione remota di PowerShell per la macchina virtuale del provider di risorse e richiamare il comando. È anche possibile specificare parametri FromDate e ToDate. Se non si specifica una o entrambe queste opzioni, FromDate sarà quattro ore prima dell'ora corrente e il ToDate sarà l'ora corrente.

Questo script di esempio viene illustrato l'utilizzo di questi comandi:

```
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="maintenance-operations-integrated-systems"></a>Operazioni di manutenzione (sistemi integrati)
Il provider di risorse SQL è un controllo bloccato inattivo macchina virtuale. Aggiornamento della protezione della macchina virtuale la risorsa del provider può essere eseguita tramite l'endpoint di PowerShell Just Enough Administration (JEA) _DBAdapterMaintenance_.

Uno script viene fornito con il pacchetto di installazione del componente per semplificare queste operazioni.

### <a name="update-the-virtual-machine-operating-system"></a>Aggiornare il sistema operativo della macchina virtuale
Esistono diversi modi per aggiornare la macchina virtuale di Windows Server:
* Installare il pacchetto di provider di risorse più recente usando un'immagine di Windows Server 2016 Core attualmente con patch
* Installa un pacchetto di Windows Update durante l'installazione o aggiornamento del componente


### <a name="update-the-virtual-machine-windows-defender-definitions"></a>Aggiornare le definizioni di Windows Defender di macchina virtuale

Seguire questi passaggi per aggiornare le definizioni di Defender:

1. Download di aggiornare le definizioni di Windows Defender [Windows Defender definizione](https://www.microsoft.com/en-us/wdsi/definitions)

    In questa pagina in "Scaricare e installare manualmente le definizioni" Scarica "Antivirus di Windows Defender per Windows 10 e Windows 8.1" il file a 64 bit. 
    
    Collegamento diretto: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. Creare una sessione di PowerShell all'endpoint di manutenzione della macchina virtuale di SQL RP adapter
3. Copiare il file di aggiornamento delle definizioni al computer di adapter DB utilizzando la sessione di manutenzione endpoint
4. Nella pagina Manutenzione PowerShell sessione richiamare il _aggiornamento DBAdapterWindowsDefenderDefinitions_ comando
5. Dopo l'installazione, è consigliabile rimuovere il file di aggiornamento tra le definizioni. Può essere rimosso della sessione di manutenzione utilizzando la _Remove-ItemOnUserDrive)_ comando.


Di seguito è riportato un esempio di script per aggiornare le definizioni di Defender (sostituire l'indirizzo o il nome della macchina virtuale con il valore effettivo):

```
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```

## <a name="remove-the-sql-resource-provider-adapter"></a>Rimuovere la scheda provider di risorse SQL

Per rimuovere il provider di risorse, è necessario innanzitutto rimuovere tutte le dipendenze.

1. Assicurarsi di disporre il pacchetto di distribuzione originale scaricati per questa versione di adattatore di provider di risorse SQL.

2. È necessario eliminare tutti i database utente dal provider di risorse. (L'eliminazione dei database utente non elimina i dati.) Questa attività deve essere eseguita dagli utenti stessi.

3. L'amministratore deve eliminare il server di hosting dalla scheda provider di risorse di SQL.

4. L'amministratore deve eliminare i piani che fanno riferimento l'adapter di provider di risorse SQL.

5. L'amministratore deve eliminare qualsiasi SKU e quote che sono associate all'adapter di provider di risorse SQL.

6. Eseguire nuovamente lo script di distribuzione con gli elementi seguenti:
    - -Disinstallare parametro
    - Gli endpoint di gestione risorse di Azure
    - The DirectoryTenantID
    - Le credenziali per l'account amministratore del servizio


## <a name="next-steps"></a>Passaggi successivi

[Aggiungere i server di hosting](azure-stack-sql-resource-provider-hosting-servers.md) e [creare database](azure-stack-sql-resource-provider-databases.md).

Provare altre [servizi PaaS](azure-stack-tools-paas-services.md) come il [il provider di risorse di MySQL Server](azure-stack-mysql-resource-provider-deploy.md) e [il provider di risorse di servizi App](azure-stack-app-service-overview.md).
