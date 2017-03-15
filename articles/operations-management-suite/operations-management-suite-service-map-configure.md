---
title: Configurare l&quot;elenco dei servizi in Operations Management Suite (OMS) | Microsoft Docs
description: "L&quot;elenco dei servizi è una soluzione di Operations Management Suite (OMS) che individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux e mappa la comunicazione fra i servizi.  Questo articolo fornisce informazioni dettagliate su come distribuire l&quot;elenco dei servizi nell&quot;ambiente e su come usarlo in svariati scenari."
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
translationtype: Human Translation
ms.sourcegitcommit: 450755b94d7abab9ab7fa626e02f404a73c97865
ms.openlocfilehash: ac17a567092b8ecf4908d28e6743ba0816fa214e
ms.lasthandoff: 02/22/2017


---
# <a name="configuring-service-map-solution-in-operations-management-suite-oms"></a>Configurare la soluzione di elenco dei servizi in Operations Management Suite (OMS)
L'elenco dei servizi individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue la mappatura della comunicazione fra i servizi. Consente di visualizzare i server nel modo in cui si pensa a essi, ovvero come sistemi interconnessi che forniscono servizi critici.  L'elenco dei servizi mostra le connessioni fra i server, i processi e le porte di tutte le architetture connesse via TCP senza il bisogno di alcuna configurazione a parte l'installazione di un agente.

Questo articolo descrive in dettaglio come configurare l'elenco dei servizi e il caricamento degli agenti.  Per informazioni sull'uso dell'elenco dei servizi, vedere [Using Service Map solution in Operations Management Suite (OMS)](operations-management-suite-service-map.md) (Usare la soluzione di elenco dei servizi in Operations Management Suite (OMS)).


## <a name="connected-sources"></a>Origini connesse
Elenco dei servizi ottiene i dati da Microsoft Dependency Agent,  che dipende da OMS Agent per le connessioni a OMS.  È quindi necessario che OMS Agent venga installato e configurato nel server prima di Microsoft Dependency Agent.  La tabella seguente descrive le origini connesse che sono supportate dalla soluzione di elenco dei servizi.

| Origine connessa | Supportato | Descrizione |
|:--|:--|:--|
| Agenti di Windows | Sì | L'elenco dei servizi analizza e raccoglie i dati dai computer agente Windows.  <br><br>Oltre a [OMS Agent](../log-analytics/log-analytics-windows-agents.md), gli agenti Windows richiedono Microsoft Dependency Agent.  Per un elenco completo delle versioni del sistema operativo, vedere la sezione sui [sistemi operativi supportati](#supported-operating-systems). |
| Agenti Linux | Sì | L'elenco dei servizi analizza e raccoglie i dati dai computer agente Linux.  <br><br>Oltre a [OMS Agent](../log-analytics/log-analytics-linux-agents.md), gli agenti Linux richiedono Microsoft Dependency Agent.  Per un elenco completo delle versioni del sistema operativo, vedere la sezione sui [sistemi operativi supportati](#supported-operating-systems). |
| Gruppo di gestione SCOM | Sì | L'elenco dei servizi analizza e raccoglie i dati dagli agenti Windows e Linux in un [gruppo di gestione System Center Operations Manager (SCOM)](../log-analytics/log-analytics-om-agents.md) connesso. <br><br>È necessaria una connessione diretta dal computer agente SCOM a OMS. I dati vengono inviati direttamente dal gruppo di gestione al repository OMS.|
| Account di archiviazione di Azure | No | L'elenco dei servizi raccoglie i dati dai computer agente, pertanto non presenta dati che possano essere raccolti dall'archiviazione di Azure. |

L'elenco dei servizi supporta solo piattaforme a 64 bit.

In Windows, Microsoft Monitoring Agent (MMA) viene usato sia da SCOM che da OMS per raccogliere e inviare i dati di monitoraggio.  Questo agente è chiamato agente SCOM, agente OMS, MMA o agente diretto, a seconda del contesto.  SCOM e OMS forniscono diverse versioni già pronte di MMA, ma ciascuna di queste versioni può inviare i dati a SCOM, a OMS o a entrambi.  In Linux, l'agente OMS per Linux raccoglie e invia i dati di monitoraggio a OMS.  L'elenco dei servizi può essere usato nei server con agenti diretti OMS o nei server collegati a OMS tramite gruppi di gestione di SCOM.  In questa documentazione tutti gli agenti, che siano in Linux o in Windows e connessi a un gruppo di gestione SCOM o direttamente a OMS, saranno chiamati "agente OMS", a meno che, per il contesto, sia necessario il nome di distribuzione dell'agente.

L'agente dell'elenco dei servizi non trasmette dati e non richiede modifiche ai firewall o alle porte.  I dati dell'elenco dei servizi sono sempre trasmessi dall'agente OMS a OMS, direttamente o tramite il gateway OMS.

![Agenti dell'elenco dei servizi](media/oms-service-map/agents.png)

Per i clienti SCOM con un gruppo di gestione connesso a OMS:

- Se gli agenti SCOM possono accedere a Internet per connettersi al servizio OMS, non è necessaria alcuna configurazione aggiuntiva.  
- Se gli agenti SCOM non possono accedere a OMS tramite Internet, è necessario configurare il gateway OMS per l'uso di SCOM.
  
Se si usa l'agente diretto OMS, è necessario configurare l'agente OMS in modo che si connetta a OMS o al gateway OMS.  Il gateway OMS può essere scaricato dall'indirizzo [https://www.microsoft.com/en-us/download/details.aspx?id=52666](https://www.microsoft.com/en-us/download/details.aspx?id=52666)


### <a name="avoiding-duplicate-data"></a>Evitare dati duplicati

I clienti SCOM non devono configurare gli agenti SCOM in modo che comunichino direttamente con OMS, perché in questo modo i dati verrebbero inviati due volte.  Di conseguenza, nell'elenco dei servizi i computer nell'elenco dei computer comparirebbero due volte.

La configurazione di OMS deve essere eseguita solo in una delle seguenti posizioni:

- Il pannello SCOM Console Operations Management Suite per i computer gestiti
- Configurazione di Azure Operational Insights nelle proprietà MMA

Usando entrambe le configurazioni con la *stessa* area di lavoro, in ognuna si produrrà la duplicazione dei dati. Usando entrambe le configurazioni con aree di lavoro *diverse*, può verificarsi un conflitto tra le configurazioni (una con la soluzione di elenco dei servizi abilitata e l'altra senza) che potrebbe impedire il completo trasferimento dei dati verso l'elenco dei servizi.  

Anche se il computer non è specificato nella configurazione OMS della console SCOM, se è attivo un gruppo di istanze come il "Gruppo di istanze di Windows Server", come conseguenza il computer potrebbe comunque ricevere la configurazione OMS tramite SCOM.



## <a name="management-packs"></a>Management Pack
Quando viene attivato un elenco dei servizi in un'area di lavoro OMS, un management pack da 300 KB viene inviato a tutti i Microsoft Monitoring Agent nell'area di lavoro.  Se si usano gli agenti di SCOM in un [gruppo di gestione connesso](../log-analytics/log-analytics-om-agents.md), il management pack dell'elenco dei servizi verrà distribuito da SCOM.  Se gli agenti sono connessi direttamente, il management pack verrà consegnato da OMS.

Il management pack è denominato Microsoft.IntelligencePacks.ApplicationDependencyMonitor*.  Viene scritto in *%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\*.  L'origine dati utilizzata dal management pack è *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*.



## <a name="configuration"></a>Configurazione
Oltre al fatto che i computer Windows e Linux devono avere un agente installato e connesso a OMS, il programma di installazione di Dependency Agent deve essere scaricato dalla soluzione di elenco dei servizi e quindi installato dall'utente ROOT o da un amministratore in ogni server gestito.  Dopo aver installato l'agente dell'elenco dei servizi in un server che invia dati a OMS, le mappe di dipendenza dell'elenco dei servizi compariranno entro 10 minuti.


### <a name="migrating-from-bluestripe-factfinder"></a>Migrazione da BlueStripe FactFinder
L'elenco dei servizi implementerà la tecnologia BlueStripe in OMS a fasi. FactFinder è ancora supportato per i clienti esistenti ma non è più disponibile per l'acquisto individuale.  Questa versione di anteprima dell'agente di dipendenza può comunicare solo con OMS.  I clienti FactFinder dovrebbero identificare un set di server di prova per l'elenco dei servizi che non sono gestiti da FactFinder. 

### <a name="download-the-dependency-agent"></a>Scaricare l'agente di dipendenza
Oltre a Microsoft Management Agent (MMA) e OMS Linux Agent che servono a connettere il computer e OMS, in tutti i computer analizzati dall'elenco dei servizi deve essere installato Dependency Agent.  In Linux l'agente OMS per Linux deve essere installato prima dell'agente di dipendenza. 

![Riquadro dell'elenco dei servizi](media/oms-service-map/additional_configuration.png)

Per scaricare Dependency Agent, fare clic su **Configura soluzione** nel riquadro **Elenco dei servizi** per aprire il pannello **Dependency Agent**.  Il pannello dell'agente dipendenza presenta collegamenti per gli agenti Windows e Linux. Per informazioni sull'installazione dell'agente nei vari sistemi, vedere le sezioni seguenti.

### <a name="install-the-dependency-agent"></a>Installare l'agente di dipendenza

#### <a name="microsoft-windows"></a>Microsoft Windows
Per installare o disinstallare l'agente sono necessari i privilegi di amministratore.

Dependency Agent viene installato nei computer Windows con InstallDependencyAgent-Windows.exe. Se si avvia questo eseguibile senza opzioni, si ottiene una procedura guidata che consente di installare in modo interattivo.  

Per installare Dependency Agent in ogni computer Windows, seguire questa procedura:

1.    Installare l'agente OMS seguendo le istruzioni per connettere i computer direttamente a OMS.
2.    Scaricare l'agente Windows ed eseguirlo con il comando seguente: <br>*InstallDependencyAgent-Windows.exe*
3.    Seguire la procedura guidata per installare l'agente.
4.    Se l'agente di dipendenza non si avvia, controllare i registri per vedere le informazioni dettagliate sull'errore. Per gli agenti Windows la directory di log è *C:\Program Files\Microsoft Dependency Agent\logs*. 

L'agente di dipendenza per Windows può essere installato da un amministratore tramite il Pannello di controllo.


#### <a name="linux"></a>Linux
Per installare o configurare l'agente è necessario l'accesso alla radice.

Dependency Agent viene installato nei computer Linux con InstallDependencyAgent-Linux64.bin, uno script della shell con un file binario autoestraente. È possibile eseguire il file con sh o aggiungere autorizzazioni di esecuzione al file stesso.
 
Per installare Dependency Agent in ogni computer Linux, seguire questa procedura:

1.    Installare l'agente OMS seguendo le istruzioni per [raccogliere e gestire i dati da computer Linux.  L'agente OMS deve essere installato prima di Linux Dependency Agent](https://technet.microsoft.com/library/mt622052.aspx).
2.    Installare Linux Dependency Agent come utente ROOT usando il comando seguente:<br>*sh InstallDependencyAgent-Linux64.bin*.
3.    Se l'agente di dipendenza non si avvia, controllare i registri per vedere le informazioni dettagliate sull'errore. Per gli agenti Linux la directory di log è */var/opt/microsoft/dependency-agent/log*.

### <a name="uninstalling-the-dependency-agent-on-linux"></a>Disinstallazione dell'agente di dipendenza in Linux
Per disinstallare completamente Dependency Agent da Linux, è necessario rimuovere l'agente stesso e il connettore che viene installato automaticamente con l'agente.  È possibile disinstallare entrambi con il seguente comando singolo:

    rpm -e dependency-agent dependency-agent-connector


### <a name="installing-from-a-command-line"></a>Installazione dalla riga di comando
La sezione precedente fornisce istruzioni sull'installazione dell'agente di monitoraggio delle dipendenze con le opzioni predefinite.  Le sezioni seguenti forniscono istruzioni per l'installazione dell'agente dalla riga di comando con opzioni personalizzate.

#### <a name="windows"></a>Windows
Usare le opzioni della tabella seguente per eseguire l'installazione dalla riga di comando. Per vedere un elenco dei flag di installazione eseguire il programma di installazione con il flag /? come segue.

    InstallDependencyAgent-Windows.exe /?

| Flag | Descrizione |
|:--|:--|
| /S | Eseguire un'installazione invisibile all'utente senza prompt per l'utente. |

Per impostazione predefinita, i file di Windows Dependency Agent sono posizionati in *C:\Program Files\Microsoft Dependency Agent*.


#### <a name="linux"></a>Linux
Eseguire l'installazione usando le opzioni della tabella seguente. Per vedere un elenco dei flag di installazione eseguire il programma di installazione con il flag -help come segue.

    InstallDependencyAgent-Linux64.bin -help

| Descrizione del flag
|:--|:--|
| -s | Eseguire un'installazione invisibile all'utente senza prompt per l'utente. |
| --check | Controlla le autorizzazioni e il sistema operativo ma non installa l'agente. |

I file relativi a Dependency Agent sono memorizzati nelle directory seguenti.

| File | Località |
|:--|:--|
| File core | /opt/microsoft/dependency-agent |
| File di log | /var/opt/microsoft/dependency-agent/log |
| File di configurazione | /etc/opt/microsoft/dependency-agent/config |
| Eseguibili del servizio | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| File binary di archiviazione | /var/opt/microsoft/dependency-agent/storage |



## <a name="troubleshooting"></a>Risoluzione dei problemi
In caso di problemi con l'elenco dei servizi, è possibile usare le informazioni seguenti per raccogliere le informazioni sulla risoluzione da più componenti.

### <a name="windows-agents"></a>Agenti di Windows

#### <a name="microsoft-dependency-agent"></a>Microsoft Dependency Agent
Per generare dati di risoluzione dei problemi di Dependency Agent, aprire un prompt dei comandi come amministratore ed eseguire lo script CollectDependencyAgentData.vbs mediante il seguente comando.  È possibile aggiungere il flag --help per mostrare opzioni aggiuntive.

    cd C:\Program Files\Microsoft Dependency Agent\scripts
    cscript CollectDependencyData.vbs

Il pacchetto dei dati di supporto viene salvato nella directory %USERPROFILE% per l'utente corrente.  È possibile usare l'opzione --file <filename> per salvarlo in un'altra posizione.

#### <a name="microsoft-dependency-agent-management-pack-for-mma"></a>Microsoft Dependency Agent Management Pack per MMA
Microsoft Dependency Agent Management Pack si esegue all'interno di Microsoft Management Agent.  Riceve i dati da Dependency Agent e li inoltra al servizio cloud di elenco dei servizi.
  
Verificare che il management pack venga scaricato eseguendo la procedura seguente:

1.    Cercare un file denominato Microsoft.IntelligencePacks.ApplicationDependencyMonitor.mp in C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs.  
2.    Se il file non è presente e l'agente è connesso a un gruppo di gestione di SCOM, verificare che sia stato importato in SCOM controllando Management Packs nell'area di lavoro di amministrazione della Console operatore.

Il management pack dell'elenco dei servizi scrive gli eventi nel log eventi di Windows per Operations Manager.  Il log può essere [ricercato in OMS](../log-analytics/log-analytics-log-searches.md) mediante la soluzione del log di sistema, dove è possibile configurare quali file log caricare.  Se sono abilitati gli eventi di debug, verranno scritti nel log eventi dell'applicazione con l'origine evento *ADMConnector*.

#### <a name="microsoft-monitoring-agent"></a>Microsoft Monitoring Agent
Per raccogliere tracce diagnostiche, aprire un prompt dei comandi come amministratore ed eseguire i seguenti comandi: 

    cd \Program Files\Microsoft Monitoring Agent\Agent\Tools
    net stop healthservice 
    StartTracing.cmd ERR
    net start healthservice

Le tracce vengono scritte in C:\Windows\Logs\OpsMgrTrace.  È possibile arrestare la traccia con StopTracing.cmd.


### <a name="linux-agents"></a>Agenti Linux

#### <a name="microsoft-dependency-agent"></a>Microsoft Dependency Agent
Per generare dati di risoluzione dei problemi dall'agente di dipendenza, accedere con un account dotato di privilegi sudo o root ed eseguire il comando seguente.  È possibile aggiungere il flag --help per mostrare opzioni aggiuntive.

    /opt/microsoft/dependency-agent/lib/scripts/collect-dependency-agent-data.sh

Il pacchetto dei dati di supporto viene salvato in /var/opt/microsoft/dependency-agent/log (utente root) sotto la directory di installazione dell'agente o nella home directory dell'utente che esegue lo script (utente non root).  È possibile usare l'opzione --file <filename> per salvarlo in un'altra posizione.

#### <a name="microsoft-dependency-agent-fluentd-plug-in-for-linux"></a>Plug-in Microsoft Dependency Agent Fluentd per Linux
Il plug-in Dependency Agent Fluentd si esegue all'interno dell'agente OMS Linux.  Riceve i dati da Dependency Agent e li inoltra al servizio cloud di elenco dei servizi.  

I log vengono scritti nei due file seguenti.

- /var/opt/microsoft/omsagent/log/omsagent.log
- /var/log/messages

#### <a name="oms-agent-for-linux"></a>Agente OMS per Linux
Una risorsa di risoluzione dei problemi per la connessione di server Linux a OMS è disponibile qui: [https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md) 

I log per l'agente OMS per Linux si trovano in */var/opt/microsoft/omsagent/log/*.  

I log per omsconfig (configurazione dell'agente) si trovano in */var/opt/microsoft/omsconfig/log/*.
 
Il log per i componenti OMI e SCX che forniscono dati relativi alle metriche di prestazioni si trovano in */var/opt/omi/log/* e */var/opt/microsoft/scx/log*.


## <a name="data-collection"></a>Raccolta dei dati
È possibile aspettarsi che ogni agente trasmetta circa 25 MB al giorno, in base alla complessità delle dipendenze del sistema.  I dati sulle dipendenze dell'elenco dei servizi vengono inviati da ogni agente ogni 15 secondi.  

Dependency Agent usa in genere lo 0,1% della memoria di sistema e lo 0,1% della CPU di sistema.


## <a name="supported-operating-systems"></a>Sistemi operativi supportati
Le sezioni seguenti elencano i sistemi operativi supportati per l'agente di dipendenza.   Le architetture a&32; bit non sono supportate per nessun sistema operativo.

### <a name="windows-server"></a>Windows Server
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1,

### <a name="windows-desktop"></a>Desktop di Windows
- WIndows 10
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux e Oracle Linux (con il kernel RHEL)
- Sono supportate solo versioni predefinita e SMP del kernel Linux.
- Le versioni del kernel non standard, ad esempio PAE e Xen, non sono supportate per nessuna distribuzione Linux. Ad esempio, un sistema con la stringa di versione "2.6.16.21-0.8-xen" non è supportato.
- I kernel personalizzati, tra cui le ricompilazioni dei kernel standard, non sono supportate
- Il kernel Centos Plus non è supportato.
- Oracle Unbreakable Kernel (UEK) è trattato in un'altra sezione di seguito.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| Versione sistema operativo | Versione del kernel |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7,2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| Versione sistema operativo | Versione del kernel |
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
| Versione sistema operativo | Versione del kernel |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411<br>2.6.18-412<br>2.6.18-416<br>2.6.18-417 |

#### <a name="oracle-enterprise-linux-w-unbreakable-kernel-uek"></a>Oracle Enterprise Linux con Unbreakable Kernel (UEK)

#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Versione sistema operativo | Versione del kernel
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6,5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Versione sistema operativo | Versione del kernel
|:--|:--|
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Versione sistema operativo | Versione del kernel
|:--|:--|
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10
| Versione sistema operativo | Versione del kernel
|:--|:--|
| 10 SP4 | 2.6.16.60 |

## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo
Microsoft raccoglie automaticamente i dati di utilizzo e prestazioni tramite l'uso del servizio relativo all'elenco dei servizi da parte dell'utente. Microsoft usa questi dati per fornire e migliorare la qualità, la sicurezza e l'integrità del servizio relativo all'elenco dei servizi. I dati includono informazioni sulla configurazione del software, come il sistema operativo e la versione, e comprendono anche l'indirizzo IP, il nome DNS e il nome della workstation, al fine di fornire capacità di risoluzione dei problemi accurate ed efficienti. Non vengono raccolti nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta dei dati e sull'utilizzo, vedere l'[Informativa sulla privacy di Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [usare l'elenco dei servizi](operations-management-suite-service-map.md) una volta distribuito e configurato.

