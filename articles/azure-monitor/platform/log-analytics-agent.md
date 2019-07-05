---
title: Raccogliere dati di log con l'agente di Azure Log Analytics | Microsoft Docs
description: Questo argomento contiene informazioni su come raccogliere i dati e monitorare i computer ospitati in Azure, in locale o in un altro ambiente cloud con Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: magoedte
ms.openlocfilehash: 583845b2ea63efd42f382c9c150de650f34bafed
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514116"
---
# <a name="collect-log-data-with-the-log-analytics-agent"></a>Raccogliere dati di log con l'agente di Log Analitica

L'agente di Azure Log Analytics, precedentemente noto come agente MMA (Microsoft Monitoring Agent) o Linux OMS, è stato sviluppato per la gestione completa di computer locali, computer monitorati da [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/) e macchine virtuali in qualsiasi cloud. Gli agenti Windows e Linux collegare a un monitoraggio di Azure e archiviano i dati di log raccolti da origini diverse nell'area di lavoro di Log Analitica, nonché qualsiasi log univoco o metriche come definito in una soluzione di monitoraggio. 

Questo articolo offre una panoramica dettagliata dell'agente, dei requisiti di sistema e di rete, nonché dei diversi metodi di distribuzione.   

## <a name="overview"></a>Panoramica

![Diagramma delle comunicazioni dell'agente di Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

Prima di analizzare e agisce sui dati raccolti, è necessario innanzitutto installare e connettere gli agenti per tutti i computer che si desidera inviare dati al servizio di monitoraggio di Azure. È possibile installare gli agenti nelle macchine virtuali di Azure usando l'estensione macchina virtuale di Azure Log Analytics per Windows e Linux e per i computer in un ambiente ibrido usando il programma di installazione, la riga di comando o Desired State Configuration (DSC) in Automazione di Azure. 

L'agente per Linux e Windows comunica in uscita al servizio di monitoraggio di Azure sulla porta TCP 443 e se il computer si connette tramite un firewall o server proxy per comunicare in Internet, rivedere i requisiti seguenti per comprendere la configurazione di rete richiesto. Se i criteri di sicurezza IT non consentono ai computer nella rete per la connessione a Internet, è possibile configurare un [gateway Log Analitica](gateway.md) e quindi configurare l'agente possa connettersi tramite il gateway per i log di monitoraggio di Azure. L'agente può quindi ricevere le informazioni di configurazione e inviare i dati raccolti a seconda delle regole di raccolta e monitoraggio delle soluzioni è abilitata nell'area di lavoro. 

Se si sta monitorando un computer con System Center Operations Manager 2012 R2 o versione successiva, possono essere multihomed con il servizio di monitoraggio di Azure per raccogliere dati e inoltrarli al servizio, mantenendo il monitoraggio [Operations Manager](../../azure-monitor/platform/om-agents.md). Con i computer Linux, l'agente non include un componente del servizio integrità quando esegue l'agente di Windows e le informazioni vengono raccolte ed elaborato da un server di gestione per suo conto. Poiché i computer Linux monitorati in modo diverso con Operations Manager, essi non ricevono la configurazione o raccogliere i dati direttamente e inoltrare tramite il gruppo di gestione, ad esempio, un sistema Windows gestiti tramite agente. Di conseguenza, questo scenario non è supportato con i computer Linux a Operations Manager reporting ed è necessario configurare il computer Linux [report a un gruppo di gestione di Operations Manager](../platform/agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) e un'area di lavoro di Log Analitica in due procedura.

L'agente Windows può inviare report a un massimo di quattro aree di lavoro di Log Analytics, mentre l'agente Linux supporta solo l'invio di report a una singola area di lavoro.  

L'agente per Linux e Windows non serve solo per la connessione a monitoraggio di Azure, ma supporta anche l'automazione di Azure per ospitare il ruolo di lavoro ibrido per Runbook e altri servizi, ad esempio [Change Tracking](../../automation/change-tracking.md), [gestionedegliaggiornamenti](../../automation/automation-update-management.md), e [Centro sicurezza di Azure](../../security-center/security-center-intro.md). Per altre informazioni sul ruolo di lavoro ibrido per runbook, vedere [Ruolo di lavoro ibrido per runbook di Automazione di Azure](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Sistemi operativi Windows supportati
Le versioni seguenti del sistema operativo Windows sono ufficialmente supportate per l'agente Windows:

* Windows Server 2019
* Windows Server 2008 R2, 2012, 2012 R2, 2016, versione 1709 e 1803
* Windows 7 SP1 e versioni successive

## <a name="supported-linux-operating-systems"></a>Sistemi operativi Linux supportati
Questa sezione fornisce informazioni dettagliate sulle distribuzioni Linux supportate.    

A partire dalle versioni rilasciate dopo il mese di agosto 2018, al modello di supporto sono state apportate le modifiche seguenti:  

* Sono supportate solo le versioni server, non quelle client.  
* Sono sempre supportate le nuove versioni delle [distribuzioni di Linux approvate per Azure](../../virtual-machines/linux/endorsed-distros.md).  
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

Nella tabella seguente evidenzia i pacchetti necessari per distribuzioni Linux supportate che l'agente verrà installato in.

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
Per garantire la sicurezza dei dati in transito per i log di monitoraggio di Azure, si consiglia di configurare l'agente a utilizzare almeno Transport Layer Security (TLS) 1.2. Le versioni precedenti di TLS/Secure Sockets Layer (SSL) sono state considerate vulnerabili. Nonostante siano ancora attualmente in uso per questioni di compatibilità con le versioni precedenti, **non sono consigliate**.  Per altre informazioni, vedere [Invio dei dati in modo sicuro tramite TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Requisiti del firewall di rete
Le informazioni sotto l'elenco delle informazioni di configurazione del proxy e firewall necessarie all'agente Linux e Windows comunicare con i log di monitoraggio di Azure.  

|Risorsa agente|Porte |Direzione |Ignorare l'analisi HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |In uscita|Sì |  
|*.oms.opinsights.azure.com |Porta 443 |In uscita|Sì |  
|*.blob.core.windows.net |Porta 443 |In uscita|Sì |  
|*.azure-automation.net |Porta 443 |In uscita|Sì |  

Per informazioni sul firewall necessarie per Azure per enti pubblici, vedere [gestione di Azure per enti pubblici](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Se si prevede di usare il ruolo di lavoro ibrido per runbook di Automazione di Azure per connettersi e registrarsi al servizio di automazione per usare i runbook nell'ambiente in uso, è necessario avere accesso al numero di porta e agli URL descritti in [Configurare la rete per il ruolo di lavoro ibrido per runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

L'agente Windows e Linux supporta la comunicazione tramite un server proxy o gateway Log Analitica per monitoraggio di Azure usando il protocollo HTTPS.  Sono supportate sia l'autenticazione anonima che quella di base (nome utente/password).  Per l'agente di Windows connesso direttamente al servizio, la configurazione del proxy viene specificata durante l'installazione o [dopo la distribuzione](agent-manage.md#update-proxy-settings) dal Pannello di controllo o con PowerShell.  

Per l'agente di Linux, è possibile specificare il server proxy durante l'installazione o [dopo l'installazione](agent-manage.md#update-proxy-settings) modificando il file di configurazione proxy.conf.  Il valore di configurazione del proxy dell'agente Linux ha la sintassi seguente:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Se il server proxy non richiede l'autenticazione, l'agente Linux richiede tuttavia di immettere uno pseudoutente o una pseudopassword. Può trattarsi di qualsiasi nome utente o password.

|Proprietà| DESCRIZIONE |
|--------|-------------|
|Protocollo | https |
|user | Nome utente facoltativo per l'autenticazione proxy |
|password | Password facoltativa per l'autenticazione proxy |
|proxyhost | Indirizzo o FQDN del server proxy/gateway Log Analytics |
|port | Numero di porta facoltativo del server proxy/gateway Log Analytics |

Ad esempio: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Se si usano caratteri speciali nella password, ad esempio "\@", viene visualizzato un errore di connessione proxy perché il valore viene analizzato in modo non corretto.  Per aggirare il problema, codificare la password nell'URL usando uno strumento come [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Installare e configurare l'agente 
La connessione dei computer nel sottoscrizione di Azure o nell'ambiente ibrido direttamente con i log di monitoraggio di Azure può essere eseguita usando metodi diversi a seconda dei requisiti. Nella tabella seguente illustra ogni metodo per determinare quello più adatto alla propria organizzazione.

|Source (Sorgente) | Metodo | DESCRIZIONE|
|-------|-------------|-------------|
|Macchina virtuale di Azure| - Estensione della macchina virtuale di Log Analytics per [Windows](../../virtual-machines/extensions/oms-windows.md) o [Linux](../../virtual-machines/extensions/oms-linux.md) tramite l'interfaccia della riga di comando di Azure oppure con un modello di Azure Resource Manager<br>- [Manualmente dal portale di Azure](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json). | L'estensione installa l'agente di Log Analytics nelle macchine virtuali di Azure e le registra in un'area di lavoro di Log Analytics esistente.|
| Computer Windows ibrido|- [Installazione manuale](agent-windows.md)<br>- [Automation DSC per Azure](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Modello di Resource Manager con Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Installare Microsoft Monitoring Agent dalla riga di comando o usando un metodo automatizzato, ad esempio Automation DSC per Azure, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications) oppure con un modello di Azure Resource Manager se nel data center è stato distribuito Microsoft Azure Stack.| 
| Computer Linux ibrido| [Installazione manuale](../../azure-monitor/learn/quick-collect-linux-computer.md)|Installare l'agente per Linux chiamando uno script wrapper ospitato in GitHub. | 
| System Center Operations Manager|[Integrare Operations Manager con Log Analytics](../../azure-monitor/platform/om-agents.md) | Configurare l'integrazione tra i log di Operations Manager e monitoraggio di Azure per inoltrare i dati raccolti dai computer Windows report in un gruppo di gestione.|  

## <a name="next-steps"></a>Passaggi successivi

* Esaminare le [origini dati](../../azure-monitor/platform/agent-data-sources.md) per informazioni sulle origini dati disponibili per raccogliere i dati dal sistema Windows o Linux. 

* Altre informazioni sulle [query di log](../../azure-monitor/log-query/log-query-overview.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni. 

* Altre informazioni sulle [soluzioni di monitoraggio](../../azure-monitor/insights/solutions.md) che aggiungono funzionalità a Monitoraggio di Azure e raccolgono anche dati nell'area di lavoro Log Analytics.
