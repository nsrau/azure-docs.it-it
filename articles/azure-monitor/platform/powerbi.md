---
title: Importare dati di Azure Log Analytics in Power BI | Microsoft Docs
description: Power BI è un servizio di analisi business basato sul cloud di Microsoft che fornisce report e visualizzazioni dettagliate per l'analisi di diversi set di dati.  Questo articolo descrive come configurare e importare i dati di Log Analytics in Power BI e configurarli per l'aggiornamento automatico.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 62a010480dc83561a11c6ee99c76f35b29e808c1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932122"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Importare i dati di log di monitoraggio di Azure in Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) è un servizio di analisi business basato sul cloud di Microsoft che fornisce visualizzazioni dettagliate e report per l'analisi di differenti set di dati.  È possibile importare i risultati di una query di log di monitoraggio di Azure in un set di dati Power BI in modo da poter sfruttare le funzionalità, ad esempio la combinazione di dati di origini diverse e la condivisione di report sul Web e sui dispositivi mobili.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Panoramica
Per importare dati da un' [area di lavoro log Analytics](manage-access.md) in monitoraggio di azure in Power bi, è possibile creare un set di dati in Power bi in base a una [query di log](../log-query/log-query-overview.md) in monitoraggio di Azure.  La query viene eseguita ogni volta che viene aggiornato il set di dati.  È quindi possibile creare report di Power BI basati sui dati provenienti dal set di dati.  Per creare il set di dati in Power BI, esportare la query da Log Analytics nella [lingua di Power Query (M)](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification)  e usarla per creare una query in Power BI Desktop e pubblicarla in Power BI come set di dati.  Di seguito sono descritti i dettagli relativi a questo processo.

![Log Analytics in Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Esportare la query
Per iniziare, creare una [query di log](../log-query/log-query-overview.md) che restituisca i dati per i quali si desidera popolare il set di dati Power bi.  Esportare quindi la query nella [lingua di Power Query (M)](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification), in modo che possa essere usata da Power BI Desktop.

1. [Creare la query di log in log Analytics](../log-query/get-started-portal.md) per estrarre i dati per il set di dati.
2. Selezionare **esporta** > **Power bi query (M)** .  Questa operazione consente di esportare la query in un file di testo denominato **PowerBIQuery. txt**. 

    ![Esportare la ricerca log](media/powerbi/export-analytics.png)

3. Aprire il file di testo e copiarne il contenuto.

## <a name="import-query-into-power-bi-desktop"></a>Importare query in Power BI Desktop
Power BI Desktop è un'applicazione desktop che consente di creare set di dati e report che possono essere pubblicati in Power BI.  È anche possibile usarlo per creare una query usando il linguaggio di Power Query esportato da monitoraggio di Azure. 

1. Installare [Power BI Desktop](https://powerbi.microsoft.com/desktop/), se non è già stato installato, e aprire quindi l'applicazione.
2. Selezionare **Recupera dati** > **Query vuota** per aprire una nuova query.  Selezionare quindi **Editor avanzato** e incollare il contenuto del file esportato nella query. Fare clic su **Done**.

    ![Query di Power BI Desktop](media/powerbi/desktop-new-query.png)

5. La query viene eseguita e vengono visualizzati i risultati.  È possibile che vengano richieste le credenziali per connettersi ad Azure.  
6. Immettere un nome descrittivo per la query.  Il nome predefinito è **Query1**. Fare clic su **Chiudi e applica** per aggiungere il set di dati al report.

    ![Nome in Power BI Desktop](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Pubblicare in Power BI
Quando si pubblica in Power BI, vengono creati un set di dati e un report.  Se si crea un report in Power BI Desktop, verrà pubblicato completo dei dati.  In caso contrario, verrà creato un report vuoto.  È possibile modificare il report in Power BI o crearne uno nuovo in base al set di dati ottenuto.

1. Creare un report basato sui dati disponibili.  Se necessario, fare riferimento alla [documentazione di Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-report-view).  
1. Quando si è pronti per inviare il report a Power BI, fare clic su **Pubblica**.  
1. Quando richiesto, selezionare una destinazione nell'account di Power BI.  A meno che non si abbia in mente una destinazione specifica, usare **Area di lavoro personale**.

    ![Pubblicazione in Power BI Desktop](media/powerbi/desktop-publish.png)

1. Al termine del processo di pubblicazione, fare clic su **Apri in Power BI** per aprire Power BI con il nuovo set di dati.


### <a name="configure-scheduled-refresh"></a>Configurare l'aggiornamento pianificato
Il set di dati creato in Power BI avrà gli stessi dati precedentemente visualizzati in Power BI Desktop.  È necessario aggiornare periodicamente il set di dati per eseguire nuovamente la query e popolarla con i dati più recenti da monitoraggio di Azure.  

1. Fare clic sull'area di lavoro in cui è stato caricato il report e quindi selezionare il menu **Set di dati**. 
1. Selezionare il menu di scelta rapida accanto al nuovo set di dati e selezionare **Impostazioni**. 
1. In **Credenziali dell'origine dati** viene visualizzato un messaggio per indicare che le credenziali non sono valide.  Questo messaggio viene visualizzato perché non sono ancora state fornite le credenziali per il set di dati da usare quando vengono aggiornati i dati.  
1. Fare clic su **Modifica credenziali** e specificare le credenziali con accesso all'area di lavoro log Analytics in monitoraggio di Azure. Se è necessaria l'autenticazione a due fattori, selezionare **OAuth2** per chiedere al **metodo di autenticazione** di eseguire l'accesso con le proprie credenziali.

    ![Pianificazione di Power BI](media/powerbi/powerbi-schedule.png)

5. In **Aggiornamento pianificato** attivare l'opzione **Mantieni aggiornati i dati**.  Facoltativamente, è possibile modificare il valore **Frequenza di aggiornamento** e l'ora in cui si vuole eseguire l'aggiornamento.

    ![Aggiornamento di Power BI](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Passaggi successivi
* Informazioni su [ricerche dei log](../log-query/log-query-overview.md) per compilare query che possono essere esportate in Power BI.
* Scopri di più su [Power bi](https://powerbi.microsoft.com) per creare visualizzazioni basate sulle esportazioni dei log di monitoraggio di Azure.