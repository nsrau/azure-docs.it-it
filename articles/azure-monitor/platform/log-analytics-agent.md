---
title: Panoramica dell'agente Log Analytics
description: Questo argomento contiene informazioni su come raccogliere i dati e monitorare i computer ospitati in Azure, in locale o in un altro ambiente cloud con Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: d52d8e6d0f6e3325b5c5cdc9a2e21654e6a2b621
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80520727"
---
# <a name="log-analytics-agent-overview"></a>Panoramica dell'agente Log Analytics
Azure Log Analytics Agent è stato sviluppato per una gestione completa tra macchine virtuali in qualsiasi cloud, computer locali e quelli monitorati da [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/). Gli agenti Windows e Linux inviano i dati raccolti da origini diverse all'area di lavoro Log Analytics in monitoraggio di Azure, nonché a qualsiasi metrica o log univoco definito in una soluzione di monitoraggio. L'agente di Log Analytics supporta anche informazioni dettagliate e altri servizi in monitoraggio di Azure, ad esempio [monitoraggio di Azure per le macchine virtuali](../insights/vminsights-enable-overview.md), il [Centro sicurezza di Azure](/azure/security-center/)e [automazione di Azure](../../automation/automation-intro.md).

Questo articolo offre una panoramica dettagliata dell'agente, dei requisiti di sistema e di rete, nonché dei diversi metodi di distribuzione.

> [!NOTE]
> È anche possibile vedere l'agente di Log Analytics denominato Microsoft Monitoring Agent (MMA) o agente Linux OMS.

> [!NOTE]
> Diagnostica di Azure estensione è uno degli agenti disponibili per raccogliere i dati di monitoraggio dal sistema operativo guest delle risorse di calcolo. Vedere [Panoramica degli agenti di monitoraggio di Azure](agents-overview.md) per una descrizione dei diversi agenti e informazioni aggiuntive sulla selezione degli agenti appropriati per le proprie esigenze.

## <a name="comparison-to-azure-diagnostics-extension"></a>Confronto con l'estensione diagnostica di Azure
L' [estensione diagnostica](diagnostics-extension-overview.md) di Azure in monitoraggio di Azure può essere usata anche per raccogliere i dati di monitoraggio dal sistema operativo guest di macchine virtuali di Azure. È possibile scegliere di usare uno dei due o entrambi, a seconda dei requisiti. Per un confronto dettagliato degli agenti di monitoraggio di Azure, vedere [Panoramica degli agenti di monitoraggio di Azure](agents-overview.md) . 

Le differenze principali da considerare sono:

- Diagnostica di Azure estensione può essere usata solo con le macchine virtuali di Azure. L'agente di Log Analytics può essere usato con macchine virtuali in Azure, in altri cloud e in locale.
- Diagnostica di Azure estensione invia dati ad archiviazione di Azure, [metriche di monitoraggio di Azure](data-platform-metrics.md) (solo Windows) e hub eventi. L'agente di Log Analytics raccoglie i dati nei [log di monitoraggio di Azure](data-platform-logs.md).
- L'agente di Log Analytics è necessario per le [soluzioni](../monitor-reference.md#insights-and-core-solutions), [monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md)e altri servizi, ad esempio il [Centro sicurezza di Azure](/azure/security-center/).

## <a name="costs"></a>Costi
Non è previsto alcun costo per Log Analytics Agent, ma è possibile che vengano addebitati costi per i dati inseriti. Per informazioni dettagliate sui prezzi per i dati raccolti in un'area di lavoro Log Analytics, vedere [gestire l'utilizzo e i costi con i log di monitoraggio di Azure](manage-cost-storage.md) .

## <a name="data-collected"></a>Dati raccolti
Nella tabella seguente sono elencati i tipi di dati che è possibile configurare un'area di lavoro di Log Analytics per la raccolta da tutti gli agenti connessi. Vedere [che cosa viene monitorato da monitoraggio di Azure?](../monitor-reference.md) per un elenco di informazioni dettagliate, soluzioni e altre soluzioni che usano l'agente di log Analytics per raccogliere altri tipi di dati.

| origine dati | Descrizione |
| --- | --- |
| [Registri eventi di Windows](data-sources-windows-events.md) | Informazioni inviate al sistema di registrazione eventi di Windows. |
| [syslog](data-sources-syslog.md)                     | Informazioni inviate al sistema di registrazione eventi di Linux. |
| [Prestazioni](data-sources-performance-counters.md)  | Valori numerici che misurano le prestazioni di diversi aspetti del sistema operativo e dei carichi di lavoro. |
| [Log di IIS](data-sources-iis-logs.md)                 | Informazioni sull'utilizzo per i siti Web IIS in esecuzione nel sistema operativo guest. |
| [Log personalizzati](data-sources-custom-logs.md)           | Eventi dai file di testo nei computer Windows e Linux. |

## <a name="data-destinations"></a>Destinazioni dei dati
L'agente Log Analytics invia i dati a un'area di lavoro Log Analytics in monitoraggio di Azure. L'agente di Windows può essere multihomed per inviare dati a più aree di lavoro e System Center Operations Manager gruppi di gestione. L'agente Linux può inviare solo a una singola destinazione.

## <a name="other-services"></a>Altri servizi
L'agente per Linux e Windows non è solo per la connessione a monitoraggio di Azure, ma supporta anche automazione di Azure per ospitare il ruolo di lavoro ibrido per Runbook e altri servizi, ad esempio [rilevamento modifiche](../../automation/change-tracking.md), [Gestione aggiornamenti](../../automation/automation-update-management.md)e il [Centro sicurezza di Azure](../../security-center/security-center-intro.md). Per altre informazioni sul ruolo di lavoro ibrido per runbook, vedere [Ruolo di lavoro ibrido per runbook di Automazione di Azure](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="installation-and-configuration"></a>Installazione e configurazione

Quando si usano gli agenti di Log Analytics per raccogliere dati, è necessario comprendere quanto segue per pianificare la distribuzione dell'agente:

* Per raccogliere i dati dagli agenti Windows, è possibile [configurare ogni agente in modo che faccia riferimento a una o più aree di lavoro](agent-windows.md), anche quando invia un report a un gruppo di gestione di System Center Operations Manager. L'agente Windows può segnalare fino a quattro aree di lavoro.
* L'agente Linux non supporta il multihosting e può segnalare solo a una singola area di lavoro.
* L'agente Windows supporta lo [standard FIPS 140](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation), mentre l'agente Linux non lo supporta.  

Se si usa System Center Operations Manager 2012 R2 o versione successiva:

* Ogni gruppo di gestione di Operations Manager può essere [connesso a una sola area di lavoro](om-agents.md).
* I computer Linux che inviano report a un gruppo di gestione devono essere configurati per segnalare direttamente a un'area di lavoro Log Analytics. Se i computer Linux hanno già segnalato direttamente a un'area di lavoro e si desidera monitorarli con Operations Manager, attenersi alla procedura seguente per creare [un report a un gruppo di gestione Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* È possibile installare l'agente di Log Analytics Windows nel computer Windows e fare in modo che il report sia Operations Manager integrato con un'area di lavoro e un'area di lavoro diversa.


Sono disponibili diversi metodi per installare l'agente di Log Analytics e connettere il computer a monitoraggio di Azure in base alle esigenze. Nella tabella seguente illustra ogni metodo per determinare quello più adatto alla propria organizzazione.

|Source (Sorgente) | Metodo | Descrizione|
|-------|-------------|-------------|
|Macchina virtuale Azure| [Manualmente dalla portale di Azure](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | Specificare le macchine virtuali da distribuire dall'area di lavoro Log Analytics. |
| | Log Analytics estensione VM per [Windows](../../virtual-machines/extensions/oms-windows.md) o [Linux](../../virtual-machines/extensions/oms-linux.md) con l'interfaccia della riga di comando di Azure o con un modello di Azure Resource Manager | L'estensione installa l'agente di Log Analytics nelle macchine virtuali di Azure e le registra in un'area di lavoro di Log Analytics esistente. |
| | [Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-enable-overview.md) | Quando si Abilita il monitoraggio con Monitoraggio di Azure per le macchine virtuali, vengono installate le estensioni Log Analytics e Agent. |
| | [Provisioning automatico del Centro sicurezza di Azure](../../security-center/security-center-enable-data-collection.md) | Il Centro sicurezza di Azure può effettuare il provisioning dell'agente di Log Analytics in tutte le VM di Azure supportate e in quelle nuove che vengono create se si Abilita il monitoraggio di vulnerabilità e minacce alla sicurezza. Se abilitata, verrà eseguito il provisioning di qualsiasi macchina virtuale nuova o esistente senza un agente installato. |
| Computer Windows ibrido| [Installazione manuale](agent-windows.md) | Installare Microsoft Monitoring Agent dalla riga di comando. |
| | [Automation DSC per Azure](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Automatizzare l'installazione con Automation DSC di Azure. |
| | [Modello di Resource Manager con Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Usare un modello di Azure Resource Manager se è stato distribuito Microsoft Azure Stack nel Data Center.| 
| Computer Linux ibrido| [Installazione manuale](../../azure-monitor/learn/quick-collect-linux-computer.md)|Installare l'agente per Linux chiamando uno script wrapper ospitato in GitHub. | 
| System Center Operations Manager|[Integrare Operations Manager con Log Analytics](../../azure-monitor/platform/om-agents.md) | Configurare l'integrazione tra Operations Manager e i log di monitoraggio di Azure per l'invio dei dati raccolti dai computer Windows che inviano report a un gruppo di gestione.|  


## <a name="supported-windows-operating-systems"></a>Sistemi operativi Windows supportati

Le versioni seguenti del sistema operativo Windows sono ufficialmente supportate per l'agente Windows:

* Windows Server 2019
* Windows Server 2008 SP2 (x64), 2008 R2, 2012, 2012 R2, 2016, versione 1709 e 1803
* Windows 7 SP1, Windows 8 Enterprise e Pro e Windows 10 Enterprise e Pro

>[!NOTE]
>Anche se l'agente di Log Analytics per Windows è stato progettato per supportare scenari di monitoraggio del server, è possibile eseguire client Windows per supportare i carichi di lavoro configurati e ottimizzati per il sistema operativo server. L'agente supporta il client Windows, ma le soluzioni di monitoraggio non si concentrano sugli scenari di monitoraggio client, a meno che non vengano esplicitamente indicate.

## <a name="supported-linux-operating-systems"></a>Sistemi operativi Linux supportati

Questa sezione fornisce informazioni dettagliate sulle distribuzioni Linux supportate.

A partire dalle versioni rilasciate dopo il mese di agosto 2018, al modello di supporto sono state apportate le modifiche seguenti:  

* Sono supportate solo le versioni server, non quelle client.  
* Concentrare il supporto su una delle [distribuzioni approvate per Linux di Azure](../../virtual-machines/linux/endorsed-distros.md). Si noti che potrebbe verificarsi un ritardo tra una nuova distribuzione o una nuova versione di Azure Linux approvata e supportata per l'agente Log Analytics Linux.
* Tutte le versioni minori sono supportate per ogni versione principale elencata.
* Non sono supportate le versioni che hanno superato la data di fine del supporto.  
* Non sono supportate le nuove versioni di AMI.  
* Per impostazione predefinita, sono supportate solo le versioni che eseguono SSL 1.x.

>[!NOTE]
>Se si usa una distribuzione o una versione attualmente non supportata e non allineata al modello di supporto, è consigliabile creare una copia tramite fork del repository, nella consapevolezza che il supporto tecnico Microsoft non fornisce assistenza per versioni dell'agente con fork.

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86/x64) e 7 (x64)  
* Oracle Linux 6 e 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) e 7 (x64)
* Debian GNU/Linux 8 e 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64), 16.04 LTS (x86/x64) e 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) e 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 è supportato solo su piattaforme x86_x64 (64 bit), mentre le versioni di OpenSSL precedenti a 1.x non sono supportate su alcuna piattaforma.
>

### <a name="agent-prerequisites"></a>Prerequisiti agente

La tabella seguente evidenzia i pacchetti necessari per le distribuzioni Linux supportate in cui verrà installato l'agente.

|Pacchetto necessario |Descrizione |Versione minima |
|-----------------|------------|----------------|
|Glibc |    Libreria GNU C | 2.5-12 
|Openssl    | Librerie OpenSSL | 1.0. x o 1.1. x |
|Curl | Client Web cURL | 7.15.5 |
|Python-ctypes | | 
|PAM | Moduli di autenticazione modulare | | 

>[!NOTE]
>Per raccogliere i messaggi SysLog, è necessario rsyslog o syslog-ng. Il daemon SysLog predefinito nella versione 5 di Red Hat Enterprise Linux, CentOS e nella versione Oracle Linux (sysklog) non è supportato per la raccolta di eventi SysLog. Per raccogliere i dati di SysLog da questa versione delle distribuzioni, è necessario installare e configurare il daemon rsyslog in modo da sostituire sysklog.

## <a name="tls-12-protocol"></a>Protocollo TLS 1.2

Per garantire la sicurezza dei dati in transito nei log di monitoraggio di Azure, si consiglia di configurare l'agente per l'uso di almeno Transport Layer Security (TLS) 1,2. Le versioni precedenti di TLS/Secure Sockets Layer (SSL) sono state considerate vulnerabili. Nonostante siano ancora attualmente in uso per questioni di compatibilità con le versioni precedenti, **non sono consigliate**.  Per altre informazioni, vedere [Invio dei dati in modo sicuro tramite TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Requisito del supporto per la firma del codice SHA-2 per Windows
L'agente Windows inizierà a usare esclusivamente la firma SHA-2 del 18 maggio 2020. Questa modifica influirà sui clienti che usano l'agente di Log Analytics in un sistema operativo legacy come parte di qualsiasi servizio di Azure (monitoraggio di Azure, automazione di Azure, Azure Gestione aggiornamenti, Azure Rilevamento modifiche, Centro sicurezza di Azure, Azure Sentinel, Windows Defender ATP). La modifica non richiede alcuna azione da parte dei clienti, a meno che l'agente non venga eseguito in una versione legacy del sistema operativo (Windows 7, Windows Server 2008 R2 e Windows Server 2008). I clienti che eseguono una versione legacy del sistema operativo devono eseguire le azioni seguenti sui computer prima del 18 maggio 2020 o i relativi agenti smetteranno di inviare dati alle aree di lavoro Log Analytics:

1. Installare il Service Pack più recente per il sistema operativo. Le versioni di Service Pack richieste sono:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Installare gli aggiornamenti di Windows per la firma SHA-2 per il sistema operativo come descritto nel [requisito del supporto per la firma del codice SHA-2 2019 per Windows e WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Eseguire l'aggiornamento alla versione più recente dell'agente di Windows (versione 10.20.18029).
4. È consigliabile configurare l'agente per l' [uso di TLS 1,2](agent-windows.md#configure-agent-to-use-tls-12). 


## <a name="network-requirements"></a>Requisiti di rete
L'agente per Linux e Windows comunica in uscita con il servizio monitoraggio di Azure sulla porta TCP 443 e, se il computer si connette tramite un firewall o un server proxy per comunicare via Internet, esaminare i requisiti indicati di seguito per comprendere la configurazione di rete necessaria. Se i criteri di sicurezza IT non consentono ai computer della rete di connettersi a Internet, è possibile configurare un [gateway log Analytics](gateway.md) e quindi configurare l'agente per la connessione tramite il gateway ai log di monitoraggio di Azure. L'agente può quindi ricevere informazioni di configurazione e inviare dati raccolti a seconda delle regole di raccolta dati e delle soluzioni di monitoraggio abilitate nell'area di lavoro.

![Diagramma delle comunicazioni dell'agente di Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

La tabella seguente elenca le informazioni di configurazione del proxy e del firewall necessarie per la comunicazione degli agenti Linux e Windows con i log di monitoraggio di Azure.

### <a name="firewall-requirements"></a>Requisiti del firewall

|Risorsa agente|Porte |Direction |Ignorare l'analisi HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |In ingresso e in uscita|Sì |  
|*.oms.opinsights.azure.com |Porta 443 |In ingresso e in uscita|Sì |  
|*.blob.core.windows.net |Porta 443 |In ingresso e in uscita|Sì |
|*.azure-automation.net |Porta 443 |In ingresso e in uscita|Sì |
|*.azure.com |Porta 443|In ingresso e in uscita|Sì |

Per informazioni sul firewall richieste per Azure per enti pubblici, vedere [gestione di Azure per enti pubblici](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Se si prevede di usare il ruolo di lavoro ibrido per Runbook di automazione di Azure per connettersi e registrarsi al servizio di automazione per usare manuali operativi o soluzioni di gestione nell'ambiente, deve avere accesso al numero di porta e agli URL descritti in [configurare la rete per il ruolo di lavoro ibrido per Runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

### <a name="proxy-configuration"></a>Configurazione proxy

L'agente Windows e Linux supporta la comunicazione tramite un server proxy o Log Analytics gateway a monitoraggio di Azure tramite il protocollo HTTPS.  Sono supportate sia l'autenticazione anonima che quella di base (nome utente/password).  Per l'agente di Windows connesso direttamente al servizio, la configurazione del proxy viene specificata durante l'installazione o [dopo la distribuzione](agent-manage.md#update-proxy-settings) dal Pannello di controllo o con PowerShell.  

Per l'agente di Linux, è possibile specificare il server proxy durante l'installazione o [dopo l'installazione](agent-manage.md#update-proxy-settings) modificando il file di configurazione proxy.conf.  Il valore di configurazione del proxy dell'agente Linux ha la sintassi seguente:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Se il server proxy non richiede l'autenticazione, l'agente Linux richiede tuttavia di immettere uno pseudoutente o una pseudopassword. Può trattarsi di qualsiasi nome utente o password.

|Proprietà| Descrizione |
|--------|-------------|
|Protocollo | https |
|utente | Nome utente facoltativo per l'autenticazione proxy |
|password | Password facoltativa per l'autenticazione proxy |
|proxyhost | Indirizzo o FQDN del server proxy/gateway Log Analytics |
|port | Numero di porta facoltativo del server proxy/gateway Log Analytics |

ad esempio `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Se si usano caratteri speciali, ad esempio\@"" nella password, viene visualizzato un errore di connessione del proxy perché il valore viene analizzato in modo errato.  Per aggirare il problema, codificare la password nell'URL usando uno strumento come [URLDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Passaggi successivi

* Esaminare le [origini dati](agent-data-sources.md) per informazioni sulle origini dati disponibili per raccogliere i dati dal sistema Windows o Linux. 
* Altre informazioni sulle [query di log](../log-query/log-query-overview.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni. 
* Altre informazioni sulle [soluzioni di monitoraggio](../insights/solutions.md) che aggiungono funzionalità a Monitoraggio di Azure e raccolgono anche dati nell'area di lavoro Log Analytics.

