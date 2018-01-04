---
title: Raccogliere i dati dall'ambiente in uso con Azure Log Analitica | Documenti Microsoft
description: In questo argomento contribuisce di comprendere come raccogliere i dati e monitorare i computer ospitati in locale o un altro ambiente cloud con Log Analitica.
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: magoedte
ms.openlocfilehash: 513855084c8b89d97b049f1df2ec24d0f9789afe
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="collect-data-from-computers-in-your-environment-with-log-analytics"></a>Raccogliere dati dai computer nell'ambiente con Log Analitica

Azure Analitica di Log è possibile raccogliere e agire sui dati da computer Windows o Linux che si trova in:

* [Macchine virtuali di Azure](log-analytics-quick-collect-azurevm.md) utilizzando l'estensione VM Analitica di Log 
* Il Data Center come server fisici o macchine virtuali
* Macchine virtuali in un servizio ospitato su cloud come Amazon Web Services (AWS)

Ospitato nell'ambiente di computer può essere connesso direttamente a Analitica Log o se si siano già monitorando questi computer con System Center Operations Manager 2012 R2 o 2016, è possibile integrare il gruppo di gestione di gestire le operazioni con Log Analitica e continuare a gestire i processi di operazioni del servizio e la strategia.  

## <a name="overview"></a>Panoramica

![log-Analytics-Agent-Direct-Connect-Diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

Prima di analisi e i dati raccolti, è necessario innanzitutto installare e collegare gli agenti per tutti i computer che si desidera inviare dati al servizio Registro Analitica. È possibile installare gli agenti nei computer locale utilizzando l'installazione, la riga di comando, o con Desired State Configuration (DSC) in automazione di Azure. 

L'agente per Linux e Windows comunica in uscita con il servizio Registro Analitica sulla porta TCP 443 e se il computer si connette a un firewall o server proxy per comunicare su Internet, rivedere [configurazione dell'agente per l'utilizzo con un server proxy Gateway di OMS o](#configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway) per comprendere quale configurazione viene modificato verrà devono essere applicati. Se si sta monitorando il computer con System Center 2016 - Operations Manager o Operations Manager 2012 R2, è possibile multihomed con il servizio Registro Analitica per raccogliere i dati e diretta al servizio e ancora essere monitorata da [Operations Manager ](log-analytics-om-agents.md). I computer Linux monitorati da un gruppo di gestione di Operations Manager integrato con Log Analitica non è possibile configurazione di origini dati e i dati raccolti in avanti tramite il gruppo di gestione. L'agente di Windows può segnalare fino a quattro aree di lavoro, mentre l'agente Linux supporta solo la creazione di report a una singola area di lavoro.  

L'agente per Linux e Windows non è solo per la connessione nel registro Analitica, supporta anche la connessione con l'automazione di Azure per il ruolo di lavoro ibridi Runbook host e le soluzioni di gestione, ad esempio il rilevamento delle modifiche e gestione degli aggiornamenti.  Per ulteriori informazioni sul ruolo di lavoro ibridi per Runbook, vedere [Runbook Worker ibrido automazione di Azure](../automation/automation-offering-get-started.md#automation-architecture-overview).  

Se i criteri di sicurezza IT non consentono ai computer nella rete di connettersi a Internet, è possibile configurare l'agente per la connessione al gateway OMS, per ricevere informazioni di configurazione e inviare i dati raccolti a seconda della soluzione abilitata. Per ulteriori informazioni e procedure su come configurare l'agente Linux o Windows di comunicare attraverso un Gateway di OMS per il servizio Registro Analitica, vedere [connettere i computer a OMS con il Gateway OMS](log-analytics-oms-gateway.md). 

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, esaminare i dettagli seguenti per verificare di soddisfare i requisiti minimi di sistema.

### <a name="windows-operating-system"></a>Sistema operativo Windows
Le seguenti versioni del sistema operativo Windows sono ufficialmente supportate per l'agente di Windows:

* Windows Server 2008 Service Pack 1 (SP1) o versione successiva
* Windows 7 SP1 e versioni successive.

#### <a name="network-configuration"></a>Network configuration
Le informazioni seguenti elenco le informazioni di configurazione del proxy e firewall necessarie per l'agente di Windows comunicare con Log Analitica. Il traffico è in uscita dalla rete per il servizio Registro Analitica. 

| Risorsa agente | Porte | Ignorare l'analisi HTTPS|
|----------------|-------|------------------------|
|*.ods.opinsights.azure.com |443 | Sì |
|*.oms.opinsights.azure.com | 443 | Sì | 
|*.blob.core.windows.net | 443 | Sì | 
|*.azure-automation.net | 443 | Sì | 

### <a name="linux-operating-systems"></a>Sistemi operativi Linux
Le distribuzioni Linux seguenti sono supportate ufficialmente.  Tuttavia, l'agente Linux può eseguire anche in altre distribuzioni non elencate.

* Amazon Linux da 2012.09 a 2015.09 (x86/x64)
* CentOS Linux 5, 6 e 7 (x86/x64)
* Oracle Linux 5, 6 e 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 e 12 (x86/x64)

#### <a name="network-configuration"></a>Network configuration
Le informazioni seguenti elenco le informazioni di configurazione del proxy e firewall necessarie per l'agente Linux comunicare con Log Analitica. Il traffico è in uscita dalla rete per il servizio Registro Analitica. 

|Risorsa agente| Porte |  
|------|---------|  
|*.ods.opinsights.azure.com | Porta 443|   
|*.oms.opinsights.azure.com | Porta 443|   
|*.blob.core.windows.net | Porta 443|   
|*.azure-automation.net | Porta 443|  

L'agente Linux supporta la comunicazione tramite un server proxy o Gateway di OMS per il servizio Registro Analitica mediante il protocollo HTTPS.  L'autenticazione anonima e di base (nome utente/password) sono supportati.  È possibile specificare il server proxy durante l'installazione o modificando il file di configurazione proxy.conf dopo l'installazione.  

Il valore di configurazione proxy ha la sintassi seguente:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Se il server proxy richiede l'autenticazione, l'agente Linux richiede ancora fornendo una pseudo utente/password. Può trattarsi di qualsiasi nome utente o password.

|Proprietà| DESCRIZIONE |
|--------|-------------|
|Protocollo | https |
|user | Nome utente facoltativo per l'autenticazione proxy |
|password | Password facoltativa per l'autenticazione proxy |
|proxyhost | Indirizzo o FQDN del server proxy/gateway OMS |
|port | Numero di porta facoltativo del server proxy/gateway OMS |

Ad esempio: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Se si utilizzano caratteri speciali, ad esempio "@" la password, viene visualizzato un errore di connessione proxy perché il valore viene analizzato in modo non corretto.  Per risolvere questo problema, codificare la password nell'URL utilizzando uno strumento come [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Installare e configurare l'agente 
Connessione dei computer locali direttamente con Log Analitica può essere eseguita utilizzando metodi diversi a seconda dei requisiti. Nella tabella seguente illustra ogni metodo per determinare quello più adatto all'interno dell'organizzazione.

|Sorgente | Metodo | DESCRIZIONE|
|-------|-------------|-------------|
| Computer Windows|- [Installazione manuale](log-analytics-agent-windows.md)<br>- [DSC di automazione di Azure](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Modello di gestione delle risorse con lo Stack di Azure](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Installare Microsoft Monitoring agent dalla riga di comando o tramite un metodo automatico, ad esempio Automation DSC per Azure, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), o con un modello di gestione risorse di Azure, se è stato distribuito Microsoft Stack di Azure nel Data Center.| 
|Computer Linux| [Installazione manuale](log-analytics-quick-collect-linux-computer.md)|Installare l'agente per Linux, la chiamata a uno script di wrapper ospitato in GitHub. | 
| System Center Operations Manager|[Integrare Operations Manager con Log Analitica](log-analytics-om-agents.md) | Configurare l'integrazione tra Operations Manager e Log Analitica per inoltrare i dati raccolti dai computer Linux e Windows a un gruppo di gestione.|  

## <a name="next-steps"></a>Passaggi successivi

* Revisione [origini dati](log-analytics-data-sources.md) per comprendere le origini dati disponibili per raccogliere i dati dal sistema di Windows o Linux. 

* Altre informazioni sulle [ricerche nei log](log-analytics-log-searches.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni. 

* Informazioni sulle [soluzioni](log-analytics-add-solutions.md) che aggiungono funzionalità a Log Analytics e raccolgono dati nel repository OMS.