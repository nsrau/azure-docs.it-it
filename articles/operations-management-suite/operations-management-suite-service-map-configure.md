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
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 3297fec53e97c33461b7fa5bfd9e813c30a3da9d
ms.lasthandoff: 04/17/2017


---
# <a name="configuring-service-map-solution-in-operations-management-suite-oms"></a>Configurare la soluzione di elenco dei servizi in Operations Management Suite (OMS)
L'elenco dei servizi individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue la mappatura della comunicazione fra i servizi. Consente di visualizzare i server nel modo in cui si pensa a essi, ovvero come sistemi interconnessi che forniscono servizi critici.  L'elenco dei servizi mostra le connessioni fra i server, i processi e le porte di tutte le architetture connesse via TCP senza il bisogno di alcuna configurazione a parte l'installazione di un agente.

Questo articolo descrive in dettaglio come configurare l'elenco dei servizi e il caricamento degli agenti.  Per informazioni sull'uso dell'elenco dei servizi, vedere [Using Service Map solution in Operations Management Suite (OMS)](operations-management-suite-service-map.md) (Usare la soluzione di elenco dei servizi in Operations Management Suite (OMS)).

## <a name="dependency-agent-downloads"></a>Download di Dependency Agent
| File | OS | Versione | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.4 | EF9BF7106BD6856384CDAF704FDE5872DD0A6D0C3BE26F62EAB17BA5D50702DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.4 | D685A2BAEEE30151EAD074F2BF5774F32446EAAB89B0FC04DC36D24A8A6397AC |


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
  
Se si usa l'agente diretto OMS, è necessario configurare l'agente OMS in modo che si connetta a OMS o al gateway OMS.  Il gateway OMS può essere scaricato da [https://www.microsoft.com/download/details.aspx?id=52666](https://www.microsoft.com/download/details.aspx?id=52666)


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

| Flag | Descrizione |
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
Se si verificano problemi di installazione o esecuzione di Elenco dei servizi, questa sezione consente di risolvere i problemi e proseguire con l'esecuzione.  Se si non riesce a risolvere il problema, contattare il supporto tecnico Microsoft.

### <a name="dependency-agent-installation-issues"></a>Problemi di installazione di Dependency Agent
#### <a name="installer-asks-for-a-reboot"></a>Il programma di installazione richiede il riavvio
Dependency Agent *in genere* non richiede un riavvio dopo l'installazione o la disinstallazione.  Tuttavia, in alcuni casi rari, Windows Server può richiedere di riavviare il computer per continuare con l'installazione.  Ciò si verifica quando una dipendenza, in genere i componenti ridistribuibili di Microsoft VC++, richiede un riavvio a causa di un file bloccato.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber"></a>Messaggio "Unable to install Dependency Agent: Visual Studio Runtime libraries failed to install (code = [code_number])." (Impossibile installare Dependency Agent: impossibile installare le librerie di runtime di Visual Studio (codice = [numero_di_codice]).)

Microsoft Dependency Agent si basa sulle librerie di runtime di Microsoft Visual Studio. Si è verificato un problema durante il tentativo di installazione delle librerie. I programmi di installazione delle librerie di runtime creano log nella cartella %LOCALAPPDATA%\temp. Il file sarà dd_vcredist_arch_aaaammggghhmmss.log, in cui arch sarà "x86" oppure "amd64" e aaaammggg sarà la data e l'ora (formato 24 ore) di creazione del log. Il log fornisce informazioni dettagliate sul problema che blocca l'installazione.

Potrebbe essere utile installare manualmente prima [la versione più recente delle librerie di runtime](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

Di seguito sono elencati alcuni numeri di codici e le risoluzioni consigliate.

| Codice | Descrizione | Risoluzione |
|:--|:--|:--|
| 0x17 | Il programma di installazione della libreria richiede un aggiornamento di Windows che non è stato installato. | Cercare nel log del programma di installazione della libreria più recente (vedere sopra).<br><br>Se il riferimento a "Windows8.1-KB2999226-x64.msu" è seguito da una riga "Error 0x80240017: Failed to execute MSU package." ("Errore 0x80240017: impossibile eseguire il pacchetto MSU."), significa che non si dispone dei prerequisiti necessari per installare KB2999226.  Seguire le istruzioni riportate nella sezione relativa ai prerequisiti in https://support.microsoft.com/kb/2999226.  Si noti che potrebbe essere necessario eseguire Windows Update e riavviare più volte per installare i prerequisiti necessari.<br><br>Eseguire nuovamente il programma di installazione di Microsoft Dependency Agent. |

### <a name="post-installation-issues"></a>Problemi successivi all'installazione
#### <a name="server-doesnt-show-in-service-map"></a>Il server non viene visualizzato nell'Elenco dei servizi
Se l'installazione di Dependency Agent è stata completata correttamente, ma nella soluzione Elenco dei servizi non viene visualizzato il server:
1. Dependency Agent è stato installato correttamente?  È possibile verificarlo controllando se il servizio è installato ed è in esecuzione.<br><br>
**Windows**: cercare il servizio denominato "Microsoft Dependency Agent"<br>
**Linux**: cercare il processo in esecuzione "microsoft-dependency-agent"

2. L'utente è nel [Piano tariffario gratuito di OMS/Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)?  Il piano gratuito consente di avere fino a cinque server univoci dell'Elenco dei servizi.  Tutti i server successivi non verranno visualizzati nell'Elenco dei servizi, anche se i primi cinque non inviano più dati.

3. Il server invia il log e i dati delle prestazioni a OMS?  Passare a Ricerca log ed eseguire la query seguente per il computer: 

        * Computer="<your computer name here>" | measure count() by Type
        
I risultati mostrano eventi diversi?  I dati sono aggiornati?  In questo caso, l'agente OMS funziona correttamente e comunica con il servizio OMS. In caso contrario, controllare l'agente OMS sul server: [Risoluzione dei problemi dell'agente OMS per Windows](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).  [Risoluzione dei problemi dell'agente OMS per Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md).

#### <a name="server-shows-in-service-map-but-has-no-processes"></a>Il server viene visualizzato nell'Elenco dei servizi, ma non dispone di alcun processo
Se il server viene visualizzato nell'Elenco dei servizi, ma non presenta processi o dati di connessione, vuol dire che Dependency Agent è installato ed è in esecuzione, ma non è stato caricato il driver del kernel.  Per scoprire il motivo per cui il driver non è stato caricato, controllare il file wrapper.log (Windows) o il file service.log (Linux).  Le ultime righe del file devono indicare il motivo (ad esempio il kernel non è supportato, un problema che si verifica in Linux se il kernel è stato aggiornato) per cui il kernel non è stato caricato.

Windows: C:\Programmi\Microsoft Dependency Agent\logs\wrapper.log

Linux: /var/opt/microsoft/dependency-agent/log/service.log

## <a name="data-collection"></a>Raccolta dei dati
È possibile aspettarsi che ogni agente trasmetta circa 25 MB al giorno, in base alla complessità delle dipendenze del sistema.  I dati sulle dipendenze dell'elenco dei servizi vengono inviati da ogni agente ogni 15 secondi.  

Dependency Agent usa in genere lo 0,1% della memoria di sistema e lo 0,1% della CPU di sistema.


## <a name="supported-operating-systems"></a>Sistemi operativi supportati
Le sezioni seguenti elencano i sistemi operativi supportati per l'agente di dipendenza.   Le architetture a 32 bit non sono supportate per nessun sistema operativo.

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
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411<br>2.6.18-412<br>2.6.18-416<br>2.6.18-417<br>2.6.18-419 |

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

