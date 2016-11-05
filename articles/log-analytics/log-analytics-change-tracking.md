---
title: Soluzione di rilevamento modifiche in Log Analytics | Microsoft Docs
description: È possibile usare la soluzione Change Tracking (Rilevamento modifiche) relativa alla configurazione in Log Analytics per identificare facilmente le modifiche apportate al software e ai servizi Windows nell'ambiente in uso. Questa funzionalità può semplificare l'individuazione dei problemi operativi.
services: operations-management-suite
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: operations-management-suite
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders

---
# <a name="change-tracking-solution-in-log-analytics"></a>Soluzione di rilevamento modifiche in Log Analytics
È possibile usare la soluzione di rilevamento delle modifiche di configurazione in Log Analytics per identificare facilmente le modifiche apportate al software, ai servizi Windows e ai daemon Linux nell'ambiente in uso. Questa funzionalità può semplificare l'individuazione dei problemi operativi.

La soluzione viene installata per aggiornare il tipo di agente che è stato installato. Vengono lette le modifiche al software installato e ai servizi Windows nei server monitorati e i dati vengono inviati al servizio Log Analytics nel cloud per l'elaborazione. Viene applicata la logica ai dati ricevuti, quindi questi ultimi vengono registrati nel servizio cloud. I server in cui vengono rilevate le modifiche vengono visualizzati nel dashboard Change Tracking. Usando le informazioni nel dashboard Change Tracking, è possibile visualizzare facilmente le modifiche apportate all'infrastruttura del server.

## <a name="installing-and-configuring-the-solution"></a>Installazione e configurazione della soluzione
Usare le informazioni seguenti per installare e configurare la soluzione.

* Per la soluzione di rilevamento modifiche è richiesto Operations Manager.
* È necessario avere un agente di Windows o un agente Operations Manager su ciascun computer in cui si desidera monitorare le modifiche.
* Aggiungere la soluzione di rilevamento modifiche all'area di lavoro OMS usando la procedura descritta nell'articolo sull' [aggiunta di soluzioni di Log Analytics dalla raccolta soluzioni](log-analytics-add-solutions.md).  Non è richiesta alcuna ulteriore configurazione.

## <a name="change-tracking-data-collection-details"></a>Informazioni dettagliate sulla raccolta dei dati di Change Tracking
Change Tracking consente di raccogliere l'inventario software e i metadati del servizio Windows tramite gli agenti abilitati.

La tabella seguente mostra i metodi di raccolta di dati e altre informazioni dettagliate sul modo in cui vengono raccolti i dati per Change Tracking.

| piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![Sì](./media/log-analytics-change-tracking/oms-bullet-green.png) |![Sì](./media/log-analytics-change-tracking/oms-bullet-green.png) |![No](./media/log-analytics-change-tracking/oms-bullet-red.png) |![No](./media/log-analytics-change-tracking/oms-bullet-red.png) |![Sì](./media/log-analytics-change-tracking/oms-bullet-green.png) |ogni ora |

## <a name="use-change-tracking"></a>Uso di Change Tracking
Dopo l'installazione della soluzione, è possibile visualizzare il riepilogo delle modifiche per i server monitorati usando il riquadro **Rilevamento modifiche** nella pagina **Panoramica** di OMS.

![immagine del riquadro Change Tracking](./media/log-analytics-change-tracking/oms-changetracking-tile.png)

È possibile vedere le modifiche all'infrastruttura e quindi analizzare i dettagli per le categorie seguenti:

* Modifiche per tipo di configurazione per i servizi software e di Windows.
* Modifiche software ad applicazioni e aggiornamenti per singoli server.
* Numero totale di modifiche software per ogni applicazione.
* Modifiche dei servizi di Windows per singoli server.

![image of Change Tracking dashboard](./media/log-analytics-change-tracking/oms-changetracking01.png)

![image of Change Tracking dashboard](./media/log-analytics-change-tracking/oms-changetracking02.png)

### <a name="to-view-changes-for-any-change-type"></a>Per visualizzare le modifiche di qualsiasi tipo
1. Nella pagina **Panoramica** fare clic sul riquadro **Rilevamento modifiche**.
2. Nel dashboard **Rilevamento modifiche** esaminare le informazioni di riepilogo in uno dei pannelli dei tipi di modifiche e quindi fare clic su un tipo per visualizzare le relative informazioni dettagliate nella pagina di **ricerca nei log**.
3. In una pagina di ricerca di log qualsiasi è possibile visualizzare i risultati in base all'ora, ai dettagli e alla cronologia di ricerca. È anche possibile filtrare per facet in modo da limitare i risultati.

## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare dati dettagliati di monitoraggio delle modifiche, usare [Ricerche nei log in Log Analytics](log-analytics-log-searches.md) .

<!--HONumber=Oct16_HO2-->


