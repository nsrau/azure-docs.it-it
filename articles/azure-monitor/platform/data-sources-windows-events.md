---
title: Raccogliere e analizzare registri eventi di Windows in Log Analytics | Microsoft Docs
description: Descrive come configurare la raccolta di registri eventi di Windows tramite Log Analytics con informazioni dettagliate sui record creati.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: a60c5c41c3f7f0c26788aa9f986af076d9e82c2f
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54102602"
---
# <a name="windows-event-log-data-sources-in-log-analytics"></a>Origini dei dati del registro eventi di Windows in Log Analytics
I registri eventi di Windows rappresentano una delle più comuni [origini dati](agent-data-sources.md) per raccogliere i dati tramite gli agenti di Windows, dal momento che molte applicazioni scrivono nel registro eventi di Windows.  È possibile raccogliere gli eventi dai log standard, ad esempio sistema e applicazioni, oltre a specificare qualsiasi log personalizzato creato dalle applicazioni da monitorare.

![Eventi Windows](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Configurazione dei registri eventi di Windows
Configurare i registri eventi di Windows nel [menu Dati in Impostazioni avanzate](agent-data-sources.md#configuring-data-sources).

Log Analytics raccoglie solo gli eventi dai registri eventi di Windows che vengono specificati nelle impostazioni.  È possibile aggiungere un registro eventi digitandone il nome e facendo clic su **+**.  Per ogni log vengono raccolti solo gli eventi con i livelli di gravità selezionati.  Controllare i livelli di gravità del log specifico da raccogliere.  Non è possibile specificare altri criteri per filtrare gli eventi.

Mentre si digita il nome di un registro eventi, Log Analytics fornisce suggerimenti sui nomi comunemente usati per il registro eventi. Se il registro che si desidera aggiungere non viene visualizzato nell'elenco, è possibile aggiungerlo digitandone il nome completo. È possibile trovare il nome completo del registro tramite il Visualizzatore eventi. Nel Visualizzatore eventi, aprire la pagina *Proprietà* del registro e copiare la stringa dal campo *Nome completo*.

![Configurare gli eventi di Windows](media/data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Raccolta dei dati
Log Analytics raccoglie ogni evento corrispondente a un livello di gravità selezionato da un registro eventi monitorato quando viene creato l'evento.  L'agente registra la propria posizione in ogni registro eventi da cui esegue la raccolta.  Se l'agente risulta offline per un certo periodo di tempo, raccoglie gli eventi dal momento in cui è stato interrotto, anche se gli eventi sono stati creati mentre l'agente era offline.  Esiste la possibilità che questi eventi non vengano raccolti se il registro eventi esegue il wrapping con eventi non raccolti che vengono sovrascritti mentre l'agente è offline.

>[!NOTE]
>Log Analytics non raccoglie gli eventi di controllo creati da SQL Server dal *MSSQLSERVER* di origine con l'ID evento 18453 che contiene le parole chiave  *Classico* o *Successo del controllo* e la parola chiave *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Proprietà dei record eventi di Windows
I record eventi di Windows sono di tipo **Evento** ; nella tabella seguente vengono riportate le loro proprietà:

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| Computer |Nome del computer da cui è stato raccolto l'evento. |
| EventCategory |Categoria dell'evento. |
| EventData |Tutti i dati dell'evento in formato non elaborato. |
| EventID |Numero di evento. |
| EventLevel |Gravità dell'evento in formato numerico. |
| EventLevelName |Gravità dell'evento in formato di testo. |
| EventLog |Nome del registro eventi da cui è stato raccolto l'evento. |
| ParameterXml |Valori dei parametri dell'evento in formato XML. |
| ManagementGroupName |Nome del gruppo di gestione per gli agenti di System Center Operations Manager.  Per gli altri agenti, questo valore corrisponde a AOI-<workspace ID> |
| RenderedDescription |Descrizione dell'evento con i valori dei parametri. |
| Sorgente |Origine dell'evento. |
| SourceSystem |Tipo di agente da cui è stato raccolto l'evento. <br> OpsManager: agente Windows, con connessione diretta o gestita da Operations Manager <br>  Linux – Tutti gli agenti Linux  <br>  AzureStorage: Diagnostica di Azure |
| TimeGenerated |Data e ora in cui l'evento è stato creato in Windows. |
| UserName |Nome utente dell'account che ha registrato l'evento. |

## <a name="log-queries-with-windows-events"></a>Query di log con Eventi Windows
La tabella seguente mostra alcuni esempi di query di log che recuperano i record di Eventi Windows.

| Query | DESCRIZIONE |
|:---|:---|
| Event |Tutti gli eventi di Windows. |
| Event &#124; where EventLevelName == "error" |Tutti gli eventi di Windows con livello di gravità dell'errore. |
| Event &#124; summarize count() by Source |Numero di eventi di Windows per origine. |
| Event &#124; where EventLevelName == "error" &#124; summarize count() by Source |Numero di eventi di errore di Windows per origine. |


## <a name="next-steps"></a>Passaggi successivi
* Configurare Log Analytics per raccogliere altre [origini dati](agent-data-sources.md) per l'analisi.
* Altre informazioni sulle [query di log](../../log-analytics/log-analytics-queries.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.  
* Configurare la [raccolta dei contatori delle prestazioni](data-sources-performance-counters.md) dagli agenti di Windows.
