---
title: Utilizzo di database SQL nello Stack di Azure | Documenti Microsoft
description: Informazioni su come distribuire i database SQL come servizio in Azure Stack e le azioni rapide per distribuire l'adapter di provider di risorse di SQL Server.
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
ms.openlocfilehash: e1505761a0bd1ea9dabdd0b2cbab7af902198311
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938333"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>Distribuire il provider di risorse SQL Server in Azure Stack

Utilizzare il provider di risorse di Azure Stack SQL Server per esporre i database SQL come un servizio di Azure Stack. Il provider di risorse SQL viene eseguito come un servizio in una macchina virtuale di Server Core di Windows Server 2016 (VM).

## <a name="prerequisites"></a>Prerequisiti

Esistono diversi prerequisiti che devono essere presenti prima di poter distribuire il provider di risorse di SQL Azure dello Stack. Per soddisfare questi requisiti, completare i passaggi seguenti in un computer in cui possa accedere all'endpoint con privilegi VM:

- Se ancora provveduto, [Azure lo Stack di registri](.\azure-stack-registration.md) con Azure in modo è possibile scaricare gli elementi di Azure marketplace.
- Aggiungere richiesto Windows Server core VM nel Marketplace Stack Azure scaricando il **Datacenter di Windows Server 2016 - Server Core** immagine. È inoltre possibile utilizzare uno script per creare una [immagine di Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). Assicurarsi di che selezionare l'opzione dei componenti di base quando si esegue lo script.

  >[!NOTE]
  >Se è necessario installare un aggiornamento, è possibile inserire un singolo pacchetto MSU nel percorso locale. Se viene trovato più di un file MSU, l'installazione del provider di risorse SQL avrà esito negativo.

- Scaricare il provider di risorse SQL binario e quindi eseguire il programma di autoestrazione per estrarre il contenuto in una directory temporanea. Il provider di risorse dispone di uno Stack di Azure corrispondente minimo di compilazione. Assicurarsi che scaricare il file binario corretto per la versione dello Stack di Azure che si sta eseguendo.

    |Versione di Azure Stack|Versione di SQL RP|
    |-----|-----|
    |Versione 1804 (1.0.180513.1)|[SQL RP versione 1.1.24.0](https://aka.ms/azurestacksqlrp1804)
    |Versione 1802 (1.0.180302.1)|[SQL RP versione 1.1.18.0](https://aka.ms/azurestacksqlrp1802)|
    |Versione 1712 (1.0.180102.3, 1.0.180103.2 o 1.0.180106.1 (sistemi integrati))|[SQL RP versione 1.1.14.0](https://aka.ms/azurestacksqlrp1712)|
    |     |     |

### <a name="certificates"></a>Certificati

Sistemi integrati solo per le installazioni. È necessario fornire il certificato PKI PaaS SQL descritto nella sezione certificati PaaS facoltativa dei [requisiti di infrastruttura a chiave pubblica di Azure Stack distribuzione](.\azure-stack-pki-certs.md#optional-paas-certificates). Inserire il file con estensione pfx nel percorso specificato per il **DependencyFilesLocalPath** parametro.

## <a name="deploy-the-sql-resource-provider"></a>Distribuire il provider di risorse SQL

Dopo che hai tutti i prerequisiti installati, eseguire la **DeploySqlProvider.ps1** script per distribuire il provider di risorse SQL. Lo script DeploySqlProvider.ps1 verrà estratte come parte del file binario del provider di risorse SQL che è stato scaricato per la versione dello Stack di Azure.

> [!IMPORTANT]
> Il sistema che su cui viene eseguito lo script deve essere un sistema di Windows 10 o Windows Server 2016 con la versione più recente del runtime .NET installata.

Per distribuire il provider di risorse SQL, aprire una **nuovo** finestra della console di PowerShell con privilegi elevati e passare alla directory in cui sono stati estratti i file binari di risorse del provider SQL. È consigliabile utilizzare una nuova finestra di PowerShell per evitare potenziali problemi causati dai moduli di PowerShell che sono già caricati.

Eseguire lo script DeploySqlProvider.ps1, che completa le attività seguenti:

- Carica i certificati e altri elementi di un account di archiviazione nello Stack di Azure.
- Pubblica i pacchetti di raccolta in modo da poter distribuire i database SQL usando la raccolta.
- Pubblica un pacchetto di raccolta per la distribuzione di server di hosting.
- Consente di distribuire una macchina virtuale usando l'immagine di Windows Server 2016 core scaricato e quindi installa il provider di risorse SQL.
- Registra un record DNS locale che esegue il mapping al provider di risorse macchina virtuale.
- Registra il provider di risorse con locale Gestione risorse di Azure per gli account utente e operatore.
- Facoltativamente, consente di installare un singolo aggiornamento di Windows Server durante l'installazione di provider di risorse.

> [!NOTE]
> Quando viene avviata la distribuzione di provider di risorse SQL, il **system.local.sqladapter** gruppo di risorse viene creato. Potrebbero occorrere fino a 75 minuti dal completamento di quattro le distribuzioni richieste al gruppo di risorse.

### <a name="deploysqlproviderps1-parameters"></a>Parametri DeploySqlProvider.ps1

È possibile specificare i seguenti parametri dalla riga di comando. Se non sono presenti o se si verifica un errore di convalida qualsiasi parametro, viene chiesto di fornire i parametri obbligatori.

| Nome parametro | DESCRIZIONE | Commento o il valore predefinito |
| --- | --- | --- |
| **CloudAdminCredential** | Le credenziali per l'amministratore del cloud, necessaria per l'accesso all'endpoint con privilegi. | _Obbligatorio_ |
| **AzCredential** | Le credenziali per l'account amministratore del servizio Azure Stack. Utilizzare le stesse credenziali utilizzate per la distribuzione dello Stack di Azure. | _Obbligatorio_ |
| **VMLocalCredential** | Le credenziali per l'account amministratore locale del provider di risorse SQL macchina virtuale. | _Obbligatorio_ |
| **PrivilegedEndpoint** | L'indirizzo IP o nome DNS dell'endpoint con privilegi. |  _Obbligatorio_ |
| **DependencyFilesLocalPath** | Il file pfx del certificato deve trovarsi in questa directory. | _Parametro facoltativo_ (_obbligatorio_ per i sistemi integrati) |
| **DefaultSSLCertificatePassword** | La password per il certificato con estensione pfx. | _Obbligatorio_ |
| **MaxRetryCount** | Il numero di volte in cui che si desidera ripetere ogni operazione se si verifica un errore.| 2 |
| **RetryDuration** | L'intervallo di timeout tra due tentativi, in secondi. | 120 |
| **Disinstallare** | Rimuove il provider di risorse e tutte le risorse associate (vedere le note seguenti). | No  |
| **DebugMode** | Impedisce la pulizia automatica in caso di errore. | No  |

>[!NOTE]
> SKU può richiedere fino a un'ora sia visibile nel portale. È possibile creare un database fino a quando non lo SKU sia distribuito e in esecuzione.

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Distribuire il provider di risorse SQL utilizzando uno script personalizzato

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
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory If folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

Al termine dello script di installazione provider di risorse, aggiornare il browser per assicurarsi che è possibile visualizzare gli aggiornamenti più recenti.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Verificare la distribuzione tramite il portale di Azure Stack

È possibile utilizzare i seguenti passaggi verificare che il provider di risorse SQL è stato distribuito correttamente.

1. Accedere al portale di amministrazione come l'amministratore del servizio.
2. Selezionare **gruppi di risorse**.
3. Selezionare il **system.\< percorso\>.sqladapter** gruppo di risorse.
4. Il messaggio sotto **distribuzioni**, illustrato nell'acquisizione schermata successiva, deve essere **Succeeded 4**.

      ![Verificare la distribuzione del provider di risorse SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

5. È possibile ottenere informazioni più dettagliate sulla distribuzione di provider di risorse sotto **impostazioni**. Selezionare **distribuzioni** per ottenere informazioni, ad esempio: lo stato, TIMESTAMP e la durata di ogni distribuzione.

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere server di hosting](azure-stack-sql-resource-provider-hosting-servers.md)
