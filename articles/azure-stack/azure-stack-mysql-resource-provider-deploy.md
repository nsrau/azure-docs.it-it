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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e4c3eb1d7dfd4894576d5fbed52cf4de5fed9e44
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938114"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Distribuire il provider di risorse MySQL in Azure Stack

Utilizzare il provider di risorse MySQL Server per esporre i database MySQL come servizio Stack di Azure. Il provider di risorse MySQL viene eseguito come un servizio in una macchina virtuale di Server Core di Windows Server 2016 (VM).

## <a name="prerequisites"></a>Prerequisiti

Esistono diversi prerequisiti che devono essere presenti prima di poter distribuire il provider di risorse MySQL dello Stack di Azure. Per soddisfare questi requisiti, completare i passaggi descritti in questo articolo in un computer che possa accedere all'endpoint con privilegi macchina virtuale.

* Se ancora provveduto, [Azure lo Stack di registri](.\azure-stack-registration.md) con Azure in modo è possibile scaricare gli elementi di Azure marketplace.
* Aggiungere richiesto Windows Server core VM nel Marketplace Stack Azure scaricando il **Datacenter di Windows Server 2016 - Server Core** immagine. È inoltre possibile utilizzare uno script per creare una [immagine di Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). Assicurarsi di che selezionare l'opzione dei componenti di base quando si esegue lo script.

  >[!NOTE]
  >Se è necessario installare un aggiornamento, è possibile inserire un singolo. Pacchetto MSU nel percorso locale. Se più oggetti. Il file MSU si trova, installazione del provider di risorse MySQL avrà esito negativo.

* Scaricare il provider di risorse MySQL binario e quindi eseguire il programma di autoestrazione per estrarre il contenuto in una directory temporanea.

  >[!NOTE]
  >Per distribuire il provider di MySQL in un sistema che non hanno accesso a Internet, copiare il [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) file in una condivisione locale. Specificare il nome di condivisione quando viene chiesto per esso. È necessario installare i moduli di Azure e Azure PowerShell dello Stack.

* Il provider di risorse dispone di uno Stack di Azure corrispondente minimo di compilazione. Assicurarsi che scaricare il file binario corretto per la versione dello Stack di Azure che si sta eseguendo.

    | Versione di Azure Stack | Versione di MySQL RP|
    | --- | --- |
    | Versione 1804 (1.0.180513.1)|[RP MySQL versione 1.1.24.0](https://aka.ms/azurestackmysqlrp1804) |
    | Versione 1802 (1.0.180302.1) | [RP MySQL versione 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | Versione 1712 (1.0.180102.3 o 1.0.180106.1 (sistemi integrati)) | [RP MySQL versione 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |

### <a name="certificates"></a>Certificati

Per ASDK, viene creato un certificato autofirmato come parte di questo processo di installazione. Per un sistema integrato dello Stack di Azure, è necessario fornire un certificato appropriato. Se è necessario fornire il proprio certificato, inserire un file con estensione pfx nel **DependencyFilesLocalPath** che soddisfa i criteri seguenti:

* Un certificato con caratteri jolly per \*.dbadapter.\< area\>.\< fqdn esterno\> o un certificato singolo sito con un nome comune mysqladapter.dbadapter.\< area\>.\< fqdn esterno\>.
* Il certificato deve essere attendibile. La catena di trust deve esistere senza richiedere i certificati intermedi.
* Solo un singolo file di certificato esiste nel DependencyFilesLocalPath.
* Il nome del file non può contenere spazi o caratteri speciali.

## <a name="deploy-the-resource-provider"></a>Distribuire il provider di risorse

Dopo che hai tutti i prerequisiti installati, eseguire la **DeployMySqlProvider.ps1** script per distribuire il provider di risorse MYSQL. Lo script DeployMySqlProvider.ps1 verrà estratte come parte del file binario del provider di risorse MySQL scaricato per la versione dello Stack di Azure.

> [!IMPORTANT]
> Il sistema che su cui viene eseguito lo script deve essere un sistema di Windows 10 o Windows Server 2016 con la versione più recente del runtime .NET installata.

Per distribuire il provider di risorse MySQL, aprire una finestra della console di PowerShell new con privilegi elevata e passare alla directory in cui sono stati estratti i file binari di risorsa provider MySQL. È consigliabile utilizzare una nuova finestra di PowerShell per evitare potenziali problemi causati dai moduli di PowerShell che sono già caricati.

Eseguire la **DeployMySqlProvider.ps1** script, che completa le attività seguenti:

* Carica i certificati e altri elementi di un account di archiviazione nello Stack di Azure.
* Pubblica i pacchetti di raccolta in modo che è possibile distribuire i database di MySQL mediante la raccolta.
* Pubblica un pacchetto di raccolta per la distribuzione di server di hosting.
* Consente di distribuire una macchina virtuale usando l'immagine di Windows Server 2016 core scaricato e quindi installa il provider di risorse MySQL.
* Registra un record DNS locale che esegue il mapping al provider di risorse macchina virtuale.
* Registra il provider di risorse con locale Gestione risorse di Azure per gli account utente e operatore.
* Facoltativamente, consente di installare un singolo aggiornamento di Windows Server durante l'installazione di provider di risorse.

> [!NOTE]
> Quando viene avviata la distribuzione di provider di risorse MySQL, il **system.local.mysqladapter** gruppo di risorse viene creato. Potrebbero occorrere fino a 75 minuti dal completamento di quattro distribuzioni necessari per il gruppo di risorse.

### <a name="deploymysqlproviderps1-parameters"></a>Parametri DeployMySqlProvider.ps1

È possibile specificare questi parametri dalla riga di comando. Se non sono presenti o se si verifica un errore di convalida qualsiasi parametro, viene chiesto di fornire i parametri obbligatori.

| Nome parametro | DESCRIZIONE | Commento o il valore predefinito |
| --- | --- | --- |
| **CloudAdminCredential** | Le credenziali per l'amministratore del cloud, necessaria per l'accesso all'endpoint con privilegi. | _Obbligatorio_ |
| **AzCredential** | Le credenziali per l'account amministratore del servizio Azure Stack. Utilizzare le stesse credenziali utilizzate per la distribuzione dello Stack di Azure. | _Obbligatorio_ |
| **VMLocalCredential** | Le credenziali per l'account amministratore locale del provider di risorse MySQL macchina virtuale. | _Obbligatorio_ |
| **PrivilegedEndpoint** | L'indirizzo IP o nome DNS dell'endpoint con privilegi. |  _Obbligatorio_ |
| **DependencyFilesLocalPath** | Percorso di una condivisione locale che contiene [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Se si specifica uno di questi percorsi, il file del certificato deve trovarsi in questa directory. | _Parametro facoltativo_ per singolo nodo, _obbligatorio_ a nodi multipli. |
| **DefaultSSLCertificatePassword** | La password per il certificato con estensione pfx. | _Obbligatorio_ |
| **MaxRetryCount** | Il numero di volte in cui che si desidera ripetere ogni operazione se si verifica un errore.| 2 |
| **RetryDuration** | L'intervallo di timeout tra due tentativi, in secondi. | 120 |
| **Disinstallare** | Rimuove il provider di risorse e tutte le risorse associate (vedere le note seguenti). | No  |
| **DebugMode** | Impedisce la pulizia automatica in caso di errore. | No  |
| **AcceptLicense** | Ignora la richiesta di accettare le condizioni di licenza GPL.  <http://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

> [!NOTE]
> SKU può richiedere fino a un'ora sia visibile nel portale. È possibile creare un database fino a quando non lo SKU sia distribuito e in esecuzione.

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Distribuire il provider di risorse MySQL utilizzando uno script personalizzato

Per eliminare qualsiasi configurazione manuale quando si distribuisce il provider di risorse, è possibile personalizzare lo script seguente. Modificare le informazioni sull'account predefinito e le password necessarie per la distribuzione dello Stack di Azure.

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
# Find the ERCS01 IP address first, and make sure the certificate file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

Al termine dello script di installazione provider di risorse, aggiornare il browser per assicurarsi che è possibile visualizzare gli aggiornamenti più recenti.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Verificare la distribuzione tramite il portale di Azure Stack

1. Accedere al portale di amministrazione come l'amministratore del servizio.
2. Selezionare **gruppi di risorse**
3. Selezionare il **system.\< percorso\>.mysqladapter** gruppo di risorse.
4. Nella pagina di riepilogo per la risorsa gruppo Panoramica, il messaggio sotto **distribuzioni** deve essere **Succeeded 3**.
5. È possibile ottenere informazioni più dettagliate sulla distribuzione di provider di risorse sotto **impostazioni**. Selezionare **distribuzioni** per ottenere informazioni, ad esempio: lo stato, TIMESTAMP e la durata di ogni distribuzione.

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere server di hosting](azure-stack-mysql-resource-provider-hosting-servers.md)
