---
title: Configurare Mapping dei servizi in Azure | Microsoft Docs
description: Service Map è una soluzione di Azure che rileva automaticamente i componenti delle applicazioni nei sistemi Windows e Linux e mappa la comunicazione tra i servizi. Questo articolo fornisce informazioni dettagliate su come distribuire Mapping dei servizi nell'ambiente e su come usarlo in svariati scenari.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: magoedte
ms.openlocfilehash: ef5ab3d4fe71f688a0b4f3879248ea1715874a0b
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793731"
---
# <a name="configure-service-map-in-azure"></a>Configurare Mapping dei servizi in Azure

Mapping dei servizi individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue la mappatura della comunicazione fra i servizi. Consente di avere una vista dei server corrispondente alla loro rappresentazione concettuale, ovvero come sistemi interconnessi che offrono servizi critici. Mapping dei servizi mostra le connessioni fra i server, i processi e le porte di tutte le architetture connesse via TCP senza il bisogno di alcuna configurazione a parte l'installazione di un agente.

Questo articolo descrive in dettaglio come configurare l'elenco dei servizi e il caricamento degli agenti. Per informazioni sull'uso di Mapping dei servizi, vedere [Usare la soluzione di Mapping dei servizi in Azure]( service-map.md).

## <a name="supported-azure-regions"></a>Aree di Azure supportate

Mapping dei servizi è attualmente disponibile nelle aree seguenti:
- Stati Uniti orientali
- Stati Uniti centro-occidentali
- Canada centrale
- Regno Unito meridionale
- Europa occidentale
- Asia sud-orientale

## <a name="supported-windows-operating-systems"></a>Sistemi operativi Windows supportati

La sezione seguenti elenca i sistemi operativi supportati per Dependency Agent in Windows. 

>[!NOTE]
>Mapping dei servizi supporta solo piattaforme a 64 bit.
>

### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Desktop di Windows

- Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

## <a name="supported-linux-operating-systems"></a>Sistemi operativi Linux supportati

La sezione seguente elenca i sistemi operativi supportati per l'agente di dipendenza in Linux.  

- Sono supportate solo versioni predefinita e SMP del kernel Linux.
- Le versioni del kernel non standard, ad esempio PAE e Xen, non sono supportate per le distribuzioni Linux. Ad esempio, un sistema con la stringa di versione "2.6.16.21-0.8-xen" non è supportato.
- I kernel personalizzati, tra cui le ricompilazioni dei kernel standard, non sono supportati.

### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="centosplus"></a>CentOSPlus

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

### <a name="ubuntu-server"></a>Ubuntu Server

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Versione del sistema operativo | Versione del kernel
|:--|:--|
| 11 SP4 | 3.0.* |

### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versione del sistema operativo | Versione del kernel
|:--|:--|
| 12 SP2 | 4.4.* |
| 12 SP3 | 4.4.* |

## <a name="dependency-agent-downloads"></a>Download di Dependency Agent

| File | OS | Versione | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="connected-sources"></a>Origini connesse

Mapping dei servizi ottiene i dati da Microsoft Dependency Agent. Dependency Agent dipende dall'agente Log Analytics per le connessioni a Log Analytics, quindi nel server con Dependency Agent deve essere installato e configurato Log Analytics.  La tabella seguente descrive le origini connesse che sono supportate dalla soluzione di Mapping dei servizi.

| Origine connessa | Supportato | DESCRIZIONE |
|:--|:--|:--|
| Agenti di Windows | Sì | Mapping dei servizi analizza e raccoglie i dati dai computer Windows. <br><br>Oltre all'[agente di Log Analytics per Windows](../../azure-monitor/platform/log-analytics-agent.md), gli agenti Windows richiedono Microsoft Dependency Agent. Per un elenco completo delle versioni del sistema operativo, vedere Sistemi operativi supportati. |
| Agenti Linux | Sì | Mapping dei servizi analizza e raccoglie i dati dai computer Linux. <br><br>Oltre all'[agente di Log Analytics per Linux](../../azure-monitor/platform/log-analytics-agent.md), gli agenti Linux richiedono Microsoft Dependency Agent. Per un elenco completo delle versioni del sistema operativo, vedere Sistemi operativi supportati. |
| Gruppo di gestione di System Center Operations Manager | Sì | Mapping dei servizi analizza e raccoglie i dati dagli agenti Windows e Linux in un [gruppo di gestione System Center Operations Manager](../../azure-monitor/platform/om-agents.md) connesso. <br><br>È necessaria una connessione diretta dal computer agente System Center Operations Manager a Log Analytics. |
| Account di archiviazione di Azure | No  | Mapping dei servizi raccoglie i dati dai computer agente, pertanto non presenta dati che possano essere raccolti dall'Archiviazione di Azure. |

In Windows, Microsoft Monitoring Agent (MMA) viene usato sia da System Center Operations Manager che da Log Analytics per raccogliere e inviare dati di monitoraggio. Questo agente è chiamato agente System Center Operations Manager, agente di Log Analytics, agente MMA o diretto, a seconda del contesto. System Center Operations Manager e Log Analytics offrono versioni diverse pronte all'uso dell'agente MMA. Ognuna di queste versioni può inviare segnalazioni a System Center Operations Manager, Log Analytics o entrambi.  

In Linux, l'agente di Log Analytics per Linux raccoglie e invia i dati di monitoraggio a Log Analytics. È possibile usare Mapping dei servizi nei server con gli agenti di Log Analytics connessi direttamente al servizio o che inviano report a un gruppo di gestione di Operations Manager integrato con Log Analytics.  

In questo articolo si fa riferimento a tutti gli agenti, sia Linux che Windows, connessi a un gruppo di gestione di System Center Operations Manager o direttamente a Log Analytics, con l'espressione *agente di Log Analytics*. 

L'agente dell'elenco dei servizi non trasmette dati e non richiede modifiche ai firewall o alle porte. I dati in Mapping dei servizi vengono sempre trasmessi dall'agente di Log Analytics al servizio Log Analytics, direttamente o tramite il gateway Log Analytics.

![Agenti di Mapping dei servizi](media/service-map-configure/agents.png)

Per un cliente di System Center Operations Manager con un gruppo di gestione connesso a Log Analytics:

- Non è necessaria alcuna configurazione aggiuntiva se gli agenti System Center Operations Manager possono accedere a Internet per connettersi a Log Analytics.  
- Se gli agenti System Center Operations Manager non possono accedere a Log Analytics tramite Internet è necessario configurare il gateway Log Analytics per l'uso con System Center Operations Manager.
  
Se i computer Windows o Linux in uso non possono connettersi direttamente al servizio, è necessario configurare l'agente di Log Analytics per la connessione all'area di lavoro Log Analytics tramite il gateway. Per altre informazioni su come distribuire e configurare il gateway Log Analytics, vedere [Connettere computer senza accesso a Internet tramite il gateway Log Analytics](../../azure-monitor/platform/gateway.md).  

### <a name="management-packs"></a>Management Pack

Quando viene attivato Mapping dei servizi in un'area di lavoro Log Analytics, a tutti i server Windows nell'area di lavoro viene inoltrato un Management Pack di 300 KB. Se si usano gli agenti System Center Operations Manager in un [gruppo di gestione connesso](../../azure-monitor/platform/om-agents.md), il management pack di Mapping dei servizi viene distribuito da System Center Operations Manager. 

Il Management Pack è denominato Microsoft.IntelligencePacks.ApplicationDependencyMonitor Viene scritto in %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\. L'origine dati usata dal management pack è %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="data-collection"></a>Raccolta dei dati

È possibile aspettarsi che ogni agente trasmetta circa 25 MB al giorno, in base alla complessità delle dipendenze del sistema. I dati sulle dipendenze di Mapping dei servizi vengono inviati da ogni agente ogni 15 secondi.  

Dependency Agent usa in genere lo 0,1% della memoria di sistema e lo 0,1% della CPU di sistema.

## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo

Microsoft raccoglie automaticamente i dati di utilizzo e prestazioni tramite l'uso del servizio Mapping dei servizi da parte dell'utente. Microsoft usa questi dati per offrire e migliorare la qualità, la sicurezza e l'integrità del servizio Mapping dei servizi. I dati includono informazioni sulla configurazione del software, ad esempio sistema operativo e versione. Includono anche l'indirizzo IP, il nome DNS e il nome della workstation per offrire funzionalità di risoluzione dei problemi accurate ed efficienti. Non vengono raccolti nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta e l'uso dei dati, vedere l'[Informativa sulla privacy di Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="installation"></a>Installazione

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="azure-vm-extension"></a>Estensione di VM Azure

È disponibile un'estensione per Windows (DependencyAgentWindows) e per Linux (DependencyAgentLinux) ed è possibile distribuire facilmente Dependency Agent nelle macchine virtuali di Azure usando un'[estensione di VM Azure](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).  Con l'estensione di VM Azure è possibile distribuire Dependency Agent nelle macchine virtuali Windows e Linux usando uno script PowerShell o direttamente nella macchina virtuale usando un modello di Azure Resource Manager.  Se si distribuisce l'agente con l'estensione di VM Azure, gli agenti vengono automaticamente aggiornati alla versione più recente.

Per distribuire l'estensione di VM Azure con PowerShell, è possibile usare l'esempio seguente:

```powershell
#
# Deploy the Dependency agent to every VM in a Resource Group
#

$version = "9.4"
$ExtPublisher = "Microsoft.Azure.Monitoring.DependencyAgent"
$OsExtensionMap = @{ "Windows" = "DependencyAgentWindows"; "Linux" = "DependencyAgentLinux" }
$rmgroup = "<Your Resource Group Here>"

Get-AzVM -ResourceGroupName $rmgroup |
ForEach-Object {
    ""
    $name = $_.Name
    $os = $_.StorageProfile.OsDisk.OsType
    $location = $_.Location
    $vmRmGroup = $_.ResourceGroupName
    "${name}: ${os} (${location})"
    Date -Format o
    $ext = $OsExtensionMap.($os.ToString())
    $result = Set-AzVMExtension -ResourceGroupName $vmRmGroup -VMName $name -Location $location `
    -Publisher $ExtPublisher -ExtensionType $ext -Name "DependencyAgent" -TypeHandlerVersion $version
    $result.IsSuccessStatusCode
}
```

Un modo ancora più semplice per assicurarsi che Dependency Agent sia installato in tutte le macchine virtuali è di includere l'agente nel modello di Azure Resource Manager.  È possibile aggiungere il seguente esempio di codice JSON alla sezione delle *risorse* del modello.

```JSON
"type": "Microsoft.Compute/virtualMachines/extensions",
"name": "[concat(parameters('vmName'), '/DependencyAgent')]",
"apiVersion": "2017-03-30",
"location": "[resourceGroup().location]",
"dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
],
"properties": {
    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
    "type": "DependencyAgentWindows",
    "typeHandlerVersion": "9.4",
    "autoUpgradeMinorVersion": true
}

```

### <a name="install-the-dependency-agent-on-microsoft-windows"></a>Installare Dependency Agent in Microsoft Windows

Dependency Agent può essere installato manualmente nei computer Windows eseguendo `InstallDependencyAgent-Windows.exe`. Se si esegue questo file eseguibile senza opzioni, si avvia una procedura guidata che consente di completare l'installazione in modo interattivo.  

>[!NOTE]
>Per installare o disinstallare l'agente sono necessari i privilegi di amministratore.

Per installare Dependency Agent in ogni computer Windows, seguire questa procedura:

1.  Installare l'agente di Log Analytics per Windows attenendosi a uno dei metodi descritti in [Panoramica dell'agente di Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).
2.  Scaricare l'agente Windows ed eseguirlo usando il comando seguente: 
    
    `InstallDependencyAgent-Windows.exe`

3.  Seguire la procedura guidata per installare l'agente.
4.  Se Dependency Agent non si avvia, controllare i registri per vedere le informazioni dettagliate sull'errore. Per gli agenti Windows, la directory di log è %Programfiles%\Microsoft Dependency Agent\logs. 

#### <a name="windows-command-line"></a>Riga di comando di Windows

Usare le opzioni della tabella seguente per eseguire l'installazione dalla riga di comando. Per visualizzare un elenco dei flag di installazione, eseguire il programma di installazione con il flag /? come segue.

    InstallDependencyAgent-Windows.exe /?

| Flag | DESCRIZIONE |
|:--|:--|
| /? | Ottenere un elenco delle opzioni della riga di comando. |
| /S | Eseguire un'installazione invisibile all'utente senza prompt per l'utente. |

Per impostazione predefinita, i file di Dependency Agent per Windows si trovano in C:\Program Files\Microsoft Dependency Agent.

### <a name="install-the-dependency-agent-on-linux"></a>Installare Dependency Agent in Linux

Dependency Agent viene installato sui computer Linux da `InstallDependencyAgent-Linux64.bin`, uno script shell con un file binario auto-estraente. È possibile eseguire il file con `sh` oppure aggiungere autorizzazioni di esecuzione al file stesso.

>[!NOTE]
> Per installare o configurare l'agente è necessario l'accesso alla radice.

Per installare Dependency Agent in ogni computer Linux, seguire questa procedura:

1.  Installare l'agente di Log Analytics attenendosi a uno dei metodi descritti in [Panoramica dell'agente di Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).
2.  Installare Dependency Agent per Linux come utente ROOT eseguendo questo comando:
    
    `sh InstallDependencyAgent-Linux64.bin`

3.  Se Dependency Agent non si avvia, controllare i registri per vedere le informazioni dettagliate sull'errore. Per gli agenti Linux la directory di log è /var/opt/microsoft/dependency-agent/log.

Per vedere un elenco dei flag di installazione eseguire il programma di installazione con il flag -help come segue.

    InstallDependencyAgent-Linux64.bin -help

| Flag | DESCRIZIONE |
|:--|:--|
| -help | Ottenere un elenco delle opzioni della riga di comando. |
| -s | Eseguire un'installazione invisibile all'utente senza prompt per l'utente. |
| --check | Controllare le autorizzazioni e il sistema operativo senza installare l'agente. |

I file relativi a Dependency Agent sono memorizzati nelle directory seguenti:

| File | Località |
|:--|:--|
| File core | /opt/microsoft/dependency-agent |
| File di log | /var/opt/microsoft/dependency-agent/log |
| File di configurazione | /etc/opt/microsoft/dependency-agent/config |
| File eseguibili del servizio | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| File binary di archiviazione | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Esempi di script di installazione

Per distribuire facilmente Dependency Agent in più server contemporaneamente, viene fornito lo script di esempio seguente per scaricare e installare Dependency Agent in Windows o Linux.

### <a name="powershell-script-for-windows"></a>Script di PowerShell per Windows

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Script della shell per Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Configurazione dello stato desiderato

Per distribuire Dependency Agent tramite Desired State Configuration (DSC), è possibile usare il modulo xPSDesiredStateConfiguration con il codice di esempio seguente:

```powershell
configuration ServiceMap {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage 
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="remove-the-dependency-agent"></a>Rimuovere Dependency Agent

### <a name="uninstall-agent-on-windows"></a>Disinstallare l'agente in Windows

Dependency Agent per Windows può essere disinstallato da un amministratore tramite il Pannello di controllo.

Per disinstallare Dependency Agent, un amministratore può anche eseguire %Programfiles%\Microsoft Dependency Agent\Uninstall.exe.

### <a name="uninstall-agent-on-linux"></a>Disinstallare l'agente in Linux

È possibile disinstallare Dependency Agent da Linux con il comando seguente.

RHEL, CentOS oppure Oracle:

```
sudo rpm -e dependency-agent
```

Ubuntu:

```
sudo apt -y purge dependency-agent
```

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si verificano problemi di installazione o esecuzione di Mapping dei servizi, questa sezione può essere d'aiuto. Se si non riesce a risolvere il problema, contattare il supporto tecnico Microsoft.

### <a name="dependency-agent-installation-problems"></a>Problemi di installazione di Dependency Agent

#### <a name="installer-prompts-for-a-reboot"></a>Il programma di installazione richiede il riavvio

Dependency Agent *in genere* non richiede un riavvio durante l'installazione o la disinstallazione. Tuttavia, in alcuni casi rari, Windows Server può richiedere di riavviare il computer per continuare con l'installazione. Ciò si verifica quando una dipendenza, in genere Visual C++ Redistributable di Microsoft, richiede un riavvio a causa di un file bloccato.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>Viene visualizzato il messaggio "Non è stato possibile installare Dependency Agent: impossibile installare le librerie di runtime di Visual Studio (codice = [numero_di_codice])"

Microsoft Dependency Agent si basa sulle librerie di runtime di Microsoft Visual Studio. Se si verifica un problema durante l'installazione delle librerie, si riceverà un messaggio. 

I programmi di installazione delle librerie di runtime creano log nella cartella %LOCALAPPDATA%\temp. Il file è dd_vcredist_arch_aaaammgghhmmss.log, in cui *arch* è "x86" oppure "amd64" e *aaaammgghhmmss* sarà la data e l'ora, in formato 24 ore, di creazione del log. Il log offre informazioni dettagliate sul problema che impedisce l'installazione.

Potrebbe essere utile per l'utente installare personalmente prima [la versione più recente delle librerie di runtime](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

La tabella seguente elenca i codici e le risoluzioni consigliate.

| Codice | DESCRIZIONE | Risoluzione |
|:--|:--|:--|
| 0x17 | Il programma di installazione della libreria richiede un aggiornamento di Windows che non è stato installato. | Cercare nel log del programma di installazione della libreria più recente.<br><br>Se il riferimento a "Windows8.1-KB2999226-x64.msu" è seguito da una riga "Errore 0x80240017: impossibile eseguire il pacchetto MSU", significa che non si dispone dei prerequisiti necessari per installare KB2999226. Seguire le istruzioni riportate nella sezione relativa ai prerequisiti in [Universal C Runtime in Windows](https://support.microsoft.com/kb/2999226). Potrebbe essere necessario eseguire Windows Update e riavviare più volte per installare i prerequisiti.<br><br>Eseguire nuovamente il programma di installazione di Microsoft Dependency Agent. |

### <a name="post-installation-issues"></a>Problemi successivi all'installazione

#### <a name="server-doesnt-appear-in-service-map"></a>Il server non viene visualizzato in Mapping dei servizi

Se l'installazione di Dependency Agent è stata completata correttamente, ma nella soluzione Mapping dei servizi non viene visualizzato il server:
* Dependency Agent è stato installato correttamente? È possibile verificarlo controllando se il servizio è installato ed è in esecuzione.<br><br>
**Windows**: cercare il servizio denominato "Microsoft Dependency Agent".<br>
**Linux**: cercare il processo in esecuzione "microsoft-dependency-agent".

* L'utente dispone del [piano tariffario gratuito di Log Analytics/Operations Management Suite](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)? Il piano gratuito consente di avere fino a cinque server univoci dell'Elenco dei servizi. Tutti i server successivi non verranno visualizzati nell'Elenco dei servizi, anche se i primi cinque non inviano più dati.

* Il server invia il log e i dati delle prestazioni a Log Analytics? Passare a Ricerca log ed eseguire la query seguente per il computer: 

    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType

I risultati mostrano eventi diversi? I dati sono aggiornati? In questo caso, l'agente di Log Analytics funziona correttamente e comunica con Log Analytics. In caso contrario, controllare l'agente sul server: [Risoluzione dei problemi dell'agente di Log Analytics per Windows](https://support.microsoft.com/help/3126513/how-to-troubleshoot-monitoring-onboarding-issues) o [Risoluzione dei problemi dell'agente di Log Analytics per Linux](../../azure-monitor/platform/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Il server viene visualizzato in Mapping dei servizi, ma non dispone di alcun processo

Se il server viene visualizzato in Mapping dei servizi, ma non sono presenti dati di processi o connessioni, vuol dire che Dependency Agent è installato ed è in esecuzione, ma non è stato caricato il driver del kernel. 

Controllare il file C:\Programmi\Microsoft Dependency Agent\logs\wrapper.log per Windows o il file /var/opt/microsoft/dependency-agent/log/service.log per Linux. Le ultime righe del file dovrebbero indicare il motivo per cui il kernel non è stato caricato. Ad esempio, il kernel potrebbe non essere supportato in Linux se è stato aggiornato.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Mapping dei servizi]( service-map.md) dopo averlo distribuito e configurato.