---
title: Avviso su problemi in Servizi cloud di Azure tramite l'integrazione di Diagnostica di Azure con Azure Application Insights | Microsoft Docs
description: Monitorare problemi come errori di avvio, arresti anomali e cicli di riciclo ruolo in Servizi cloud di Azure con Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: harelbr; mbullwin
ms.openlocfilehash: 18817fd84a86a72d379f96973b71658f2cdf4afd
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851121"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Avviso su problemi in Servizi cloud di Azure tramite l'integrazione di Diagnostica di Azure con Azure Application Insights

In questo articolo viene illustrato come configurare le regole di avviso che eseguono il monitoraggio di problemi come errori di avvio, arresti anomali e cicli di riciclo ruolo in Servizi cloud di Azure (ruoli Web e di lavoro).

Il metodo descritto in questo articolo si basa sull'[integrazione di Diagnostica di Azure con Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/) e la funzionalità relativa agli [avvisi di log per Application Insights](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) rilasciata di recente.

## <a name="define-a-base-query"></a>Definire una query di base

Per iniziare, verrà definita una query di base che recupera gli eventi del registro eventi di Windows dal canale di Windows Azure che vengono acquisiti in Application Insights come record di traccia.
Questi record possono essere usati per rilevare una serie di problemi nei Servizi cloud di Azure, ad esempio errori di avvio, errori di runtime e cicli di riciclo ruolo.

> [!NOTE]
> La query di base seguente verifica la presenza di problemi in una finestra temporale di 30 minuti e presuppone una latenza di 10 minuti nell'inserimento dei record di dati di telemetria. Queste impostazioni predefinite possono essere configurate in base alle esigenze.

```
let window = 30m;
let endTime = ago(10m);
let EventLogs = traces
| where timestamp > endTime - window and timestamp < endTime
| extend channel = tostring(customDimensions.Channel), eventId = tostring(customDimensions.EventId)
| where channel == 'Windows Azure' and isnotempty(eventId)
| where tostring(customDimensions.DeploymentName) !contains 'deployment' // discard records captured from local machines
| project timestamp, channel, eventId, message, cloud_RoleInstance, cloud_RoleName, itemCount;
```

## <a name="check-for-specific-event-ids"></a>Ricerca di ID evento specifici

Dopo aver recuperato gli eventi del registro eventi di Windows, i problemi specifici possono essere rilevati ricercando le rispettive proprietà di messaggio e ID evento (vedere gli esempi di seguito).
È sufficiente combinare la query di base indicata in precedenza con una delle query riportate di seguito e usare tale query combinata al momento della definizione della regola di avviso log.

> [!NOTE]
> Negli esempi seguenti verrà rilevato un problema se vengono trovati più di tre eventi durante l'intervallo di tempo analizzato. Questa impostazione predefinita può essere configurata per modificare la sensibilità della regola di avviso.

```
// Detect failures in the OnStart method
EventLogs
| where eventId == '2001'
| where message contains '.OnStart()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures during runtime
EventLogs
| where eventId == '2001'
| where message contains '.Run()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures when running a startup task
EventLogs
| where eventId == '1000'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect recycle loops
EventLogs
| where eventId == '1006'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

## <a name="create-an-alert"></a>Creare un avviso

Nel menu di navigazione all'interno della risorsa di Application Insights andare ad **Avvisi** e quindi selezionare **Nuova regola di avviso**.

![Schermata di creazione regola](./media/app-insights-proactive-cloud-services/001.png)

Nella finestra **Crea regola** nella sezione **Definire la condizione dell'avviso** fare clic su **Aggiungi criteri** e quindi selezionare **Custom log search** (Ricerca log personalizzata).

![Schermata di definizione dei criteri condizionali per l'avviso](./media/app-insights-proactive-cloud-services/002.png)

Nella casella **Query di ricerca** incollare la query combinata preparata nel passaggio precedente.

Continuare quindi con la casella **Soglia** e impostarne il valore su 0. È possibile eventualmente modificare i campi **Periodo** e **Frequenza**.
Fare clic su **Done**.

![Schermata di configurazione della query logica dei segnali](./media/app-insights-proactive-cloud-services/003.png)

Nella sezione **Definire i dettagli dell'avviso** specificare un **Nome** e una **Descrizione** per la regola di avviso e quindi impostarne la **Gravità**.
Verificare anche che il pulsante **Abilita regola alla creazione** sia impostato su **Sì**.

![Schermata dei dettagli degli avvisi](./media/app-insights-proactive-cloud-services/004.png)

Nella sezione **Definire il gruppo di azioni** è possibile selezionare un **Gruppo di azioni** esistente o crearne uno nuovo.
È possibile scegliere che il gruppo di azioni contenga più azioni di diversi tipi.

![Schermata del gruppo di azioni](./media/app-insights-proactive-cloud-services/005.png)

Dopo aver definito il gruppo di azioni, confermare le modifiche e fare clic su **Crea regola di avviso**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul rilevamento automatico:

[Anomalie degli errori](app-insights-proactive-failure-diagnostics.md)
[Perdita di memoria](app-insights-proactive-potential-memory-leak.md)
[Anomalie delle prestazioni](app-insights-proactive-performance-diagnostics.md)

