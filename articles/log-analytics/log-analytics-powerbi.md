---
title: Importare dati di Azure Log Analytics in Power BI | Microsoft Docs
description: Power BI è un servizio di analisi business basato sul cloud di Microsoft che fornisce report e visualizzazioni dettagliate per l'analisi di diversi set di dati.  Questo articolo descrive come configurare e importare i dati di Log Analytics in Power BI e configurarli per l'aggiornamento automatico.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: bwren
ms.openlocfilehash: 725828c2acc5ac4bb53c5e6af14d20578a3d3652
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Importare dati di Azure Log Analytics in Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) è un servizio di analisi business basato sul cloud di Microsoft che fornisce visualizzazioni dettagliate e report per l'analisi di differenti set di dati.  È possibile importare i risultati di una ricerca log di Log Analytics in un set di dati di Power BI in modo da poter sfruttare le funzionalità di questa soluzione, come la possibilità di combinare dati provenienti da origini diverse e di condividere report sul Web e su dispositivi mobili.

## <a name="overview"></a>Panoramica
Per importare dati da un'area di lavoro di Log Analytics in Power BI, si crea un set di dati in Power BI basato su una query di ricerca log in Log Analytics.  La query viene eseguita ogni volta che viene aggiornato il set di dati.  È quindi possibile creare report di Power BI basati sui dati provenienti dal set di dati.  Per creare il set di dati in Power BI, esportare la query da Log Analytics nella [lingua di Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx)  e usarla per creare una query in Power BI Desktop e pubblicarla in Power BI come set di dati.  Di seguito sono descritti i dettagli relativi a questo processo.

![Log Analytics in Power BI](media/log-analytics-powerbi/overview.png)

## <a name="export-query"></a>Esportare la query
Iniziare creando una [ricerca log](log-analytics-log-search-new.md) che restituisca i dati di Log Analytics che si vuole usare per compilare il set di dati di Power BI.  Esportare quindi la query nella [lingua di Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx), in modo che possa essere usata da Power BI Desktop.

1. Creare la ricerca log in Log Analytics per estrarre i dati necessari per il set di dati.
2. Se si usa il portale di ricerca log, fare clic su **Power BI**.  Se si usa il portale di Analytics, selezionare **Esporta** > **Power BI Query (M)** (Query Power BI - M).  Entrambe le opzioni esportano la query in un file di testo denominato **PowerBIQuery.txt**. 

    ![Esportare la ricerca log](media/log-analytics-powerbi/export-logsearch.png) ![Esportare la ricerca log](media/log-analytics-powerbi/export-analytics.png)

3. Aprire il file di testo e copiarne il contenuto.

## <a name="import-query-into-power-bi-desktop"></a>Importare query in Power BI Desktop
Power BI Desktop è un'applicazione desktop che consente di creare set di dati e report che possono essere pubblicati in Power BI.  Può essere usata anche per creare una query usando la lingua di Power Query esportata da Log Analytics. 

1. Installare [Power BI Desktop](https://powerbi.microsoft.com/desktop/), se non è già stato installato, e aprire quindi l'applicazione.
2. Selezionare **Recupera dati** > **Query vuota** per aprire una nuova query.  Selezionare quindi **Editor avanzato** e incollare il contenuto del file esportato nella query. Fare clic su **Done**.

    ![Query di Power BI Desktop](media/log-analytics-powerbi/desktop-new-query.png)

5. La query viene eseguita e vengono visualizzati i risultati.  È possibile che vengano richieste le credenziali per connettersi ad Azure.  
6. Immettere un nome descrittivo per la query.  Il nome predefinito è **Query1**. Fare clic su **Chiudi e applica** per aggiungere il set di dati al report.

    ![Nome in Power BI Desktop](media/log-analytics-powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Pubblicare in Power BI
Quando si pubblica in Power BI, vengono creati un set di dati e un report.  Se si crea un report in Power BI Desktop, verrà pubblicato completo dei dati.  In caso contrario, verrà creato un report vuoto.  È possibile modificare il report in Power BI o crearne uno nuovo in base al set di dati ottenuto.

8. Creare un report basato sui dati disponibili.  Se necessario, fare riferimento alla [documentazione di Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-report-view).  Quando si è pronti per inviare il report a Power BI, fare clic su **Pubblica**.  Quando richiesto, selezionare una destinazione nell'account di Power BI.  A meno che non si abbia in mente una destinazione specifica, usare **Area di lavoro personale**.

    ![Pubblicazione in Power BI Desktop](media/log-analytics-powerbi/desktop-publish.png)

3. Al termine del processo di pubblicazione, fare clic su **Apri in Power BI** per aprire Power BI con il nuovo set di dati.


### <a name="configure-scheduled-refresh"></a>Configurare l'aggiornamento pianificato
Il set di dati creato in Power BI avrà gli stessi dati precedentemente visualizzati in Power BI Desktop.  È necessario aggiornare il set di dati a intervalli regolari per eseguire nuovamente la query e popolarla con i dati più recenti di Log Analytics.  

1. Fare clic sull'area di lavoro in cui è stato caricato il report e quindi selezionare il menu **Set di dati**. Selezionare il menu di scelta rapida accanto al nuovo set di dati e selezionare **Impostazioni**. In **Credenziali dell'origine dati** viene visualizzato un messaggio per indicare che le credenziali non sono valide.  Questo messaggio viene visualizzato perché non sono ancora state fornite le credenziali per il set di dati da usare quando vengono aggiornati i dati.  Fare clic su **Modifica credenziali** e specificare le credenziali per accedere a Log Analytics.

    ![Pianificazione di Power BI](media/log-analytics-powerbi/powerbi-schedule.png)

5. In **Aggiornamento pianificato** attivare l'opzione **Mantieni aggiornati i dati**.  Facoltativamente, è possibile modificare il valore **Frequenza di aggiornamento** e l'ora in cui si vuole eseguire l'aggiornamento.

    ![Aggiornamento di Power BI](media/log-analytics-powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Passaggi successivi
* Informazioni su [ricerche dei log](log-analytics-log-searches.md) per compilare query che possono essere esportate in Power BI.
* Altre informazioni su [Power BI](http://powerbi.microsoft.com) per generare visualizzazioni basate sulle esportazioni di Log Analytics.
