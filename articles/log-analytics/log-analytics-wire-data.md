---
title: Soluzione Wire Data in Log Analytics | Documentazione Microsoft
description: I dati in transito sono dati consolidati di rete e sulle prestazioni provenienti da computer con agenti OMS, inclusi Operations Manager e gli agenti connessi a Windows. I dati di rete vengono combinati con i dati dei log per poter correlare i dati.
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: magoedte
ms.openlocfilehash: 658d92900763249aeeb3542132f4fe0c43ba005f
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="wire-data-20-preview-solution-in-log-analytics"></a>Soluzione Wire Data 2.0 (anteprima) in Log Analytics

![Simbolo di Wire Data](./media/log-analytics-wire-data/wire-data2-symbol.png)

I dati in transito sono dati consolidati di rete e sulle prestazioni raccolti da computer connessi tramite Windows o Linux all'agente OMS, inclusi i dati monitorati da Operations Manager nell'ambiente in uso. I dati di rete vengono combinati con altri dati di log per consentire la correlazione dei dati.

Oltre all'agente OMS, la soluzione Wire Data usa le istanze di Microsoft Dependency Agent installate nei computer dell'infrastruttura IT. Le istanze di Dependency Agent monitorano i dati di rete inviati da e verso i computer per i livelli di rete 2-3 del [modello OSI](https://en.wikipedia.org/wiki/OSI_model), che includono le diverse porte e i vari protocolli usati. I dati vengono quindi inviati a Log Analytics usando gli agenti.  

> [!NOTE]
> Non è possibile aggiungere la versione precedente della soluzione Wire Data a nuove aree di lavoro. Se è stata abilitata la soluzione Wire Data originale, è possibile continuare a usarla. Per usare Wire Data 2.0, tuttavia, è prima necessario rimuovere la versione originale.

Per impostazione predefinita, Log Analytics registra i dati relativi a CPU, memoria, disco e prestazioni di rete da contatori predefiniti di Windows e Linux, nonché da altri contatori delle prestazioni eventualmente specificati dall'utente. La raccolta dei dati di rete e di altro tipo viene eseguita in tempo reale per ogni agente, inclusi subnet e protocolli a livello di applicazione usati dal computer.  Wire Data esamina i dati di rete a livello di applicazione, non a livello di trasporto TCP, più basso.  La soluzione non esamina ACK e SYN singoli.  Dopo l'esecuzione dell'handshake, la connessione viene considerata attiva e viene contrassegnata come connessa. La connessione rimane attiva finché entrambe le parti acconsentono all'apertura del socket e alla trasmissione di dati nei due sensi.  Se una delle parti chiude la connessione, questa viene contrassegnata come disconnessa.  Viene quindi contata solo la larghezza di banda dei pacchetti completati correttamente e non vengono segnalati i nuovi tentativi di invio né i pacchetti la cui trasmissione non è riuscita.

Se si è usato [sFlow](http://www.sflow.org/) o un altro software con il [protocollo NetFlow di Cisco](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), le statistiche e i dati visualizzati dai dati in transito risulteranno familiari.

Alcuni tipi di query di ricerca nei log predefinite includono:

- Agenti che forniscono dati in transito
- Indirizzo IP degli agenti che forniscono dati in transito
- Comunicazioni in uscita da parte degli indirizzi IP
- Numero di byte inviati dai protocolli applicativi
- Numero di byte inviati da un servizio dell’applicazione
- Byte ricevuti da protocolli diversi
- Byte totali inviati e ricevuti per versione IP
- Latenza media per le connessioni misurate in modo affidabile
- Processi dei computer che hanno avviato o ricevuto traffico di rete
- Quantità di traffico di rete per un processo

Quando si esegue una ricerca usando i dati in transito, è possibile filtrare e raggruppare i dati per visualizzare le informazioni su agenti e protocolli principali. In alternativa, è possibile visualizzare quando determinati computer (indirizzi IP/indirizzi MAC) hanno comunicato tra loro, per quanto tempo e quanti dati sono stati inviati, visualizzando essenzialmente metadati relativi al traffico di rete basati sulla ricerca.

La visualizzazione di metadati, tuttavia, non è necessariamente utile per una risoluzione dei problemi approfondita. I dati in transito in Log Analytics non sono un'acquisizione completa dei dati di rete  e non sono destinati a una risoluzione dei problemi approfondita a livello di pacchetto. Il vantaggio di usare l'agente, rispetto ad altri metodi di raccolta, è che non è necessario installare appliance, riconfigurare i commutatori di rete o eseguire configurazioni complesse. I dati in transito sono basati semplicemente sull'agente, che viene installato in un computer e monitora il proprio traffico di rete. Un altro vantaggio si riscontra quando si vogliono monitorare carichi di lavoro in esecuzione in provider di servizi cloud, provider di servizi di hosting o Microsoft Azure, in cui l'utente non è proprietario del livello infrastruttura.

## <a name="connected-sources"></a>Origini connesse

Wire Data ottiene i dati da Microsoft Dependency Agent. Dependency Agent dipende dall'agente OMS per le connessioni a Log Analytics, quindi prima di installare Dependency Agent è necessario che in un server sia installato e configurato l'agente OMS. La tabella seguente descrive le origini connesse supportate dalla soluzione Wire Data.

| **Origine connessa** | **Supportato** | **Descrizione** |
| --- | --- | --- |
| Agenti di Windows | Sì | Wire Data analizza e raccoglie i dati da computer agente Windows. <br><br> Oltre a [OMS Agent](log-analytics-windows-agent.md), gli agenti Windows richiedono Microsoft Dependency Agent. Per un elenco completo delle versioni del sistema operativo, vedere [Sistemi operativi supportati](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems). |
| Agenti Linux | Sì | Wire Data analizza e raccoglie i dati da computer agente Linux.<br><br> Oltre a [OMS Agent](log-analytics-quick-collect-linux-computer.md), gli agenti Linux richiedono Microsoft Dependency Agent. Per un elenco completo delle versioni del sistema operativo, vedere [Sistemi operativi supportati](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems). |
| Gruppo di gestione di System Center Operations Manager | Sì | Wire Data analizza e raccoglie i dati dagli agenti Windows e Linux in un [gruppo di gestione di System Center Operations Manager](log-analytics-om-agents.md) connesso. <br><br> È necessaria una connessione diretta dal computer agente System Center Operations Manager a Log Analytics. I dati vengono inoltrati dal gruppo di gestione a Log Analytics. |
| Account di archiviazione di Azure | No  | Wire Data raccoglie i dati dai computer agente, quindi non devono essere raccolti dati da Archiviazione di Azure. |

In Windows, Microsoft Monitoring Agent (MMA) viene usato sia da System Center Operations Manager che da Log Analytics per raccogliere e inviare dati. A seconda del contesto, l'agente viene chiamato agente System Center Operations Manager, agente OMS, agente Log Analytics, agente MMA o agente diretto. System Center Operations Manager e Log Analytics offrono versioni leggermente diverse dell'agente MMA. Ognuna di queste versioni può inviare segnalazioni a System Center Operations Manager, Log Analytics o entrambi.

In Linux, l'agente OMS per Linux raccoglie e invia i dati a Log Analytics. È possibile usare Wire Data in server con agenti OMS diretti o in server collegati a Log Analytics tramite gruppi di gestione di System Center Operations Manager.

In questo articolo, il termine _agente OMS_ viene usato per fare riferimento a tutti gli agenti, sia Linux che Windows e sia connessi a un gruppo di gestione di System Center Operations Manager che direttamente a Log Analytics. Il nome della distribuzione specifica dell'agente verrà usato solo se necessario per il contesto.

L'istanza di Dependency Agent non trasmette dati e non richiede modifiche ai firewall o alle porte. I dati in Wire Data vengono sempre trasmessi dall'agente OMS a Log Analytics, direttamente o con il gateway OMS.

![Diagramma degli agenti](./media/log-analytics-wire-data/agents.png)

Per un utente di System Center Operations Manager con un gruppo di gestione connesso a Log Analytics:

- Non è necessaria alcuna configurazione aggiuntiva se gli agenti System Center Operations Manager possono accedere a Internet per connettersi a Log Analytics.
- È necessario configurare il gateway OMS in modo da usare System Center Operations Manager quando gli agenti System Center Operations Manager non possono connettersi a Log Analytics tramite Internet.

Se si usa l'agente diretto, è necessario configurare l'agente OMS in modo che si connetta a Log Analytics o al gateway OMS. È possibile scaricare il gateway OMS dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>prerequisiti

- È necessaria la soluzione [Insight & Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) offerta.
- Se si usa la versione precedente della soluzione Wire Data, prima di tutto è necessario rimuoverla. Tutti i dati acquisiti tramite la soluzione Wire Data originale, tuttavia, saranno ancora disponibili in Wire Data 2.0 e nella ricerca log.
- Per installare o disinstallare Dependency Agent sono necessari privilegi di amministratore.
- Dependency Agent deve essere installato in un computer con un sistema operativo a 64 bit.

### <a name="operating-systems"></a>Sistemi operativi

Le sezioni seguenti elencano i sistemi operativi supportati per l'agente di dipendenza. Wire Data non supporta architetture a 32 bit per i sistemi operativi.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Desktop di Windows

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux e Oracle Linux (con il kernel RHEL)

- Sono supportate solo versioni predefinita e SMP del kernel Linux.
- Le versioni del kernel non standard, ad esempio PAE e Xen, non sono supportate per le distribuzioni Linux. Un sistema con stringa di versione _2.6.16.21-0.8-xen_, ad esempio, non è supportato.
- I kernel personalizzati, tra cui le ricompilazioni dei kernel standard, non sono supportati.
- Il kernel CentOSPlus non è supportato.
- Unbreakable Enterprise Kernel (UEK) di Oracle è illustrato in una sezione successiva di questo articolo.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| **Versione del sistema operativo** | **Versione del kernel** |
| --- | --- |
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7,2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| **Versione del sistema operativo** | **Versione del kernel** |
| --- | --- |
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

| **Versione del sistema operativo** | **Versione del kernel** |
| --- | --- |
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398 <br> 2.6.18-400 <br>2.6.18-402 <br>2.6.18-404 <br>2.6.18-406 <br> 2.6.18-407 <br> 2.6.18-408 <br> 2.6.18-409 <br> 2.6.18-410 <br> 2.6.18-411 <br> 2.6.18-412 <br> 2.6.18-416 <br> 2.6.18-417 <br> 2.6.18-419 |

#### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux con Unbreakable Enterprise Kernel

#### <a name="oracle-linux-6"></a>Oracle Linux 6

| **Versione del sistema operativo** | **Versione del kernel** |
| --- | --- |
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6,5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| **Versione del sistema operativo** | **Versione del kernel** |
| --- | --- |
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11

| **Versione del sistema operativo** | **Versione del kernel** |
| --- | --- |
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10

| **Versione del sistema operativo** | **Versione del kernel** |
| --- | --- |
| 10 SP4 | 2.6.16.60 |

#### <a name="dependency-agent-downloads"></a>Download di Dependency Agent

| **File** | **Sistema operativo** | **Versione** | **SHA-256** |
| --- | --- | --- | --- |
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |



## <a name="configuration"></a>Configurazione

Per configurare la soluzione Wire Data per le proprie aree di lavoro, seguire questa procedura.

1. Abilitare la soluzione Log Analytics attività da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) o seguendo la procedura illustrata in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).
2. Installare Dependency Agent in ogni computer in cui si vogliono ottenere i dati. Dependency Agent può monitorare le connessioni con i vicini immediati e potrebbe quindi non essere necessario un agente in ogni computer.

### <a name="install-the-dependency-agent-on-windows"></a>Installare Dependency Agent in Windows

Per installare o disinstallare l'agente sono necessari i privilegi di amministratore.

Dependency Agent viene installato nei computer che eseguono Windows con InstallDependencyAgent-Windows.exe. Se si esegue questo file eseguibile senza opzioni, avvia una procedura guidata che consente di completare l'installazione in modo interattivo.

Per installare Dependency Agent in ogni computer che esegue Windows, seguire questa procedura:

1. Installare l'agente OMS seguendo la procedura descritta in [Raccogliere dati dai computer Windows ospitati nell'ambiente](log-analytics-windows-agent.md).
2. Scaricare Windows Dependency Agent tramite il collegamento riportato nella sezione precedente e quindi eseguirlo con il comando seguente: `InstallDependencyAgent-Windows.exe`
3. Seguire la procedura guidata per installare l'agente.
4. Se l'agente di dipendenza non si avvia, controllare i registri per vedere le informazioni dettagliate sull'errore. Per gli agenti Windows, la directory di log è %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Riga di comando di Windows

Usare le opzioni della tabella seguente per eseguire l'installazione dalla riga di comando. Per visualizzare un elenco dei flag di installazione, eseguire il programma di installazione con il flag /? come segue.

InstallDependencyAgent-Windows.exe /?

| **Flag** | **Descrizione** |
| --- | --- |
| <code>/?</code> | Ottenere un elenco delle opzioni della riga di comando. |
| <code>/S</code> | Eseguire un'installazione invisibile all'utente senza prompt per l'utente. |

Per impostazione predefinita, i file di Dependency Agent per Windows si trovano in C:\Program Files\Microsoft Dependency Agent.

### <a name="install-the-dependency-agent-on-linux"></a>Installare Dependency Agent in Linux

Per installare o configurare l'agente è necessario l'accesso alla radice.

Dependency Agent viene installato nei computer Linux con InstallDependencyAgent-Linux64.bin, uno script della shell con un file binario autoestraente. È possibile eseguire il file con _sh_ oppure aggiungere autorizzazioni di esecuzione al file stesso.

Per installare Dependency Agent in ogni computer Linux, seguire questa procedura:

1. Installare l'agente OMS seguendo la procedura descritta in [Collect data from Linux computers hosted in your environment](log-analytics-quick-collect-linux-computer.md#obtain-workspace-id-and-key) (Raccogliere dati dai computer Linux ospitati nell'ambiente).
2. Scaricare Linux Dependency Agent tramite il collegamento riportato nella sezione precedente e quindi installarlo come radice con il comando seguente: sh InstallDependencyAgent-Linux64.bin
3. Se l'agente di dipendenza non si avvia, controllare i registri per vedere le informazioni dettagliate sull'errore. Per gli agenti Linux, la directory di log è /var/opt/microsoft/dependency-agent/log.

Per visualizzare un elenco dei flag di installazione, eseguire il programma di installazione con il flag `-help` come segue.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Flag** | **Descrizione** |
| --- | --- |
| <code>-help</code> | Ottenere un elenco delle opzioni della riga di comando. |
| <code>-s</code> | Eseguire un'installazione invisibile all'utente senza prompt per l'utente. |
| <code>--check</code> | Controllare le autorizzazioni e il sistema operativo senza installare l'agente. |

I file relativi a Dependency Agent sono memorizzati nelle directory seguenti.

| **File** | **Posizione** |
| --- | --- |
| File core | /opt/microsoft/dependency-agent |
| File di log | /var/opt/microsoft/dependency-agent/log |
| File di configurazione | /etc/opt/microsoft/dependency-agent/config |
| File eseguibili del servizio | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| File binary di archiviazione | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Esempi di script di installazione

Per distribuire facilmente Dependency Agent in più server contemporaneamente, è utile usare uno script. È possibile usare gli esempi di script seguenti per scaricare e installare Dependency Agent in Windows o Linux.

#### <a name="powershell-script-for-windows"></a>Script di PowerShell per Windows

```PowerShell

Invoke-WebRequest &quot;https://aka.ms/dependencyagentwindows&quot; -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Script della shell per Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Configurazione dello stato desiderato

Per distribuire Dependency Agent tramite Desired State Configuration, è possibile usare il modulo xPSDesiredStateConfiguration e un frammento di codice come il seguente:

```
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = &quot;C:\InstallDependencyAgent-Windows.exe&quot;



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = &quot;https://aka.ms/dependencyagentwindows&quot;

        DestinationPath = $DAPackageLocalPath

        DependsOn = &quot;[Package]OI&quot;

    }

    xPackage DA

    {

        Ensure=&quot;Present&quot;

        Name = &quot;Dependency Agent&quot;

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = &quot;&quot;

        InstalledCheckRegKey = &quot;HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent&quot;

        InstalledCheckRegValueName = &quot;DisplayName&quot;

        InstalledCheckRegValueData = &quot;Dependency Agent&quot;

    }

}

```
### <a name="uninstall-the-dependency-agent"></a>Disinstallare Dependency Agent

Per rimuovere Dependency Agent, usare le sezioni seguenti.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Disinstallare Dependency Agent in Windows

Dependency Agent per Windows può essere disinstallato da un amministratore tramite il Pannello di controllo.

Per disinstallare Dependency Agent, un amministratore può anche eseguire %Programfiles%\Microsoft Dependency Agent\Uninstall.exe.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Disinstallare Dependency Agent in Linux

Per disinstallare completamente Dependency Agent da Linux, è necessario rimuovere l'agente stesso e il connettore che viene installato automaticamente con l'agente. È possibile disinstallare entrambi con il singolo comando seguente:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Management Pack

Quando viene attivato Wire Data in un'area di lavoro di Log Analytics, a tutti i server Windows nell'area di lavoro viene inviato un Management Pack di 300 KB. Se si usano agenti System Center Operations Manager in un [gruppo di gestione connesso](log-analytics-om-agents.md), il Management Pack di Dependency Monitor viene distribuito da System Center Operations Manager. Se gli agenti sono connessi direttamente, il Management Pack viene fornito da Log Analytics.

Il Management Pack è denominato Microsoft.IntelligencePacks.ApplicationDependencyMonitor e viene inserito in %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\. L'origine dati usata dal Management Pack è %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;IDGeneratoAutomaticamente&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Uso della soluzione

**Installazione e configurazione della soluzione**

Usare le informazioni seguenti per installare e configurare la soluzione.

- La soluzione Wire Data acquisisce i dati dai computer che eseguono Windows Server 2012 R2, Windows 8.1 e versioni successive.
- Nei computer da cui si desidera acquisire i dati in transito è necessario che sia installato Microsoft .NET Framework 4.0 o versione successiva.
- Aggiungere la soluzione Wire Data all'area di lavoro di Log Analytics usando la procedura descritta nell'articolo su come [aggiungere soluzioni di Log Analytics dalla raccolta soluzioni](log-analytics-add-solutions.md). Non è richiesta alcuna ulteriore configurazione.
- Se si vogliono visualizzare i dati in transito per una soluzione specifica, è necessario che la soluzione sia già stata aggiunta all'area di lavoro.

Dopo l'installazione degli agenti e della soluzione, nell'area di lavoro verrà visualizzato il riquadro Wire Data 2.0.

![Riquadro Wire Data](./media/log-analytics-wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Uso della soluzione Wire Data 2.0

Nel portale di OMS fare clic sul riquadro **Wire Data 2.0** per aprire il dashboard di Wire Data. Il dashboard include i pannelli nella tabella seguente. Ogni panello elenca fino a 10 elementi corrispondenti ai criteri del pannello per lo scope e l'intervallo di tempo specificati. È possibile eseguire una ricerca log per ottenere tutti i record facendo clic su **Vedi tutto** nella parte inferiore del pannello o facendo clic sull'intestazione del pannello.

| **Pannello** | **Descrizione** |
| --- | --- |
| Agenti che acquisiscono il traffico di rete | Mostra il numero degli agenti che acquisiscono il traffico di rete e un elenco dei primi 10 computer che acquisiscono il traffico. Fare clic sul numero per eseguire una ricerca nei log per <code>Type:WireData &#124; measure Sum(TotalBytes) by Computer &#124; top 500000</code>. Fare clic su un computer nell'elenco per eseguire una ricerca nei log che restituisca il numero totale dei byte acquisiti. |
| Subnet locali | Mostra il numero delle subnet locali individuate dagli agenti.  Fare clic sul numero per eseguire una ricerca nei log per <code>Type:WireData &#124; Measure Sum(TotalBytes) by LocalSubnet</code> e ottenere un elenco di tutte le subnet con il numero dei byte inviati tramite ognuna. Fare clic su una subnet nell'elenco per eseguire una ricerca nei log che restituisca il numero totale dei byte inviati tramite la subnet. |
| Protocolli a livello dell'applicazione | Mostra il numero di protocolli a livello di applicazione in uso, in base a quanto individuato dagli agenti. Fare clic sul numero per eseguire una ricerca nei log per <code>Type:WireData &#124; Measure Sum(TotalBytes) by ApplicationProtocol</code>. Fare clic su un protocollo per eseguire una ricerca nei log che restituisca il numero totale dei byte inviati usando il protocollo. |

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Dashboard di Wire Data](./media/log-analytics-wire-data/wire-data-dash.png)

È possibile usare il pannello **Agenti che acquisiscono il traffico di rete** per determinare la quantità di larghezza di banda utilizzata dai computer. Questo pannello consente di trovare facilmente il computer _più comunicativo_ nel proprio ambiente. Tali computer potrebbero essere sovraccaricati, presentare un funzionamento anomalo o usare una quantità di risorse di rete superiore alla norma.

![Esempio di ricerca log](./media/log-analytics-wire-data/log-search-example01.png)

Analogamente, è possibile usare il pannello **Subnet locali** per determinare la quantità di traffico di rete sulle subnet. Gli utenti spesso definiscono le subnet per aree critiche per le applicazioni. Questo pannello offre un quadro di tali aree.

![Esempio di ricerca log](./media/log-analytics-wire-data/log-search-example02.png)

Il pannello **Protocolli a livello dell'applicazione** è utile perché è opportuno sapere quali protocolli vengono usati. Se ad esempio si prevede che SSH non venga usato nel proprio ambiente di rete, visualizzando le informazioni disponibili nel pannello è possibile ottenere rapidamente conferma o smentita di tale previsione.

![Esempio di ricerca log](./media/log-analytics-wire-data/log-search-example03.png)

In questo esempio si potrebbero esaminare i dettagli su SSH per scoprire quali computer usano SSH e molti altri dettagli relativi alle comunicazioni.

![Risultati della ricerca su SSH](./media/log-analytics-wire-data/ssh-details.png)

È anche utile sapere se il traffico dei protocolli aumenta o diminuisce nel tempo. L'aumento della quantità di dati trasmessa da un'applicazione, ad esempio, può essere un aspetto di cui è consigliabile essere a conoscenza o che si potrebbe trovare degno di nota.

## <a name="input-data"></a>Dati di input

Wire Data raccoglie i metadati sul traffico di rete tramite gli agenti abilitati. Ogni agente invia dati ogni 15 secondi circa.

## <a name="output-data"></a>Dati di output

Per ogni tipo di dati di input vene creato un record con tipo _WireData_. I record WireData includono le proprietà elencate nella tabella seguente:

| Proprietà | DESCRIZIONE |
|---|---|
| Computer | Nome del computer in cui sono stati raccolti i dati |
| TimeGenerated | Ora del record |
| LocalIP | Indirizzo IP del computer locale |
| SessionState | Sessione connessa o disconnessa |
| ReceivedBytes | Quantità di byte ricevuta |
| ProtocolName | Nome del protocollo di rete usato |
| IPVersion | Versione IP |
| Direzione | In ingresso o in uscita |
| MaliciousIP | Indirizzo IP di un'origine dannosa nota |
| Gravità | Gravità del software dannoso sospetto |
| RemoteIPCountry | Paese dell'indirizzo IP remoto |
| ManagementGroupName | Nome del gruppo di gestione di Operations Manager |
| SourceSystem | Origine in cui sono stati raccolti i dati |
| SessionStartTime | Data e ora di inizio della sessione |
| SessionEndTime | Data e ora di fine della sessione |
| LocalSubnet | Subnet in cui sono stati raccolti i dati |
| LocalPortNumber | Numero di porta locale |
| RemoteIP | Indirizzo IP remoto usato dal computer remoto |
| RemotePortNumber | Numero di porta usato dall'indirizzo IP remoto |
| SessionID | Valore univoco che identifica la sessione di comunicazione tra due indirizzi IP |
| SentBytes | Numero di byte inviati |
| TotalBytes | Numero totale dei byte inviati durante la sessione |
| ApplicationProtocol | Tipo di protocollo di rete usato   |
| ProcessID | ID processo Windows |
| ProcessName | Percorso e nome file del processo |
| RemoteIPLongitude | Valore di longitudine dell'indirizzo IP |
| RemoteIPLatitude | Valore di latitudine dell'indirizzo IP |


## <a name="next-steps"></a>Passaggi successivi

- [Ricerche nei log](log-analytics-log-searches.md) per visualizzare i record di ricerca dettagliati su Wire Data.
