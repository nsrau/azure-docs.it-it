---
title: Utilizzo di database SQL in Azure Stack | Microsoft Docs
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
ms.date: 07/02/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: d830a9c07890bde3c7d9e482e904ba3cbf5bfffe
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344921"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>Distribuire il provider di risorse di SQL Server in Azure Stack

Usare il provider di risorse di Server SQL di Azure Stack per esporre i database SQL come servizio di Azure Stack. Il provider di risorse SQL viene eseguito come servizio in una macchina virtuale di Windows Server 2016 Server Core (VM).

## <a name="prerequisites"></a>Prerequisiti

Esistono diversi prerequisiti che devono essere presenti prima di poter distribuire il provider di risorse SQL di Azure Stack. Per soddisfare questi requisiti, completare i passaggi seguenti in un computer che possa accedere all'endpoint della macchina virtuale con privilegi:

- Se non è già fatto, [registrare Azure Stack](.\azure-stack-registration.md) con Azure in modo che è possibile scaricare elementi di Azure marketplace.
- È necessario installare i moduli di Azure e PowerShell per Azure Stack in se il sistema verrà eseguita l'installazione. Che il sistema deve essere un'immagine di Windows 10 o Windows Server 2016 con la versione più recente del runtime .NET. Visualizzare [installare PowerShell per Azure Stack](.\azure-stack-powershell-install.md).
- Aggiungere la macchina virtuale a core Windows Server necessaria nel Marketplace di Azure Stack, scaricare il **Windows Server 2016 Datacenter, Server Core** immagine. 

  >[!NOTE]
  >Se è necessario installare un aggiornamento, è possibile inserire un singolo pacchetto MSU nel percorso della dipendenza locale. Se viene trovato più di un file MSU, installazione del provider di risorse SQL avrà esito negativo.

- Scaricare il provider di risorse SQL binario e quindi eseguire il programma di autoestrazione per estrarre il contenuto in una directory temporanea. Il provider di risorse dispone di uno Stack di Azure corrispondente minimo di compilazione. Assicurarsi di che scaricare il file binario corretto per la versione di Azure Stack in esecuzione.

    |Versione di Azure Stack|Versione di SQL RP|
    |-----|-----|
    |Versione 1804 (1.0.180513.1)|[SQL RP versione 1.1.24.0](https://aka.ms/azurestacksqlrp1804)
    |Versione 1802 (1.0.180302.1)|[SQL RP versione 1.1.18.0](https://aka.ms/azurestacksqlrp1802)|
    |Versione 1712 (1.0.180102.3, 1.0.180103.2 o 1.0.180106.1 (sistemi integrati))|[SQL RP versione 1.1.14.0](https://aka.ms/azurestacksqlrp1712)|
    |     |     |

### <a name="certificates"></a>Certificati

_Per le installazioni di sistemi integrati solo_. È necessario fornire il certificato PKI PaaS SQL descritto nella sezione certificati PaaS facoltativa del [i requisiti di infrastruttura a chiave pubblica di Azure Stack deployment](.\azure-stack-pki-certs.md#optional-paas-certificates). Inserire il file con estensione pfx nel percorso specificato per il **DependencyFilesLocalPath** parametro. Non specificare un certificato per i sistemi ASDK.

## <a name="deploy-the-sql-resource-provider"></a>Distribuire il provider di risorse SQL

Dopo avere ottenuto installati tutti i prerequisiti, eseguire la **DeploySqlProvider.ps1** script per distribuire il provider di risorse SQL. Lo script DeploySqlProvider.ps1 viene estratta come parte del file binario del provider di risorse SQL che è stato scaricato per la versione di Azure Stack.

Per distribuire il provider di risorse SQL, aprire una **nuovo** finestra della console di PowerShell con privilegi elevati e passare alla directory in cui sono stati estratti i file binari di risorsa del provider SQL. È consigliabile usare una nuova finestra di PowerShell per evitare potenziali problemi causati da moduli di PowerShell che sono già caricati.

Eseguire lo script DeploySqlProvider.ps1, che completa le attività seguenti:

- Carica i certificati e altri elementi in un account di archiviazione in Azure Stack.
- Pubblica pacchetti di raccolta in modo che è possibile distribuire database SQL usando la raccolta.
- Pubblica un pacchetto di raccolta per la distribuzione di server di hosting.
- Consente di distribuire una macchina virtuale usando l'immagine di Windows Server 2016 core scaricato e quindi installa il provider di risorse SQL.
- Registra un record DNS locale che esegue il mapping al provider di risorse della macchina virtuale.
- Registra il provider di risorse con locale Azure Resource Manager per gli account utente e operatore.
- Facoltativamente, installa un unico aggiornamento di Windows Server durante l'installazione del provider di risorse.

> [!NOTE]
> All'avvio di distribuzione del provider di risorse SQL, il **system.local.sqladapter** viene creato il gruppo di risorse. Potrebbero occorrere fino a 75 minuti per completare le distribuzioni richieste per questo gruppo di risorse.

### <a name="deploysqlproviderps1-parameters"></a>Parametri DeploySqlProvider.ps1

È possibile specificare i parametri seguenti dalla riga di comando. In caso contrario, o se ha esito negativo qualsiasi convalida dei parametri, viene chiesto di fornire i parametri obbligatori.

| Nome parametro | DESCRIZIONE | Commento o il valore predefinito |
| --- | --- | --- |
| **CloudAdminCredential** | Le credenziali per l'amministratore del cloud, necessaria per l'accesso all'endpoint con privilegi. | _Obbligatorio_ |
| **AzCredential** | Le credenziali per l'account di amministratore del servizio di Azure Stack. Usare le stesse credenziali usate per la distribuzione di Azure Stack. | _Obbligatorio_ |
| **VMLocalCredential** | Le credenziali per l'account amministratore locale del provider di risorse SQL macchina virtuale. | _Obbligatorio_ |
| **PrivilegedEndpoint** | L'indirizzo IP o nome DNS dell'endpoint con privilegi. |  _Obbligatorio_ |
| **DependencyFilesLocalPath** | Per i sistemi integrati, i file con estensione pfx del certificato deve trovarsi in questa directory. È facoltativamente possibile copiare un pacchetto di Windows Update MSU qui. | _Facoltativo_ (_obbligatorio_ per i sistemi integrati) |
| **DefaultSSLCertificatePassword** | La password per il certificato con estensione pfx. | _Obbligatorio_ |
| **MaxRetryCount** | Il numero di volte in cui che si desidera ripetere ogni operazione se si verifica un errore.| 2 |
| **RetryDuration** | L'intervallo di timeout tra i tentativi, in secondi. | 120 |
| **Disinstallare** | Rimuove il provider di risorse e tutte le risorse associate (vedere le note seguenti). | No  |
| **DebugMode** | Impedisce la pulizia automatica in caso di errore. | No  |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Distribuire il provider di risorse SQL usando uno script personalizzato

Per eliminare qualsiasi configurazione manuale quando si distribuisce il provider di risorse, è possibile personalizzare lo script seguente. Modificare le informazioni sull'account predefinito e le password in base alle esigenze per la distribuzione di Azure Stack.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module  -Name AzureStack -RequiredVersion 1.3.0

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

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

Al termine dello script di installazione provider di risorse, aggiornare il browser per assicurarsi che è possibile visualizzare gli aggiornamenti più recenti.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Verificare la distribuzione usando il portale di Azure Stack

È possibile usare i seguenti passaggi verificare che il provider di risorse SQL sia distribuito correttamente.

1. Accedere al portale di amministrazione come amministratore del servizio.
2. Selezionare **gruppi di risorse**.
3. Selezionare il **system.\< ubicazione\>.sqladapter** gruppo di risorse.
4. Nella pagina di riepilogo per il gruppo di risorse panoramica, non dovrebbe esserci alcun distribuzioni non riuscite.

      ![Verificare la distribuzione del provider di risorse SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere server di hosting](azure-stack-sql-resource-provider-hosting-servers.md)
