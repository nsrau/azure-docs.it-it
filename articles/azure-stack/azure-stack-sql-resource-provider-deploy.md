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
ms.openlocfilehash: 20b289c16a73bd20ed020987116975c8abe893f0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Utilizzare i database SQL nello Stack di Microsoft Azure
Utilizzare il provider di risorse di Azure Stack SQL Server per esporre i database SQL come servizio dello Stack di Azure. Il servizio provider di risorse SQL viene eseguito nel provider di risorse macchina virtuale, che è una macchina virtuale di Windows Server core di SQL.

## <a name="prerequisites"></a>Prerequisiti
Esistono diversi prerequisiti che devono essere presenti prima di poter distribuire il provider di risorse di SQL Azure dello Stack. Eseguire la procedura seguente in un computer che possa accedere all'endpoint con privilegi VM:

- Se non si è già fatto, [Azure lo Stack di registri](.\azure-stack-registration.md) con Azure in modo che è possibile scaricare gli elementi di Azure marketplace.
- Aggiungere richiesto Windows Server core VM nel Marketplace Stack Azure scaricando il **Server di Windows Server 2016 core** immagine. Se è necessario installare un aggiornamento, è possibile inserire un singolo. Pacchetto MSU nel percorso locale. Se più oggetti. Il file MSU si trova, installazione del provider di risorse SQL avrà esito negativo.
- Scaricare il provider di risorse SQL binario, quindi eseguire il programma di autoestrazione per estrarre il contenuto in una directory temporanea. Il provider di risorse dispone di uno Stack di Azure corrispondente minimo di compilazione. Assicurarsi di scaricare il file binario corretto per la versione dello Stack di Azure in esecuzione:
    - Stack di Azure versione 1802 (1.0.180302.1): [RP SQL versione 1.1.18.0](https://aka.ms/azurestacksqlrp1802).
    - Stack di Azure versione 1712 (1.0.180102.3, 1.0.180103.2 o 1.0.180106.1 (sistemi integrati)): [RP SQL versione 1.1.14.0](https://aka.ms/azurestacksqlrp1712).
- Sistemi integrati solo per le installazioni, è necessario fornire il certificato PKI PaaS SQL come descritto nella sezione certificati PaaS facoltativa del [requisiti di infrastruttura a chiave pubblica di Azure Stack distribuzione](.\azure-stack-pki-certs.md#optional-paas-certificates), inserendo il file con estensione pfx nel percorso specificato per il **DependencyFilesLocalPath** parametro.
- Assicurarsi di aver la [versione più recente di Azure PowerShell Stack](.\azure-stack-powershell-install.md) (v1.2.11) installato. 

## <a name="deploy-the-sql-resource-provider"></a>Distribuire il provider di risorse SQL
Dopo aver preparato correttamente per installare il provider di risorse SQL da soddisfare tutti i prerequisiti, è ora possibile eseguire la **DeploySqlProvider.ps1** script per distribuire il provider di risorse SQL. Lo script DeploySqlProvider.ps1 verrà estratte come parte del provider di risorse SQL binario che è stato scaricato corrispondente alla versione dello Stack di Azure. 

> [!IMPORTANT]
> Il sistema in cui viene eseguito lo script deve essere un sistema di Windows 10 o Windows Server 2016 con la versione più recente del runtime .NET installata.


Per distribuire il provider di risorse SQL, aprire la console di PowerShell new con privilegi elevata (amministratore) e passare alla directory in cui sono stati estratti i file binari di risorse del provider SQL.

> [!NOTE]
> Per evitare problemi che potrebbero verificarsi dei moduli di PowerShell non corretti che sono già caricati nel sistema, utilizzare una nuova finestra della console di PowerShell.

Eseguire lo script DeploySqlProvider.ps1, che esegue i passaggi seguenti:
- Carica i certificati e altri elementi di un account di archiviazione nello Stack di Azure.
- Pubblica i pacchetti di raccolta in modo che è possibile distribuire il database SQL tramite la raccolta.
- Pubblica un pacchetto di raccolta per la distribuzione di server di hosting.
- Consente di distribuire una macchina virtuale usando l'immagine di Windows Server 2016 che è stato creato nel passaggio 1 e quindi installa il provider di risorse.
- Registra un record DNS locale che esegue il mapping al provider di risorse macchina virtuale.
- Registra il provider di risorse con locale Gestione risorse di Azure (utente e amministratore).
- Consente di installare, facoltativamente, un singolo aggiornamento di Windows durante l'installazione di relying Party.

Distribuzione di provider di risorse SQL inizia e crea il gruppo di risorse system.local.sqladapter. Potrebbero occorrere fino a 75 minuti per completare le distribuzioni richieste quattro al gruppo di risorse.

### <a name="deploysqlproviderps1-parameters"></a>Parametri DeploySqlProvider.ps1
È possibile specificare questi parametri nella riga di comando. In caso contrario, o qualsiasi parametro di convalida non riesce, viene chiesto di fornire i parametri richiesti.

| Nome parametro | DESCRIZIONE | Commento o il valore predefinito |
| --- | --- | --- |
| **CloudAdminCredential** | Le credenziali per l'amministratore del cloud, necessaria per l'accesso all'endpoint con privilegi. | _Obbligatorio_ |
| **AzCredential** | Le credenziali per l'account amministratore del servizio Azure Stack. Utilizzare le stesse credenziali utilizzate per la distribuzione dello Stack di Azure. | _Obbligatorio_ |
| **VMLocalCredential** | Le credenziali per l'account amministratore locale del provider di risorse SQL macchina virtuale. | _Obbligatorio_ |
| **PrivilegedEndpoint** | L'indirizzo IP o nome DNS dell'endpoint con privilegi. |  _Obbligatorio_ |
| **DependencyFilesLocalPath** | Il file pfx del certificato deve trovarsi in questa directory. | _Parametro facoltativo_ (_obbligatorio_ per i sistemi integrati) |
| **DefaultSSLCertificatePassword** | La password per il certificato con estensione pfx. | _Obbligatorio_ |
| **MaxRetryCount** | Il numero di volte in cui che si desidera ripetere ogni operazione se si è verificato un errore.| 2 |
| **RetryDuration** | L'intervallo di timeout tra due tentativi, in secondi. | 120 |
| **Disinstallare** | Rimuove il provider di risorse e tutte le risorse associate (vedere le note seguenti). | No  |
| **DebugMode** | Impedisce la pulizia automatica in caso di errore. | No  |

>[!NOTE]
> SKU può richiedere a un'ora per essere visibile nel portale. È possibile creare un database fino a quando non viene creato lo SKU.


## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Distribuire il provider di risorse SQL utilizzando uno script personalizzato
Per evitare di immettere manualmente le informazioni necessarie quando viene eseguito lo script DeploySqlProvider.ps1, è possibile personalizzare lo script di esempio riportato di seguito modificando le informazioni sull'account predefinito e le password in base alle esigenze:

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

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Verificare la distribuzione tramite il portale di Azure Stack
I passaggi descritti in questa sezione è utilizzabile per garantire che il provider di risorse SQL è stato distribuito correttamente.

> [!NOTE]
>  Al termine dell'esecuzione dello script di installazione, è necessario aggiornare il portale per visualizzare gli elementi di raccolta e blade admin.

1. Accedere al portale di amministrazione con l'amministratore del servizio.

2. Verificare che la distribuzione ha avuto esito positivo. Passare a **gruppi di risorse**. Selezionare quindi il **system.\< percorso\>.sqladapter** gruppo di risorse. Verificare che tutte le distribuzioni di quattro ha avuto esito positivo.

      ![Verificare la distribuzione del provider di risorse SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="next-steps"></a>Passaggi successivi

[Aggiungere i server di hosting](azure-stack-sql-resource-provider-hosting-servers.md)
