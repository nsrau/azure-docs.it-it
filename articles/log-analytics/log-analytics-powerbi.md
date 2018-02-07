---
title: Importare dati di Azure Log Analytics in Power BI | Microsoft Docs
description: "Power BI è un servizio di analisi business basato sul cloud di Microsoft che fornisce report e visualizzazioni dettagliate per l'analisi di diversi set di dati.  Questo articolo descrive come configurare l'importazione di dati di Log Analytics in Power BI e configurarli per l'aggiornamento automatico."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: bwren
ms.openlocfilehash: e687a1ee8ac4f565062e57b07cdfa9ac5e6bbf4f
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Importare dati di Azure Log Analytics in Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) è un servizio di analisi business basato sul cloud di Microsoft che fornisce visualizzazioni dettagliate e report per l'analisi di differenti set di dati.  È possibile importare i risultati di una ricerca log di Log Analytics in un set di dati di Power BI in modo da poter sfruttare le funzionalità di questa soluzione, come la possibilità di combinare dati provenienti da origini diverse e di condividere report sul Web e dispositivi mobili.

Questo articolo fornisce informazioni dettagliate sull'importazione di dati di Log Analytics in Power BI e sulla relativa pianificazione per l'aggiornamento automatico.  Sono previsti processi diversi per un'area di lavoro [aggiornata](#upgraded-workspace) o [legacy](#legacy-workspace).

## <a name="upgraded-workspace"></a>Area di lavoro aggiornata


Per importare dati da un'[area di lavoro di Log Analytics aggiornata](log-analytics-log-search-upgrade.md) in Power BI, si crea un set di dati in Power BI basato su una query di ricerca log in Log Analytics.  La query viene eseguita ogni volta che viene aggiornato il set di dati.  È quindi possibile creare report di Power BI basati sui dati provenienti dal set di dati.  Per creare il set di dati in Power BI, esportare la query da Log Analytics nella [lingua di Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx)  e usarla per creare una query in Power BI Desktop e pubblicarla in Power BI come set di dati.  Di seguito sono descritti i dettagli relativi a questo processo.

![Log Analytics in Power BI](media/log-analytics-powerbi/overview.png)

### <a name="export-query"></a>Esportare la query
Iniziare creando una [ricerca log](log-analytics-log-search-new.md) che restituisca i dati di Log Analytics che si vuole usare per compilare il set di dati di Power BI.  Esportare quindi la query nella [lingua di Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx), in modo che possa essere usata da Power BI Desktop.

1. Creare la ricerca log in Log Analytics per estrarre i dati necessari per il set di dati.
2. Se si usa il portale di ricerca log, fare clic su **Power BI**.  Se si usa il portale di Analytics, selezionare **Esporta** > **Power BI Query (M)** (Query Power BI - M).  Entrambe le opzioni esportano la query in un file di testo denominato **PowerBIQuery.txt**. 

    ![Esportare la ricerca log](media/log-analytics-powerbi/export-logsearch.png) ![Esportare la ricerca log](media/log-analytics-powerbi/export-analytics.png)

3. Aprire il file di testo e copiarne il contenuto.

### <a name="import-query-into-power-bi-desktop"></a>Importare query in Power BI Desktop
Power BI Desktop è un'applicazione desktop che consente di creare set di dati e report che possono essere pubblicati in Power BI.  Può essere usata anche per creare una query usando la lingua di Power Query esportata da Log Analytics. 

1. Installare [Power BI Desktop](https://powerbi.microsoft.com/desktop/), se non è già stato installato, e aprire quindi l'applicazione.
2. Selezionare **Recupera dati** > **Query vuota** per aprire una nuova query.  Selezionare quindi **Editor avanzato** e incollare il contenuto del file esportato nella query. Fare clic su **Done**.

    ![Query di Power BI Desktop](media/log-analytics-powerbi/desktop-new-query.png)

5. La query viene eseguita e vengono visualizzati i risultati.  È possibile che vengano richieste le credenziali per connettersi ad Azure.  
6. Immettere un nome descrittivo per la query.  Il nome predefinito è **Query1**. Fare clic su **Chiudi e applica** per aggiungere il set di dati al report.

    ![Nome in Power BI Desktop](media/log-analytics-powerbi/desktop-results.png)



### <a name="publish-to-power-bi"></a>Pubblicare in Power BI
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

## <a name="legacy-workspace"></a>Area di lavoro legacy
Se si configura Power BI con un'[area di lavoro Log Analytics legacy](log-analytics-powerbi.md), si creano query di log che esportano i risultati nei set di dati corrispondenti in Power BI.  La query e l'esportazione continuano a essere eseguite automaticamente in base a una pianificazione definita dall'utente per mantenere aggiornato il set di dati con gli ultimi dati raccolti da Log Analytics.

![Log Analytics in Power BI](media/log-analytics-powerbi/overview-legacy.png)

### <a name="power-bi-schedules"></a>Pianificazioni di Power BI
Una *pianificazione di Power BI* include una ricerca log che esporta un set di dati da Log Analytics in un set di dati corrispondente in Power BI e una pianificazione che definisce la frequenza con cui viene eseguita la ricerca per mantenere aggiornato il set di dati.

I campi nel set di dati corrisponderanno alle proprietà dei record restituiti dalla ricerca dei log.  Se la ricerca restituisce i record di tipi diversi, il set di dati includerà tutte le proprietà di ognuno dei tipi di record inclusi.  

### <a name="connecting-log-analytics-workspace-to-power-bi"></a>Connessione dell'area di lavoro di Log Analytics a Power BI
Prima di eseguire l'esportazione da Log Analytics a Power BI, è necessario connettere l'area di lavoro all'account di Power BI usando la procedura seguente.  

1. Nella console di OMS selezionare il riquadro **Impostazioni** .
2. Selezionare **Account**.
3. Nella sezione **Informazioni area di lavoro** fare clic su **Connetti all'account Power BI**.
4. Immettere le credenziali per l'account Power BI.

### <a name="create-a-power-bi-schedule"></a>Creare una pianificazione di Power BI
Creare una pianificazione di Power BI per ogni set di dati usando la procedura seguente.

1. Nella console di OMS selezionare il riquadro **Ricerca log** .
2. Digitare una nuova query o selezionare una ricerca salvata che restituisca i dati da esportare in **Power BI**.  
3. Fare clic sul pulsante **Power BI** nella parte superiore della pagina per aprire la finestra di dialogo **Power BI**.
4. Fornire le informazioni nella tabella seguente e fare clic su **Salva**.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| NOME |Nome per identificare la pianificazione quando si visualizza l'elenco di pianificazioni di Power BI. |
| Ricerca salvata |Ricerca dei log da eseguire.  È possibile selezionare la query corrente o selezionare una ricerca salvata dalla casella a discesa. |
| Pianificazione |Frequenza con cui eseguire la ricerca salvata ed esportare nel set di dati di Power BI.  Il valore deve essere compreso tra 15 minuti e 24 ore. |
| Nome del set di dati |Nome del set di dati in Power BI.  Verrà creato se non esiste e aggiornato se esiste. |

### <a name="viewing-and-removing-power-bi-schedules"></a>Visualizzazione e rimozione di pianificazioni di Power BI
Visualizzare l'elenco di pianificazioni di Power BI esistenti con la procedura seguente.

1. Nella console di OMS selezionare il riquadro **Impostazioni** .
2. Selezionare **Power BI**.

Oltre ai dettagli della pianificazione, vengono visualizzati il numero di volte in cui è stata eseguita la pianificazione nella settimana precedente e lo stato dell'ultima sincronizzazione.  Se la sincronizzazione ha rilevato errori, è possibile fare clic sul collegamento per eseguire una ricerca dei log e trovare i record con i dettagli dell'errore.

È possibile rimuovere una pianificazione facendo clic su **X** nella colonna **Rimuovi**.  È possibile disabilitare una pianificazione selezionando **No**.  Per modificare una pianificazione è necessario rimuoverla e ricrearla con le nuove impostazioni.

![Pianificazioni di Power BI](media/log-analytics-powerbi/schedules.png)

### <a name="sample-walkthrough"></a>Procedura dettagliata di esempio
La sezione seguente illustra un esempio di creazione di una pianificazione di Power BI e dell'uso del relativo set di dati per creare un report semplice.  In questo esempio tutti i dati sulle prestazioni per un gruppo di computer vengono esportati in Power BI e viene creato un grafico a linee per visualizzare l'utilizzo del processore.

#### <a name="create-log-search"></a>Creare una ricerca dei log
Si inizierà dalla creazione di una ricerca dei log per trovare i dati da inviare al set di dati.  In questo esempio si userà una query che restituisce tutti i dati sulle prestazioni per i computer con un nome che inizia con *srv*.  

![Pianificazioni di Power BI](media/log-analytics-powerbi/walkthrough-query.png)

#### <a name="create-power-bi-search"></a>Creare una ricerca di Power BI
Fare clic sul pulsante **Power BI** per aprire la finestra di dialogo di Power BI e fornire le informazioni necessarie.  Questa ricerca dovrà essere eseguita una volta ogni ora e sarà necessario creare un set di dati denominato *Contoso Perf*.  Poiché è già aperta una ricerca che crea i dati desiderati, si manterrà il valore predefinito di *Usa query di ricerca corrente* per **Ricerca salvata**.

![Ricerca di Power BI](media/log-analytics-powerbi/walkthrough-schedule.png)

#### <a name="verify-power-bi-search"></a>Verificare la ricerca di Power BI
Per verificare che la pianificazione sia stata creata correttamente, visualizzare l'elenco delle ricerche di Power BI nel riquadro **Impostazioni** nel dashboard di OMS.  Attendere alcuni minuti e aggiornare la visualizzazione fino a quando non segnalerà che la sincronizzazione è stata eseguita.  In genere, si pianifica il set di dati in modo che si aggiorni automaticamente.

![Ricerca di Power BI](media/log-analytics-powerbi/walkthrough-schedules.png)

#### <a name="verify-the-dataset-in-power-bi"></a>Verificare il set di dati in Power BI
Accedere con il proprio account a [powerbi.microsoft.com](http://powerbi.microsoft.com/) e scorrere fino alla **et di dati** nella parte inferiore del riquadro a sinistra.  È possibile notare che il set di dati *Contoso Perf* è elencato e indica che l'esportazione è stata eseguita correttamente.

![Set di dati di Power BI](media/log-analytics-powerbi/walkthrough-datasets.png)

#### <a name="create-report-based-on-dataset"></a>Creare report basato sul set di dati
Selezionare il set di dati **Contoso Perf** e quindi fare clic su **Risultati** nel riquadro **Campi** a destra per visualizzare i campi che fanno parte di questo set di dati.  Per creare un grafico a linee che mostra l'utilizzo del processore per ogni computer, eseguire queste azioni.

1. Selezionare la visualizzazione Grafico a linee.
2. Trascinare **ObjectName** in **Filtri a livello di report** e selezionare **Processore**.
3. Trascinare **CounterName** in **Filtri a livello di report** e selezionare **% tempo processore**.
4. Trascinare **CounterValue** in **Valori**.
5. Trascinare **Computer** in **Legenda**.
6. Trascinare **TimeGenerated** in **Asse**.

Si noterà che il grafico risultante viene visualizzato con i dati dal set di dati.

![Grafico a linee di Power BI](media/log-analytics-powerbi/walkthrough-linegraph.png)

#### <a name="save-the-report"></a>Salvare il report
Per salvare il report fare clic sul pulsante Salva nella parte superiore della schermata e verificare che sia elencato nella sezione Report nel riquadro a sinistra.

![Report di Power BI](media/log-analytics-powerbi/walkthrough-report.png)



## <a name="next-steps"></a>Passaggi successivi
* Informazioni su [ricerche dei log](log-analytics-log-searches.md) per compilare query che possono essere esportate in Power BI.
* Altre informazioni su [Power BI](http://powerbi.microsoft.com) per generare visualizzazioni basate sulle esportazioni di Log Analytics.
