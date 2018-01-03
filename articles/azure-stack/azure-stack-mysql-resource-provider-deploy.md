---
title: Utilizzare i database MySQL come PaaS nello Stack di Azure | Documenti Microsoft
description: Informazioni su come distribuire il Provider di risorse MySQL e fornire i database MySQL come servizio in Azure Stack
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
ms.date: 12/15/2017
ms.author: JeffGo
ms.openlocfilehash: 71abceb1afe315a09ea88b593f9806e9e8b31f16
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Utilizzare i database MySQL in Microsoft Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile distribuire un provider di risorse MySQL nello Stack di Azure. Dopo aver distribuito il provider di risorse, è possibile creare MySQL Server e database tramite i modelli di distribuzione di gestione risorse di Azure e fornire i database MySQL come servizio. Database MySQL, che sono comuni nei siti web, supportano molte piattaforme di sito Web. Ad esempio, dopo aver distribuito il provider di risorse, è possibile creare siti Web WordPress dalla piattaforma Azure Web App come un componente aggiuntivo del servizio (PaaS) per lo Stack di Azure.

Per distribuire il provider di MySQL in un sistema che non dispone dell'accesso a internet, è possibile copiare il file [mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.9.9.msi) in una condivisione locale. Quindi, specificare il nome della condivisione quando richiesto. È inoltre necessario installare i moduli di Azure e Azure PowerShell dello Stack.


## <a name="mysql-server-resource-provider-adapter-architecture"></a>Architettura dell'Adapter Provider risorse di MySQL Server

Il provider di risorse è costituito da tre componenti:

- **Adattatore di provider di risorse MySQL VM**, che è una macchina virtuale Windows in esecuzione i servizi di provider.
- **Il provider di risorse stesso**, che elabora le richieste di provisioning ed espone le risorse di database.
- **I server che ospitano il MySQL Server**, che forniscono capacità per i database, denominato server di Hosting.

Questa versione non crea un'istanza di MySQL. È necessario crearli e/o fornire l'accesso a istanze di SQL esterne. Visitare il [raccolta Guida introduttiva di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) per un modello di esempio che è possibile:
- Crea un server di MySQL per l'utente
- scaricare e distribuire un MySQL Server da Marketplace.

> [!NOTE]
> Server installato in uno Stack di Azure a più nodi di hosting deve essere creato da una sottoscrizione tenant. Non possono essere creati dalla sottoscrizione di Provider predefinito. In altre parole, deve essere creati dal portale tenant o da una sessione di PowerShell con un account di accesso appropriato. Tutti i server di hosting sono addebitabile macchine virtuali e devono disporre delle licenze appropriate. L'amministratore del servizio può essere il proprietario della sottoscrizione in questione.

### <a name="required-privileges"></a>Privilegi necessari
L'account di sistema deve disporre dei privilegi seguenti:

1.  Database: Creare, eliminare
2.  Account di accesso: Creazione, impostare, eliminare, concedere, revocare

## <a name="deploy-the-resource-provider"></a>Distribuire il provider di risorse

1. Se non è già stato fatto, registrare il kit di sviluppo e scaricare l'immagine di Windows Server 2016 Data Center Core scaricabile tramite la gestione del Marketplace. È necessario utilizzare un'immagine di Windows Server 2016 Core. È inoltre possibile utilizzare uno script per creare un [immagine di Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) -assicurarsi di selezionare l'opzione componenti di base. Il runtime di .NET 3.5 non è più necessario.


2. Accedere a un host che può accedere con privilegi Endpoint di macchina virtuale.

    a. Nelle installazioni di Azure Stack Development Kit (ASDK), accedere all'host fisico.

    b. Nei sistemi a più nodi, l'host deve essere un sistema che possa accedere all'Endpoint con privilegi.
    
    >[!NOTE]
    > Il sistema in cui viene eseguito lo script *deve* un sistema di Windows 10 o Windows Server 2016 con la versione più recente del runtime di .NET installata. Installazione avrà esito negativo in caso contrario. L'host ASDK soddisfa i criteri.
    

3. Scaricare il provider di risorse MySQL binario ed eseguire il programma di autoestrazione per estrarre il contenuto in una directory temporanea.

    >[!NOTE] 
    > La compilazione di provider di risorse corrisponde alle compilazioni dello Stack di Azure. È necessario scaricare il file binario corretto per la versione dello Stack di Azure che è in esecuzione.

    | Compilazione di Azure Stack | Programma di installazione di MySQL RP |
    | --- | --- |
    | 1.0.171122.1 | [RP MySQL versione 1.1.12.0](https://aka.ms/azurestackmysqlrp) |
    | 1.0.171028.1 | [RP MySQL versione 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |
    | 1.0.170928.3 | [RP MySQL versione 1.1.3.0](https://aka.ms/azurestackmysqlrp1709) |

4.  Il certificato radice dello Stack di Azure viene recuperato dall'Endpoint con privilegi. Per ASDK, viene creato un certificato autofirmato come parte di questo processo. Per più nodi, è necessario fornire un certificato appropriato.

    Se è necessario fornire il proprio certificato, è necessario un file PFX nella **DependencyFilesLocalPath** (vedere sotto) come indicato di seguito:

    - Un certificato con caratteri jolly per \*.dbadapter.\< area\>.\< fqdn esterno\> o un certificato del singolo sito con un nome comune mysqladapter.dbadapter.\< area\>.\< fqdn esterno\>
    - Questo certificato deve essere considerato attendibile, ad esempio potrebbe essere emesso da un'autorità di certificazione. Vale a dire la catena di certificati deve esistere senza richiedere i certificati intermedi.
    - Solo un singolo file di certificato esiste nel DependencyFilesLocalPath.
    - Il nome del file non deve contenere caratteri speciali.



5. Aprire un **nuova** console di PowerShell con privilegi elevati (amministratore) e modifica della directory in cui sono stati estratti i file. Per evitare problemi che potrebbero emergere dalla corretti moduli PowerShell già caricati nel sistema, utilizzare una nuova finestra.

6. [Installare Azure PowerShell versione 1.2.11](azure-stack-powershell-install.md).

7. Eseguire lo script DeploySqlProvider.ps1.

Lo script esegue le operazioni:

* Scaricare il file binario connettore MySQL (ciò può essere fornito non in linea).
* Caricare i certificati e altri elementi di un account di archiviazione nello Stack Azure.
* Pubblicare pacchetti di raccolta in modo che è possibile distribuire il database SQL tramite la raccolta.
* Pubblicare un pacchetto di raccolta per la distribuzione di server di Hosting
* Distribuire una macchina virtuale utilizzando l'immagine di Windows Server 2016 creato nel passaggio 1 e installare il provider di risorse.
* Registrare un record DNS locale che esegue il mapping al provider di risorse macchina virtuale.
* Registrare il provider di risorse con locale Gestione risorse di Azure (Admin e Tenant).


È possibile:
- specificare almeno i parametri obbligatori nella riga di comando
- In alternativa, se si esegue senza parametri, immetterli quando richiesto.

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
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privleged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files
# Find the ERCS01 IP address first and make sure the certificate
# file is in the specified directory
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploysqlproviderps1-parameters"></a>Parametri DeploySqlProvider.ps1
È possibile specificare questi parametri nella riga di comando. Se non fosse possibile, o qualsiasi parametro convalida non riesce, viene chiesto di fornire le.

| Nome parametro | DESCRIZIONE | Commento o il valore predefinito |
| --- | --- | --- |
| **CloudAdminCredential** | Le credenziali per l'amministratore del cloud, necessaria per l'accesso al Privleged Endpoint. | _obbligatorio_ |
| **AzCredential** | Specificare le credenziali per l'account di amministratore del servizio Azure dello Stack. Utilizzare le stesse credenziali utilizzate per la distribuzione dello Stack di Azure). | _obbligatorio_ |
| **VMLocalCredential** | Definire le credenziali per l'account amministratore locale del provider di risorse MySQL macchina virtuale. | _obbligatorio_ |
| **PrivilegedEndpoint** | Specificare l'indirizzo IP o nome DNS dell'Endpoint con privilegi. |  _obbligatorio_ |
| **DependencyFilesLocalPath** | Percorso alla condivisione locale contenente [mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.9.9.msi). Se si specifica uno, il file del certificato deve trovarsi in questa directory. | _parametro facoltativo_ (_obbligatorio_ a nodi multipli) |
| **DefaultSSLCertificatePassword** | La password per il certificato con estensione pfx | _obbligatorio_ |
| **MaxRetryCount** | Definire il numero di volte che si desidera ripetere ogni operazione se si verifica un errore.| 2 |
| **RetryDuration** | Definire il timeout di tra due tentativi, in secondi. | 120 |
| **Disinstallare** | Rimuovere il provider di risorse e tutte le risorse associate (vedere le note sottostanti) | No  |
| **DebugMode** | Impedisce la pulizia automatica in caso di errore | No  |
| **AcceptLicense** | Ignora la richiesta di accettare le condizioni di licenza GPL (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


Seconda della velocità di download e le prestazioni del sistema, installazione potrebbe richiedere un minimo di 20 minuti o come long come diverse ore. Se il pannello MySQLAdapter non è disponibile, aggiornare il portale di amministrazione.

> [!NOTE]
> Se l'installazione richiede più di 90 minuti, potrebbe non riuscire e si verrà visualizzato un messaggio di errore sullo schermo e nel file di log. La distribuzione viene ripetuta dal passaggio non riuscito. Sistemi che non soddisfano le specifiche consigliate in memoria e i componenti di base non possono essere in grado di distribuire il RP MySQL.



## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Verificare la distribuzione tramite il portale di Azure Stack

> [!NOTE]
>  Al termine dell'esecuzione dello script di installazione, è necessario aggiornare il portale per visualizzare il pannello di amministrazione.


1. Accedere al portale di amministrazione con l'amministratore del servizio.

2. Verificare che la distribuzione ha avuto esito positivo. Cerca **gruppi di risorse** &gt;, fare clic su di **system.\< percorso\>.mysqladapter** risorse di gruppo e verificare che tutte le distribuzioni di quattro ha avuto esito positivo.

      ![Verificare la distribuzione del componente MySQL](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Offrono capacità connettendosi a un server di hosting MySQL

1. Accedere al portale di Azure Stack come un amministratore del servizio.

2. Passare a **risorse amministrative** &gt; **i server di Hosting MySQL** &gt; **+ Aggiungi**.

    Il **server di Hosting MySQL** pannello è dove è possibile connettersi il Provider di risorse di Server MySQL effettive istanze di MySQL Server che servono come back-end del provider di risorse.

    ![Server di hosting](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. Riempire il form con i dettagli della connessione dell'istanza di MySQL Server. Fornire il nome di dominio completo (FQDN) o un indirizzo IPv4 valido e non il nome breve della macchina virtuale. Questa installazione non include un'istanza di MySQL predefinita. Le dimensioni fornite consente al provider di risorse di gestire la capacità del database. Dovrebbe essere vicino la capacità fisica del server di database.

    > [!NOTE]
    > Fino a quando l'istanza di MySQL è possibile accedere dai tenant e amministrazione di gestione risorse di Azure, può essere inserita nel controllo del codice del provider di risorse. Istanza di MySQL __deve__ essere allocate esclusivamente al relying Party.

4. Quando si aggiungono server, è necessario assegnare a uno SKU di nuovo o esistente per consentire la differenziazione delle offerte di servizio.
  Ad esempio, è possibile avere un'istanza enterprise fornisce:
    - capacità di database
    - Backup automatico
    - server ad alte prestazioni per i singoli reparti di riserva
 

Il nome SKU deve riflettere le proprietà in modo che i tenant è possono posizionare i database in modo appropriato. Tutti i server di hosting in un'unità SKU devono avere le stesse funzionalità.

![Creare uno SKU di MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
> SKU può richiedere a un'ora per essere visibile nel portale. È possibile creare un database fino a quando non viene creato lo SKU.


## <a name="to-test-your-deployment-create-your-first-mysql-database"></a>Per testare la distribuzione, creare il primo database MySQL


1. Accedere al portale di Azure Stack come amministratore del servizio.

2. Fare clic su di **+ nuovo** pulsante &gt; **dati e archiviazione** &gt; **MySQL Database**.

3. Compilare il modulo con i dettagli del database.

    ![Creare un test di database MySQL](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Selezionare uno SKU.

    ![Selezionare un'unità SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. Creare un'impostazione di account di accesso. L'impostazione di account di accesso può essere riutilizzato o crearne una nuova. Questa impostazione contiene il nome utente e la password per il database.

    ![Creare un nuovo account di database](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    La stringa di connessione include il nome del server di database reali. Copiarlo dal portale.

    ![Ottenere la stringa di connessione per il database MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> La lunghezza dei nomi utente non può superare i 32 caratteri con MySQL 5.7 o 16 caratteri nelle versioni precedenti.


## <a name="add-capacity"></a>Aggiungere capacità

Aggiungere la capacità aggiungendo altri server di MySQL nel portale di Azure Stack. A uno SKU di nuovo o esistente, è possibile aggiungere ulteriori server. Verificare che le caratteristiche server sono uguali.


## <a name="make-mysql-databases-available-to-tenants"></a>Rendere disponibili ai tenant i database MySQL
Creare i piani e le offerte per rendere disponibili i database di MySQL per tenant. Aggiungere il servizio Microsoft.MySqlAdapter, aggiungere una quota e così via.

![Creare i piani e le offerte per includere i database](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>Aggiornare la password amministrativa
È possibile modificare la password può essere modificato nell'istanza del server MySQL. Passare a **risorse amministrative** &gt; **server di Hosting MySQL** &gt; e fare clic sul server di hosting. Nel pannello impostazioni, fare clic su Password.

![Aggiornare la password dell'amministratore](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="update-the-mysql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Aggiornare l'Adapter di Provider di risorse MySQL (a più nodi solo, le compilazioni 1710 e versioni successive)
Ogni volta che viene aggiornata la compilazione dello Stack di Azure, verrà rilasciato un nuovo adattatore di Provider di risorse MySQL. Mentre l'adapter esistente potrebbe continuare a funzionare, è consigliabile aggiornare quanto prima all'ultima build dopo aver aggiornato lo Stack di Azure. Il processo di aggiornamento è molto simile al processo di installazione descritto in precedenza. Verrà creata una nuova macchina virtuale con il codice più recente di relying Party e impostazioni verranno migrate a questa istanza di nuovo tra database e l'hosting di informazioni sul server, nonché il record DNS necessario.

Usare lo script UpdateMySQLProvider.ps1 con gli stessi argomenti come illustrato in precedenza. È necessario fornire anche il certificato.

> [!NOTE]
> Aggiornamento è supportato solo nei sistemi a più nodi.

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

# Set credentials for the new Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
. $tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense
 ```

### <a name="updatemysqlproviderps1-parameters"></a>Parametri UpdateMySQLProvider.ps1
È possibile specificare questi parametri nella riga di comando. Se non fosse possibile, o qualsiasi parametro convalida non riesce, viene chiesto di fornire le.

| Nome parametro | DESCRIZIONE | Commento o il valore predefinito |
| --- | --- | --- |
| **CloudAdminCredential** | Le credenziali per l'amministratore del cloud, necessaria per l'accesso all'Endpoint con privilegi. | _obbligatorio_ |
| **AzCredential** | Specificare le credenziali per l'account di amministratore del servizio Azure dello Stack. Utilizzare le stesse credenziali utilizzate per la distribuzione dello Stack di Azure). | _obbligatorio_ |
| **VMLocalCredential** | Definire le credenziali per l'account amministratore locale del provider di risorse SQL macchina virtuale. | _obbligatorio_ |
| **PrivilegedEndpoint** | Specificare l'indirizzo IP o nome DNS del Privleged Endpoint. |  _obbligatorio_ |
| **DependencyFilesLocalPath** | Il file PFX del certificato deve trovarsi in questa directory. | _parametro facoltativo_ (_obbligatorio_ a nodi multipli) |
| **DefaultSSLCertificatePassword** | La password per il certificato con estensione pfx | _obbligatorio_ |
| **MaxRetryCount** | Definire il numero di volte che si desidera ripetere ogni operazione se si verifica un errore.| 2 |
| **RetryDuration** | Definire il timeout di tra due tentativi, in secondi. | 120 |
| **Disinstallare** | Rimuovere il provider di risorse e tutte le risorse associate (vedere le note sottostanti) | No  |
| **DebugMode** | Impedisce la pulizia automatica in caso di errore | No  |
| **AcceptLicense** | Ignora la richiesta di accettare le condizioni di licenza GPL (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |

## <a name="remove-the-mysql-resource-provider-adapter"></a>Rimuovere la scheda Provider di risorse MySQL

Per rimuovere il provider di risorse, è necessario innanzitutto rimuovere tutte le dipendenze.

1. Assicurarsi di disporre del pacchetto di distribuzione originale scaricati per questa versione del Provider di risorse.

2. È necessario eliminare tutti i database tenant dal provider di risorse (non eliminare i dati). Questa deve essere eseguita per i tenant stessi.

3. È necessario annullare la registrazione di tenant dallo spazio dei nomi.

4. Amministratore deve eliminare il server di hosting dalla scheda di MySQL

5. Amministratore deve eliminare i piani che fanno riferimento l'Adapter di MySQL.

6. Amministratore deve eliminare le quote associate alla scheda di MySQL.

7. Eseguire di nuovo lo script di distribuzione con il parametro - disinstallare parametro, gli endpoint di gestione risorse di Azure, DirectoryTenantID e le credenziali per l'account amministratore del servizio.




## <a name="next-steps"></a>Passaggi successivi

Provare altre [servizi PaaS](azure-stack-tools-paas-services.md) come il [il provider di risorse di SQL Server](azure-stack-sql-resource-provider-deploy.md) e [il provider di risorse di servizi App](azure-stack-app-service-overview.md).
