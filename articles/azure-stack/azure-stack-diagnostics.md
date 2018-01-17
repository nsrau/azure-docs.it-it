---
title: Diagnostica in Azure Stack
description: Come raccogliere i file di log di diagnostica in Azure Stack
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 12/15/2017
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: e823aeb4291b3e765b35181c24b41fa58c170cca
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="azure-stack-diagnostics-tools"></a>Strumenti di diagnostica Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*
 
Stack di Azure è un numero elevato di componenti in modalità di collaborazione e l'interazione tra loro. Tutti questi componenti generano log univoco. Ciò può rendere la diagnosi di problemi di un'operazione complessa, specialmente per gli errori provenienti da più, l'interazione di componenti dello Stack di Azure. 

Gli strumenti di diagnostica garantiscono che il meccanismo di raccolta log è semplice ed efficiente. Il diagramma seguente illustra come accedere gli strumenti di raccolta in un Stack di Azure:

![Strumenti di diagnostica Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Agente di raccolta traccia
 
L'agente di raccolta traccia è abilitata per impostazione predefinita e viene eseguito in modo continuo in background per raccogliere tutti i log di Event Tracing for Windows (ETW) da Servizi componenti dello Stack di Azure. Log ETW vengono archiviati in una condivisione locale comune con un limite di età di cinque giorni. Una volta raggiunto questo limite, i file meno recenti vengono eliminati quando vengono creati nuovi. La dimensione massima predefinita è consentita per ogni file è pari a 200 MB. Ogni 2 minuti, viene effettuato un controllo di dimensioni e se il file corrente > = 200 MB, viene salvato e viene generato un nuovo file. È inoltre previsto un limite di 8 GB alle dimensioni complessive dei file generate per ogni sessione di eventi. 

## <a name="log-collection-tool"></a>Strumento di raccolta log
 
Il cmdlet PowerShell **Get AzureStackLog** può essere utilizzato per raccogliere registri da tutti i componenti in un ambiente dello Stack di Azure. Li salva nel file zip in un percorso definito dall'utente. Se il team di supporto tecnico di Azure Stack deve i log per consentire di risolvere un problema, si potrebbe chiedere di eseguire questo strumento.

> [!CAUTION]
> Questi file di log possono contenere informazioni personali (PII). Tenerne conto prima di registrare pubblicamente di ogni file di log.
 
Di seguito sono indicati alcuni tipi di log di esempio che vengono raccolti:
*   **Registri di distribuzione Azure Stack**
*   **Registri eventi di Windows**
*   **Log di Panther**
*   **Registro cluster**
*   **Log di diagnostica di archiviazione**
*   **Log ETW**

Questi file vengono raccolti e salvati in una condivisione per agente di raccolta traccia. Il **Get AzureStackLog** quindi i cmdlet di PowerShell consente di raccogliere tali informazioni quando necessario.
 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Per eseguire Get-AzureStackLog in un sistema Azure Stack Development Kit (ASDK)
1. Accedere come **AzureStack\CloudAdmin** nell'host.
2. Aprire una finestra di PowerShell come amministratore.
3. Eseguire il **Get AzureStackLog** cmdlet di PowerShell.

**Esempi:**

  Raccogliere tutti i log per tutti i ruoli:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs
  ```

  Raccogliere registri da macchine virtuali e BareMetal ruoli:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal
  ```

  Raccogliere registri da macchine virtuali e BareMetal ruoli, con data di applicazione di filtri per i file di log per le ore trascorse 8:
    
  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  Raccogliere registri dai ruoli di macchine virtuali e BareMetal, con data di applicazione di filtri per i file di log per il periodo di tempo compreso tra 8 ore fa e 2 ore fa:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="to-run-get-azurestacklog-on-an-azure-stack-integrated-system"></a>Per l'esecuzione di Get-AzureStackLog in uno Stack di Azure sistema integrato

Per eseguire lo strumento di raccolta log in un sistema integrato, è necessario avere accesso al punto finale con privilegi (PEP). Di seguito è riportato un esempio di script è possibile eseguire utilizzando la PEP per raccogliere i registri in un sistema integrato:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputPath "\\<HLH MACHINE ADDRESS>\c$\logs" -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- Quando si raccolgono i log dal PEP, specificare il **OutputPath** parametro sia un percorso sul computer Host del ciclo di vita di Hardware (HLH). Verificare inoltre che il percorso è crittografato.
- I parametri **OutputSharePath** e **OutputShareCredential** sono facoltativi e vengono utilizzati quando si carica i log in una cartella condivisa esterna. Utilizzare i parametri *inoltre* a **OutputPath**. Se **OutputPath** non viene specificato, lo strumento di raccolta log utilizza l'unità di sistema della macchina virtuale PEP per l'archiviazione. Ciò potrebbe causare l'esito negativo perché lo spazio su disco è limitato dello script.
- Come illustrato nell'esempio precedente, il **FromDate** e **ToDate** parametri possono essere utilizzati per raccogliere i log per un determinato periodo di tempo. Questo può essere utile per scenari come la raccolta di log dopo l'applicazione di un pacchetto di aggiornamento in un sistema integrato.

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Considerazioni sui parametri per ASDK e sistemi integrati

- Se il **FromDate** e **ToDate** parametri non vengono specificati, i log vengono raccolti per le ultime quattro ore per impostazione predefinita.
- È possibile utilizzare il **TimeOutInMinutes** parametro da impostare il timeout per la raccolta di log. È impostata su 150 (2,5 ore) per impostazione predefinita.

- Attualmente, è possibile utilizzare il **FilterByRole** parametro alla raccolta di log di filtro per i ruoli seguenti:

   |   |   |   |
   | - | - | - |
   | ACSMigrationService     | ACSMonitoringService   | ACSSettingsService |
   | ACS                     | ACSFabric              | ACSFrontEnd        |
   | ACSTableMaster          | ACSTableServer         | ACSWac             |
   | AD FS                    | ASAppGateway           | BareMetal          |
   | BRP                     | CA                     | CPI                |
   | CRP                     | DeploymentMachine      | DHCP               |
   | Domain                  | ECE                    | ECESeedRing        | 
   | FabricRing              | FabricRingServices     | FRP                |
   | Gateway                 | HealthMonitoring       | EFFETTUATO                |   
   | IBC                     | InfraServiceController | KeyVaultAdminResourceProvider|
   | KeyVaultControlPlane    | KeyVaultDataPlane      | NC                 |   
   | NonPrivilegedAppGateway | NRP                    | SeedRing           |
   | SeedRingServices        | SLB                    | SQL                |   
   | SRP                     | Archiviazione                | StorageController  |
   | URP                     | UsageBridge            | Macchine virtuali    |  
   | WAS                     | WASPUBLIC              | WDS                |


### <a name="bkmk_gui"></a>Raccogliere i log usando un'interfaccia utente grafica
Anziché fornire i parametri necessari per il cmdlet Get-AzureStackLog recuperare i registri di Stack di Azure, è anche possibile sfruttare gli strumenti di Azure Stack disponibile Apri origine si trova nella finestra principale dello Stack di Azure tools strumenti repository GitHub in http://aka.ms/AzureStackTools.

Il **ERCS_AzureStackLogs.ps1** script di PowerShell è archiviato nel repository GitHub degli strumenti e viene aggiornato a intervalli regolari. Per garantire che la versione più recente disponibile, è necessario scaricarlo direttamente dalla http://aka.ms/ERCS. Avviato da una sessione di PowerShell amministrativa, lo script si connette all'endpoint con privilegi e l'esecuzione di Get-AzureStackLog con parametri forniti. Se viene specificato alcun parametro, per impostazione predefinita lo script di una richiesta di conferma per i parametri tramite un'interfaccia utente grafica.

Per ulteriori informazioni sullo script ERCS_AzureStackLogs.ps1 PowerShell, è possibile guardare [un breve video](https://www.youtube.com/watch?v=Utt7pLsXEBc) o visualizzare lo script [file readme](https://github.com/Azure/AzureStack-Tools/blob/master/Support/ERCS_Logs/ReadMe.md) si trova nel repository GitHub strumenti dello Stack di Azure. 

### <a name="additional-considerations"></a>Ulteriori considerazioni

* Il comando richiede del tempo per eseguire in base alle quali ruoli per raccogliere i log. Fattori includono anche la durata specificata per la raccolta di log e i numeri di nodi nell'ambiente dello Stack di Azure.
* Al termine della raccolta di log, selezionare la cartella creata nel **OutputPath** parametro specificato nel comando.
* Ogni ruolo dispone dei log all'interno di singoli con estensione zip. A seconda delle dimensioni dei log raccolti, un ruolo può disporre dei log suddiviso in più file zip. Per un ruolo, se si desidera disporre di tutti i file di log decompressi una singola cartella, utilizzare uno strumento che è possibile decomprimere in blocco (ad esempio 7zip). Selezionare tutti i file ZIP per il ruolo e **estrarre qui**. Questo consente a tutti i file di log per tale ruolo in un'unica cartella sottoposto a merge.
* Un file denominato **Get AzureStackLog_Output.log** viene anche creato nella cartella che contiene i file di log compresso. Questo file è un log di output del comando, che può essere utilizzato per la risoluzione dei problemi durante la raccolta di log.
* Per analizzare un errore specifico, i registri possono essere necessari da più di un componente.
    -   Sistema e i registri eventi per tutte le macchine virtuali dell'infrastruttura vengono raccolti nel *VirtualMachines* ruolo.
    -   Sistema e i registri eventi per tutti gli host vengono raccolti nel *BareMetal* ruolo.
    -   Vengono raccolti i registri eventi Cluster di failover e Hyper-V nel *archiviazione* ruolo.
    -   ACS log vengono raccolti nel *archiviazione* e *ACS* ruoli.

> [!NOTE]
> Limiti di dimensioni e durata vengono applicati nei log delle raccolte perché è essenziale garantire un utilizzo efficace dello spazio di archiviazione per assicurare che non riceve di log. Tuttavia, durante la diagnosi di un problema è necessario in alcuni casi i registri che potrebbero non esistere più a causa di questi limiti. È pertanto **consigliabile** eseguire l'offload dei registri a uno spazio di archiviazione esterna (un account di archiviazione di Azure, un dispositivo di archiviazione locale aggiuntive e così via) ogni 8 a 12 ore e mantenerli sono da 1 a 3 mesi, a seconda del requisiti. Inoltre, verificare che il percorso di archiviazione è crittografato.

## <a name="next-steps"></a>Passaggi successivi
[Microsoft Azure Stack troubleshooting (Risoluzione dei problemi di Microsoft Azure Stack)](azure-stack-troubleshooting.md)

