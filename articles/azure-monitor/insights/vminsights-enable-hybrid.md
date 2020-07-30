---
title: Abilitare monitoraggio di Azure per un ambiente ibrido
description: Questo articolo descrive come abilitare Monitoraggio di Azure per le macchine virtuali per un ambiente cloud ibrido che contiene una o più macchine virtuali.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: ccf4ad960abfd737a9a05d8fdc77a8bb1ea92d2d
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87417115"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-virtual-machine"></a>Abilitare Monitoraggio di Azure per le macchine virtuali per una macchina virtuale ibrida
Questo articolo descrive come abilitare Monitoraggio di Azure per le macchine virtuali per una macchina virtuale all'esterno di Azure, inclusi ambienti locali e altri ambienti cloud.

> [!IMPORTANT]
> Il metodo consigliato per abilitare le VM ibride è la prima volta che si abilita [Azure Arc per i server](../../azure-arc/servers/overview.md) , in modo che le macchine virtuali possano essere abilitate per monitoraggio di Azure per le macchine virtuali usando processi simili alle macchine virtuali di Azure. Questo articolo descrive come eseguire l'onboarding di macchine virtuali ibride se si sceglie di non usare Azure Arc.

## <a name="prerequisites"></a>Prerequisiti

- [Creare e configurare un'area di lavoro log Analytics](vminsights-configure-workspace.md).
- Vedere [sistemi operativi supportati](vminsights-enable-overview.md#supported-operating-systems) per assicurarsi che il sistema operativo della macchina virtuale o del set di scalabilità di macchine virtuali che si sta abilitando sia supportato. 


## <a name="overview"></a>Panoramica
Le macchine virtuali esterne ad Azure richiedono lo stesso agente di Log Analytics e l'agente di dipendenza usato per le macchine virtuali di Azure. Poiché non è possibile usare le estensioni VM per installare gli agenti, è necessario installarli manualmente nel sistema operativo guest oppure installarli tramite un altro metodo. 

Vedere [connettere computer Windows a monitoraggio di Azure](../platform/agent-windows.md) o [connettere computer Linux a monitoraggio di Azure](../platform/agent-linux.md) per informazioni dettagliate sulla distribuzione dell'agente di log Analytics. In questo articolo vengono forniti i dettagli di Dependency Agent. 

## <a name="firewall-requirements"></a>Requisiti del firewall
I requisiti del firewall per l'agente Log Analytics sono disponibili nella [Panoramica di log Analytics Agent](../platform/log-analytics-agent.md#network-requirements). Dependency Agent per la mappa di Monitoraggio di Azure per le macchine virtuali non trasmette dati e non richiede modifiche ai firewall o alle porte. I dati della mappa vengono sempre trasmessi dall'agente Log Analytics al servizio monitoraggio di Azure, direttamente o tramite il [gateway di Operations Management Suite](../../azure-monitor/platform/gateway.md) se i criteri di sicurezza IT non consentono ai computer della rete di connettersi a Internet.


## <a name="dependency-agent"></a>Dependency Agent

>[!NOTE]
>Le seguenti informazioni descritte in questa sezione sono applicabili anche alla [soluzione mapping dei servizi](service-map.md).  

È possibile scaricare Dependency Agent da questi percorsi:

| File | Sistema operativo | Versione | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.4.10090 | B4E1FF9C1E5CD254AA709AEF9723A81F04EC0763C327567C582CE99C0C5A0BAE  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.4.10090 | A56E310D297CE3B343AE8F4A6F72980F1C3173862D6169F1C713C2CA09660A9F |


## <a name="install-the-dependency-agent-on-windows"></a>Installare Dependency Agent in Windows

Dependency Agent può essere installato manualmente nei computer Windows eseguendo `InstallDependencyAgent-Windows.exe`. Se si esegue questo file eseguibile senza opzioni, si avvia una procedura guidata che consente di completare l'installazione dell'agente in modo interattivo. Per installare o disinstallare l'agente, è necessario disporre dei privilegi di *amministratore* sul sistema operativo guest.

La tabella seguente illustra i parametri supportati dal programma di installazione per l'agente dalla riga di comando.

| Parametro | Descrizione |
|:--|:--|
| /? | Restituisce un elenco delle opzioni della riga di comando. |
| /S | Esegue un'installazione invisibile all'utente, senza interazione da parte dell'utente. |

Ad esempio, per eseguire il programma di installazione con il `/?` parametro, immettere **InstallDependencyAgent-Windows.exe/?**.

Per impostazione predefinita, i file di Dependency Agent per Windows si trovano in *C:\Programmi\Microsoft Dependency Agent*. Se non è possibile avviare l'agente di dipendenza dopo aver completato l'installazione, controllare i registri per informazioni dettagliate sull'errore. La directory dei log è *%Programfiles%\Microsoft Dependency Agent\logs*.

### <a name="powershell-script"></a>Script PowerShell
Usare il seguente script di PowerShell di esempio per scaricare e installare l'agente:

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```


## <a name="install-the-dependency-agent-on-linux"></a>Installare Dependency Agent in Linux

Dependency Agent viene installato nei server Linux con *InstallDependencyAgent-Linux64.bin*, uno script della shell con un file binario autoestraente. È possibile eseguire il file con `sh` oppure aggiungere autorizzazioni di esecuzione al file stesso.

>[!NOTE]
> Per installare o configurare l'agente è necessario l'accesso alla radice.
>

| Parametro | Descrizione |
|:--|:--|
| -help | Ottenere un elenco delle opzioni della riga di comando. |
| -S | Eseguire un'installazione invisibile all'utente senza prompt per l'utente. |
| --check | Controllare le autorizzazioni e il sistema operativo senza installare l'agente. |

Ad esempio, per eseguire il programma di installazione con il `-help` parametro, immettere **installdependencyagent-linux64. bin-Help**. Installare Linux Dependency Agent come root eseguendo il comando `sh InstallDependencyAgent-Linux64.bin` .

Se Dependency Agent non si avvia, controllare i log per vedere le informazioni dettagliate sull'errore. Negli agenti Linux la directory di log è */var/opt/Microsoft/Dependency-Agent/log*.

I file relativi a Dependency Agent sono memorizzati nelle directory seguenti:

| File | Location |
|:--|:--|
| File core | /opt/microsoft/dependency-agent |
| File di registro | /var/opt/microsoft/dependency-agent/log |
| File di configurazione | /etc/opt/microsoft/dependency-agent/config |
| File eseguibili del servizio | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| File binary di archiviazione | /var/opt/microsoft/dependency-agent/storage |

### <a name="shell-script"></a>Script della shell 
Usare il seguente script della shell di esempio per scaricare e installare l'agente:

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Desired State Configuration

Per distribuire Dependency Agent tramite Desired State Configuration (DSC), è possibile usare il modulo xPSDesiredStateConfiguration con il codice di esempio seguente:

```powershell
configuration VMInsights {

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



## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="vm-doesnt-appear-on-the-map"></a>La macchina virtuale non viene visualizzata nella mappa

Se l'installazione dell'agente di dipendenza ha avuto esito positivo, ma il computer non è visualizzato sulla mappa, diagnosticare il problema attenendosi alla procedura seguente.

1. Dependency Agent è stato installato correttamente? È possibile verificarlo controllando se il servizio è installato ed è in esecuzione.

    **Windows**: cercare il servizio denominato "Microsoft Dependency Agent".

    **Linux**: cercare il processo in esecuzione "Microsoft-Dependency-Agent".

2. Il piano [tariffario gratuito di log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)? Il piano gratuito consente fino a cinque computer univoci. Eventuali computer successivi non verranno visualizzati sulla mappa, anche se i cinque precedenti non inviano più dati.

3. Il computer invia i dati di log e delle prestazioni ai log di monitoraggio di Azure? Eseguire la query seguente per il computer:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Sono stati restituiti uno o più risultati? I dati sono aggiornati? In tal caso, l'agente di Log Analytics funziona correttamente e comunica con il servizio. In caso contrario, controllare l'agente sul server: [Risoluzione dei problemi dell'agente di Log Analytics per Windows](../platform/agent-windows-troubleshoot.md) o [Risoluzione dei problemi dell'agente di Log Analytics per Linux](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Il computer viene visualizzato nella mappa ma non ha processi

Se il server è visualizzato sulla mappa, ma non contiene dati di processo o di connessione, questo indica che l'agente di dipendenza è stato installato e in esecuzione, ma il driver del kernel non è stato caricato.

Controllare il file C:\Programmi\Microsoft Dependency Agent\logs\wrapper.log per Windows o il file /var/opt/microsoft/dependency-agent/log/service.log per Linux. Le ultime righe del file dovrebbero indicare il motivo per cui il kernel non è stato caricato. Ad esempio, il kernel potrebbe non essere supportato in Linux se è stato aggiornato.


## <a name="next-steps"></a>Passaggi successivi

Ora che il monitoraggio è abilitato per le macchine virtuali, queste informazioni sono disponibili per l'analisi con Monitoraggio di Azure per le macchine virtuali.

- Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md).

- Per identificare i colli di bottiglia e l'utilizzo complessivo delle prestazioni della VM, vedere [visualizzare le prestazioni delle VM di Azure](vminsights-performance.md).
