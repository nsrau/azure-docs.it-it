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
ms.date: 02/05/2019
ms.author: magoedte
ms.openlocfilehash: 5af403b2d75a552719088daa17259f0771b77a94
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765680"
---
# <a name="collect-log-data-with-the-azure-log-analytics-agent"></a>Raccogliere dati di log con l'agente di Azure Log Analytics

L'agente di Azure Log Analytics, precedentemente noto come agente MMA (Microsoft Monitoring Agent) o Linux OMS, è stato sviluppato per la gestione completa di computer locali, computer monitorati da [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/) e macchine virtuali in qualsiasi cloud. Gli agenti per Windows e Linux si collegano a un'area di lavoro di Log Analytics per raccogliere dati da origini diverse, nonché qualsiasi log o metrica univoci come definito in una soluzione di monitoraggio. 

Questo articolo offre una panoramica dettagliata dell'agente, dei requisiti di sistema e di rete, nonché dei diversi metodi di distribuzione.   

## <a name="overview"></a>Panoramica

![Diagramma delle comunicazioni dell'agente di Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

Prima di analizzare i dati raccolti ed eseguire operazioni su di essi, è necessario installare e connettere gli agenti per tutti i computer che devono inviare i dati al servizio Log Analytics. È possibile installare gli agenti nelle macchine virtuali di Azure usando l'estensione macchina virtuale di Azure Log Analytics per Windows e Linux e per i computer in un ambiente ibrido usando il programma di installazione, la riga di comando o Desired State Configuration (DSC) in Automazione di Azure. 

L'agente per Linux e Windows comunica in uscita con il servizio Log Analytics sulla porta TCP 443 e, se il computer si connette tramite un firewall o a un server proxy per comunicare in Internet, vedere i requisiti seguenti per comprendere quale configurazione di rete è necessaria. Se i criteri di sicurezza IT non consentono ai computer della rete di connettersi a Internet, è possibile configurare un [gateway Log Analytics](gateway.md) e quindi configurare l'agente per connettersi tramite il gateway a Log Analytics. L'agente può quindi ricevere le informazioni di configurazione e inviare i dati raccolti a seconda delle regole di raccolta dati e delle soluzioni di monitoraggio abilitate. 

Se si esegue il monitoraggio del computer con System Center Operations Manager 2012 R2 o versione successiva, è possibile usare una configurazione multihomed con il servizio Log Analytics per raccogliere i dati e inoltrarli al servizio, mantenendo il monitoraggio di [Operations Manager](../../azure-monitor/platform/om-agents.md). I computer Linux monitorati da un gruppo di gestione di Operations Manager integrato in Log Analytics non ricevono la configurazione per le origini dati e inoltrano i dati raccolti tramite il gruppo di gestione. L'agente Windows può inviare report a un massimo di quattro aree di lavoro di Log Analytics, mentre l'agente Linux supporta solo l'invio di report a una singola area di lavoro.  

L'agente per Linux e Windows non serve solo per la connessione a Log Analytics, ma supporta anche Automazione di Azure per ospitare il ruolo di lavoro ibrido per runbook e altri servizi, come [Rilevamento modifiche](../../automation/automation-change-tracking.md) e [Gestione aggiornamenti](../../automation/automation-update-management.md). Per altre informazioni sul ruolo di lavoro ibrido per runbook, vedere [Ruolo di lavoro ibrido per runbook di Automazione di Azure](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Sistemi operativi Windows supportati
Le versioni seguenti del sistema operativo Windows sono ufficialmente supportate per l'agente Windows:

* Windows Server 2008 R2 o versione successiva
* Windows 7 SP1 e versioni successive.

## <a name="supported-linux-operating-systems"></a>Sistemi operativi Linux supportati
Questa sezione fornisce informazioni dettagliate sulle distribuzioni Linux supportate.    

A partire dalle versioni rilasciate dopo il mese di agosto 2018, al modello di supporto sono state apportate le modifiche seguenti:  

* Sono supportate solo le versioni server, non quelle client.  
* Sono sempre supportate le nuove versioni delle [distribuzioni di Linux approvate per Azure](../../virtual-machines/linux/endorsed-distros.md).  
* Tutte le versioni minori sono supportate per ogni versione principale elencata.
* Non sono supportate le versioni che hanno superato la data di fine del supporto.  
* Non sono supportate le nuove versioni di AMI.  
* Per impostazione predefinita, sono supportate solo le versioni che eseguono SSL 1.x.

Se si usa una distribuzione o una versione attualmente non supportata e non allineata al modello di supporto, è consigliabile creare una copia tramite fork del repository, nella consapevolezza che il supporto tecnico Microsoft non fornisce assistenza per versioni dell'agente con fork.

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86/x64) e 7 (x64)  
* Oracle Linux 6 e 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) e 7 (x64)
* Debian GNU/Linux 8 e 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64), 16.04 LTS (x86/x64) e 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64)

>[!NOTE]
>OpenSSL 1.1.0 è supportato solo su piattaforme x86_x64 (64 bit), mentre le versioni di OpenSSL precedenti a 1.x non sono supportate su alcuna piattaforma.
>

## <a name="tls-12-protocol"></a>Protocollo TLS 1.2
Per garantire la sicurezza dei dati in transito verso Log Analytics, è consigliabile configurare l'agente in modo da usare almeno il protocollo Transport Layer Security (TLS) 1.2. Le versioni precedenti di TLS/Secure Sockets Layer (SSL) sono state considerate vulnerabili. Nonostante siano ancora attualmente in uso per questioni di compatibilità con le versioni precedenti, **non sono consigliate**.  Per altre informazioni, vedere [Invio dei dati in modo sicuro tramite TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Requisiti del firewall di rete
Di seguito viene riportato un elenco delle informazioni di configurazione del proxy e del firewall necessarie all'agente Windows e Linux per comunicare con Log Analytics.  

|Risorsa agente|Porte |Direzione |Ignorare l'analisi HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |In ingresso e in uscita|Sì |  
|*.oms.opinsights.azure.com |Porta 443 |In ingresso e in uscita|Sì |  
|*.blob.core.windows.net |Porta 443 |In ingresso e in uscita|Sì |  
|*.azure-automation.net |Porta 443 |In ingresso e in uscita|Sì |  


Se si prevede di usare il ruolo di lavoro ibrido per runbook di Automazione di Azure per connettersi e registrarsi al servizio di automazione per usare i runbook nell'ambiente in uso, è necessario avere accesso al numero di porta e agli URL descritti in [Configurare la rete per il ruolo di lavoro ibrido per runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

L'agente di Windows e di Linux supporta la comunicazione tramite un server proxy o un gateway Log Analytics con il servizio Log Analytics usando il protocollo HTTPS.  Sono supportate sia l'autenticazione anonima che quella di base (nome utente/password).  Per l'agente di Windows connesso direttamente al servizio, la configurazione del proxy viene specificata durante l'installazione o [dopo la distribuzione](agent-manage.md#update-proxy-settings) dal Pannello di controllo o con PowerShell.  

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
La connessione dei computer nella sottoscrizione di Azure o nell'ambiente ibrido direttamente con Azure Log Analytics può essere eseguita usando metodi diversi a seconda dei requisiti. Nella tabella seguente illustra ogni metodo per determinare quello più adatto alla propria organizzazione.

|Source (Sorgente) | Metodo | DESCRIZIONE|
|-------|-------------|-------------|
|Macchina virtuale di Azure| - Estensione della macchina virtuale di Log Analytics per [Windows](../../virtual-machines/extensions/oms-windows.md) o [Linux](../../virtual-machines/extensions/oms-linux.md) tramite l'interfaccia della riga di comando di Azure oppure con un modello di Azure Resource Manager<br>- [Manualmente dal portale di Azure](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json). | L'estensione installa l'agente di Log Analytics nelle macchine virtuali di Azure e le registra in un'area di lavoro di Log Analytics esistente.|
| Computer Windows ibrido|- [Installazione manuale](agent-windows.md)<br>- [Automation DSC per Azure](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Modello di Resource Manager con Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Installare Microsoft Monitoring Agent dalla riga di comando o usando un metodo automatizzato, ad esempio Automation DSC per Azure, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications) oppure con un modello di Azure Resource Manager se nel data center è stato distribuito Microsoft Azure Stack.| 
| Computer Linux ibrido| [Installazione manuale](../../azure-monitor/learn/quick-collect-linux-computer.md)|Installare l'agente per Linux chiamando uno script wrapper ospitato in GitHub. | 
| System Center Operations Manager|[Integrare Operations Manager con Log Analytics](../../azure-monitor/platform/om-agents.md) | Configurare l'integrazione tra Operations Manager e Log Analytics per inoltrare i dati raccolti dai computer Linux e Windows che inviano report a un gruppo di gestione.|  

## <a name="next-steps"></a>Passaggi successivi

* Esaminare le [origini dati](../../azure-monitor/platform/agent-data-sources.md) per informazioni sulle origini dati disponibili per raccogliere i dati dal sistema Windows o Linux. 

* Altre informazioni sulle [query di log](../../azure-monitor/log-query/log-query-overview.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni. 

* Altre informazioni sulle [soluzioni di monitoraggio](../../azure-monitor/insights/solutions.md) che aggiungono funzionalità a Monitoraggio di Azure e raccolgono anche dati nell'area di lavoro di Log Analytics.
