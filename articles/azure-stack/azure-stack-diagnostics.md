---
title: Diagnostica in Azure Stack
description: Come raccogliere i file di log per la diagnostica in Azure Stack
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 11/15/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 8a2a9728a9fcc0c409cd2020c4aa831433538157
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976129"
---
# <a name="azure-stack-diagnostics-tools"></a>Strumenti di diagnostica di Azure Stack

Azure Stack è un numero elevato di componenti di collaborazione e l'interazione tra loro. Tutti questi componenti generano i propri log univoco. Ciò può rendere la diagnosi di problemi di un'attività complessa, soprattutto per errori provenienti da più, l'interazione di componenti di Azure Stack. 

Gli strumenti di diagnostica consentono di verificare che il meccanismo di raccolta log è semplice ed efficiente. Il diagramma seguente illustra come accedere strumenti di raccolta del lavoro di Azure Stack:

![Strumenti di diagnostica Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Agente di raccolta traccia
 
L'agente di raccolta traccia è abilitata per impostazione predefinita e viene eseguito in modo continuo in background per raccogliere tutti i log di Event Tracing for Windows (ETW) da Servizi componenti di Azure Stack. I log ETW vengono archiviati in una condivisione locale comune con un limite di età di cinque giorni. Una volta raggiunto questo limite, i file meno recenti vengono eliminati quando vengono creati nuovi file. La dimensione massima predefinita è consentita per ogni file è di 200 MB. Ogni 2 minuti, viene eseguito un controllo di dimensioni e, se il file corrente è > = 200 MB, viene salvato e viene generato un nuovo file. È anche previsto un limite di 8 GB nella dimensione totale del file generata per ogni sessione eventi. 

## <a name="log-collection-tool"></a>Strumento di raccolta log
 
Il cmdlet di PowerShell **Get-AzureStackLog** può essere utilizzato per raccogliere log da tutti i componenti in un ambiente Azure Stack. Li salva nel file zip in un percorso definito dall'utente. Se il team di supporto tecnico di Azure Stack richiede i log per consentire di risolvere un problema, si potrebbe essere richiesto di eseguire questo strumento.

> [!CAUTION]
> Questi file di log possono contenere informazioni personali (PII). Tenerne conto prima di pubblicare pubblicamente i file di log.
 
Di seguito sono indicati alcuni tipi di log di esempio che vengono raccolti:
*   **Log di distribuzione di Azure Stack**
*   **Registri eventi di Windows**
*   **Log di Panther**
*   **Log del cluster**
*   **Log di diagnostica di archiviazione**
*   **Log ETW**

Questi file vengono raccolti e salvati in una condivisione dall'agente di raccolta traccia. Il **Get-AzureStackLog** cmdlet di PowerShell è quindi utilizzabile per raccogliere tali informazioni quando necessario.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Per eseguire Get-AzureStackLog in Azure Stack i sistemi integrati 
Per eseguire lo strumento di raccolta log in un sistema integrato, è necessario avere accesso al punto finale con privilegi (PEP). Ecco un esempio di script è possibile eseguire utilizzando la PEP per raccogliere i log in un sistema integrato:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```



### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Eseguire Get-AzureStackLog su un sistema di Azure Stack Development Kit (ASDK)
Usare questi passaggi per l'esecuzione di Get-AzureStackLog in un computer host ASDK.

- I parametri **OutputSharePath** e **OutputShareCredential** vengono usati per archiviare i log di accesso dell'utente specificato percorso.
- Il **FromDate** e **ToDate** parametri possono essere utilizzati per raccogliere i log per un periodo di tempo specifico. Se questi parametri vengono omessi, i log vengono raccolti per le ultime quattro ore per impostazione predefinita.

1. Accedi come **AzureStack\CloudAdmin** nel computer host ASDK.
2. Aprire una nuova finestra di PowerShell come amministratore.
3. Eseguire la **Get-AzureStackLog** cmdlet di PowerShell.

**Esempi:**

  Raccogliere tutti i log per tutti i ruoli:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred
  ```

  Raccogliere i log dai ruoli VirtualMachines e BareMetal:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
  ```

  Raccogliere i log dai ruoli VirtualMachines e BareMetal, con filtro per i file di log per le ultime 8 ore delle date:
    
  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  Raccogliere i log dai ruoli VirtualMachines e BareMetal, con filtri per i file di log per il periodo di tempo compreso tra 8 ore fa e 2 ore fa data:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Considerazioni sui parametri per ASDK e i sistemi integrati

- Se il **FromDate** e **ToDate** parametri vengono omessi, i log vengono raccolti per le ultime quattro ore per impostazione predefinita.
- Usare la **FilterByNode** parametro per filtrare i log dal nome del computer. Ad esempio: 

    ```powershell
    Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```
- Usare la **FilterByLogType** parametro per filtrare i log dal tipo. È possibile scegliere di filtrare in base al File, condivisione o WindowsEvent. Ad esempio: 

    ```powershell
    Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByLogType File
    ```
- È possibile usare la **TimeOutInMinutes** parametro da impostare il timeout per la raccolta di log. Si è impostato su 150 (2,5 ore) per impostazione predefinita.
- Raccolta di log file di dump è disabilitata per impostazione predefinita. Per abilitarla, usare il **IncludeDumpFile** parametro opzionale. 
- Attualmente, è possibile usare la **FilterByRole** parametro alla raccolta di log di filtro per i ruoli seguenti:

 |   |   |   |    |
 | - | - | - | -  |   
 |ACS                   |CacheService                   |IBC                            |OEM|
 |ACSDownloadService    |Calcolo                        |InfraServiceController         |OnboardRP|
 |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PXE|
 |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator|
 |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker|
 |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing|
 |ACSMonitoringService  |Domain                         |KeyVaultInternalDataPlane      |SeedRingServices|
 |ACSSettingsService    |ECE                            |KeyVaultNamingService          |SLB|
 |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL|
 |ACSTableServer        |EventRP                        |MetricsAdminRP                 |CRITERI DI RESTRIZIONE SOFTWARE   |
 |ACSWac                |ExternalDNS                    |MetricsRP                      |Archiviazione|
 |AD FS                  |FabricRing                     |MetricsServer                  |StorageController   |
 |ApplicationController |FabricRingServices             |MetricsStoreService            |URP   |
 |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |UsageBridge|
 |AzureBridge           |IN MATERIA PLASTICA RINFORZATA                            |MonRP                          |VirtualMachines   |
 |AzureMonitor          |Gateway                        |NC                             |È STATO|
 |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |WASPUBLIC|
 |BRP                   |HintingServiceV2               |NRP                            |   |
 |CA                    |EFFETTUATO                            |OboService                     |   |
 |   |   |   |    |

### <a name="additional-considerations"></a>Ulteriori considerazioni

* Il comando richiede del tempo per eseguire in base alle quali ruoli raccolgono i log. Fattori determinanti includono anche la durata di tempo specificata per la raccolta di log e i numeri di nodi nell'ambiente Azure Stack.
* Perché esecuzioni di raccolta di log, verificare la nuova cartella creata nel **OutputSharePath** parametro specificato nel comando.
* Ogni ruolo dispone i log all'interno di singoli con estensione zip. A seconda delle dimensioni dei log raccolti, un ruolo può avere relativi registri suddivisi in più file con estensione zip. Per un ruolo, se si desidera avere tutti i file di log decompressi in una singola cartella, usare uno strumento che può decomprimere in blocco (ad esempio 7zip). Selezionare tutti i file compressi per il ruolo e selezionare **estrarre qui**. Ciò decomprime tutti i file di log per il ruolo in un'unica cartella unita.
* Un file denominato **Get-AzureStackLog_Output.log** viene anche creato nella cartella che contiene i file di log compresso. Questo file è un log di output del comando, che può essere utilizzato per la risoluzione dei problemi durante la raccolta di log. In alcuni casi il file di log include `PS>TerminatingError` voci che possono essere tranquillamente ignorate, a meno che non mancano i file di log previsto dopo l'esecuzione raccolta di log.
* Per analizzare un errore specifico, i log possono essere necessari da più di un componente.
    -   Vengono raccolti nel sistema e i registri eventi per tutte le macchine virtuali dell'infrastruttura di *VirtualMachines* ruolo.
    -   Vengono raccolti nel sistema e i registri eventi per tutti gli host il *BareMetal* ruolo.
    -   I registri eventi Cluster di failover e Hyper-V vengono raccolti nel *archiviazione* ruolo.
    -   Vengono raccolti i log di ACS nel *memorizzazione* e *ACS* ruoli.

> [!NOTE]
> Limiti di dimensioni e durata sono applicati per i log raccolti perché è essenziale per garantire un utilizzo efficiente dello spazio di archiviazione per assicurarsi di che non ottenere inondato di log. Tuttavia, durante la diagnosi di un problema è necessario in alcuni casi i log che non esista più a causa di questi limiti. È pertanto **altamente consigliato** eseguire l'offload i log con uno spazio di archiviazione esterna (un account di archiviazione di Azure, un dispositivo di archiviazione aggiuntivi in locale e così via) ogni 8 a 12 ore e mantenerli presenti per 1-3 mesi, a seconda di requisiti. Assicurarsi anche questo percorso di archiviazione verrà crittografato.

## <a name="next-steps"></a>Passaggi successivi
[Microsoft Azure Stack troubleshooting (Risoluzione dei problemi di Microsoft Azure Stack)](azure-stack-troubleshooting.md)

