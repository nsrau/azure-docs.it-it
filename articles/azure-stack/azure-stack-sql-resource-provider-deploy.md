---
title: Utilizzo di database SQL Azure stack | Documenti Microsoft
description: Informazioni su come distribuire i database SQL come servizio in Azure Stack e la procedura per distribuire l'adapter di provider di risorse di SQL Server
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: 31ffd31b5d540617c4a7a1224e6cf0ee656c9678
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2017
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Utilizzare i database SQL nello Stack di Microsoft Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Utilizzare l'adapter di provider di risorse di SQL Server per esporre i database SQL come servizio di [Azure Stack](azure-stack-poc.md). Dopo aver installato il provider di risorse e connetterla a uno o più istanze di SQL Server, gli utenti possono creare:
- database per le app cloud nativo
- siti Web basati su SQL
- i carichi di lavoro che si basano su SQL non è necessario eseguire il provisioning di una macchina virtuale (VM) che ospita SQL Server ogni volta.

Il provider di risorse non supporta tutte le funzionalità di gestione di database di [Database SQL di Azure](https://azure.microsoft.com/services/sql-database/). Ad esempio, pool di database elastici e la possibilità di comporre automaticamente le prestazioni del database su e giù non sono disponibili. Tuttavia, risorse provider supportati simile creare, leggere, aggiornare ed eliminazione (CRUD). L'API non è compatibile con i database di SQL Server.

## <a name="sql-resource-provider-adapter-architecture"></a>Architettura dell'Adapter di Provider di risorse SQL
Il provider di risorse è costituito da tre componenti:

- **L'adapter di provider di risorse SQL VM**, che è una macchina virtuale Windows in esecuzione i servizi di provider.
- **Il provider di risorse stesso**, che elabora le richieste di provisioning ed espone le risorse di database.
- **I server che ospitano SQL Server**, che forniscono capacità per i database, denominato server di Hosting.

È necessario creare uno o più SQL Server e/o fornire l'accesso a istanze di SQL esterne.

## <a name="deploy-the-resource-provider"></a>Distribuire il provider di risorse

1. Se non è già stato fatto, registrare il kit di sviluppo e scaricare l'immagine di Windows Server 2016 Data Center Core scaricabile tramite la gestione del Marketplace. È necessario utilizzare un'immagine di Windows Server 2016 Core. È inoltre possibile utilizzare uno script per creare un [immagine di Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) -assicurarsi di selezionare l'opzione componenti di base. Il runtime di .NET 3.5 non è più necessario.

2. Accedere a un host che può accedere con privilegi Endpoint di macchina virtuale.

    a. Nelle installazioni di Azure Stack Development Kit (ASDK), accedere all'host fisico.

    b. Nei sistemi a più nodi, l'host deve essere un sistema che possa accedere all'Endpoint con privilegi.

3. [Scaricare i file binari del provider di risorse SQL](https://aka.ms/azurestacksqlrp) ed eseguire il programma di autoestrazione per estrarre il contenuto in una directory temporanea.

    > [!NOTE]
    > Se si è in esecuzione in uno Stack di Azure genera 20170928.3 o versioni precedenti, [scaricare questa versione](https://aka.ms/azurestacksqlrp1709).

4. Il certificato radice dello Stack di Azure viene recuperato dall'Endpoint con privilegi. Per ASDK, viene creato un certificato autofirmato come parte di questo processo. Per più nodi, è necessario fornire un certificato appropriato.

    Se è necessario fornire il proprio certificato, è necessario il certificato seguente:

    Un certificato con caratteri jolly per \*.dbadapter.\< area\>.\< fqdn esterno\>. Questo certificato deve essere considerato attendibile, ad esempio potrebbe essere emesso da un'autorità di certificazione. Vale a dire la catena di certificati deve esistere senza richiedere i certificati intermedi. Con il nome di macchina virtuale esplicito [sqladapter] utilizzato durante l'installazione, è possibile utilizzare un certificato del singolo sito.


5. Aprire un **nuova** console di PowerShell con privilegi elevati (amministratore) e modifica della directory in cui sono stati estratti i file. Per evitare problemi che potrebbero emergere dalla corretti moduli PowerShell già caricati nel sistema, utilizzare una nuova finestra.

6. [Installare Azure PowerShell versione 1.2.11](azure-stack-powershell-install.md).

7. Eseguire lo script DeploySqlProvider.ps1 con i parametri elencati di seguito.

Lo script esegue le operazioni:

- Caricare i certificati e altri elementi di un account di archiviazione nello Stack Azure.
- Pubblicare pacchetti di raccolta in modo che è possibile distribuire il database SQL tramite la raccolta.
- Pubblicare un pacchetto di raccolta per la distribuzione di server di Hosting
- Distribuire una macchina virtuale utilizzando l'immagine di Windows Server 2016 creato nel passaggio 1 e installare il provider di risorse.
- Registrare un record DNS locale che esegue il mapping al provider di risorse macchina virtuale.
- Registrare il provider di risorse con locale Gestione risorse di Azure (utente e amministratore).

> [!NOTE]
> Se l'installazione richiede più di 90 minuti, potrebbe non riuscire e viene visualizzato un messaggio di errore sullo schermo e nel file di log, ma la distribuzione viene ritentata dal passaggio non riuscito. I sistemi che non soddisfano le specifiche consigliate in memoria e CPU virtuali potrebbero non essere in grado di distribuire il RP SQL.
>

Di seguito è riportato un esempio è possibile eseguire da PowerShell richiesto (ma modificare le informazioni sull'account e password in base alle esigenze):

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack and the default prefix is AzS
# For integrated systems, the domain and the prefix will be the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>Parametri DeploySqlProvider.ps1
È possibile specificare questi parametri nella riga di comando. Se non fosse possibile, o qualsiasi parametro convalida non riesce, viene chiesto di fornire le.

| Nome parametro | Descrizione | Commento o il valore predefinito |
| --- | --- | --- |
| **CloudAdminCredential** | Le credenziali per l'amministratore del cloud, necessaria per l'accesso all'Endpoint con privilegi. | _obbligatorio_ |
| **AzCredential** | Specificare le credenziali per l'account di amministratore del servizio Azure dello Stack. Utilizzare le stesse credenziali utilizzate per la distribuzione dello Stack di Azure). | _obbligatorio_ |
| **VMLocalCredential** | Definire le credenziali per l'account amministratore locale del provider di risorse SQL macchina virtuale. | _obbligatorio_ |
| **PrivilegedEndpoint** | Specificare l'indirizzo IP o nome DNS del Privleged Endpoint. |  _obbligatorio_ |
| **DependencyFilesLocalPath** | Il file PFX del certificato deve trovarsi in questa directory. | _parametro facoltativo_ (_obbligatorio_ a nodi multipli) |
| **DefaultSSLCertificatePassword** | La password per il certificato con estensione pfx | _obbligatorio_ |
| **MaxRetryCount** | Definire il numero di volte che si desidera ripetere ogni operazione se si verifica un errore.| 2 |
| **RetryDuration** | Definire il timeout di tra due tentativi, in secondi. | 120 |
| **Disinstallare** | Rimuovere il provider di risorse e tutte le risorse associate (vedere le note sottostanti) | No |
| **DebugMode** | Impedisce la pulizia automatica in caso di errore | No |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Verificare la distribuzione tramite il portale di Azure Stack

> [!NOTE]
>  Al termine dell'esecuzione dello script di installazione, è necessario aggiornare il portale per visualizzare il pannello di amministrazione.


1. Accedere al portale di amministrazione con l'amministratore del servizio.

2. Verificare che la distribuzione ha avuto esito positivo. Cerca **gruppi di risorse** &gt;, fare clic su di **system.\< percorso\>.sqladapter** risorse di gruppo e verificare che tutte le distribuzioni di quattro ha avuto esito positivo.

      ![Verificare la distribuzione del componente SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="remove-the-sql-resource-provider-adapter"></a>Rimuovere la scheda Provider di risorse SQL

Per rimuovere il provider di risorse, è essenziale prima di rimuovere eventuali dipendenze.

1. Assicurarsi di disporre del pacchetto di distribuzione originale scaricati per questa versione di adattatore di Provider di risorse di SQL.

2. È necessario eliminare tutti i database utente dal provider di risorse (non eliminare i dati). Questa operazione deve essere eseguita dagli utenti stessi.

3. Amministratore deve eliminare il server di hosting dalla scheda Provider di risorse SQL

4. Amministratore deve eliminare i piani che fanno riferimento l'Adapter di Provider di risorse di SQL.

5. Amministratore deve eliminare qualsiasi SKU e quote associate all'adapter di Provider di risorse di SQL.

6. Eseguire di nuovo lo script di distribuzione con il parametro - disinstallare parametro, gli endpoint di gestione risorse di Azure, DirectoryTenantID e le credenziali per l'account amministratore del servizio.


## <a name="next-steps"></a>Passaggi successivi

[Aggiungere i server di Hosting](azure-stack-sql-resource-provider-hosting-servers.md) e [creare database](azure-stack-sql-resource-provider-databases.md).

Provare altre [servizi PaaS](azure-stack-tools-paas-services.md) come il [il provider di risorse di MySQL Server](azure-stack-mysql-resource-provider-deploy.md) e [il provider di risorse di servizi App](azure-stack-app-service-overview.md).
