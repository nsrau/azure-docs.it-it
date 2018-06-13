---
title: Configurare le origini dati in Azure Log Analytics | Microsoft Docs
description: Le origini dati definiscono i dati raccolti in Log Analytics da agenti e altre origini connesse.  Questo articolo descrive come Log Analytics usa le origini dati, illustra i dettagli su come configurarle e fornisce un riepilogo delle diverse origini dati disponibili.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2018
ms.author: bwren
ms.openlocfilehash: 5201d02b4f70f964f39b4fe135e4715732b9741a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792549"
---
# <a name="data-sources-in-log-analytics"></a>Origini dati in Log Analytics
Log Analytics raccoglie i dati dalle origini connesse e li archivia nell'area di lavoro di Log Analytics.  I dati raccolti da ogni origine sono definiti dalle origini dati configurate.  In Log Analytics i dati vengono archiviati come un set di record.  Ogni origine dati crea record di un tipo specifico in cui ogni tipo ha un proprio set di proprietà.

![Raccolta dei dati di Log Analytics](./media/log-analytics-data-sources/overview.png)

Le origini dati sono diverse rispetto alle [soluzioni di gestione](log-analytics-add-solutions.md) che raccolgono anche dati da origini connesse e creano record in Log Analytics.  Oltre a raccogliere i dati, le soluzioni in genere includono le ricerche log e le viste che consentono di analizzare il funzionamento di un determinato servizio o di un'applicazione.


## <a name="summary-of-data-sources"></a>Riepilogo delle origini dati
Nella tabella seguente sono elencate le origini dati attualmente disponibili in Log Analytics.  Ogni origine dati ha un collegamento a un articolo distinto che fornisce informazioni dettagliate.   Sono inoltre disponibili informazioni sul metodo e la frequenza della raccolta dati in Log Analytics.  È possibile usare le informazioni in questo articolo per identificare le diverse soluzioni disponibili e per comprendere i requisiti di flusso di dati e connessione per le diverse soluzioni di gestione. Per una spiegazione delle colonne, vedere [Informazioni dettagliate sulla raccolta dati per le soluzioni di gestione in Azure](../monitoring/monitoring-solutions-inventory.md).


| Origine dati | Piattaforma | Microsoft Monitoring Agent | Agente di Operations Manager | Archiviazione di Azure | È necessario Operations Manager? | Dati dell'agente Operations Manager inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [Log personalizzati](log-analytics-data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | all'arrivo |
| [Log personalizzati](log-analytics-data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | all'arrivo |
| [Log IIS](log-analytics-data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |5 minuti |
| [Contatori delle prestazioni](log-analytics-data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |come pianificato, almeno 10 secondi |
| [Contatori delle prestazioni](log-analytics-data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |come pianificato, almeno 10 secondi |
| [Syslog](log-analytics-data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |dall'Archiviazione di Azure: 10 minuti; dall'agente: all'arrivo |
| [Log eventi di Windows](log-analytics-data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | all'arrivo |


## <a name="configuring-data-sources"></a>Configurazione delle origini dati
Configurare le origini dati nel menu **Dati** in **Impostazioni avanzate** di Log Analytics.  Qualsiasi configurazione viene recapitata a tutte le origini connesse nell'area di lavoro.  Attualmente non è possibile escludere gli agenti da questa configurazione.

![Configurare gli eventi di Windows](./media/log-analytics-data-sources/configure-events.png)

1. Nel portale di Azure selezionare **Log Analytics** > area di lavoro personale > **Impostazioni avanzate**.
2. Selezionare **Dati**.
3. Fare clic sull'origine dati che si desidera configurare.
4. Per informazioni dettagliate relative alla configurazione, seguire il collegamento alla documentazione per ogni origine dati nella tabella precedente.


## <a name="data-collection"></a>Raccolta dei dati
Le configurazioni dell'origine dati vengono distribuite agli agenti connessi direttamente a Log Analytics entro pochi minuti.  I dati specificati vengono raccolti dall'agente e distribuiti direttamente a Log Analytics a intervalli specifici per ogni origine dati.  Per informazioni sugli intervalli specifici di ogni origine dati, vedere la documentazione.

Per gli agenti di System Center Operations Manager in un gruppo di gestione connesso, le configurazioni dell'origine dati vengono convertite in Management Pack e recapitate al gruppo di gestione ogni 5 minuti per impostazione predefinita.  L'agente scarica il Management Pack e raccoglie i dati specificati. A seconda dell'origine dati, i dati verranno inviati a un server di gestione che li inoltrerà a Log Analytics oppure verranno inviati dall'agente a Log Analytics senza passare per il server di gestione. Per informazioni approfondite fare riferimento ai [dettagli sulla raccolta dei dati](log-analytics-add-solutions.md#data-collection-details).  È possibile leggere informazioni sulla connessione di Operations Manager e Log Analytics e sulla modifica della frequenza a cui tale configurazione viene distribuita nell'articolo relativo alla [configurazione dell'integrazione con System Center Operations Manager](log-analytics-om-agents.md).

Se l'agente non riesce a connettersi a Log Analytics o a Operations Manager, continuerà a raccogliere dati che distribuirà quando stabilirà una connessione.  I dati possono andare persi se la quantità di dati raggiunge la dimensione massima della cache per il client o se l'agente non riesce a stabilire una connessione entro 24 ore.

## <a name="log-analytics-records"></a>Record di Log Analytics
Tutti i dati raccolti da Log Analytics vengono archiviati nell'area di lavoro come record.  I record raccolti da diverse origini dati avranno il proprio set di proprietà e verranno identificati dalla proprietà **Type** .  Per informazioni dettagliate su ogni tipo di record, vedere la documentazione per ogni origine dati e soluzione.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle [soluzioni](log-analytics-add-solutions.md) che aggiungono funzionalità a Log Analytics e raccolgono dati nell'area di lavoro.
* Altre informazioni sulle [ricerche nei log](log-analytics-log-searches.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.  
* Configurare gli [avvisi](log-analytics-alerts.md) per inviare notifiche immediate sui dati critici raccolti da origini dati e soluzioni.
