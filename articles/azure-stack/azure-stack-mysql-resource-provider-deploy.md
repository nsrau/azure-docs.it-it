---
title: Utilizzo di database MySQL in Azure Stack | Microsoft Docs
description: Informazioni su come distribuire i database MySQL come servizio in Azure Stack e le azioni rapide per distribuire l'adapter di provider di risorse MySQL Server.
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
ms.date: 03/18/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 52e13068b6ecd732a64b60926366ac300731dae8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58186715"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Distribuire il provider di risorse MySQL in Azure Stack

Usare il provider di risorse MySQL Server per esporre i database MySQL come servizio di Azure Stack. Il provider di risorse MySQL viene eseguito come servizio in una macchina virtuale di Windows Server 2016 Server Core (VM).

> [!IMPORTANT]
> È supportato solo il provider di risorse per creare gli elementi nei server di tale host SQL o MySQL. Gli elementi creati in un server host che non vengono creati dal provider di risorse potrebbe essere in uno stato non corrispondente.

## <a name="prerequisites"></a>Prerequisiti

Esistono diversi prerequisiti che devono essere presenti prima di poter distribuire il provider di risorse MySQL di Azure Stack. Per soddisfare questi requisiti, completare i passaggi descritti in questo articolo in un computer che possa accedere all'endpoint della macchina virtuale con privilegi.

* Se non è già fatto, [registrare Azure Stack](./azure-stack-registration.md) con Azure in modo che è possibile scaricare elementi di Azure marketplace.
* È necessario installare i moduli di Azure e PowerShell per Azure Stack nel sistema in cui si eseguirà l'installazione. Che il sistema deve essere un'immagine di Windows 10 o Windows Server 2016 con la versione più recente del runtime .NET. Visualizzare [installare PowerShell per Azure Stack](./azure-stack-powershell-install.md).
* Aggiungere la macchina virtuale a core Windows Server necessaria nel Marketplace di Azure Stack, scaricare il **Windows Server 2016 Datacenter, Server Core** immagine.

* Scaricare il provider di risorse MySQL binario e quindi eseguire il programma di autoestrazione per estrarre il contenuto in una directory temporanea.

  >[!NOTE]
  >Per distribuire il provider di MySQL in un sistema che non ha accesso a Internet, copiare il [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) file in un percorso locale. Fornire il nome di percorso utilizzando la **DependencyFilesLocalPath** parametro.

* Il provider di risorse dispone di uno Stack di Azure corrispondente minimo di compilazione.

  |Versione minima di Azure Stack|Versione di MySQL RP|
  |-----|-----|
  |Versione 1808 (1.1808.0.97)|[RP MySQL versione 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
  |Versione 1808 (1.1808.0.97)|[RP MySQL versione 1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
  |Versione 1804 (1.0.180513.1)|[RP MySQL versione 1.1.24.0](https://aka.ms/azurestackmysqlrp11240)
  |     |     |

* Assicurarsi che siano soddisfatti i prerequisiti di integrazione di Data Center:

    |Prerequisito|Riferimenti|
    |-----|-----|
    |Inoltro condizionale DNS sia impostata correttamente.|[Integrazione di Data Center Azure Stack - DNS](azure-stack-integrate-dns.md)|
    |Porte in ingresso per i provider di risorse sono aperte.|[Azure Stack datacenter integration - pubblicano endpoint](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Soggetto del certificato PKI e SAN siano impostata correttamente.|[I prerequisiti di infrastruttura a chiave pubblica di Azure Stack distribuzione obbligatori](azure-stack-pki-certs.md#mandatory-certificates)[prerequisiti di Azure Stack distribuzione PaaS certificato](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Certificati

_Per le installazioni di sistemi integrati solo_. È necessario fornire il certificato PKI PaaS SQL descritto nella sezione certificati PaaS facoltativa del [i requisiti di infrastruttura a chiave pubblica di Azure Stack deployment](./azure-stack-pki-certs.md#optional-paas-certificates). Inserire il file con estensione pfx nel percorso specificato per il **DependencyFilesLocalPath** parametro. Non specificare un certificato per i sistemi ASDK.

## <a name="deploy-the-resource-provider"></a>Distribuire il provider di risorse

Dopo aver installato tutti i prerequisiti, è possibile eseguire la **DeployMySqlProvider.ps1** script per distribuire il provider di risorse MySQL. Lo script DeployMySqlProvider.ps1 viene estratta come parte dei file di installazione del provider di risorse MySQL scaricato per la versione di Azure Stack.

 > [!IMPORTANT]
 > Prima di distribuire il provider di risorse, esaminare le note sulla versione per informazioni sulle nuove funzionalità, correzioni e i problemi noti che potrebbero influire sulla distribuzione.

Per distribuire il provider di risorse MySQL, aprire una finestra di PowerShell con privilegi elevata nuovi (non PowerShell ISE) e passare alla directory in cui sono stati estratti i file binari di MySQL resource provider. È consigliabile usare una nuova finestra di PowerShell per evitare potenziali problemi causati da moduli di PowerShell che sono già caricati.

Eseguire la **DeployMySqlProvider.ps1** script, che completa le attività seguenti:

* Carica i certificati e altri elementi in un account di archiviazione in Azure Stack.
* Pubblica pacchetti di raccolta in modo da poter distribuire i database MySQL usando la raccolta.
* Pubblica un pacchetto di raccolta per la distribuzione di server di hosting.
* Consente di distribuire una macchina virtuale usando l'immagine di Windows Server 2016 core scaricato e quindi installa il provider di risorse MySQL.
* Registra un record DNS locale che esegue il mapping al provider di risorse della macchina virtuale.
* Registra il provider di risorse con locale Azure Resource Manager per l'account operatore.

> [!NOTE]
> All'avvio di distribuzione del provider di risorse MySQL, il **system.local.mysqladapter** viene creato il gruppo di risorse. Potrebbero occorrere fino a 75 minuti per completare le distribuzioni necessarie per questo gruppo di risorse.

### <a name="deploymysqlproviderps1-parameters"></a>Parametri DeployMySqlProvider.ps1

È possibile specificare questi parametri dalla riga di comando. In caso contrario, o se ha esito negativo qualsiasi convalida dei parametri, viene chiesto di fornire i parametri obbligatori.

| Nome parametro | DESCRIZIONE | Commento o il valore predefinito |
| --- | --- | --- |
| **CloudAdminCredential** | Le credenziali per l'amministratore del cloud, necessaria per l'accesso all'endpoint con privilegi. | _Obbligatorio_ |
| **AzCredential** | Le credenziali per l'account di amministratore del servizio di Azure Stack. Usare le stesse credenziali usate per la distribuzione di Azure Stack. | _Obbligatorio_ |
| **VMLocalCredential** | Le credenziali per l'account amministratore locale del provider di risorse MySQL della macchina virtuale. | _Obbligatorio_ |
| **PrivilegedEndpoint** | L'indirizzo IP o nome DNS dell'endpoint con privilegi. |  _Obbligatorio_ |
| **AzureEnvironment** | Ambiente di Azure dell'account di amministratore del servizio utilizzato per la distribuzione di Azure Stack. Obbligatorio solo per le distribuzioni di Azure AD. I nomi di ambiente supportati sono **AzureCloud**, **AzureUSGovernment**, o se si usa una Azure AD, Cina **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Per i sistemi integrati, i file con estensione pfx del certificato deve trovarsi in questa directory. Per ambienti non connessi, scaricare [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) a questa directory. È facoltativamente possibile copiare un pacchetto di Windows Update MSU qui. | _Facoltativo_ (_obbligatorio_ per i sistemi integrati o ambienti non connessi) |
| **DefaultSSLCertificatePassword** | La password per il certificato con estensione pfx. | _Obbligatorio_ |
| **MaxRetryCount** | Il numero di volte in cui che si desidera ripetere ogni operazione se si verifica un errore.| 2 |
| **RetryDuration** | L'intervallo di timeout tra i tentativi, in secondi. | 120 |
| **Disinstallare** | Rimuove il provider di risorse e tutte le risorse associate (vedere le note seguenti). | No  |
| **DebugMode** | Impedisce la pulizia automatica in caso di errore. | No  |
| **AcceptLicense** | Ignora la richiesta di accettazione della licenza GPL.  <https://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Distribuire il provider di risorse MySQL usando uno script personalizzato

Per eliminare qualsiasi configurazione manuale quando si distribuisce il provider di risorse, è possibile personalizzare lo script seguente. Modificare le informazioni sull'account predefinito e le password in base alle esigenze per la distribuzione di Azure Stack.

```powershell
# Install the Azure and Azure Stack PowerShell modules as described in the prerequisites section above before running these commands.

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"  

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

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

# Clear the existing login information from the Azure PowerShell context.
Clear-AzureRMContext -Scope CurrentUser -Force
Clear-AzureRMContext -Scope Process -Force

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

Al termine dello script di installazione provider di risorse, aggiornare il browser per assicurarsi che è possibile visualizzare gli aggiornamenti più recenti.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Verificare la distribuzione usando il portale di Azure Stack

1. Accedere al portale di amministrazione come amministratore del servizio.
2. Selezionare **gruppi di risorse**
3. Selezionare il **system.\< ubicazione\>.mysqladapter** gruppo di risorse.
4. Nella pagina di riepilogo per il gruppo di risorse panoramica, non dovrebbe esserci alcun distribuzioni non riuscite.
5. Selezionare infine **macchine virtuali** nel portale di amministrazione per verificare che il provider di risorse della macchina virtuale di MySQL è stato correttamente creato e sia in esecuzione.

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere server di hosting](azure-stack-mysql-resource-provider-hosting-servers.md)
