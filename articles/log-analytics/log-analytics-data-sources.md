<properties 
   pageTitle="Origini dati in Log Analytics | Microsoft Azure"
   description="Le origini dati definiscono i dati raccolti in Log Analytics da agenti e altre origini connesse. Questo articolo descrive come Log Analytics usa le origini dati, illustra i dettagli su come configurarle e fornisce un riepilogo delle diverse origini dati disponibili."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="bwren" />

# Origini dati in Log Analytics

Log Analytics raccoglie i dati dalle origini connesse nell'area di lavoro OMS e li archivia nel repository OMS. I dati raccolti da ogni origine sono definiti dalle origini dati configurate. I dati nel repository OMS vengono archiviati come set di record. Ogni origine dati crea record di un tipo specifico in cui ogni tipo ha un proprio set di proprietà.

![Raccolta dei dati di Log Analytics](./media/log-analytics-data-sources/overview.png)

Le origini dati sono diverse rispetto alle soluzioni OMS che raccolgono i dati da origini connesse e creano record nel repository OMS. Le soluzioni possono essere aggiunte all'area di lavoro dalla raccolta di soluzioni e forniscono in genere strumenti di analisi aggiuntivi nel portale di OMS.

## Riepilogo delle origini dati

Nella tabella seguente sono elencate le origini dati attualmente disponibili in Log Analytics. Ogni origine dati ha un collegamento a un articolo distinto che fornisce informazioni dettagliate.

| Origine dati | Tipo evento | Descrizione |
|:--|:--|:--|
| [Log personalizzati](log-analytics-data-sources-custom-logs.md) | <NomeLog>\_CL | File di testo negli agenti di Windows o Linux contenenti informazioni di log. | | [Log eventi di Windows](log-analytics-data-sources-windows-events.md) | Evento | Eventi raccolti dal log eventi sui computer Windows. | | [Contatori delle prestazioni di Windows](log-analytics-data-sources-performance-counters.md) | Prestazioni | Contatori delle prestazioni raccolti dai computer Windows. | | [Contatori delle prestazioni di Linux](log-analytics-data-sources-performance-counters.md) | Prestazioni | Contatori delle prestazioni raccolti dai computer Linux. | | [Log IIS](log-analytics-data-sources-iis-logs.md) | W3CIISLog | Log di Internet Information Services in formato W3C. | | SysLog | SysLog | Eventi di SysLog nei computer Windows o Linux. |

## Configurazione delle origini dati

Configurare le origini dati nel menu **Dati** nelle **Impostazioni** di Log Analytics. Qualsiasi configurazione viene recapitata a tutte le origini connesse nell'area di lavoro OMS. Attualmente non è possibile escludere gli agenti da questa configurazione.

![Configurare gli eventi di Windows](./media/log-analytics-data-sources/configure-events.png)

2. Nella console di OMS selezionare il riquadro **Impostazioni**.
3. Selezionare **Dati**.
4. Fare clic sull'origine dati da configurare.
5. Per informazioni dettagliate relative alla configurazione, seguire il collegamento alla documentazione per ogni origine dati nella tabella precedente.

## Raccolta dei dati

Le configurazioni dell'origine dati vengono distribuite agli agenti connessi direttamente a OMS entro pochi minuti. I dati specificati vengono raccolti dall'agente e distribuiti direttamente a Log Analytics a intervalli specifici per ogni origine dati. Per informazioni sugli intervalli specifici di ogni origine dati, vedere la documentazione.

Per gli agenti di System Center Operations Manager (SCOM) in un gruppo di gestione connesso, le configurazioni dell'origine dati vengono convertite in Management Pack e recapitate al gruppo di gestione ogni 5 minuti per impostazione predefinita. L'agente scarica il Management Pack, raccoglie i dati specificati e li invia a un server di gestione che inoltra i dati a OMS. L'agente non richiede alcuna comunicazione diretta con OMS per tutte le origini dati. È possibile leggere informazioni sulla connessione di SCOM e OMS e sulla modifica della frequenza a cui tale configurazione viene distribuita nell'articolo relativo alla [configurazione dell'integrazione con System Center Operations Manager](log-analytics-om-agents.md).

## Record di Log Analytics

Tutti i dati raccolti da Log Analytics vengono archiviati nel repository OMS come record. I record raccolti da diverse origini dati avranno il proprio set di proprietà e verranno identificati dalla proprietà **Type**. Per informazioni dettagliate su ogni tipo di record, vedere la documentazione per ogni origine dati e soluzione.


## Passaggi successivi

- Informazioni sulle [soluzioni](log-analytics-add-solutions.md) che aggiungono funzionalità a Log Analytics e raccolgono dati nel repository OMS.
- Informazioni sulle [ricerche nei log](log-analytics-log-searches.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.  
- Configurare gli avvisi per inviare notifiche immediate sui dati critici raccolti da origini dati e soluzioni.

<!---HONumber=AcomDC_0504_2016-->