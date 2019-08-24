---
title: Raccogliere e analizzare log eventi di Windows in Monitoraggio di Azure | Microsoft Docs
description: Descrive come configurare la raccolta di log di Eventi Windows tramite il Monitoraggio di Azure e i dettagli dei record creati.
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
ms.openlocfilehash: cc81a8d8023d0724f4ecb71c157e8f575aa9edc8
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997483"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Origini dei dati del registro eventi di Windows in Monitoraggio di Azure
I log eventi di Windows rappresentano una delle più comuni [origini dati](agent-data-sources.md) per raccogliere i dati tramite gli agenti di Windows, dal momento che molte applicazioni scrivono nel log eventi di Windows.  È possibile raccogliere gli eventi dai log standard, ad esempio sistema e applicazioni, oltre a specificare qualsiasi log personalizzato creato dalle applicazioni da monitorare.

![Eventi Windows](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Configurazione dei log eventi di Windows
Configurare i log eventi di Windows nel [menu Dati in Impostazioni avanzate](agent-data-sources.md#configuring-data-sources).

Monitoraggio di Azure raccoglie solo gli eventi dai log eventi di Windows che vengono specificati nelle impostazioni.  È possibile aggiungere un registro eventi digitandone il nome e facendo clic su **+** .  Per ogni log vengono raccolti solo gli eventi con i livelli di gravità selezionati.  Controllare i livelli di gravità del log specifico da raccogliere.  Non è possibile specificare altri criteri per filtrare gli eventi.

Mentre si digita il nome di un registro eventi, Monitoraggio di Azure fornisce suggerimenti sui nomi comunemente usati per il registro eventi. Se il registro che si desidera aggiungere non viene visualizzato nell'elenco, è possibile aggiungerlo digitandone il nome completo. È possibile trovare il nome completo del registro tramite il Visualizzatore eventi. Nel Visualizzatore eventi, aprire la pagina *Proprietà* del registro e copiare la stringa dal campo *Nome completo*.

![Configurare gli eventi di Windows](media/data-sources-windows-events/configure.png)

> [!NOTE]
> Gli eventi critici del registro eventi di Windows avranno un livello di gravità "errore" nei log di monitoraggio di Azure.

## <a name="data-collection"></a>Raccolta dei dati
Monitoraggio di Azure raccoglie ogni evento corrispondente a un livello di gravità selezionato da un registro eventi monitorato quando viene creato l'evento.  L'agente registra la propria posizione in ogni registro eventi da cui esegue la raccolta.  Se l'agente risulta offline per un certo periodo di tempo, raccoglie gli eventi dal momento in cui è stato interrotto, anche se gli eventi sono stati creati mentre l'agente era offline.  Esiste la possibilità che questi eventi non vengano raccolti se il registro eventi esegue il wrapping con eventi non raccolti che vengono sovrascritti mentre l'agente è offline.

>[!NOTE]
>Monitoraggio di Azure non raccoglie gli eventi di controllo creati da SQL Server dal *MSSQLSERVER* di origine con l'ID evento 18453 che contiene le parole chiave *Classico* o *Successo del controllo* e la parola chiave *0xa0000000000000*.
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
| ManagementGroupName |Nome del gruppo di gestione per gli agenti di System Center Operations Manager.  Per gli altri agenti, questo valore è`AOI-<workspace ID>` |
| RenderedDescription |Descrizione dell'evento con i valori dei parametri. |
| Source (Sorgente) |Origine dell'evento. |
| SourceSystem |Tipo di agente da cui è stato raccolto l'evento. <br> OpsManager: agente Windows, con connessione diretta o gestita da Operations Manager <br> Linux – Tutti gli agenti Linux  <br> AzureStorage: Diagnostica di Azure |
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
* Altre informazioni sulle [query di log](../log-query/log-query-overview.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.  
* Configurare la [raccolta dei contatori delle prestazioni](data-sources-performance-counters.md) dagli agenti di Windows.
