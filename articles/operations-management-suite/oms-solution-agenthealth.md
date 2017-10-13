---
title: "Soluzione Integrità agente in OMS | Microsoft Docs"
description: "Questo articolo fornisce informazioni su come usare questa soluzione per monitorare l'integrità degli agenti che inviano report direttamente a OMS o System Center Operations Manager."
services: operations-management-suite
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: magoedte
ms.openlocfilehash: b810e37e393ddab55500f636b72450789285a4f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
#  <a name="agent-health-solution-in-oms"></a>Soluzione Integrità agente in OMS
La soluzione Integrità agente in OMS consente di individuare gli agenti che non rispondono e quelli che inviano dati operativi tra tutti gli agenti che inviano report direttamente all'area di lavoro di OMS o a un gruppo di gestione di System Center Operations Manager connesso a OMS.  È anche possibile tenere traccia del numero di agenti distribuiti, della rispettiva ubicazione ed eseguire altre query per rimanere aggiornati sulla distribuzione degli agenti distribuiti in Azure, in altri ambienti cloud o in locale.    

## <a name="prerequisites"></a>Prerequisiti
Prima di distribuire questa soluzione, assicurarsi che l'invio di report da parte degli [agenti di Windows](../log-analytics/log-analytics-windows-agents.md) all'area di lavoro di OMS o a un [gruppo di gestione di Operations Manager](../log-analytics/log-analytics-om-agents.md) integrato con l'area di lavoro di OMS sia attualmente supportato.    

## <a name="solution-components"></a>Componenti della soluzione
Questa soluzione è costituita dalle risorse seguenti che vengono aggiunte all'area di lavoro e agli agenti direttamente connessi o al gruppo di gestione connesso di Operations Manager.

### <a name="management-packs"></a>Management Pack
Se il gruppo di gestione di System Center Operations Manager è connesso all'area di lavoro di OMS, in Operations Manager verranno installati i Management Pack seguenti.  Questi Management Pack vengono installati anche su computer Windows direttamente connessi dopo l'aggiunta di questa soluzione. Non sono richieste attività di configurazione o gestione con questi Management Pack.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Per maggiori informazioni sulla modalità di aggiornamento dei Management Pack, vedere [Connettere Operations Manager a Log Analytics](../log-analytics/log-analytics-om-agents.md).

## <a name="configuration"></a>Configurazione
Aggiungere la soluzione Integrità agente all'area di lavoro di OMS usando la procedura descritta in [Aggiungere soluzioni](../log-analytics/log-analytics-add-solutions.md). Non è richiesta alcuna ulteriore configurazione.


## <a name="data-collection"></a>Raccolta dei dati
### <a name="supported-agents"></a>Agenti supportati
La tabella seguente descrive le origini connesse che sono supportate da questa soluzione.

| Origine connessa | Supportato | Descrizione |
| --- | --- | --- |
| Agenti di Windows | Sì | Gli eventi di heartbeat vengono raccolti dagli agenti di Windows diretti.|
| Gruppo di gestione di System Center Operations Manager | Sì | Gli eventi di heartbeat dagli agenti che inviano report al gruppo di gestione vengono raccolti ogni 60 secondi e vengono quindi inoltrati a Log Analytics. Non è necessaria una connessione diretta dall'agente Operations Manager a Log Analytics. I dati degli eventi di heartbeat vengono inoltrati dal gruppo di gestione al repository di Log Analytics.|

## <a name="using-the-solution"></a>Uso della soluzione
Quando si aggiunge la soluzione Integrità agente all'area di lavoro di OMS, il riquadro **Integrità agente** verrà aggiunto al dashboard OMS. Questo riquadro mostra il numero totale di agenti e il numero di agenti che non rispondono nelle ultime 24 ore.<br><br> ![Riquadro della soluzione Integrità agente nel dashboard](./media/oms-solution-agenthealth/agenthealth-solution-tile-homepage.png)

Fare clic sul riquadro **Integrità agente** per aprire il dashboard di **Integrità agente**.  Il dashboard include le colonne nella tabella seguente. Ogni colonna elenca i primi dieci eventi per numero corrispondente ai criteri della colonna per l'intervallo di tempo specificato. È possibile eseguire una ricerca log che fornisce l'intero elenco selezionando **Visualizza tutto** nella parte inferiore destra di ogni colonna o facendo clic sull'intestazione di colonna.

| Colonna | Descrizione |
|--------|-------------|
| Agent count over time (Conteggio nel tempo) | Tendenza del conteggio dell'agente in un periodo di sette giorni per agenti di Linux e Windows.|
| Count of unresponsive agents (Conteggio di agenti che non rispondono) | Elenco degli agenti che non hanno inviato heartbeat nelle ultime 24 ore.|
| Distribution by OS Type (Distribuzione per tipo di sistema operativo) | Indicazione del numero di agenti di Windows e Linux disponibili nell'ambiente.|
| Distribution by Agent Version (Distribuzione per versione dell'agente) | Indicazione delle diverse versioni dell'agente installate nell'ambiente e conteggio di ogni versione.|
| Distribution by Agent Category (Distribuzione per categoria dell'agente) | Indicazione delle diverse categorie di agenti che inviano eventi di heartbeat, ovvero agenti diretti, agenti OpsMgr o un server di gestione di OpsMgr.|
| Distribution by Management Group (Distribuzione per gruppo di gestione) | Indicazione dei diversi gruppi di gestione di SCOM disponibili nell'ambiente.|
| Geo-location of Agents (Posizione geografica degli agenti) | Indicazione dei diversi paesi in cui sono presenti agenti e conteggio totale del numero di agenti installati in ogni paese.|
| Count of Gateways Installed (Conteggio dei gateway installati) | Numero di server in cui è installato il Gateway OMS ed elenco di tali server.|

![Esempio di dashboard della soluzione Integrità agente](./media/oms-solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="log-analytics-records"></a>Record di Log Analytics
La soluzione crea un tipo di record nel repository di OMS.  

### <a name="heartbeat-records"></a>Record di heartbeat
Viene creato un record di tipo **Heartbeat**.  Questi record includono le proprietà elencate nella tabella seguente.  

| Proprietà | Descrizione |
| --- | --- |
| Tipo | *Heartbeat*|
| Categoria | Il valore è *Agente diretto*, *SCOM Agent* (Agente SCOM) o *SCOM Management Server* (Server di gestione SCOM).|
| Computer | Nome del computer.|
| OSType | Sistema operativo Windows o Linux.|
| OSMajorVersion | Versione principale del sistema operativo.|
| OSMinorVersion | Versione secondaria del sistema operativo.|
| Versione | Versione dell'agente di OMS o dell'agente di Operations Manager.|
| SCAgentChannel | Il valore è *Direct* e/o *SCManagementServer*.|
| IsGatewayInstalled | Se il Gateway OMS è installato, il valore è *true*. In caso contrario, il valore è *false*.|
| ComputerIP | Indirizzo IP del computer.|
| RemoteIPCountry | Posizione geografica in cui è distribuito il computer.|
| ManagementGroupName | Nome del gruppo di gestione di Operations Manager.|
| SourceComputerId | ID univoco del computer.|
| RemoteIPLongitude | Longitudine della posizione geografica del computer.|
| RemoteIPLatitude | Latitudine della posizione geografica del computer.|

Ogni agente che invia report a un server di gestione di Operations Manager invierà due heartbeat e il valore della proprietà SCAgentChannel includerà **Direct** e **SCManagementServer**, in base alle origini dati e alle soluzioni di Log Analytics abilitate nella sottoscrizione di OMS. Come si può ricordare, i dati delle soluzioni vengono inviati direttamente da un server di gestione di Operations Manager al servizio Web OMS oppure, a causa del volume dei dati raccolti nell'agente, vengono inviati direttamente dall'agente al servizio Web OMS. Per gli eventi di heartbeat con valore **SCManagementServer**, il valore ComputerIP è l'indirizzo IP del server di gestione, perché i dati vengono effettivamente caricati da tale server.  Per gli heartbeat il cui valore SCAgentChannel è impostato su **Direct**, corrisponde all'indirizzo IP pubblico dell'agente.  

## <a name="sample-log-searches"></a>Ricerche di log di esempio
La tabella seguente contiene esempi di ricerche nei log per i record raccolti da questa soluzione.

| Query | Descrizione |
| --- | --- |
| Type=Heartbeat &#124; distinct Computer |Numero totale di agenti |
| Type=Heartbeat &#124; measure max(TimeGenerated) as LastCall by Computer &#124; where LastCall < NOW-24HOURS |Conteggio degli agenti che non hanno risposto nelle ultime 24 ore |
| Type=Heartbeat &#124; measure max(TimeGenerated) as LastCall by Computer &#124; where LastCall < NOW-15MINUTES |Conteggio degli agenti che non hanno risposto negli ultimi 15 minuti |
| Type=Heartbeat TimeGenerated>NOW-24HOURS Computer IN {Type=Heartbeat TimeGenerated>NOW-24HOURS &#124; distinct Computer} &#124; measure max(TimeGenerated) as LastCall by Computer |Computer online (nelle ultime 24 ore) |
| Type=Heartbeat TimeGenerated>NOW-24HOURS Computer NOT IN {Type=Heartbeat TimeGenerated>NOW-30MINUTES &#124; distinct Computer} &#124; measure max(TimeGenerated) as LastCall by Computer |Totale degli agenti offline negli ultimi 30 minuti (per le ultime 24 ore) |
| Type=Heartbeat &#124; measure countdistinct(Computer) by OSType |Tendenza del numero di agenti nel tempo per OSType|
| Type=Heartbeat&#124;measure countdistinct(Computer) by OSType |Distribuzione per tipo di sistema operativo |
| Type=Heartbeat&#124;measure countdistinct(Computer) by Version |Distribuzione per versione dell'agente |
| Type=Heartbeat&#124;measure count() by Category |Distribuzione per categoria dell'agente |
| Type=Heartbeat&#124;measure countdistinct(Computer) by ManagementGroupName | Distribuzione per gruppo di gestione |
| Type=Heartbeat&#124;measure countdistinct(Computer) by RemoteIPCountry |Posizione geografica degli agenti |
| Type=Heartbeat IsGatewayInstalled=true&#124;Distinct Computer |Numero di gateway OMS installati |


>[!NOTE]
> Se l'area di lavoro è stata aggiornata al [nuovo linguaggio di query di Log Analytics](../log-analytics/log-analytics-log-search-upgrade.md), le query precedenti verranno sostituite da quelle seguenti.
>
>| Query | Descrizione |
|:---|:---|
| Heartbeat &#124; distinct Computer |Numero totale di agenti |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Conteggio degli agenti che non hanno risposto nelle ultime 24 ore |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Conteggio degli agenti che non hanno risposto negli ultimi 15 minuti |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Computer online (nelle ultime 24 ore) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Totale degli agenti offline negli ultimi 30 minuti (per le ultime 24 ore) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Tendenza del numero di agenti nel tempo per OSType|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Distribution by OS Type (Distribuzione per tipo di sistema operativo) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Distribuzione per versione dell'agente |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Distribuzione per categoria dell'agente |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Distribuzione per gruppo di gestione |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Posizione geografica degli agenti |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Numero di gateway OMS installati |

## <a name="next-steps"></a>Passaggi successivi

* Leggere l'articolo [Avvisi in Log Analytics](../log-analytics/log-analytics-alerts.md) per informazioni dettagliate sulla generazione di avvisi di Log Analytics.
