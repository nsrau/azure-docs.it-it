---
title: Panoramica dell'agente di Log Analytics
description: Questo argomento contiene informazioni su come raccogliere i dati e monitorare i computer ospitati in Azure, in locale o in un altro ambiente cloud con Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: cbefe2e2b25db7ce16a7a1bde423f60fda412590
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773356"
---
# <a name="log-analytics-agent-overview"></a>Panoramica dell'agente di Log Analytics
L'agente di Azure Log Analytics è stato sviluppato per la gestione completa di macchine virtuali in qualsiasi cloud, di computer locali e di computer monitorati da [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/). Gli agenti Windows e Linux inviano i dati raccolti da origini diverse all'area di lavoro Log Analytics in Monitoraggio di Azure, nonché qualsiasi log o metrica univoci come definito in una soluzione di monitoraggio. L'agente di Log Analytics supporta anche informazioni dettagliate e altri servizi in Monitoraggio di Azure, ad esempio [Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-enable-overview.md), [Centro sicurezza di Azure](/azure/security-center/) e [Automazione di Azure](../../automation/automation-intro.md).

Questo articolo offre una panoramica dettagliata dell'agente, dei requisiti di sistema e di rete, nonché dei diversi metodi di distribuzione.

> [!NOTE]
> È anche possibile che l'agente di Log Analytics sia denominato Microsoft Monitoring Agent (MMA) o agente di OMS per Linux.

> [!NOTE]
> L'estensione Diagnostica di Azure è uno degli agenti disponibili per raccogliere i dati di monitoraggio dal sistema operativo guest delle risorse di calcolo. Vedere [Panoramica degli agenti di Monitoraggio di Azure](agents-overview.md) per una descrizione dei diversi agenti e indicazioni sulla selezione degli agenti appropriati a seconda delle esigenze.

## <a name="comparison-to-azure-diagnostics-extension"></a>Confronto con l'estensione Diagnostica di Azure
È anche possibile usare l'[estensione Diagnostica di Azure](diagnostics-extension-overview.md) in Monitoraggio di Azure per raccogliere i dati di monitoraggio dal sistema operativo guest di macchine virtuali di Azure. È possibile scegliere di usare uno dei due agenti oppure entrambi, a seconda delle esigenze. Vedere [Panoramica degli agenti di Monitoraggio di Azure](agents-overview.md) per un confronto dettagliato degli agenti di Monitoraggio di Azure. 

Le differenze principali da considerare sono le seguenti:

- L'estensione Diagnostica di Azure può essere usata solo con le macchine virtuali di Azure. L'agente di Log Analytics può essere usato con le macchine virtuali di Azure, in altri cloud e in locale.
- L'estensione Diagnostica di Azure invia i dati ad Archiviazione di Azure, alle [metriche di Monitoraggio di Azure](data-platform-metrics.md) (solo Windows) e a Hub eventi. L'agente di Log Analytics raccoglie i dati da inviare ai [log di Monitoraggio di Azure](data-platform-logs.md).
- L'agente di Log Analytics è necessario per [soluzioni](../monitor-reference.md#insights-and-core-solutions), [Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) e altri servizi, ad esempio [Centro sicurezza di Azure](/azure/security-center/).

## <a name="costs"></a>Costi
Non sono previsti costi per l'agente di Log Analytics, ma è possibile che vengano addebitati costi per i dati inseriti. Per informazioni dettagliate sui prezzi per i dati raccolti in un'area di lavoro Log Analytics, vedere [Gestire l'utilizzo e i costi con i log di Monitoraggio di Azure](manage-cost-storage.md).

## <a name="data-collected"></a>Dati raccolti
La tabella seguente elenca i tipi di dati configurabili che un'area di lavoro Log Analytics potrà raccogliere da tutti gli agenti connessi. Per un elenco di informazioni dettagliate, soluzioni e altre soluzioni che usano l'agente di Log Analytics per raccogliere altri tipi di dati, vedere [Che cosa viene monitorato da Monitoraggio di Azure?](../monitor-reference.md).

| origine dati | Descrizione |
| --- | --- |
| [Log eventi di Windows](data-sources-windows-events.md) | Informazioni inviate al sistema di registrazione eventi di Windows. |
| [Syslog](data-sources-syslog.md)                     | Informazioni inviate al sistema di registrazione eventi di Linux. |
| [Prestazioni](data-sources-performance-counters.md)  | Valori numerici che misurano le prestazioni di diversi aspetti del sistema operativo e dei carichi di lavoro. |
| [Log IIS](data-sources-iis-logs.md)                 | Informazioni sull'utilizzo per siti Web IIS in esecuzione nel sistema operativo guest. |
| [Log personalizzati](data-sources-custom-logs.md)           | Eventi raccolti da file di testo in computer Windows e Linux. |

## <a name="data-destinations"></a>Destinazioni dei dati
L'agente di Log Analytics invia i dati a un'area di lavoro Log Analytics in Monitoraggio di Azure. L'agente Windows può essere multihomed per inviare dati a più aree di lavoro e a gruppi di gestione di System Center Operations Manager. L'agente Linux può inviare solo a un'unica destinazione.

## <a name="other-services"></a>Altri servizi
L'agente per Linux e Windows non serve solo per la connessione a Monitoraggio di Azure, ma supporta anche Automazione di Azure per ospitare il ruolo di lavoro ibrido per runbook e altri servizi, ad esempio [Rilevamento modifiche](../../automation/change-tracking.md), [Gestione aggiornamenti](../../automation/automation-update-management.md) e [Centro sicurezza di Azure](../../security-center/security-center-intro.md). Per altre informazioni sul ruolo di lavoro ibrido per runbook, vedere [Ruolo di lavoro ibrido per runbook di Automazione di Azure](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="installation-and-configuration"></a>Installazione e configurazione

Quando si usano gli agenti di Log Analytics per raccogliere dati, è necessario comprendere i concetti seguenti per pianificare la distribuzione dell'agente:

* Per raccogliere i dati dagli agenti Windows, è possibile [configurare che ogni agente invii i report a una o più aree di lavoro](agent-windows.md), anche durante l'invio di report a un gruppo di gestione di System Center Operations Manager. L'agente Windows può inviare i report a un massimo di quattro aree di lavoro.
* L'agente Linux non supporta il multihoming e può inviare i report solo a un'unica area di lavoro.
* L'agente Windows supporta lo [standard FIPS 140](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation), mentre l'agente Linux non lo supporta.  

Se si usa System Center Operations Manager 2012 R2 o versione successiva:

* Ogni gruppo di gestione di Operations Manager può essere [connesso a una sola area di lavoro](om-agents.md).
* I computer Linux che inviano i report a un gruppo di gestione devono essere configurati perché i report vengano inviati direttamente a un'area di lavoro Log Analytics. Se i computer Linux inviano già i report direttamente a un'area di lavoro e si vuole monitorarli con Operations Manager, attenersi a questa procedura per [inviare i report a un gruppo di gestione di Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* È possibile installare l'agente di Log Analytics per Windows nel computer Windows e fare in modo che i report siano inviati sia a Operations Manager con l'integrazione di un'area di lavoro sia a un'area di lavoro diversa.


Sono disponibili diversi metodi per installare l'agente di Log Analytics e connettere il computer a Monitoraggio di Azure in base alle esigenze. Nella tabella seguente illustra ogni metodo per determinare quello più adatto alla propria organizzazione.

|Source (Sorgente) | Metodo | Descrizione|
|-------|-------------|-------------|
|Macchina virtuale di Azure| [Manualmente dal portale di Azure](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | Specificare le macchine virtuali da distribuire dall'area di lavoro Log Analytics. |
| | Estensione della macchina virtuale di Log Analytics per [Windows](../../virtual-machines/extensions/oms-windows.md) o [Linux](../../virtual-machines/extensions/oms-linux.md) tramite l'interfaccia della riga di comando di Azure oppure con un modello di Azure Resource Manager | L'estensione installa l'agente di Log Analytics nelle macchine virtuali di Azure e le registra in un'area di lavoro di Log Analytics esistente. |
| | [Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-enable-overview.md) | Quando si abilita il monitoraggio con Monitoraggio di Azure per le macchine virtuali, vengono installate l'estensione e l'agente di Log Analytics. |
| | [Provisioning automatico del Centro sicurezza di Azure](../../security-center/security-center-enable-data-collection.md) | Il Centro sicurezza di Azure può effettuare il provisioning dell'agente di Log Analytics in tutte le macchine virtuali di Azure supportate e in quelle nuove che vengono create se si abilita il monitoraggio della vulnerabilità e delle minacce per la sicurezza. Se abilitato, verrà eseguito il provisioning di tutte le macchine virtuali nuove o esistenti che non hanno un agente installato. |
| Computer Windows ibrido| [Installazione manuale](agent-windows.md) | Installare Microsoft Monitoring Agent dalla riga di comando. |
| | [Panoramica della piattaforma DSC di Automazione di Azure](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Automatizzare l'installazione con Automation DSC per Azure. |
| | [Modello di Resource Manager con Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Usare un modello di Azure Resource Manager se è stato distribuito Microsoft Azure Stack nel data center.| 
| Computer Linux ibrido| [Installazione manuale](../../azure-monitor/learn/quick-collect-linux-computer.md)|Installare l'agente per Linux chiamando uno script wrapper ospitato in GitHub. | 
| System Center Operations Manager|[Integrare Operations Manager con Log Analytics](../../azure-monitor/platform/om-agents.md) | Configurare l'integrazione tra Operations Manager e i log di Monitoraggio di Azure per inoltrare i dati raccolti dai computer Windows che inviano report a un gruppo di gestione.|  


## <a name="supported-windows-operating-systems"></a>Sistemi operativi Windows supportati

Le versioni seguenti del sistema operativo Windows sono ufficialmente supportate per l'agente Windows:

* Windows Server 2019
* Windows Server 2016, versione 1709 e 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (inclusa la versione multisessione) e Pro
* Windows 8 Enterprise e Pro 
* Windows 7 SP1

>[!NOTE]
>Sebbene l'agente di Log Analytics per Windows sia stato progettato per supportare scenari di monitoraggio del server, è possibile che il client Windows sia eseguito per supportare carichi di lavoro configurati e ottimizzati per il sistema operativo server. L'agente supporta il client Windows, ma le soluzioni di monitoraggio Microsoft non si concentrano sugli scenari di monitoraggio client, a meno che non venga esplicitamente specificato.

## <a name="supported-linux-operating-systems"></a>Sistemi operativi Linux supportati

Questa sezione fornisce informazioni dettagliate sulle distribuzioni Linux supportate.

A partire dalle versioni rilasciate dopo il mese di agosto 2018, al modello di supporto sono state apportate le modifiche seguenti:  

* Sono supportate solo le versioni server, non quelle client.  
* Maggiore supporto per una delle [distribuzioni Linux approvate in Azure](../../virtual-machines/linux/endorsed-distros.md). Si noti che potrebbe passare del tempo tra una nuova distribuzione e/o una nuova versione Linux approvata in Azure e supportata per l'agente di Log Analytics per Linux.
* Tutte le versioni minori sono supportate per ogni versione principale elencata.
* Non sono supportate le versioni che hanno superato la data di fine del supporto.  
* Non sono supportate le nuove versioni di AMI.  
* Per impostazione predefinita, sono supportate solo le versioni che eseguono SSL 1.x.

>[!NOTE]
>Se si usa una distribuzione o una versione attualmente non supportata e non allineata al modello di supporto, è consigliabile creare una copia tramite fork del repository, nella consapevolezza che il supporto tecnico Microsoft non fornisce assistenza per versioni dell'agente con fork.

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x64) e 7 (x64)  
* Oracle Linux 6 e 7 (x64) 
* Red Hat Enterprise Linux Server 6 (x64), 7 (x64), e 8 (x64)
* Debian GNU/Linux 8 e 9 (x64)
* Ubuntu 14.04 LTS (x86/x64), 16.04 LTS (x64) e 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) e 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 è supportato solo su piattaforme x86_x64 (64 bit), mentre le versioni di OpenSSL precedenti a 1.x non sono supportate su alcuna piattaforma.
>

### <a name="agent-prerequisites"></a>Prerequisiti per l'agente

La tabella seguente elenca i pacchetti necessari per le distribuzioni Linux supportate in cui verrà installato l'agente.

|Pacchetto necessario |Descrizione |Versione minima |
|-----------------|------------|----------------|
|Glibc |    Libreria GNU C | 2.5-12 
|Openssl    | Librerie OpenSSL | 1.0.x o 1.1.x |
|Curl | Client Web cURL | 7.15.5 |
|Python-ctypes | | 
|PAM | Moduli di autenticazione modulare | | 

>[!NOTE]
>Per raccogliere i messaggi SysLog, è necessario rsyslog o syslog-ng. Il daemon SysLog predefinito nella versione 5 di Red Hat Enterprise Linux, CentOS e nella versione Oracle Linux (sysklog) non è supportato per la raccolta di eventi SysLog. Per raccogliere i dati di SysLog da questa versione delle distribuzioni, è necessario installare e configurare il daemon rsyslog in modo da sostituire sysklog.

## <a name="tls-12-protocol"></a>Protocollo TLS 1.2

Per garantire la sicurezza dei dati in transito verso i log di Monitoraggio di Azure, è consigliabile configurare l'agente in modo da usare almeno il protocollo Transport Layer Security (TLS) 1.2. Le versioni precedenti di TLS/Secure Sockets Layer (SSL) sono state considerate vulnerabili. Nonostante siano ancora attualmente in uso per questioni di compatibilità con le versioni precedenti, **non sono consigliate**.  Per altre informazioni, vedere [Invio dei dati in modo sicuro tramite TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Requisito di supporto per la firma del codice SHA-2 per Windows
Il 17 agosto 2020 l'agente Windows inizierà a usare esclusivamente la firma SHA-2. Questa modifica interesserà i clienti che usano l'agente di Log Analytics in un sistema operativo legacy come parte di un servizio di Azure (Monitoraggio di Azure, Automazione di Azure, Gestione aggiornamenti di Azure, Rilevamento modifiche di Azure, Centro sicurezza di Azure, Azure Sentinel e Windows Defender ATP). La modifica non richiede intervento da parte dei clienti, a meno che l'agente non venga eseguito in una versione legacy del sistema operativo (Windows 7, Windows Server 2008 R2 e Windows Server 2008). I clienti che eseguono una versione legacy del sistema operativo devono intraprendere le azioni seguenti nei computer prima del 17 agosto 2020. In caso contrario gli agenti non invieranno più i dati alle aree di lavoro Log Analytics:

1. Installare il Service Pack più recente per il sistema operativo. Le versioni del Service Pack richieste sono le seguenti:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Installare gli aggiornamenti di Windows per la firma SHA-2 per il sistema operativo come descritto in [Requisito di supporto per la firma del codice di SHA-2 2019 per Windows e WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Eseguire l'aggiornamento alla versione più recente dell'agente Windows (versione 10.20.18029).
4. È consigliabile configurare l'agente per [usare il protocollo TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12). 


## <a name="network-requirements"></a>Requisiti di rete
L'agente per Linux e Windows comunica in uscita con il servizio Monitoraggio di Azure sulla porta TCP 443 e, se il computer si connette tramite un firewall o un server proxy per comunicare in Internet, vedere i requisiti seguenti per comprendere quale configurazione di rete è necessaria. Se i criteri di sicurezza IT non consentono ai computer della rete di connettersi a Internet, è possibile configurare un [gateway di Log Analytics](gateway.md), quindi configurare l'agente per connettersi tramite il gateway ai log di Monitoraggio di Azure. L'agente può quindi ricevere le informazioni di configurazione e inviare i dati raccolti a seconda delle regole di raccolta dati e delle soluzioni di monitoraggio abilitate nell'area di lavoro.

![Diagramma delle comunicazioni dell'agente di Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

La tabella seguente elenca le informazioni di configurazione del proxy e del firewall necessarie agli agenti Windows e Linux per comunicare con i log di Monitoraggio di Azure.

### <a name="firewall-requirements"></a>Requisiti del firewall

|Risorsa agente|Porte |Direction |Ignorare l'analisi HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |In uscita|Sì |  
|*.oms.opinsights.azure.com |Porta 443 |In uscita|Sì |  
|*.blob.core.windows.net |Porta 443 |In uscita|Sì |
|*.azure-automation.net |Porta 443 |In uscita|Sì |

Per informazioni sul firewall necessarie per Azure per enti pubblici, vedere [Gestione di Azure per enti pubblici](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Se si prevede di usare il ruolo di lavoro ibrido per runbook di Automazione di Azure per connettersi e registrarsi al servizio di automazione per usare i runbook o le soluzioni di gestione nell'ambiente in uso, è necessario avere accesso al numero di porta e agli URL descritti in [Configurare la rete per il ruolo di lavoro ibrido per runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

### <a name="proxy-configuration"></a>Configurazione proxy

L'agente Windows e Linux supporta la comunicazione con Monitoraggio di Azure tramite un server proxy o un gateway di Log Analytics usando il protocollo HTTPS.  Sono supportate sia l'autenticazione anonima che quella di base (nome utente/password).  Per l'agente di Windows connesso direttamente al servizio, la configurazione del proxy viene specificata durante l'installazione o [dopo la distribuzione](agent-manage.md#update-proxy-settings) dal Pannello di controllo o con PowerShell.  

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

Ad esempio: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Se si usano caratteri speciali nella password, ad esempio "\@", viene visualizzato un errore di connessione proxy perché il valore viene analizzato in modo non corretto.  Per aggirare il problema, codificare la password nell'URL usando uno strumento come [URLDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Passaggi successivi

* Esaminare le [origini dati](agent-data-sources.md) per informazioni sulle origini dati disponibili per raccogliere i dati dal sistema Windows o Linux. 
* Altre informazioni sulle [query di log](../log-query/log-query-overview.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni. 
* Altre informazioni sulle [soluzioni di monitoraggio](../insights/solutions.md) che aggiungono funzionalità a Monitoraggio di Azure e raccolgono anche dati nell'area di lavoro Log Analytics.

