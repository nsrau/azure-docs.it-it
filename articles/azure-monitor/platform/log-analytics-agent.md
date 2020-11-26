---
title: Panoramica dell'agente di Log Analytics
description: Questo argomento contiene informazioni su come raccogliere i dati e monitorare i computer ospitati in Azure, in locale o in un altro ambiente cloud con Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 8563f734db8524d6e90171bb2272723f14533055
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185930"
---
# <a name="log-analytics-agent-overview"></a>Panoramica dell'agente di Log Analytics
L'agente di Log Analytics di Azure raccoglie i dati di telemetria da macchine virtuali Windows e Linux in qualsiasi cloud, computer locali e quelli monitorati da [System Center Operations Manager](/system-center/scom/) e invia i dati raccolti all'area di lavoro log Analytics in monitoraggio di Azure. L'agente di Log Analytics supporta anche informazioni dettagliate e altri servizi in Monitoraggio di Azure, ad esempio [Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-enable-overview.md), [Centro sicurezza di Azure](../../security-center/index.yml) e [Automazione di Azure](../../automation/automation-intro.md). Questo articolo fornisce una panoramica dettagliata dell'agente, dei requisiti di sistema e di rete e dei metodi di distribuzione.

> [!NOTE]
> È anche possibile che l'agente di Log Analytics sia denominato Microsoft Monitoring Agent (MMA) o agente di OMS per Linux.

## <a name="comparison-to-azure-diagnostics-extension"></a>Confronto con l'estensione Diagnostica di Azure
È anche possibile usare l'[estensione Diagnostica di Azure](diagnostics-extension-overview.md) in Monitoraggio di Azure per raccogliere i dati di monitoraggio dal sistema operativo guest di macchine virtuali di Azure. È possibile scegliere di usare uno dei due agenti oppure entrambi, a seconda delle esigenze. Vedere [Panoramica degli agenti di Monitoraggio di Azure](agents-overview.md) per un confronto dettagliato degli agenti di Monitoraggio di Azure. 

Le differenze principali da considerare sono le seguenti:

- L'estensione Diagnostica di Azure può essere usata solo con le macchine virtuali di Azure. L'agente di Log Analytics può essere usato con le macchine virtuali di Azure, in altri cloud e in locale.
- L'estensione Diagnostica di Azure invia i dati ad Archiviazione di Azure, alle [metriche di Monitoraggio di Azure](data-platform-metrics.md) (solo Windows) e a Hub eventi. L'agente Log Analytics invia dati ai [log di monitoraggio di Azure](data-platform-logs.md).
- L'agente di Log Analytics è necessario per [soluzioni](../monitor-reference.md#insights-and-core-solutions), [Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) e altri servizi, ad esempio [Centro sicurezza di Azure](../../security-center/index.yml).

## <a name="costs"></a>Costi
Non sono previsti costi per l'agente di Log Analytics, ma è possibile che vengano addebitati costi per i dati inseriti. Per informazioni dettagliate sui prezzi per i dati raccolti in un'area di lavoro Log Analytics, vedere [Gestire l'utilizzo e i costi con i log di Monitoraggio di Azure](manage-cost-storage.md).

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

 Per un elenco delle versioni del sistema operativo Windows e Linux supportate dall'agente di Log Analytics, vedere [sistemi operativi supportati](agents-overview.md#supported-operating-systems) . 


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
L'agente di Log Analytics invia i dati a un'area di lavoro Log Analytics in Monitoraggio di Azure. L'agente Windows può essere multihomed per inviare dati a più aree di lavoro e a gruppi di gestione di System Center Operations Manager. L'agente Linux può inviare solo a una singola destinazione, ovvero un'area di lavoro o un gruppo di gestione.

## <a name="other-services"></a>Altri servizi
L'agente per Linux e Windows non è solo per la connessione a monitoraggio di Azure. Altri servizi come il Centro sicurezza di Azure e Azure Sentinel si basano sull'agente e sulla relativa area di lavoro Log Analytics connessa. L'agente supporta anche automazione di Azure per ospitare il ruolo di lavoro ibrido per Runbook e altri servizi, ad esempio [rilevamento modifiche](../../automation/change-tracking/overview.md), [Gestione aggiornamenti](../../automation/update-management/overview.md)e il [Centro sicurezza di Azure](../../security-center/security-center-introduction.md). Per altre informazioni sul ruolo di lavoro ibrido per runbook, vedere [Ruolo di lavoro ibrido per runbook di Automazione di Azure](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="workspace-and-management-group-limitations"></a>Limitazioni dell'area di lavoro e del gruppo di gestione

Per informazioni dettagliate sulla connessione di un agente a un gruppo di gestione Operations Manager, vedere [configurare l'agente per la segnalazione a un gruppo di gestione Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) .

* Gli agenti Windows possono connettersi a un massimo di quattro aree di lavoro, anche se sono connesse a un gruppo di gestione di System Center Operations Manager.
* L'agente Linux non supporta il multihoming e può connettersi solo a una singola area di lavoro o a un gruppo di gestione.


## <a name="security-limitations"></a>Limitazioni di sicurezza

* Gli agenti Windows e Linux supportano lo [standard FIPS 140](/windows/security/threat-protection/fips-140-validation), ma [potrebbero non essere supportati altri tipi di protezione avanzata](agent-linux.md#supported-linux-hardening).


## <a name="installation-options"></a>Opzione di installazione

Sono disponibili diversi metodi per installare l'agente di Log Analytics e connettere il computer a Monitoraggio di Azure in base alle esigenze. Nelle sezioni seguenti sono elencati i possibili metodi per diversi tipi di macchine virtuali.
> [!NOTE]
> Non è supportata la clonazione di un computer con l'agente di Log Analytics già configurato. Se l'agente è già stato associato a un'area di lavoro, questo non funzionerà per "immagini dorate".

### <a name="azure-virtual-machine"></a>Macchina virtuale di Azure

- [Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-enable-overview.md) offre più metodi che abilitano gli agenti su larga scala. Ciò include l'installazione dell'agente di Log Analytics e dell'agente di dipendenza. 
- [Il Centro sicurezza di Azure può effettuare il provisioning dell'agente di log Analytics](../../security-center/security-center-enable-data-collection.md) in tutte le VM di Azure supportate e in quelle nuove che vengono create se si Abilita il monitoraggio di vulnerabilità e minacce alla sicurezza.
- Log Analytics estensione della macchina virtuale per [Windows](../../virtual-machines/extensions/oms-windows.md) o [Linux](../../virtual-machines/extensions/oms-linux.md) può essere installata con l'portale di Azure, l'interfaccia della riga di comando di Azure, Azure PowerShell o un modello di Azure Resource Manager.
- Installare manualmente per le singole macchine virtuali [di Azure dalla portale di Azure](../learn/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json).


### <a name="windows-virtual-machine-on-premises-or-in-another-cloud"></a>Macchina virtuale Windows locale o in un altro cloud 

- [Installare manualmente](agent-windows.md) l'agente dalla riga di comando.
- Automatizzare l'installazione con [Automation DSC di Azure](agent-windows.md#install-agent-using-dsc-in-azure-automation).
- Usare un [modello di gestione risorse con Azure stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win)

### <a name="linux-virtual-machine-on-premises-or-in-another-cloud"></a>Macchina virtuale Linux in locale o in un altro cloud

- [Installare manualmente](../learn/quick-collect-linux-computer.md) l'agente che chiama uno script wrapper ospitato in GitHub.
- System Center Operations Manager | [Integrare Operations Manager con monitoraggio di Azure](./om-agents.md) per l'invio di dati raccolti da computer Windows che inviano report a un gruppo di gestione.

## <a name="workspace-id-and-key"></a>ID e chiave dell'area di lavoro
Indipendentemente dal metodo di installazione usato, sarà necessario specificare l'ID e la chiave dell'area di lavoro per l'area di lavoro Log Analytics a cui si connetterà l'agente. Selezionare l'area di lavoro dal menu **log Analytics aree di lavoro** del portale di Azure. Quindi selezionare **Agents Management** nella sezione **Settings** . 

[![Dettagli di un'area di lavoro](media/log-analytics-agent/workspace-details.png)](media/log-analytics-agent/workspace-details.png#lightbox)

## <a name="tls-12-protocol"></a>Protocollo TLS 1.2

Per garantire la sicurezza dei dati in transito verso i log di Monitoraggio di Azure, è consigliabile configurare l'agente in modo da usare almeno il protocollo Transport Layer Security (TLS) 1.2. Le versioni precedenti di TLS/Secure Sockets Layer (SSL) sono state considerate vulnerabili. Nonostante siano ancora attualmente in uso per questioni di compatibilità con le versioni precedenti, **non sono consigliate**.  Per altre informazioni, vedere [Invio dei dati in modo sicuro tramite TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-requirements"></a>Requisiti di rete
L'agente per Linux e Windows comunica in uscita con il servizio monitoraggio di Azure sulla porta TCP 443. Se il computer si connette tramite un firewall o un server proxy per la comunicazione tramite Internet, esaminare i requisiti indicati di seguito per comprendere la configurazione di rete necessaria. Se i criteri di sicurezza IT non consentono ai computer della rete di connettersi a Internet, è possibile configurare un [gateway log Analytics](gateway.md) e quindi configurare l'agente per la connessione tramite il gateway a monitoraggio di Azure. L'agente può quindi ricevere informazioni di configurazione e inviare dati raccolti.

![Diagramma delle comunicazioni dell'agente di Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

La tabella seguente elenca le informazioni di configurazione del proxy e del firewall necessarie per la comunicazione degli agenti Linux e Windows con i log di monitoraggio di Azure.

### <a name="firewall-requirements"></a>Requisiti del firewall

|Risorsa agente|Porte |Direction |Ignorare l'analisi HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |In uscita|Sì |  
|*.oms.opinsights.azure.com |Porta 443 |In uscita|Sì |  
|*.blob.core.windows.net |Porta 443 |In uscita|Sì |
|*.azure-automation.net |Porta 443 |In uscita|Sì |

Per informazioni sul firewall necessarie per Azure per enti pubblici, vedere [Gestione di Azure per enti pubblici](../../azure-government/compare-azure-government-global-azure.md#azure-monitor). 

Se si prevede di usare il ruolo di lavoro ibrido per runbook di Automazione di Azure per connettersi e registrarsi al servizio di automazione per usare i runbook o le soluzioni di gestione nell'ambiente in uso, è necessario avere accesso al numero di porta e agli URL descritti in [Configurare la rete per il ruolo di lavoro ibrido per runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

### <a name="proxy-configuration"></a>Configurazione proxy

L'agente Windows e Linux supporta la comunicazione con Monitoraggio di Azure tramite un server proxy o un gateway di Log Analytics usando il protocollo HTTPS.  Sono supportate sia l'autenticazione anonima che quella di base (nome utente/password).  Per l'agente di Windows connesso direttamente al servizio, la configurazione del proxy viene specificata durante l'installazione o [dopo la distribuzione](agent-manage.md#update-proxy-settings) dal Pannello di controllo o con PowerShell.  

Per l'agente di Linux, è possibile specificare il server proxy durante l'installazione o [dopo l'installazione](agent-manage.md#update-proxy-settings) modificando il file di configurazione proxy.conf.  Il valore di configurazione del proxy dell'agente Linux ha la sintassi seguente:

`[protocol://][user:password@]proxyhost[:port]`

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