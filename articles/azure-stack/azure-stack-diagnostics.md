---
title: Diagnostica in Azure Stack
description: Come raccogliere i file di log per la diagnostica in Azure Stack
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 06/27/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 50fef25a3b7b71821e64638729eb8d93f65b9e31
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064163"
---
# <a name="azure-stack-diagnostics-tools"></a>Strumenti di diagnostica Azure Stack

Stack di Azure è un numero elevato di componenti in modalità di collaborazione e l'interazione tra loro. Tutti questi componenti generano i propri registri univoci. Ciò può rendere la diagnosi di problemi particolarmente difficile, specialmente per gli errori provenienti da più, l'interazione dei componenti dello Stack di Azure. 

Questi strumenti di diagnostica contribuiscono a garantire che il meccanismo di raccolta log è semplice ed efficiente. Il diagramma seguente illustra come registrare strumenti di raccolta in un Stack di Azure:

![Strumenti di diagnostica Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Agente di raccolta traccia
 
L'agente di raccolta traccia è abilitata per impostazione predefinita e continuamente in esecuzione in background per raccogliere tutti i log di Event Tracing for Windows (ETW) da Servizi componenti dello Stack di Azure. Log ETW vengono archiviati in una condivisione locale comune con un limite di età di cinque giorni. Una volta raggiunto questo limite, i file meno recenti vengono eliminati quando vengono creati nuovi. Le dimensioni massime predefinite consentite per ogni file sono pari a 200 MB. Un controllo della dimensione si verifica ogni 2 minuti, e se il file corrente > = 200 MB, viene salvato e viene generato un nuovo file. È inoltre previsto un limite di 8 GB alle dimensioni complessive dei file generate per ogni sessione eventi. 

## <a name="log-collection-tool"></a>Strumento di raccolta dei log
 
Il cmdlet di PowerShell **Get-AzureStackLog** può essere utilizzato per raccogliere registri da tutti i componenti in un ambiente dello Stack di Azure. Li salva nel file zip in un percorso definito dall'utente. Se il team di supporto tecnico di Azure Stack deve i log per consentire di risolvere un problema, si potrebbe chiedere di eseguire questo strumento.

> [!CAUTION]
> Questi file di log possono contenere informazioni identificabili personalmente (PII). Prendere in considerazione questa prima di registrare pubblicamente di ogni file di log.
 
Di seguito sono indicati alcuni tipi di log di esempio che vengono raccolti:
*   **Registri di distribuzione Azure Stack**
*   **Registri eventi di Windows**
*   **Log di Panther**
*   **Registro cluster**
*   **Log di diagnostica di archiviazione**
*   **Log ETW**

Questi file sono raccolti e salvati in una condivisione per agente di raccolta traccia. Il **Get-AzureStackLog** cmdlet di PowerShell è quindi utilizzabile per raccogliere tali informazioni quando necessario.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Per l'esecuzione di Get-AzureStackLog nello Stack di Azure integrata sistemi 
Per eseguire lo strumento di raccolta log in un sistema integrato, è necessario avere accesso al punto finale con privilegi (PEP). Ecco un esempio di script è possibile eseguire utilizzando la PEP per raccogliere i registri su un sistema integrato:

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

- I parametri **OutputSharePath** e **OutputShareCredential** vengono utilizzati per caricare i log in una cartella condivisa esterna.
- Come illustrato nell'esempio precedente, il **FromDate** e **ToDate** parametri possono essere utilizzati per raccogliere i log per un determinato periodo di tempo. Ciò può essere utile per scenari come la raccolta dei log dopo l'applicazione di un pacchetto di aggiornamento in un sistema integrato.


 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Per eseguire Get-AzureStackLog in un sistema Azure Stack Development Kit (ASDK)
1. Accedi come **AzureStack\CloudAdmin** nell'host.
2. Aprire una finestra di PowerShell come amministratore.
3. Eseguire la **Get-AzureStackLog** cmdlet di PowerShell.

**Esempi:**

  Raccogliere tutti i log per tutti i ruoli:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs
  ```

  Raccogliere i registri dai ruoli VirtualMachines e BareMetal:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal
  ```

  Raccogliere registri da ruoli VirtualMachines e BareMetal, con data di applicazione di filtri per i file di log per le ore trascorse 8:
    
  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  Raccogliere registri da ruoli VirtualMachines e BareMetal, con data di applicazione di filtri per i file di log per il periodo di tempo compreso tra 8 ore fa e 2 ore fa:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Considerazioni sui parametri per ASDK e sistemi integrati

- Se il **FromDate** e **ToDate** parametri non vengono specificati, i log vengono raccolti per le ultime quattro ore per impostazione predefinita.
- È possibile usare il **TimeOutInMinutes** parametro da impostare il timeout per la raccolta di log. Si è impostato su 150 (2 ore e mezzo) per impostazione predefinita.
- Nella versione 1805 e versioni successive, la raccolta di log file di dump è disabilitata per impostazione predefinita. Per abilitarla, usare il **IncludeDumpFile** parametro opzionale. 
- Attualmente, è possibile usare il **FilterByRole** parametro alla raccolta di log di filtro per i ruoli seguenti:

   |   |   |   |
   | - | - | - |
   | ACS                    | DeploymentMachine                | NC                         |
   | ACSBlob                | DiskRP                           | Rete                    |
   | ACSFabric              | Domain                           | NonPrivilegedAppGateway    |
   | ACSFrontEnd            | ECE                              | NRP                        |
   | ACSMetrics             | ExternalDNS                      | OEM                        |
   | ACSMigrationService    | Infrastruttura                           | PXE                        |
   | ACSMonitoringService   | FabricRing                       | SeedRing                   | 
   | ACSSettingsService     | FabricRingServices               | SeedRingServices           |
   | ACSTableMaster         | IN MATERIA PLASTICA RINFORZATA                              | SLB                        |   
   | ACSTableServer         | Gallery                          | SlbVips                    |
   | ACSWac                 | Gateway                          | SQL                        |   
   | AD FS                   | HealthMonitoring                 | CRITERI DI RESTRIZIONE SOFTWARE                        |
   | ASAppGateway           | EFFETTUATO                              | Archiviazione                    |   
   | NCAzureBridge          | IBC                              | StorageAccounts            |    
   | AzurePackConnector     | IdentityProvider                 | StorageController          |  
   | AzureStackBitlocker    | nomi IDN                             | Tenant                     |
   | BareMetal              | InfraServiceController           | TraceCollector             |
   | BRP                    | Infrastruttura                   | URP                        |
   | CA                     | KeyVaultAdminResourceProvider    | UsageBridge                |
   | Cloud                  | KeyVaultControlPlane             | VirtualMachines            |
   | HDInsight                | KeyVaultDataPlane                | È STATO                        |
   | Calcolo                | KeyVaultInternalControlPlane     | WASBootstrap               |
   | CPI                    | KeyVaultInternalDataPlane        | WASPUBLIC                  |
   | CRP                    | KeyVaultNamingService            |                            |
   | DatacenterIntegration  | MonitoringAgent                  |                            |
   |                        |                                  |                            |

### <a name="bkmk_gui"></a>Raccogliere i log usando un'interfaccia utente grafica
Anziché fornire i parametri obbligatori per il cmdlet Get-AzureStackLog recuperare i registri di Stack di Azure, è anche possibile sfruttare gli strumenti di Azure Stack disponibile Apri origine che si trova in principale dello Stack di Azure tools strumenti repository GitHub in http://aka.ms/AzureStackTools.

Il **ERCS_AzureStackLogs.ps1** script di PowerShell è archiviato nel repository GitHub degli strumenti e viene aggiornato a intervalli regolari. Per garantire che è la versione più recente disponibile, è necessario scaricarlo direttamente dalla http://aka.ms/ERCS. Avviato da una sessione di PowerShell amministrativa, lo script si connette all'endpoint con privilegi e Get-AzureStackLog viene eseguito con parametri forniti. Se viene specificato alcun parametro, impostazione predefinita lo script una richiesta di conferma per i parametri tramite un'interfaccia utente grafica.

Per ulteriori informazioni sullo script ERCS_AzureStackLogs.ps1 PowerShell, è possibile guardare [un breve video](https://www.youtube.com/watch?v=Utt7pLsXEBc) o visualizzare lo script [file Leggimi](https://github.com/Azure/AzureStack-Tools/blob/master/Support/ERCS_Logs/ReadMe.md) che si trova nel repository GitHub degli strumenti dello Stack di Azure. 

### <a name="additional-considerations"></a>Ulteriori considerazioni

* Il comando richiede un certo tempo per eseguire in base alle quali ruoli raccolgono i log. Fattori determinanti includono anche la durata specificata per la raccolta di log e i numeri di nodi nell'ambiente dello Stack di Azure.
* Come viene eseguito di raccolta del log, controllare la cartella creata nel **OutputSharePath** parametro specificato nel comando.
* Ogni ruolo con i registri in file con estensione zip singoli. A seconda della dimensione dei log raccolti, un ruolo può essere relativi registri suddiviso in più file zip. Per un ruolo, se si desidera disporre di tutti i file di log decompressi a un'unica cartella, utilizzare uno strumento che è possibile decomprimere in blocco (ad esempio 7zip). Selezionare tutti i file compressi per il ruolo e selezionare **estrarre qui**. Ciò decomprime tutti i file di log per tale ruolo in un'unica cartella unita.
* Un file denominato **Get-AzureStackLog_Output.log** viene anche creato nella cartella che contiene i file di log compresso. Questo file è riportato un log di output del comando, che può essere utilizzato per la risoluzione dei problemi durante la raccolta di log. A volte il file di log include `PS>TerminatingError` voci che possono essere tranquillamente ignorate, a meno che i file di log previsto risultano mancanti dopo l'accesso viene eseguito insieme.
* Per analizzare un errore specifico, i log potrebbero essere necessarie da più di un componente.
    -   Vengono raccolti nel sistema e i registri eventi per tutte le macchine virtuali dell'infrastruttura di *VirtualMachines* ruolo.
    -   Sistema e i registri eventi per tutti gli host vengono raccolti nel *BareMetal* ruolo.
    -   Vengono raccolti i registri eventi Cluster di failover e Hyper-V nel *archiviazione* ruolo.
    -   ACS log vengono raccolti nel *Storage* e *ACS* ruoli.

> [!NOTE]
> Limiti di dimensioni e durata vengono applicati nei log raccolti, è fondamentale per garantire un utilizzo efficace dello spazio di archiviazione per assicurare che non ottenere flood con log. Tuttavia, durante la diagnosi di un problema è necessario in alcuni casi i registri che potrebbero non esistere più a causa di questi limiti. È pertanto **consigliato** eseguire l'offload dei log in uno spazio di archiviazione esterna (un account di archiviazione di Azure, un dispositivo di archiviazione aggiuntivo in locale e così via) ogni 8 a 12 ore e mantenerli sono da 1 a 3 mesi, a seconda del requisiti. Inoltre, verificare che il percorso di archiviazione è crittografato.

## <a name="next-steps"></a>Passaggi successivi
[Microsoft Azure Stack troubleshooting (Risoluzione dei problemi di Microsoft Azure Stack)](azure-stack-troubleshooting.md)

