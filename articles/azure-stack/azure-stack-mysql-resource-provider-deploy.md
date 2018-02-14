---
title: Utilizzare i database MySQL come PaaS nello Stack di Azure | Documenti Microsoft
description: Informazioni su come distribuire il Provider di risorse MySQL e fornire i database MySQL come servizio nello Stack di Azure.
services: azure-stack
documentationCenter: 
author: mattbriggs
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: mabrigg
ms.openlocfilehash: 3273f435cb65411c85e3a22369682d51e7a12baf
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Utilizzare i database MySQL in Microsoft Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile distribuire un provider di risorse MySQL nello Stack di Azure. Dopo aver distribuito il provider di risorse, è possibile creare MySQL Server e database tramite i modelli di distribuzione Azure Resource Manager. È anche possibile fornire i database MySQL come servizio. 

Database MySQL, che sono comuni nei siti web, supportano molte piattaforme di sito Web. Ad esempio, dopo aver distribuito il provider di risorse, è possibile creare siti Web WordPress dalla piattaforma Web App come un componente aggiuntivo del servizio (PaaS) per lo Stack di Azure.

Per distribuire il provider di MySQL in un sistema che non dispone dell'accesso a Internet, copiare il file [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) in una condivisione locale. Quando richiesto, quindi specificare il nome della condivisione. È necessario installare i moduli di Azure e Azure PowerShell dello Stack.


## <a name="mysql-server-resource-provider-adapter-architecture"></a>Architettura dell'adapter provider risorse MySQL Server

Il provider di risorse è costituito da tre componenti:

- **Adattatore di provider di risorse MySQL VM**, che è una macchina virtuale di Windows che esegue il provider di servizi.

- **Il provider di risorse stesso**, che elabora le richieste di provisioning ed espone le risorse di database.

- **I server che ospitano il MySQL Server**, che forniscono capacità per i database che vengono chiamati i server di hosting.

Questa versione non crea più istanze di MySQL. Ciò significa che è necessario crearle manualmente e/o fornire l'accesso a istanze di SQL esterne. Visitare il [raccolta Guida introduttiva di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) per un modello di esempio che è possibile:
- Creare un server MySQL.
- Scaricare e distribuire un MySQL Server da Azure Marketplace.

> [!NOTE]
> I server che vengono installati in un'implementazione dello Stack di Azure a più nodi di hosting deve essere creato da una sottoscrizione tenant. Non possono essere creati dalla sottoscrizione provider predefinito. Essi devono essere creati dal portale tenant o da una sessione di PowerShell con un accesso appropriato. Tutti i server di hosting sono addebitabile macchine virtuali e devono disporre delle licenze appropriate. L'amministratore del servizio può essere il proprietario della sottoscrizione tenant.

### <a name="required-privileges"></a>Privilegi necessari
L'account di sistema deve disporre dei privilegi seguenti:

1.  Database: Creare, eliminare
2.  Account di accesso: Creazione, impostare, eliminare, concedere, revocare

## <a name="deploy-the-resource-provider"></a>Distribuire il provider di risorse

1. Se non è già stato fatto, registrare il kit di sviluppo e scaricare l'immagine di Windows Server 2016 Data Center Core scaricabile tramite la gestione del Marketplace. È necessario utilizzare un'immagine di Windows Server 2016 Core. È inoltre possibile utilizzare uno script per creare un [immagine di Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Assicurarsi di selezionare l'opzione di core.) Il runtime di .NET 3.5 non è più necessario.


2. Accedere a un host che possa accedere all'endpoint con privilegi di macchina virtuale.

    - Nelle installazioni di Azure SDK, accedere all'host fisico. 
    - Nei sistemi a più nodi, l'host deve essere un sistema che possa accedere all'endpoint con privilegi.
    
    >[!NOTE]
    > Il sistema in cui è in esecuzione lo script *deve* un sistema di Windows 10 o Windows Server 2016 con la versione più recente del runtime di .NET installata. Installazione non riesce in caso contrario. L'host di Azure SDK soddisfa i criteri.
    

3. Scaricare il provider di risorse MySQL binario. Quindi eseguire il programma di autoestrazione per estrarre il contenuto in una directory temporanea.

    >[!NOTE] 
    > La compilazione di provider di risorse corrisponde alle compilazioni dello Stack di Azure. Assicurarsi di scaricare il file binario corretto per la versione dello Stack di Azure che è in esecuzione.

    | Compilazione di Azure Stack | Programma di installazione di MySQL RP |
    | --- | --- |
    | 1.0.180102.3 o 1.0.180106.1 (a più nodi) | [RP MySQL versione 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |
    | 1.0.171122.1 | [RP MySQL versione 1.1.12.0](https://aka.ms/azurestackmysqlrp1711) |
    | 1.0.171028.1 | [RP MySQL versione 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |

4.  Il certificato radice dello Stack di Azure viene recuperato dall'endpoint con privilegi. Per Azure SDK, viene creato un certificato autofirmato come parte di questo processo. Per più nodi, è necessario fornire un certificato appropriato.

    Se è necessario fornire il proprio certificato, inserire un file con estensione pfx nel **DependencyFilesLocalPath** che soddisfa i criteri seguenti:

    - Un certificato con caratteri jolly per \*.dbadapter.\< area\>.\< fqdn esterno\> o un certificato del singolo sito con un nome comune mysqladapter.dbadapter.\< area\>.\< fqdn esterno\>.

    - Questo certificato deve essere considerato attendibile. Vale a dire la catena di certificati deve esistere senza richiedere i certificati intermedi.

    - Solo un singolo file di certificato esiste nel DependencyFilesLocalPath.
    
    - Il nome del file non deve contenere spazi o caratteri speciali.


5. Aprire un **nuova** console di PowerShell con privilegi elevati (amministratore). Quindi passare alla directory in cui sono stati estratti i file. Per evitare problemi che potrebbero verificarsi dei moduli di PowerShell non corretti che sono già caricati nel sistema, utilizzare una nuova finestra.

6. [Installare Azure PowerShell versione 1.2.11](azure-stack-powershell-install.md).

7. Eseguire lo script `DeployMySqlProvider.ps1`.

Lo script esegue le operazioni:

* Scarica il file binario connettore MySQL (ciò può essere fornito non in linea).
* Carica i certificati e altri elementi di un account di archiviazione nello Stack di Azure.
* Pubblica i pacchetti di raccolta in modo che è possibile distribuire il database SQL tramite la raccolta.
* Pubblica un pacchetto di raccolta per la distribuzione di server di hosting.
* Consente di distribuire una macchina virtuale usando l'immagine di Windows Server 2016 che è stato creato nel passaggio 1. Inoltre, viene installato il provider di risorse.
* Registra un record DNS locale che esegue il mapping al provider di risorse macchina virtuale.
* Registra il provider di risorse con locale Gestione risorse di Azure (admin e tenant).


È possibile:
- Specificare i parametri obbligatori nella riga di comando.
- Eseguire senza parametri e quindi immetterli quando richiesto.

Di seguito è riportato un esempio che è possibile eseguire dal prompt di PowerShell. Assicurarsi di modificare le informazioni sull'account e password in base alle esigenze:


```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure SDK, the default is AzureStack, and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files.
# Find the ERCS01 IP address first, and make sure the certificate
# file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploysqlproviderps1-parameters"></a>Parametri DeploySqlProvider.ps1
È possibile specificare questi parametri nella riga di comando. In caso contrario, o qualsiasi parametro di convalida non riesce, viene chiesto di fornire i parametri richiesti.

| Nome parametro | DESCRIZIONE | Commento o il valore predefinito |
| --- | --- | --- |
| **CloudAdminCredential** | Le credenziali per l'amministratore del cloud, necessaria per l'accesso all'endpoint con privilegi. | _Obbligatorio_ |
| **AzCredential** | Le credenziali per l'account amministratore del servizio Azure Stack. Utilizzare le stesse credenziali utilizzate per la distribuzione dello Stack di Azure. | _Obbligatorio_ |
| **VMLocalCredential** | Le credenziali per l'account amministratore locale del provider di risorse MySQL macchina virtuale. | _Obbligatorio_ |
| **PrivilegedEndpoint** | L'indirizzo IP o nome DNS dell'endpoint con privilegi. |  _Obbligatorio_ |
| **DependencyFilesLocalPath** | Percorso di una condivisione locale contenente [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Se si specifica uno di questi percorsi, il file del certificato deve trovarsi in questa directory. | _Parametro facoltativo_ (_obbligatorio_ a nodi multipli) |
| **DefaultSSLCertificatePassword** | La password per il certificato con estensione pfx. | _Obbligatorio_ |
| **MaxRetryCount** | Il numero di volte in cui che si desidera ripetere ogni operazione se si è verificato un errore.| 2 |
| **RetryDuration** | L'intervallo di timeout tra due tentativi, in secondi. | 120 |
| **Disinstallare** | Rimuove il provider di risorse e tutte le risorse associate (vedere le note seguenti). | No  |
| DebugMode | Impedisce la pulizia automatica in caso di errore. | No  |
| **AcceptLicense** | Ignora la richiesta di accettare le condizioni di licenza GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |



Seconda della velocità di download e le prestazioni del sistema, installazione potrebbe richiedere un minimo di 20 minuti o come long come diverse ore. Se il **MySQLAdapter** blade non è disponibile, aggiornare il portale di amministrazione.

> [!NOTE]
> Se l'installazione richiede più di 90 minuti, potrebbe non riuscire. In caso affermativo, viene visualizzato un messaggio di errore sullo schermo e nel file di log. La distribuzione viene ripetuta dal passaggio non riuscito. I sistemi che non soddisfano le specifiche consigliate in memoria e i componenti di base potrebbero non essere in grado di distribuire il RP MySQL.



## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Verificare la distribuzione tramite il portale di Azure Stack

> [!NOTE]
>  Al termine dell'esecuzione dello script di installazione, è necessario aggiornare il portale per visualizzare il pannello di amministrazione.


1. Accedere al portale di amministrazione con l'amministratore del servizio.

2. Verificare che la distribuzione ha avuto esito positivo. Passare a **gruppi di risorse**, quindi selezionare il **system.\< percorso\>.mysqladapter** gruppo di risorse. Verificare che tutte le distribuzioni di quattro ha avuto esito positivo.

      ![Verificare la distribuzione di RP di MySQL](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Offrono capacità connettendosi a un server di hosting MySQL

1. Accedere al portale di Azure Stack come un amministratore del servizio.

2. Selezionare **risorse amministrative** > **i server di Hosting MySQL** > **+ Aggiungi**.

    Nel **server di Hosting MySQL** pannello, è possibile connettersi al provider di risorse di MySQL Server effettive istanze di MySQL Server che servono come back-end del provider di risorse.

    ![Server di hosting](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. Fornire i dettagli di connessione dell'istanza di MySQL Server. Assicurarsi di fornire il nome di dominio completo (FQDN) o un indirizzo IPv4 valido e non il nome breve della macchina virtuale. Questa installazione non include un'istanza di MySQL predefinita. La dimensione che viene fornita consente il provider di risorse di gestire la capacità del database. Dovrebbe essere vicino la capacità fisica del server di database.

    > [!NOTE]
    >Se l'istanza di MySQL è possibile accedere dai tenant e amministrazione di gestione risorse di Azure, può essere inserita nel controllo del codice del provider di risorse. Istanza di MySQL *deve* essere allocata in modo esclusivo al provider di risorse.

4. Quando si aggiungono server, è necessario assegnare a uno SKU di nuovo o esistente per consentire la differenziazione delle offerte di servizio.
  Ad esempio, è possibile avere un'istanza enterprise fornisce:
    - capacità di database
    - Backup automatico
    - server ad alte prestazioni per i singoli reparti di riserva
 

Il nome SKU deve riflettere le proprietà in modo che i tenant è possono posizionare i database in modo appropriato. Tutti i server di hosting in un'unità SKU devono avere le stesse funzionalità.

![Creare uno SKU di MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
> SKU può richiedere a un'ora per essere visibile nel portale. È possibile creare un database fino a quando non viene creato lo SKU.


## <a name="test-your-deployment-by-creating-your-first-mysql-database"></a>Testare la distribuzione tramite la creazione di un database MySQL


1. Accedere al portale di Azure Stack come un amministratore del servizio.

2. Selezionare **+ nuova** > **dati e archiviazione** > **Database MySQL**.

3. Fornire i dettagli del database.

    ![Creare un test di database MySQL](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Selezionare uno SKU.

    ![Selezionare un'unità SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. Creare un'impostazione di account di accesso. È possibile riutilizzare un'impostazione di account di accesso esistente o crearne uno nuovo. Questa impostazione contiene il nome utente e la password per il database.

    ![Creare un nuovo account di database](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    La stringa di connessione include il nome del server di database reali. Copiarlo dal portale.

    ![Ottenere la stringa di connessione per il database MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> La lunghezza dei nomi utente non può superare i 32 caratteri 5.7 MySQL. Nelle versioni precedenti, non può superare i 16 caratteri.


## <a name="add-capacity"></a>Aggiungere capacità

Aggiungere la capacità aggiungendo altri server di MySQL nel portale di Azure Stack. A uno SKU di nuovo o esistente, è possibile aggiungere ulteriori server. Verificare che le caratteristiche server sono uguali.


## <a name="make-mysql-databases-available-to-tenants"></a>Rendere disponibili ai tenant i database MySQL
Creare i piani e le offerte per rendere disponibili i database di MySQL per tenant. Ad esempio, aggiungere il servizio Microsoft.MySqlAdapter, aggiungere una quota e così via.

![Creare i piani e le offerte per includere i database](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>Aggiornare la password amministrativa
È possibile modificare la password può essere modificato nell'istanza del server MySQL. Selezionare **risorse amministrative** > **i server di Hosting MySQL**. Selezionare quindi il server di hosting. Nel **impostazioni** Pannello di seleziona **Password**.

![Aggiornare la password dell'amministratore](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="update-the-mysql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Aggiornare l'adapter di provider di risorse MySQL (a più nodi solo, le compilazioni 1710 e versioni successive)
Ogni volta che viene aggiornata la compilazione dello Stack di Azure, viene rilasciata una nuova scheda di provider di risorse MySQL. L'adapter esistente potrebbe continuare a funzionare. Tuttavia, è consigliabile l'aggiornamento all'ultima build appena possibile dopo l'aggiornamento dello Stack di Azure. 

Il processo di aggiornamento è simile al processo di installazione descritto in precedenza. Si crea una nuova macchina virtuale con il codice del provider di risorse più recente. Quindi si esegue la migrazione le impostazioni per questa nuova istanza, tra cui database e le informazioni sul server di hosting. È possibile la migrazione del record DNS necessario.

Usare lo script UpdateMySQLProvider.ps1 con gli stessi argomenti illustrati in precedenza. Specificare anche il certificato.

> [!NOTE]
> Aggiornamento è supportato solo nei sistemi a più nodi.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

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
. $tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense
 ```

### <a name="updatemysqlproviderps1-parameters"></a>Parametri UpdateMySQLProvider.ps1
È possibile specificare questi parametri nella riga di comando. In caso contrario, o qualsiasi parametro di convalida non riesce, viene chiesto di fornire i parametri richiesti.

| Nome parametro | DESCRIZIONE | Commento o il valore predefinito |
| --- | --- | --- |
| **CloudAdminCredential** | Le credenziali per l'amministratore del cloud, necessaria per l'accesso all'endpoint con privilegi. | _Obbligatorio_ |
| **AzCredential** | Le credenziali per l'account amministratore del servizio Azure Stack. Utilizzare le stesse credenziali utilizzate per la distribuzione dello Stack di Azure. | _Obbligatorio_ |
| **VMLocalCredential** |Le credenziali per l'account amministratore locale del provider di risorse SQL macchina virtuale. | _Obbligatorio_ |
| **PrivilegedEndpoint** | L'indirizzo IP o nome DNS dell'endpoint con privilegi. |  _Obbligatorio_ |
| **DependencyFilesLocalPath** | Il file pfx del certificato deve trovarsi in questa directory. | _Parametro facoltativo_ (_obbligatorio_ a nodi multipli) |
| **DefaultSSLCertificatePassword** | La password per il certificato con estensione pfx | _Obbligatorio_ |
| **MaxRetryCount** | Il numero di volte in cui che si desidera ripetere ogni operazione se si è verificato un errore.| 2 |
| **RetryDuration** | L'intervallo di timeout tra due tentativi, in secondi. | 120 |
| **Disinstallare** | Rimuovere il provider di risorse e tutte le risorse associate (vedere le note seguenti). | No  |
| DebugMode | Impedisce la pulizia automatica in caso di errore. | No  |
| **AcceptLicense** | Ignora la richiesta di accettare le condizioni di licenza GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |

## <a name="remove-the-mysql-resource-provider-adapter"></a>Rimuovere la scheda provider di risorse MySQL

Per rimuovere il provider di risorse, è necessario innanzitutto rimuovere tutte le dipendenze.

1. Assicurarsi di disporre il pacchetto di distribuzione originale scaricati per questa versione del provider di risorse.

2. È necessario eliminare tutti i database tenant dal provider di risorse. (Eliminare i database tenant non eliminare i dati). Questa attività deve essere eseguita per i tenant stessi.

3. È necessario annullare la registrazione di tenant dallo spazio dei nomi.

4. L'amministratore deve eliminare il server di hosting dalla scheda di MySQL.

5. L'amministratore deve eliminare i piani che fanno riferimento l'Adapter di MySQL.

6. L'amministratore deve eliminare le quote che sono associate con l'Adapter di MySQL.

7. Eseguire nuovamente lo script di distribuzione con gli elementi seguenti:
    - -Disinstallare parametro
    - Gli endpoint di gestione risorse di Azure
    - The DirectoryTenantID
    - Le credenziali per l'account amministratore del servizio
