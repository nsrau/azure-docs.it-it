---
title: Raccogliere dati dall'ambiente con Azure Log Analytics | Microsoft Docs
description: Questo argomento contiene informazioni su come raccogliere i dati e monitorare i computer ospitati in locale o in un altro ambiente cloud con Log Analytics.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: magoedte
ms.openlocfilehash: 9346e9a9ad310a21c6d6ce388b76ce491041289c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="collect-data-from-computers-in-your-environment-with-log-analytics"></a>Raccogliere dati dai computer dell'ambiente con Log Analytics

Azure Log Analytics può raccogliere dati ed eseguire operazioni su di essi da computer Windows o Linux che si trovano in:

* [Macchine virtuali di Azure](log-analytics-quick-collect-azurevm.md) che usano l'estensione macchina virtuale di Log Analytics 
* Data center come server fisici o macchine virtuali
* Macchine virtuali in un servizio ospitato nel cloud, ad esempio Amazon Web Services (AWS)

I computer ospitati nell'ambiente possono essere connessi direttamente a Log Analytics oppure, se si monitorano già questi computer con System Center Operations Manager 2012 R2 o 2016, è possibile integrare il gruppo di gestione di Operations Manager con Log Analytics e continuare a gestire i processi di operazioni del servizio e la strategia.  

## <a name="overview"></a>Panoramica

![log-analytics-agent-direct-connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

Prima di analizzare i dati raccolti ed eseguire operazioni su di essi, è necessario installare e connettere gli agenti per tutti i computer che devono inviare i dati al servizio Log Analytics. È possibile installare gli agenti nei computer locali tramite il programma di installazione, la riga di comando o con Desired State Configuration in Automazione di Azure. 

L'agente per Linux e Windows comunica in uscita con il servizio Log Analytics sulla porta TCP 443 e, se il computer si connette a un firewall o a un server proxy per comunicare in Internet, vedere [Configurazione dell'agente per l'uso con un server proxy o un gateway OMS](#configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway) per comprendere quali modifiche alla configurazione è necessario applicare. Se si esegue il monitoraggio del computer con System Center 2016 - Operations Manager oppure Operations Manager 2012 R2, è possibile usare una configurazione multihomed con il servizio Log Analytics per raccogliere i dati e inoltrarli al servizio, mantenendo il monitoraggio di [Operations Manager](log-analytics-om-agents.md). I computer Linux monitorati da un gruppo di gestione di Operations Manager integrato in Log Analytics non ricevono la configurazione per le origini dati e inoltrano i dati raccolti tramite il gruppo di gestione. L'agente Windows può inviare report a un massimo di quattro aree di lavoro, mentre l'agente Linux supporta solo l'invio di report a una singola area di lavoro.  

L'agente per Linux e Windows non serve solo per la connessione a Log Analytics, ma supporta anche la connessione con Automazione di Azure per ospitare il ruolo di lavoro ibrido per runbook e le soluzioni di gestione, ad esempio Rilevamento modifiche e Gestione aggiornamenti.  Per altre informazioni sul ruolo di lavoro ibrido per runbook, vedere [Ruolo di lavoro ibrido per runbook di Automazione di Azure](../automation/automation-offering-get-started.md#automation-architecture-overview).  

Se i criteri di sicurezza IT non consentono ai computer nella rete di connettersi a Internet, è possibile configurare l'agente per la connessione al gateway OMS, per ricevere informazioni di configurazione e inviare i dati raccolti a seconda della soluzione abilitata. Per altre informazioni e procedure per la configurazione dell'agente Linux o Windows per la comunicazione tramite un gateway OMS con il servizio Log Analytics, vedere [Connettere computer a OMS usando il gateway OMS](log-analytics-oms-gateway.md). 

> [!NOTE]
> L'agente per Windows supporta solo TLS (Transport Layer Security) 1.0 e 1.1.  
> 

## <a name="prerequisites"></a>prerequisiti
Prima di iniziare, esaminare i dettagli seguenti per verificare che i requisiti minimi di sistema siano soddisfatti.

### <a name="windows-operating-system"></a>Sistema operativo Windows
Le versioni seguenti del sistema operativo Windows sono ufficialmente supportate per l'agente Windows:

* Windows Server 2008 Service Pack 1 (SP1) o versione successiva
* Windows 7 SP1 e versioni successive.

#### <a name="network-configuration"></a>Network configuration
La tabella seguente contiene un elenco delle informazioni di configurazione del proxy e del firewall necessarie all'agente Windows per comunicare con Log Analytics. Il traffico in uscita dalla rete viene indirizzato al servizio Log Analytics. 

| Risorsa agente | Porte | Ignorare l'analisi HTTPS|
|----------------|-------|------------------------|
|*.ods.opinsights.azure.com |443 | Sì |
|*.oms.opinsights.azure.com | 443 | Sì | 
|*.blob.core.windows.net | 443 | Sì | 
|*.azure-automation.net | 443 | Sì | 

### <a name="linux-operating-systems"></a>Sistemi operativi Linux
Le distribuzioni Linux seguenti sono supportate ufficialmente.  È tuttavia possibile che l'agente Linux sia eseguito in altre distribuzioni non elencate.  Se non diversamente indicato, tutte le versioni minori sono supportate per ogni versione principale elencata.  

* Amazon Linux da 2012.09 a 2015.09 (x86/x64)
* CentOS Linux 5, 6 e 7 (x86/x64)  
* Oracle Linux 5, 6 e 7 (x86/x64) 
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 e 12 (x86/x64)

#### <a name="network-configuration"></a>Network configuration
La tabella seguente contiene un elenco delle informazioni di configurazione del proxy e del firewall necessarie all'agente Linux per comunicare con Log Analytics.  

|Risorsa agente| Porte | Direzione |  
|------|---------|--------|  
|*.ods.opinsights.azure.com | Porta 443 | In ingresso e in uscita|  
|*.oms.opinsights.azure.com | Porta 443 | In ingresso e in uscita|  
|*.blob.core.windows.net | Porta 443 | In ingresso e in uscita|  
|*.azure-automation.net | Porta 443 | In ingresso e in uscita|  

L'agente Linux supporta la comunicazione tramite un server proxy o un gateway OMS con il servizio Log Analytics usando il protocollo HTTPS.  Sono supportate sia l'autenticazione anonima che quella di base (nome utente/password).  È possibile specificare il server proxy durante l'installazione o modificando il file di configurazione proxy.conf dopo l'installazione.  

Il valore di configurazione proxy ha la sintassi seguente:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Se il server proxy non richiede l'autenticazione, l'agente Linux richiede tuttavia di immettere uno pseudoutente o una pseudopassword. Può trattarsi di qualsiasi nome utente o password.

|Proprietà| DESCRIZIONE |
|--------|-------------|
|Protocollo | https |
|user | Nome utente facoltativo per l'autenticazione proxy |
|password | Password facoltativa per l'autenticazione proxy |
|proxyhost | Indirizzo o FQDN del server proxy/gateway OMS |
|port | Numero di porta facoltativo del server proxy/gateway OMS |

Ad esempio: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Se si usano caratteri speciali, ad esempio \"\@\" nella password, viene visualizzato un errore di connessione proxy perché il valore viene analizzato in modo non corretto.  Per aggirare il problema, codificare la password nell'URL usando uno strumento come [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Installare e configurare l'agente 
Per connettere i computer locali direttamente con Log Analytics, è possibile usare metodi diversi a seconda dei requisiti. Nella tabella seguente illustra ogni metodo per determinare quello più adatto alla propria organizzazione.

|Sorgente | Metodo | DESCRIZIONE|
|-------|-------------|-------------|
| Computer Windows|- [Installazione manuale](log-analytics-agent-windows.md)<br>- [Automation DSC per Azure](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Modello di Resource Manager con Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Installare Microsoft Monitoring Agent dalla riga di comando o usando un metodo automatizzato, ad esempio Automation DSC per Azure, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications) oppure con un modello di Azure Resource Manager se nel data center è stato distribuito Microsoft Azure Stack.| 
|Computer Linux| [Installazione manuale](log-analytics-quick-collect-linux-computer.md)|Installare l'agente per Linux chiamando uno script wrapper ospitato in GitHub. | 
| System Center Operations Manager|[Integrare Operations Manager con Log Analytics](log-analytics-om-agents.md) | Configurare l'integrazione tra Operations Manager e Log Analytics per inoltrare i dati raccolti dai computer Linux e Windows che inviano report a un gruppo di gestione.|  

## <a name="next-steps"></a>Passaggi successivi

* Esaminare le [origini dati](log-analytics-data-sources.md) per informazioni sulle origini dati disponibili per raccogliere i dati dal sistema Windows o Linux. 

* Altre informazioni sulle [ricerche nei log](log-analytics-log-searches.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni. 

* Informazioni sulle [soluzioni](log-analytics-add-solutions.md) che aggiungono funzionalità a Log Analytics e raccolgono dati nel repository OMS.