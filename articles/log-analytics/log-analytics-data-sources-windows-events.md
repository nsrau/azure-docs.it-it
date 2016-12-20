---
title: Registri eventi di Windows in Log Analytics | Documentazione Microsoft
description: "I registri eventi di Windows sono una delle origini dati più comuni usate da Log Analytics.  Questo articolo descrive come configurare una raccolta di log di Eventi Windows e i dettagli dei record creati nel repository OMS."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 055a60c174c280ba56ea40cb47779e233b54fe8e
ms.openlocfilehash: 867dc9fd84793debc8c8dcd2a27dfc0b0ccf69c4


---
# <a name="windows-event-log-data-sources-in-log-analytics"></a>Origini dei dati del registro eventi di Windows in Log Analytics
I registri eventi di Windows rappresentano una delle più comuni [origini dati](log-analytics-data-sources.md) per raccogliere i dati tramite gli agenti di Windows, dal momento che molte applicazioni scrivono nel registro eventi di Windows.  È possibile raccogliere gli eventi dai log standard, ad esempio sistema e applicazioni, oltre a specificare qualsiasi log personalizzato creato dalle applicazioni da monitorare.

![Eventi Windows](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Configurazione dei registri eventi di Windows
Configurare i registri eventi di Windows nel [menu Dati in Impostazioni di Log Analytics](log-analytics-data-sources.md#configuring-data-sources).

Log Analytics raccoglie solo gli eventi dai registri eventi di Windows che vengono specificati nelle impostazioni.  È possibile aggiungere un registro eventi digitandone il nome e facendo clic su **+**.  Per ogni log vengono raccolti solo gli eventi con i livelli di gravità selezionati.  Controllare i livelli di gravità del log specifico da raccogliere.  Non è possibile specificare altri criteri per filtrare gli eventi.

Mentre si digita il nome di un registro eventi, Log Analytics fornisce suggerimenti sui nomi comunemente usati per il registro eventi. Se il registro che si desidera aggiungere non viene visualizzato nell'elenco, è possibile aggiungerlo digitandone il nome completo. È possibile trovare il nome completo del registro tramite il Visualizzatore eventi. Nel Visualizzatore eventi, aprire la pagina *Proprietà* del registro e copiare la stringa dal campo *Nome completo*.

![Configurare gli eventi di Windows](media/log-analytics-data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Raccolta dei dati
Log Analytics raccoglie ogni evento corrispondente a un livello di gravità selezionato da un registro eventi monitorato quando viene creato l'evento.  L'agente registra la propria posizione in ogni registro eventi da cui esegue la raccolta.  Se l'agente risulta offline per un certo periodo di tempo, Log Analytics raccoglie gli eventi dal momento in cui è stato interrotto, anche se gli eventi sono stati creati mentre l'agente era offline.

## <a name="windows-event-records-properties"></a>Proprietà dei record eventi di Windows
I record eventi di Windows sono di tipo **Evento** ; nella tabella seguente vengono riportate le loro proprietà:

| Proprietà | Descrizione |
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
| Source |Origine dell'evento. |
| SourceSystem |Tipo di agente da cui è stato raccolto l'evento. <br> OpsManager: agente Windows, con connessione diretta o gestita da Operations Manager <br>  Linux – Tutti gli agenti Linux  <br>  AzureStorage: Diagnostica di Azure |
| TimeGenerated |Data e ora in cui l'evento è stato creato in Windows. |
| UserName |Nome utente dell'account che ha registrato l'evento. |

## <a name="log-searches-with-windows-events"></a>Ricerche nei log con Eventi Windows
La tabella seguente mostra alcuni esempi di ricerche nei log che recuperano i record di Eventi Windows.

| Query | Descrizione |
|:--- |:--- |
| Tipo=Event |Tutti gli eventi di Windows. |
| Tipo=Event EventLevelName=error |Tutti gli eventi di Windows con livello di gravità dell'errore. |
| Tipo=Event &#124; Measure count() by Source |Numero di eventi di Windows per origine. |
| Tipo=Event EventLevelName=error &#124; Measure count() by Source |Numero di eventi di errore di Windows per origine. |

## <a name="next-steps"></a>Passaggi successivi
* Configurare Log Analytics per raccogliere altre [origini dati](log-analytics-data-sources.md) per l'analisi.
* Altre informazioni sulle [ricerche nei log](log-analytics-log-searches.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.  
* Usare i [Campi personalizzati](log-analytics-custom-fields.md) per analizzare i record degli eventi nei singoli campi.
* Configurare la [raccolta dei contatori delle prestazioni](log-analytics-data-sources-performance-counters.md) dagli agenti di Windows.




<!--HONumber=Nov16_HO3-->


