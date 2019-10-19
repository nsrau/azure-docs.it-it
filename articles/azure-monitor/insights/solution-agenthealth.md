---
title: Soluzione Integrità agente in monitoraggio di Azure | Microsoft Docs
description: L'obiettivo di questo articolo è spiegare come usare questa soluzione per monitorare l'integrità degli agenti che inviano report direttamente a Log Analytics o System Center Operations Manager.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 03/19/2017
ms.openlocfilehash: 5a48bbff89f0d6a0be9adf2ad242dbca41eec6db
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555329"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Soluzione Integrità agente in monitoraggio di Azure
La soluzione Integrità agente in Azure consente di comprendere, per tutti gli agenti che inviano report direttamente all'area di lavoro Log Analytics in monitoraggio di Azure o a un gruppo di gestione System Center Operations Manager connesso a monitoraggio di Azure, che non rispondono e invio di dati operativi.  È anche possibile tenere traccia del numero di agenti distribuiti, della rispettiva ubicazione ed eseguire altre query per rimanere aggiornati sulla distribuzione degli agenti distribuiti in Azure, in altri ambienti cloud o in locale.    

## <a name="prerequisites"></a>Prerequisiti
Prima di distribuire questa soluzione, assicurarsi che sia attualmente supportato l'invio di report da parte degli [agenti di Windows](../../log-analytics/log-analytics-windows-agent.md) all'area di lavoro Log Analytics o a un [gruppo di gestione di Operations Manager](../../azure-monitor/platform/om-agents.md) integrato con l'area di lavoro.

## <a name="solution-components"></a>Componenti della soluzione
Questa soluzione è costituita dalle risorse seguenti che vengono aggiunte all'area di lavoro e agli agenti direttamente connessi o al gruppo di gestione connesso di Operations Manager.

### <a name="management-packs"></a>Management Pack
Se il gruppo di gestione di System Center Operations Manager è connesso all'area di lavoro Log Analytics, in Operations Manager verranno installati i Management Pack seguenti.  Questi Management Pack vengono installati anche su computer Windows direttamente connessi dopo l'aggiunta di questa soluzione. Non sono richieste attività di configurazione o gestione con questi Management Pack.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Per altre informazioni sulla modalità di aggiornamento dei Management Pack, vedere [Connettere Operations Manager a Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="configuration"></a>Configurazione
Aggiungere la soluzione Integrità agente all'area di lavoro Log Analytics usando la procedura descritta in [Aggiungere soluzioni](solutions.md). Non è richiesta alcuna ulteriore configurazione.


## <a name="data-collection"></a>Raccolta dei dati
### <a name="supported-agents"></a>Agenti supportati
La tabella seguente descrive le origini connesse che sono supportate da questa soluzione.

| Origine connessa | Supportato | Description |
| --- | --- | --- |
| Agenti Windows | SÌ | Gli eventi di heartbeat vengono raccolti dagli agenti di Windows diretti.|
| Gruppo di gestione di System Center Operations Manager | SÌ | Gli eventi di heartbeat vengono raccolti dagli agenti che inviano report al gruppo di gestione ogni 60 secondi, quindi vengono quindi trasmessi a monitoraggio di Azure. Non è necessaria una connessione diretta da agenti Operations Manager a monitoraggio di Azure. I dati degli eventi heartbeat vengono trasmessi dal gruppo di gestione all'area di lavoro Log Analytics.|

## <a name="using-the-solution"></a>Uso della soluzione
Quando si aggiunge la soluzione Integrità agente all'area di lavoro Log Analytics, il riquadro **Integrità agente** viene aggiunto al dashboard. Questo riquadro mostra il numero totale di agenti e il numero di agenti che non rispondono nelle ultime 24 ore.<br><br> ![Riquadro della soluzione Integrità agente nel dashboard](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Fare clic sul riquadro **Integrità agente** per aprire il dashboard di **Integrità agente**.  Il dashboard include le colonne nella tabella seguente. Ogni colonna elenca i primi dieci eventi per numero corrispondente ai criteri della colonna per l'intervallo di tempo specificato. È possibile eseguire una ricerca log che fornisce l'intero elenco selezionando **Visualizza tutto** nella parte inferiore destra di ogni colonna o facendo clic sull'intestazione di colonna.

| Colonna | Description |
|--------|-------------|
| Agent count over time (Conteggio nel tempo) | Tendenza del conteggio dell'agente in un periodo di sette giorni per agenti di Linux e Windows.|
| Count of unresponsive agents (Conteggio di agenti che non rispondono) | Elenco degli agenti che non hanno inviato heartbeat nelle ultime 24 ore.|
| Distribuzione per tipo di sistema operativo | Indicazione del numero di agenti di Windows e Linux disponibili nell'ambiente.|
| Distribution by Agent Version (Distribuzione per versione dell'agente) | Indicazione delle diverse versioni dell'agente installate nell'ambiente e conteggio di ogni versione.|
| Distribution by Agent Category (Distribuzione per categoria dell'agente) | Indicazione delle diverse categorie di agenti che inviano eventi di heartbeat, ovvero agenti diretti, agenti OpsMgr o un server di gestione di OpsMgr.|
| Distribution by Management Group (Distribuzione per gruppo di gestione) | Una partizione dei diversi gruppi di gestione di Operations Manager nell'ambiente in uso.|
| Geo-location of Agents (Posizione geografica degli agenti) | Una partizione dei diversi paesi o aree geografiche in cui sono presenti agenti e un conteggio totale del numero di agenti installati in ogni paese/area geografica.|
| Count of Gateways Installed (Conteggio dei gateway installati) | Numero di server in cui è installato il gateway Log Analytics ed elenco dei server.|

![Esempio di dashboard della soluzione Integrità agente](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Record di log di Monitoraggio di Azure
La soluzione crea un tipo di record nell'area di lavoro Log Analytics.  

### <a name="heartbeat-records"></a>Record di heartbeat
Viene creato un record di tipo **Heartbeat**.  Questi record includono le proprietà elencate nella tabella seguente.  

| Proprietà | Description |
| --- | --- |
| `Type` | *Heartbeat*|
| `Category` | Il valore è *Agente diretto*, *SCOM Agent* (Agente SCOM) o *SCOM Management Server* (Server di gestione SCOM).|
| `Computer` | Nome del computer.|
| `OSType` | Sistema operativo Windows o Linux.|
| `OSMajorVersion` | Versione principale del sistema operativo.|
| `OSMinorVersion` | Versione secondaria del sistema operativo.|
| `Version` | Versione dell'agente di Log Analytics o dell'agente di Operations Manager.|
| `SCAgentChannel` | Il valore è *Direct* e/o *SCManagementServer*.|
| `IsGatewayInstalled` | Se il gateway Log Analytics è installato, il valore è *true*. In caso contrario, il valore è *false*.|
| `ComputerIP` | Indirizzo IP del computer.|
| `RemoteIPCountry` | Posizione geografica in cui è distribuito il computer.|
| `ManagementGroupName` | Nome del gruppo di gestione di Operations Manager.|
| `SourceComputerId` | ID univoco del computer.|
| `RemoteIPLongitude` | Longitudine della posizione geografica del computer.|
| `RemoteIPLatitude` | Latitudine della posizione geografica del computer.|

Ogni agente che segnala a un Operations Manager server di gestione invierà due heartbeat e il valore della proprietà SCAgentChannel includerà sia **Direct** che **SCManagementServer** a seconda delle origini dati e delle soluzioni di monitoraggio disponibili. abilitato nella sottoscrizione. Se si richiama, i dati delle soluzioni vengono inviati direttamente da un server di gestione Operations Manager a monitoraggio di Azure o a causa del volume dei dati raccolti nell'agente, che vengono inviati direttamente dall'agente a monitoraggio di Azure. Per gli eventi di heartbeat con valore **SCManagementServer**, il valore ComputerIP è l'indirizzo IP del server di gestione, perché i dati vengono effettivamente caricati da tale server.  Per gli heartbeat il cui valore SCAgentChannel è impostato su **Direct**, corrisponde all'indirizzo IP pubblico dell'agente.  

## <a name="sample-log-searches"></a>Ricerche di log di esempio
La tabella seguente contiene esempi di ricerche nei log per i record raccolti da questa soluzione.

| Query | Description |
|:---|:---|
| Heartbeat &#124; distinct Computer |Numero totale di agenti |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Conteggio degli agenti che non hanno risposto nelle ultime 24 ore |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Conteggio degli agenti che non hanno risposto negli ultimi 15 minuti |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Computer online (nelle ultime 24 ore) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Totale degli agenti offline negli ultimi 30 minuti (per le ultime 24 ore) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Tendenza del numero di agenti nel tempo per OSType|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Distribuzione per tipo di sistema operativo |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Distribution by Agent Version (Distribuzione per versione dell'agente) |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Distribution by Agent Category (Distribuzione per categoria dell'agente) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Distribution by Management Group (Distribuzione per gruppo di gestione) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Geo-location of Agents (Posizione geografica degli agenti) |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Numero di gateway Log Analytics installati |




## <a name="next-steps"></a>Passaggi successivi

* Informazioni sugli [avvisi in monitoraggio di Azure](../platform/alerts-overview.md) per informazioni dettagliate sulla generazione di avvisi dalle query di log. 
