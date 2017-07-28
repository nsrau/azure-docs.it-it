---
title: Configurare Mapping dei servizi in Operations Management Suite | Microsoft Docs
description: "Elenco dei servizi è una soluzione di Operations Management Suite che individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux e mappa la comunicazione fra i servizi. Questo articolo fornisce informazioni dettagliate su come distribuire Mapping dei servizi nell'ambiente e su come usarlo in svariati scenari."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/18/2016
ms.author: daseidma;bwren;dairwin
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: 9af6c0fc3df2863c8e7b9a6a62acf5ba6b7d2d0a
ms.contentlocale: it-it
ms.lasthandoff: 06/09/2017

---
# <a name="configure-service-map-in-operations-management-suite"></a>Configurare Mapping dei servizi in Operations Management Suite
Mapping dei servizi individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue la mappatura della comunicazione fra i servizi. Consente di visualizzare i server nel modo in cui si pensa a essi, ovvero come sistemi interconnessi che offrono servizi critici. Mapping dei servizi mostra le connessioni fra i server, i processi e le porte di tutte le architetture connesse via TCP senza il bisogno di alcuna configurazione a parte l'installazione di un agente.

Questo articolo descrive in dettaglio come configurare l'elenco dei servizi e il caricamento degli agenti. Per informazioni sull'uso di Mapping dei servizi, vedere [Configurare la soluzione di elenco dei servizi in Operations Management Suite (OMS)](operations-management-suite-service-map.md).

## <a name="dependency-agent-downloads"></a>Download di Dependency Agent
| File | OS | Versione | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |


## <a name="connected-sources"></a>Origini connesse
Elenco dei servizi ottiene i dati da Microsoft Dependency Agent, che dipende dall'agente OMS per le connessioni a Operations Management Suite. È quindi necessario che OMS Agent venga installato e configurato nel server prima di Microsoft Dependency Agent. La tabella seguente descrive le origini connesse che sono supportate dalla soluzione di Mapping dei servizi.

| Origine connessa | Supportato | Descrizione |
|:--|:--|:--|
| Agenti di Windows | Sì | L'elenco dei servizi analizza e raccoglie i dati dai computer agente Windows. <br><br>Oltre a [OMS Agent](../log-analytics/log-analytics-windows-agents.md), gli agenti Windows richiedono Microsoft Dependency Agent. Per un elenco completo delle versioni del sistema operativo, consultare i [sistemi operativi supportati](#supported-operating-systems). |
| Agenti Linux | Sì | L'elenco dei servizi analizza e raccoglie i dati dai computer agente Linux. <br><br>Oltre a [OMS Agent](../log-analytics/log-analytics-linux-agents.md), gli agenti Linux richiedono Microsoft Dependency Agent. Per un elenco completo delle versioni del sistema operativo, consultare i [sistemi operativi supportati](#supported-operating-systems). |
| Gruppo di gestione di System Center Operations Manager | Sì | Mapping dei servizi analizza e raccoglie i dati dagli agenti Windows e Linux in un [gruppo di gestione System Center Operations Manager](../log-analytics/log-analytics-om-agents.md) connesso. <br><br>È necessaria una connessione diretta dal computer agente System Center Operations Manager a Operations Management Suite. I dati vengono inoltrati dal gruppo di gestione al repository Operations Management Suite.|
| Account di archiviazione di Azure | No | Mapping dei servizi raccoglie i dati dai computer agente, pertanto non presenta dati che possano essere raccolti dall'Archiviazione di Azure. |

Mapping dei servizi supporta solo piattaforme a 64 bit.

In Windows Microsoft Monitoring Agent (MMA) viene usato da System Center Operations Manager e Operations Management Suite per raccogliere e inviare i dati di monitoraggio. Questo agente viene chiamato agente System Center Operations Manager, agente OMS, agente Log Analytics, agente MMA o diretto, a seconda del contesto. System Center Operations Manager e Operations Management Suite offrono versioni diverse pronte all'uso di MMA. Ognuna di queste versioni può inviare delle segnalazioni a System Center Operations Manager a Operations Management Suite o a entrambi.  

In Linux, l'agente OMS per Linux raccoglie e invia i dati di monitoraggio a Operations Management Suite. È possibile usare Mapping dei servizi nei server con agenti diretti OMS o nei server collegati a Operations Management Suite tramite gruppi di gestione di System Center Operations Manager.  

In questo articolo tutti gli agenti, Linux o Windows, connessi a un gruppo di gestione di System Center Operations Manager o direttamente a Operations Management Suite, verranno chiamati "agenti OMS". Il nome della distribuzione specifica dell'agente verrà usato solo se è necessario per il contesto.

L'agente dell'elenco dei servizi non trasmette dati e non richiede modifiche ai firewall o alle porte. I dati di Mapping dei servizi sono sempre trasmessi dall'agente OMS a Operations Management Suite, direttamente o tramite il gateway OMS.

![Agenti di Mapping dei servizi](media/oms-service-map/agents.png)

Se l'utente è un cliente di System Center Operations Manager con un gruppo di gestione connesso a Operations Management Suite:

- Se gli agenti System Center Operations Manager possono accedere a Internet per connettersi a Operations Management Suite, non è richiesta alcuna configurazione aggiuntiva.  
- Se gli agenti System Center Operations Manager non possono accedere a Operations Management Suite tramite Internet, è necessario configurare il gateway di OMS affinché funzioni con System Center Operations Manager.
  
Se si usa l'agente diretto OMS, è necessario configurare l'agente OMS in modo che si connetta a Operations Management Suite o al gateway OMS. Il gateway OMS può essere scaricato dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=52666).

### <a name="management-packs"></a>Management Pack
Quando viene attivato Mapping dei servizi in un'area di lavoro Operations Management Suite, un management pack da 300 KB viene inviato a tutti i server Windows nell'area di lavoro. Se si usano gli agenti System Center Operations Manager in un [gruppo di gestione connesso](../log-analytics/log-analytics-om-agents.md), il management pack di Mapping dei servizi viene distribuito da System Center Operations Manager. Se gli agenti sono connessi direttamente, Operations Management Suite consente di recapitare il management pack.

Il management pack è denominato Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Viene scritto in %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\. L'origine dati usata dal management pack è %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="installation"></a>Installazione
### <a name="install-the-dependency-agent-on-microsoft-windows"></a>Installare Dependency Agent in Microsoft Windows
Per installare o disinstallare l'agente sono necessari i privilegi di amministratore.

Dependency Agent viene installato nei computer Windows con InstallDependencyAgent-Windows.exe. Se si esegue questo file eseguibile senza opzioni, si avvia una procedura guidata che consente di installare in modo interattivo.  

Per installare Dependency Agent in ogni computer Windows, seguire questa procedura:

1.  Installare l'agente OMS seguendo le istruzioni in [Connettere computer Windows al servizio Log Analytics in Azure](../log-analytics/log-analytics-windows-agents.md).
2.  Scaricare l'agente Windows ed eseguirlo usando il comando seguente: <br>`InstallDependencyAgent-Windows.exe`
3.  Seguire la procedura guidata per installare l'agente.
4.  Se l'agente di dipendenza non si avvia, controllare i registri per vedere le informazioni dettagliate sull'errore. Per gli agenti Windows la directory di log è %Programfiles%\Microsoft Dependency Agent\logs. 

#### <a name="windows-command-line"></a>Riga di comando di Windows
Usare le opzioni della tabella seguente per eseguire l'installazione dalla riga di comando. Per vedere un elenco dei flag di installazione eseguire il programma di installazione con /? come segue.

    InstallDependencyAgent-Windows.exe /?

| Flag | Descrizione |
|:--|:--|
| /? | Ottenere un elenco delle opzioni della riga di comando. |
| /S | Eseguire un'installazione invisibile all'utente senza prompt per l'utente. |

Per impostazione predefinita, i file di Windows Dependency Agent sono posizionati in C:\Program Files\Microsoft Dependency Agent.

### <a name="install-the-dependency-agent-on-linux"></a>Installare Dependency Agent in Linux
Per installare o configurare l'agente è necessario l'accesso alla radice.

Dependency Agent viene installato nei computer Linux con InstallDependencyAgent-Linux64.bin, uno script della shell con un file binario autoestraente. È possibile eseguire il file con sh o aggiungere autorizzazioni di esecuzione al file stesso.
 
Per installare Dependency Agent in ogni computer Linux, seguire questa procedura:

1.  Installare l'agente OMS seguendo le istruzioni in [Collegare e gestire i dati da computer Linux](https://technet.microsoft.com/library/mt622052.aspx).
2.  Installare Linux Dependency Agent come utente ROOT usando il comando seguente:<br>`sh InstallDependencyAgent-Linux64.bin`
3.  Se l'agente di dipendenza non si avvia, controllare i registri per vedere le informazioni dettagliate sull'errore. Per gli agenti Linux la directory di log è /var/opt/microsoft/dependency-agent/log.

Per vedere un elenco dei flag di installazione eseguire il programma di installazione con il flag -help come segue.

    InstallDependencyAgent-Linux64.bin -help

| Flag | Descrizione |
|:--|:--|
| -help | Ottenere un elenco delle opzioni della riga di comando. |
| -s | Eseguire un'installazione invisibile all'utente senza prompt per l'utente. |
| --check | Controllare le autorizzazioni e il sistema operativo ma non installare l'agente. |

I file relativi a Dependency Agent sono memorizzati nelle directory seguenti.

| File | Località |
|:--|:--|
| File core | /opt/microsoft/dependency-agent |
| File di log | /var/opt/microsoft/dependency-agent/log |
| File di configurazione | /etc/opt/microsoft/dependency-agent/config |
| File eseguibili del servizio | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| File binary di archiviazione | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Esempi di script di installazione
Per distribuire facilmente Dependency Agent su più server contemporaneamente è utile usare uno script. È possibile usare gli esempi di script seguenti per scaricare e installare Dependency Agent in Windows o Linux.

### <a name="powershell-script-for-windows"></a>Script di PowerShell per Windows
```PowerShell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Script Shell per Linux
```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Configurazione dello stato desiderato
Per distribuire Dependency Agent tramite Desired State Configuration, è possibile usare il modulo xPSDesiredStateConfiguration e un frammento di codice simile al seguente:
```
configuration ServiceMap {

Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

Node localhost
{ 
    # Download and install the Dependency Agent
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

## <a name="uninstallation"></a>Disinstallazione
### <a name="uninstall-the-dependency-agent-on-windows"></a>Disinstallare Dependency Agent in Windows
Dependency Agent per Windows può essere disinstallato da un amministratore tramite il Pannello di controllo.

Per disinstallare Dependency Agent l'amministratore può anche eseguire %Programfiles%\Microsoft Dependency Agent\Uninstall.exe.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Disinstallare Dependency Agent in Linux
Per disinstallare completamente Dependency Agent da Linux, è necessario rimuovere l'agente stesso e il connettore che viene installato automaticamente con l'agente. È possibile disinstallare entrambi con il seguente comando singolo:

    rpm -e dependency-agent dependency-agent-connector

## <a name="troubleshooting"></a>Risoluzione dei problemi
Se si verificano problemi di installazione o esecuzione di Mapping dei servizi, questa sezione può essere d'aiuto. Se si non riesce a risolvere il problema, contattare il supporto tecnico Microsoft.

### <a name="dependency-agent-installation-problems"></a>Problemi di installazione di Dependency Agent
#### <a name="installer-asks-for-a-reboot"></a>Il programma di installazione richiede il riavvio
Dependency Agent *in genere* non richiede un riavvio dopo l'installazione o la disinstallazione. Tuttavia, in alcuni casi rari, Windows Server può richiedere di riavviare il computer per continuare con l'installazione. Ciò si verifica quando una dipendenza, in genere Visual C++ Redistributable di Microsoft, richiede un riavvio a causa di un file bloccato.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>Viene visualizzato il messaggio "Non è stato possibile installare Dependency Agent: impossibile installare le librerie di runtime di Visual Studio (codice = [numero_di_codice])"

Microsoft Dependency Agent si basa sulle librerie di runtime di Microsoft Visual Studio. Se si verifica un problema durante l'installazione delle librerie, si riceverà un messaggio. 

I programmi di installazione delle librerie di runtime creano log nella cartella %LOCALAPPDATA%\temp. Il file è dd_vcredist_arch_aaaammgghhmmss.log, in cui *arch* è "x86" oppure "amd64" e *aaaammgghhmmss* sarà la data e l'ora, in formato 24 ore, di creazione del log. Il log offre informazioni dettagliate sul problema che impedisce l'installazione.

Potrebbe essere utile per l'utente installare personalmente prima [la versione più recente delle librerie di runtime](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

La tabella seguente elenca i codici e le risoluzioni consigliate.

| Codice | Descrizione | Risoluzione |
|:--|:--|:--|
| 0x17 | Il programma di installazione della libreria richiede un aggiornamento di Windows che non è stato installato. | Cercare nel log del programma di installazione della libreria più recente.<br><br>Se il riferimento a "Windows8.1-KB2999226-x64.msu" è seguito da una riga "Errore 0x80240017: impossibile eseguire il pacchetto MSU", significa che non si dispone dei prerequisiti necessari per installare KB2999226. Seguire le istruzioni riportate nella sezione relativa ai prerequisiti in [Universal C Runtime in Windows](https://support.microsoft.com/kb/2999226). Potrebbe essere necessario eseguire Windows Update e riavviare più volte per installare i prerequisiti.<br><br>Eseguire nuovamente il programma di installazione di Microsoft Dependency Agent. |

### <a name="post-installation-issues"></a>Problemi successivi all'installazione
#### <a name="server-doesnt-appear-in-service-map"></a>Il server non viene visualizzato in Mapping dei servizi
Se l'installazione di Dependency Agent è stata completata correttamente, ma nella soluzione Elenco dei servizi non viene visualizzato il server:
* Dependency Agent è stato installato correttamente? È possibile verificarlo controllando se il servizio è installato ed è in esecuzione.<br><br>
**Windows**: cercare il servizio denominato "Microsoft Dependency Agent".<br>
**Linux**: cercare il processo in esecuzione "microsoft-dependency-agent".

* L'utente dispone del [piano tariffario gratuito di Log Analytics/Operations Management Suite](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)? Il piano gratuito consente di avere fino a cinque server univoci dell'Elenco dei servizi. Tutti i server successivi non verranno visualizzati nell'Elenco dei servizi, anche se i primi cinque non inviano più dati.

* Il server invia il log e i dati delle prestazioni a Operations Management Suite? Passare a Ricerca log ed eseguire la query seguente per il computer: 

        * Computer="<your computer name here>" | measure count() by Type
        
  I risultati mostrano eventi diversi? I dati sono aggiornati? In questo caso, l'agente OMS funziona correttamente e comunica con il servizio Operations Management Suite. In caso contrario, controllare l'agente OMS sul server: [Risoluzione dei problemi dell'agente OMS per Windows](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues) o [Risoluzione dei problemi dell'agente OMS per Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Il server viene visualizzato in Mapping dei servizi, ma non dispone di alcun processo
Se il server viene visualizzato nell'Elenco dei servizi, ma non presenta processi o dati di connessione, vuol dire che Dependency Agent è installato ed è in esecuzione, ma non è stato caricato il driver del kernel. 

Controllare il file C:\Programmi\Microsoft Dependency Agent\logs\wrapper.log per Windows o il file /var/opt/microsoft/dependency-agent/log/service.log per Linux. Le ultime righe del file dovrebbero indicare il motivo per cui il kernel non è stato caricato. Ad esempio, il kernel potrebbe non essere supportato in Linux se è stato aggiornato.

## <a name="data-collection"></a>Raccolta dei dati
È possibile aspettarsi che ogni agente trasmetta circa 25 MB al giorno, in base alla complessità delle dipendenze del sistema. I dati sulle dipendenze di Mapping dei servizi vengono inviati da ogni agente ogni 15 secondi.  

Dependency Agent usa in genere lo 0,1% della memoria di sistema e lo 0,1% della CPU di sistema.

## <a name="supported-azure-regions"></a>Aree di Azure supportate
Mapping dei servizi è attualmente disponibile nelle aree seguenti:
- Stati Uniti orientali
- Europa occidentale
- Stati Uniti centro-occidentali


## <a name="supported-operating-systems"></a>Sistemi operativi supportati
Le sezioni seguenti elencano i sistemi operativi supportati per l'agente di dipendenza. Mapping dei servizi non supporta le architetture a 32 bit per i sistemi operativi.

### <a name="windows-server"></a>Windows Server
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1,

### <a name="windows-desktop"></a>Desktop di Windows
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux e Oracle Linux (con il kernel RHEL)
- Sono supportate solo versioni predefinita e SMP del kernel Linux.
- Le versioni del kernel non standard, ad esempio PAE e Xen, non sono supportate per le distribuzioni Linux. Ad esempio, un sistema con la stringa di versione "2.6.16.21-0.8-xen" non è supportato.
- I kernel personalizzati, tra cui le ricompilazioni dei kernel standard, non sono supportate.
- Il kernel CentOSPlus non è supportato.
- In una sezione successiva di questo articolo viene illustrato Unbreakable Enterprise Kernel (UEK) di Oracle.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7,2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6,5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411<br>2.6.18-412<br>2.6.18-416<br>2.6.18-417<br>2.6.18-419 |

#### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux con Unbreakable Enterprise Kernel

#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Versione del sistema operativo | Versione del kernel
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6,5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Versione del sistema operativo | Versione del kernel
|:--|:--|
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Versione del sistema operativo | Versione del kernel
|:--|:--|
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10
| Versione del sistema operativo | Versione del kernel
|:--|:--|
| 10 SP4 | 2.6.16.60 |

## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo
Microsoft raccoglie automaticamente i dati di utilizzo e prestazioni tramite l'uso del servizio Mapping dei servizi da parte dell'utente. Microsoft usa questi dati per offrire e migliorare la qualità, la sicurezza e l'integrità del servizio Mapping dei servizi. I dati includono informazioni sulla configurazione del software, ad esempio sistema operativo e versione. Includono anche l'indirizzo IP, il nome DNS e il nome della workstation per offrire funzionalità di risoluzione dei problemi accurate ed efficienti. Non vengono raccolti nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta e l'uso dei dati, vedere l'[Informativa sulla privacy di Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [usare Mapping dei servizi](operations-management-suite-service-map.md) dopo averlo distribuito e configurato.

