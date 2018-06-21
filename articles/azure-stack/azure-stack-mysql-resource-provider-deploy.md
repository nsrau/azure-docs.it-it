---
title: Utilizzando i database MySQL su Azure Stack | Documenti Microsoft
description: Informazioni su come distribuire i database MySQL come servizio in Azure Stack e le azioni rapide per distribuire l'adapter di provider di risorse di MySQL Server.
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
ms.openlocfilehash: 061d9431890622dafafa8c180027b8df735c100e
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295756"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Utilizzare i database MySQL in Microsoft Azure Stack
Utilizzare il provider di risorse di MySQL Server dello Stack di Azure per esporre i database MySQL come servizio dello Stack di Azure. Il servizio provider di risorse MySQL viene eseguito nel provider di risorse MySQL macchina virtuale, che è una macchina virtuale di Windows Server core.

## <a name="deploy-the-resource-provider"></a>Distribuire il provider di risorse 
1. Se non è già stato fatto, registrare il kit di sviluppo e scaricare l'immagine di Windows Server 2016 Data Center Core scaricabile tramite la gestione del Marketplace. È necessario utilizzare un'immagine di Windows Server 2016 Core. È inoltre possibile utilizzare uno script per creare un [immagine di Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Assicurarsi di selezionare l'opzione di core.) 

2. Accedi a un host che possa accedere all'endpoint con privilegi VM:
    - Nelle installazioni di Azure SDK, accedere all'host fisico.  
    - Nei sistemi integrati, l'host deve essere un sistema che possa accedere all'endpoint con privilegi. 
    
    >[!NOTE] 
    > Il sistema in cui è in esecuzione lo script *deve* un sistema di Windows 10 o Windows Server 2016 con la versione più recente del runtime di .NET installata. Installazione non riesce in caso contrario. L'host di Azure Stack ASDK soddisfa questo criterio. 
    
3. Scaricare il provider di risorse MySQL binario. Quindi eseguire il programma di autoestrazione per estrarre il contenuto in una directory temporanea. 
    >[!NOTE]  
    > Il provider di risorse dispone di uno Stack di Azure corrispondente minimo di compilazione. Assicurarsi di scaricare il file binario corretto per la versione dello Stack di Azure che è in esecuzione.

    | Versione di Azure Stack | Versione di MySQL RP| 
    | --- | --- | 
    | Versione 1804 (1.0.180513.1)|[RP MySQL versione 1.1.24.0](https://aka.ms/azurestackmysqlrp1804) | 
    | Versione 1802 (1.0.180302.1) | [RP MySQL versione 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) | 
    | Versione 1712 (1.0.180102.3 o 1.0.180106.1 (sistemi integrati)) | [RP MySQL versione 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) | 

4.  Per ASDK, come parte di questo processo viene creato un certificato autofirmato. Per i sistemi integrati, è necessario fornire un certificato appropriato. Se è necessario fornire il proprio certificato, inserire un file con estensione pfx nel **DependencyFilesLocalPath** che soddisfa i criteri seguenti: 
    - Un certificato con caratteri jolly per \*.dbadapter.\< area\>.\< fqdn esterno\> o un certificato del singolo sito con un nome comune mysqladapter.dbadapter.\< area\>.\< fqdn esterno\>. 
    - Questo certificato deve essere considerato attendibile. Vale a dire la catena di certificati deve esistere senza richiedere i certificati intermedi. 
    - Solo un singolo file di certificato esiste nel DependencyFilesLocalPath. 
    - Il nome del file non deve contenere spazi o caratteri speciali. 

5. Aprire un **nuova** console di PowerShell con privilegi elevati (amministratore). Quindi passare alla directory in cui sono stati estratti i file. Per evitare problemi che potrebbero verificarsi dei moduli di PowerShell non corretti che sono già caricati nel sistema, utilizzare una nuova finestra. 

6. Eseguire la **DeployMySqlProvider.ps1** script. Lo script esegue le operazioni: 
    - Scarica il file binario connettore MySQL (ciò può essere fornito non in linea). 
    - Carica i certificati e altri elementi di un account di archiviazione nello Stack di Azure. 
    - Pubblica i pacchetti di raccolta in modo che è possibile distribuire il database SQL tramite la raccolta. 
    - Pubblica un pacchetto di raccolta per la distribuzione di server di hosting. 
    - Consente di distribuire una macchina virtuale usando un'immagine del marketplace Stack di Windows Server 2016 Azure e installa il provider di risorse. 
    - Registra un record DNS locale che esegue il mapping al provider di risorse macchina virtuale. 
    - Registra il provider di risorse con locale Gestione risorse di Azure (admin e tenant). 

    È possibile specificare i parametri obbligatori nella riga di comando o eseguire lo script senza parametri e quindi immetterli quando richiesto. 

    Di seguito è riportato un esempio che è possibile eseguire dal prompt di PowerShell. Assicurarsi di modificare le informazioni sull'account e password in base alle esigenze: 

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

    # Set the credentials for the new resource provider VM local administrator account 
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
    $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds ` 
        -VMLocalCredential $vmLocalAdminCreds ` 
        -CloudAdminCredential $cloudAdminCreds ` 
        -PrivilegedEndpoint $privilegedEndpoint ` 
        -DefaultSSLCertificatePassword $PfxPass ` 
        -DependencyFilesLocalPath $tempDir\cert ` 
        -AcceptLicense 
    ``` 

### <a name="deploymysqlproviderps1-parameters"></a>Parametri DeployMySqlProvider.ps1 
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
| **DebugMode** | Impedisce la pulizia automatica in caso di errore. | No  | 
| **AcceptLicense** | Ignora la richiesta di accettare le condizioni di licenza GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

> [!NOTE] 
> SKU può richiedere a un'ora per essere visibile nel portale. È possibile creare un database fino a quando non viene creato lo SKU. 

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Verificare la distribuzione tramite il portale di Azure Stack 
> [!NOTE] 
>  Al termine dell'esecuzione dello script di installazione, è necessario aggiornare il portale per visualizzare il pannello di amministrazione. 

1. Accedere al portale di amministrazione con l'amministratore del servizio. 
2. Verificare che la distribuzione ha avuto esito positivo. Passare a **gruppi di risorse**, quindi selezionare il **system.\< percorso\>.mysqladapter** gruppo di risorse. Verificare che tutte le distribuzioni di quattro ha avuto esito positivo:

      ![Verificare la distribuzione di RP di MySQL](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png) 


## <a name="next-steps"></a>Passaggi successivi
[Aggiungere i server di hosting](azure-stack-mysql-resource-provider-hosting-servers.md)
