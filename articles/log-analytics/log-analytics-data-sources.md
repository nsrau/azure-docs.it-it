---
title: Configurare origini dati in Log Analytics di OMS | Documentazione Microsoft
description: Le origini dati definiscono i dati raccolti in Log Analytics da agenti e altre origini connesse.  Questo articolo descrive come Log Analytics usa le origini dati, illustra i dettagli su come configurarle e fornisce un riepilogo delle diverse origini dati disponibili.
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2017
ms.author: bwren
ms.openlocfilehash: 00d030a502cf70ea9a5dea767f560cdf2919573e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="data-sources-in-log-analytics"></a>Origini dati in Log Analytics
Log Analytics raccoglie i dati dalle origini connesse nell'area di lavoro OMS e li archivia nel repository OMS.  I dati raccolti da ogni origine sono definiti dalle origini dati configurate.  I dati nel repository OMS vengono archiviati come set di record.  Ogni origine dati crea record di un tipo specifico in cui ogni tipo ha un proprio set di proprietà.

![Raccolta dei dati di Log Analytics](./media/log-analytics-data-sources/overview.png)

Le origini dati sono diverse rispetto alle soluzioni OMS che raccolgono i dati da origini connesse e creano record nel repository OMS.  Le soluzioni possono essere aggiunte all'area di lavoro dalla raccolta di soluzioni e forniscono in genere strumenti di analisi aggiuntivi nel portale di OMS.  

## <a name="summary-of-data-sources"></a>Riepilogo delle origini dati
Nella tabella seguente sono elencate le origini dati attualmente disponibili in Log Analytics.  Ogni origine dati ha un collegamento a un articolo distinto che fornisce informazioni dettagliate.

| Origine dati | Tipo evento | Descrizione |
|:--- |:--- |:--- |
| [Log personalizzati](log-analytics-data-sources-custom-logs.md) |\<LogName\>_CL |File di testo negli agenti Windows o Linux contenenti le informazioni di log. |
| [Log eventi di Windows](log-analytics-data-sources-windows-events.md) |Evento |Eventi raccolti dal registro eventi nei computer Windows. |
| [Contatori delle prestazioni di Windows](log-analytics-data-sources-performance-counters.md) |Perf |Contatori delle prestazioni raccolti dai computer Windows. |
| [Contatori delle prestazioni di Linux](log-analytics-data-sources-performance-counters.md) |Perf |Contatori delle prestazioni raccolti dai computer Linux. |
| [Log IIS](log-analytics-data-sources-iis-logs.md) |W3CIISLog |Log di Internet Information Services in formato W3C. |
| [Syslog](log-analytics-data-sources-syslog.md) |syslog |Eventi syslog nei computer Windows o Linux. |

## <a name="configuring-data-sources"></a>Configurazione delle origini dati
Configurare le origini dati nel menu **Dati** in **Impostazioni** di Log Analytics.  Qualsiasi configurazione viene recapitata a tutte le origini connesse nell'area di lavoro OMS.  Attualmente non è possibile escludere gli agenti da questa configurazione.

![Configurare gli eventi di Windows](./media/log-analytics-data-sources/configure-events.png)

1. Nella console OMS fare clic sul riquadro **Impostazioni** o sul pulsante **Impostazioni** nella parte superiore della schermata.
2. Selezionare **Dati**.
3. Fare clic sull'origine dati da configurare.
4. Per informazioni dettagliate relative alla configurazione, seguire il collegamento alla documentazione per ogni origine dati nella tabella precedente.

> [!NOTE]
> Attualmente non è possibile configurare le origini dati di Log Analytics nel portale di Azure.

## <a name="data-collection"></a>Raccolta dei dati
Le configurazioni dell'origine dati vengono distribuite agli agenti connessi direttamente a Log Analytics entro pochi minuti.  I dati specificati vengono raccolti dall'agente e distribuiti direttamente a Log Analytics a intervalli specifici per ogni origine dati.  Per informazioni sugli intervalli specifici di ogni origine dati, vedere la documentazione.

Per gli agenti di System Center Operations Manager (SCOM) in un gruppo di gestione connesso, le configurazioni dell'origine dati vengono convertite in Management Pack e recapitate al gruppo di gestione ogni 5 minuti per impostazione predefinita.  L'agente scarica il Management Pack e raccoglie i dati specificati. A seconda dell'origine dati, i dati verranno inviati a un server di gestione che inoltra i dati a Log Analytics oppure l'agente invierà i dati a Log Analytics senza passare per il server di gestione. Vedere [Dettagli di raccolta dati per le funzionalità e le soluzioni OMS](log-analytics-add-solutions.md#data-collection-details) per altre informazioni.  È possibile leggere informazioni sulla connessione di SCOM e OMS e sulla modifica della frequenza a cui tale configurazione viene distribuita nell'articolo relativo alla [configurazione dell'integrazione con System Center Operations Manager](log-analytics-om-agents.md).

Se l'agente non riesce a connettersi a Log Analytics o a Operations Manager, continuerà a raccogliere dati che distribuirà quando stabilirà una connessione.  I dati possono andare persi se la quantità di dati raggiunge la dimensione massima della cache per il client o se l'agente non riesce a stabilire una connessione entro 24 ore.

## <a name="log-analytics-records"></a>Record di Log Analytics
Tutti i dati raccolti da Log Analytics vengono archiviati nel repository OMS come record.  I record raccolti da diverse origini dati avranno il proprio set di proprietà e verranno identificati dalla proprietà **Type** .  Per informazioni dettagliate su ogni tipo di record, vedere la documentazione per ogni origine dati e soluzione.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle [soluzioni](log-analytics-add-solutions.md) che aggiungono funzionalità a Log Analytics e raccolgono dati nel repository OMS.
* Informazioni sulle [ricerche nei log](log-analytics-log-searches.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.  
* Configurare gli [avvisi](log-analytics-alerts.md) per inviare notifiche immediate sui dati critici raccolti da origini dati e soluzioni.
