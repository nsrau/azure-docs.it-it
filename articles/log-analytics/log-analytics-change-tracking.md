---
title: Soluzione di rilevamento delle modifiche in Log Analytics | Microsoft Docs
description: "È possibile usare la soluzione Change Tracking (Rilevamento modifiche) relativa alla configurazione in Log Analytics per identificare facilmente le modifiche apportate al software e ai servizi Windows nell&quot;ambiente in uso. Questa funzionalità può semplificare l&quot;individuazione dei problemi operativi."
services: operations-management-suite
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f8040d5d-3c89-4f0c-8520-751c00251cb7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 2a66cdb9825cfc3935d534afaf3f6f0cf5d5fb5a
ms.openlocfilehash: d4226882ded34a79f7e227886a396db0e927bad2


---
# <a name="change-tracking-solution-in-log-analytics"></a>Soluzione di rilevamento modifiche in Log Analytics
Questo articolo spiega come usare la soluzione di rilevamento delle modifiche in Log Analytics per identificare facilmente le modifiche nell'ambiente. La soluzione rileva le modifiche apportate a software Windows and Linux, file di Windows, servizi di Windows e daemon Linux. Rilevando le modifiche alla configurazione è possibile localizzare eventuali problemi operativi.

La soluzione viene installata per aggiornare il tipo di agente che è stato installato. La soluzione legge le modifiche al software installato, ai servizi Windows e ai daemon Linux nei server monitorati e invia i dati al servizio Log Analytics nel cloud per l'elaborazione. Viene applicata la logica ai dati ricevuti, quindi questi ultimi vengono registrati nel servizio cloud. Usando le informazioni nel dashboard Change Tracking, è possibile visualizzare facilmente le modifiche apportate all'infrastruttura del server.

## <a name="installing-and-configuring-the-solution"></a>Installazione e configurazione della soluzione
Usare le informazioni seguenti per installare e configurare la soluzione.

* È necessario avere un agente [Windows](log-analytics-windows-agents.md), [Operations Manager](log-analytics-om-agents.md) o [Linux](log-analytics-linux-agents.md) su ogni computer in cui si vuole monitorare le modifiche.
* Aggiungere la soluzione di rilevamento modifiche all'area di lavoro OMS usando la procedura descritta nell'articolo sull' [aggiunta di soluzioni di Log Analytics dalla raccolta soluzioni](log-analytics-add-solutions.md).  Non è richiesta alcuna ulteriore configurazione.

### <a name="configure-windows-files-to-track"></a>Configurare i file di Windows da rilevare
Seguire questa procedura per configurare i file da rilevare in computer Windows.

1. Nel portale di OMS, fare clic su **Impostazioni** (simbolo dell'ingranaggio).
2. Nella pagina **Impostazioni** fare clic su **Dati** e poi su **Rilevamento file di Windows**.
3. In Rilevamento modifiche file di Windows digitare l'intero percorso, includendo il nome del file che si vuole rilevare, e fare clic sul simbolo **Aggiungi**. Ad esempio: C:\Programmi (x86)\Internet Explorer\iexplore.exe o C:\Windows\System32\drivers\etc\hosts.
4. Fare clic su **Save**.  
   ![Rilevamento modifiche file di Windows](./media/log-analytics-change-tracking/windows-file-change-tracking.png)

### <a name="limitations"></a>Limitazioni
La soluzione di rilevamento modifiche non supporta attualmente gli elementi seguenti:

* cartelle (directory)
* ricorsione
* caratteri jolly
* variabili di percorso
* file system di rete

Altre limitazioni:

* La colonna **Dimensioni massime file** e i valori non sono usati nell'implementazione corrente.
* Se si raccolgono oltre 2500 file in un ciclo di recupero di 30 minuti, è possibile assistere a una riduzione delle prestazioni della soluzione.
* Quando il traffico di rete è elevato, i record di modifica possono impiegare fino a un massimo di sei ore prima di essere visualizzati.
* Se si modifica la configurazione mentre un computer è arrestato, il computer potrebbe registrare le modifiche ai file che appartenevano alla configurazione precedente.

## <a name="change-tracking-data-collection-details"></a>Informazioni dettagliate sulla raccolta dei dati di Change Tracking
Change Tracking consente di raccogliere l'inventario software e i metadati del servizio Windows tramite gli agenti abilitati.

La tabella seguente mostra i metodi di raccolta di dati e altre informazioni dettagliate sul modo in cui vengono raccolti i dati per Change Tracking.

| piattaforma | Agente diretto | Agente SCOM | Agente Linux | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Windows e Linux |![Sì](./media/log-analytics-change-tracking/oms-bullet-green.png) |![Sì](./media/log-analytics-change-tracking/oms-bullet-green.png) |![Sì](./media/log-analytics-change-tracking/oms-bullet-green.png) |![No](./media/log-analytics-change-tracking/oms-bullet-red.png) |![No](./media/log-analytics-change-tracking/oms-bullet-red.png) |![Sì](./media/log-analytics-change-tracking/oms-bullet-green.png) | Da 15 minuti a 1 ora, a seconda del tipo di modifica |

## <a name="use-change-tracking"></a>Uso di Change Tracking
Dopo l'installazione della soluzione, è possibile visualizzare il riepilogo delle modifiche per i server monitorati usando il riquadro **Rilevamento modifiche** nella pagina **Panoramica** di OMS.

![immagine del riquadro Change Tracking](./media/log-analytics-change-tracking/change-tracking-tile.png)

È possibile vedere le modifiche all'infrastruttura e quindi analizzare i dettagli per le categorie seguenti:

* Modifiche per tipo di configurazione per i servizi software e di Windows.
* Modifiche software ad applicazioni e aggiornamenti per singoli server.
* Numero totale di modifiche software per ogni applicazione.
* Pacchetti Linux
* Modifiche dei servizi di Windows per singoli server.
* Modifiche a daemon Linux

![image of Change Tracking dashboard](./media/log-analytics-change-tracking/change-tracking-dash01.png)

![image of Change Tracking dashboard](./media/log-analytics-change-tracking/change-tracking-dash02.png)

### <a name="to-view-changes-for-any-change-type"></a>Per visualizzare le modifiche di qualsiasi tipo
1. Nella pagina **Panoramica** fare clic sul riquadro **Rilevamento modifiche**.
2. Nel dashboard **Rilevamento modifiche** esaminare le informazioni di riepilogo in uno dei pannelli dei tipi di modifiche e quindi fare clic su un tipo per visualizzare le relative informazioni dettagliate nella pagina di **ricerca nei log**.
3. In una pagina di ricerca di log qualsiasi è possibile visualizzare i risultati in base all'ora, ai dettagli e alla cronologia di ricerca. È anche possibile filtrare per facet in modo da limitare i risultati.

## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare dati dettagliati di monitoraggio delle modifiche, usare [Ricerche nei log in Log Analytics](log-analytics-log-searches.md) .



<!--HONumber=Jan17_HO3-->


