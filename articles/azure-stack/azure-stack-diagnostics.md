---
title: Diagnostica in Azure Stack
description: Come raccogliere i file di log di diagnostica in Azure Stack
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 11/22/2017
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 8afde912ca48297ae60eb7d05aa624a1d72c1637
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2017
---
# <a name="azure-stack-diagnostics-tools"></a>Strumenti di diagnostica Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*
 
Stack di Azure è un numero elevato di componenti in modalità di collaborazione e l'interazione tra loro. Tutti questi componenti generano log univoco. Ciò può rendere la diagnosi di problemi di un'operazione complessa, specialmente per gli errori provenienti da più, l'interazione di componenti dello Stack di Azure. 

Gli strumenti di diagnostica garantiscono che il meccanismo di raccolta log è semplice ed efficiente. Il diagramma seguente illustra come accedere gli strumenti di raccolta in un Stack di Azure:

![Strumenti di diagnostica Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Agente di raccolta traccia
 
L'agente di raccolta traccia è abilitata per impostazione predefinita. Viene continuamente eseguito in background e raccoglie tutti i log di Event Tracing for Windows (ETW) da Servizi componenti nello Stack di Azure e li archivia in una condivisione locale comune. 

Di seguito sono importanti per conoscere l'agente di raccolta traccia:
 
* L'agente di raccolta traccia viene eseguito continuamente con limiti di dimensione predefinito. Il valore predefinito di dimensioni massime consentite per ogni file (200 MB) è **non** una dimensione di cambio Data. Un controllo della dimensione si verifica periodicamente (attualmente ogni 2 minuti) e se il file corrente > = 200 MB, viene salvato e viene generato un nuovo file. È inoltre disponibile un limite di 8 GB (configurabile) alle dimensioni complessive dei file generate per ogni sessione di eventi. Una volta raggiunto questo limite, i file meno recenti vengono eliminati quando vengono creati nuovi.
* È previsto un limite di 5 giorni age sui registri. Inoltre, questo limite è configurabile. 
* Ogni componente definisce le proprietà di configurazione di traccia tramite un file JSON. I file JSON vengono archiviati **C:\TraceCollector\Configuration**. Se necessario, è possono modificare questi file per modificare i limiti di validità e le dimensioni dei log raccolti. Le modifiche apportate a questi file è necessario riavviare il *agente di raccolta traccia di Microsoft Azure Stack* servizio rendere effettive le modifiche.

L'esempio seguente è un file JSON di configurazione traccia per le operazioni di FabricRingServices dalla VM XRP: 

```json
{
    "LogFile": 
    {
        "SessionName": "FabricRingServicesOperationsLogSession",
        "FileName": "\\\\SU1FileServer\\SU1_ManagementLibrary_1\\Diagnostics\\FabricRingServices\\Operations\\AzureStack.Common.Infrastructure.Operations.etl",
        "RollTimeStamp": "00:00:00",
        "MaxDaysOfFiles": "5",
        "MaxSizeInMB": "200",
        "TotalSizeInMB": "5120"
    },
    "EventSources":
    [
        {"Name": "Microsoft-AzureStack-Common-Infrastructure-ResourceManager" },
        {"Name": "Microsoft-OperationManager-EventSource" },
        {"Name": "Microsoft-Operation-EventSource" }
    ]
}
```

* **MaxDaysOfFiles**. Questo parametro controlla la validità dei file da mantenere. File di registro meno recenti vengono eliminati.
* **MaxSizeInMB**. Questo parametro controlla la soglia delle dimensioni per un singolo file. Se viene raggiunta la dimensione, viene creato un nuovo file con estensione etl.
* **TotalSizeInMB**. Questo parametro controlla la dimensione totale dei file con estensione etl generati da una sessione eventi. Se la dimensione totale è maggiore del valore di questo parametro, i file meno recenti vengono eliminati.
  
## <a name="log-collection-tool"></a>Strumento di raccolta log
 
Il comando PowerShell **Get AzureStackLog** può essere utilizzato per raccogliere registri da tutti i componenti in un ambiente dello Stack di Azure. Li salva nel file zip in un percorso definito dall'utente. Se il team di supporto tecnico deve i log per consentire di risolvere un problema, si potrebbe chiedere di eseguire questo strumento.

> [!CAUTION]
> Questi file di log possono contenere informazioni personali (PII). Tenerne conto prima di registrare pubblicamente di ogni file di log.
 
Di seguito sono indicati alcuni tipi di log di esempio che vengono raccolti:
*   **Registri di distribuzione Azure Stack**
*   **Registri eventi di Windows**
*   **Log di Panther**
*   **Registro cluster**
*   **Log di diagnostica di archiviazione**
*   **Log ETW**

Questi file sono raccolti dall'agente di raccolta traccia e archiviati in una condivisione da cui **Get AzureStackLog** li recupera.
 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Per eseguire Get-AzureStackLog in un sistema Azure Stack Development Kit (ASDK)
1. Accedere come **AzureStack\CloudAdmin** nell'host.
2. Aprire una finestra di PowerShell come amministratore.
3. Eseguire il **Get AzureStackLog** cmdlet di PowerShell.

   **esempi**

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
   | BRP                     | CA                     | IPC                |
   | CRP                     | DeploymentMachine      | DHCP               |
   | Domain                  | ECE                    | ECESeedRing        | 
   | FabricRing              | FabricRingServices     | IN MATERIA PLASTICA RINFORZATA                |
   | Gateway                 | HealthMonitoring       | EFFETTUATO                |   
   | IBC                     | InfraServiceController |KeyVaultAdminResourceProvider|
   | KeyVaultControlPlane    | KeyVaultDataPlane      | NC                 |   
   | NonPrivilegedAppGateway | NRP                    | SeedRing           |
   | SeedRingServices        | SLB                    | SQL                |   
   | CRITERI DI RESTRIZIONE SOFTWARE                     | Archiviazione                | Che controller di archiviazione  |
   | URP                     | UsageBridge            | Macchine virtuali    |  
   | È STATO                     | WASPUBLIC              | SERVIZI DI DISTRIBUZIONE WINDOWS                |


### <a name="additional-considerations"></a>Considerazioni aggiuntive

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
