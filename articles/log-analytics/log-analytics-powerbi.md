---
title: Esportare i dati di Log Analytics in Power BI | Microsoft Docs
description: "Power BI è un servizio di analisi business basato sul cloud di Microsoft che fornisce report e visualizzazioni dettagliate per l'analisi di diversi set di dati.  Log Analytics può esportare continuamente dati dal repository OMS in Power BI per poterne sfruttare gli strumenti di analisi e le visualizzazioni.  Questo articolo descrive come configurare query in Log Analytics che eseguono automaticamente l'esportazione in Power BI a intervalli regolari."
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
ms.date: 07/24/2017
ms.author: bwren
ms.openlocfilehash: 271747e25f319c76195ec643025d24c6b7cdc9c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="export-log-analytics-data-to-power-bi"></a>Esportare i dati di Log Analytics in Power BI

>[!NOTE]
> Se l'area di lavoro è stata aggiornata al [nuovo linguaggio di query di Log Analytics](log-analytics-log-search-upgrade.md), questo processo per esportare i dati di Log Analytics in Power BI non funzionerà più.  Le pianificazioni esistenti create prima dell'aggiornamento verranno disabilitate. Non verrà più visualizzata la possibilità di attivare la funzionalità di esportazione di Power BI in Impostazioni nelle Funzionalità di anteprima, poiché questa funzionalità viene completamente rilasciata nelle aree di lavoro aggiornate. 
>
> Dopo l'aggiornamento, Azure Log Analytics usa la stessa piattaforma di Application Insights e il processo per esportare le query di Log Analytics in Power BI è uguale al [processo usato per esportare le query di Application Insights in Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).  È possibile esportare la query usando la console di Analytics descritta in tale articolo oppure è possibile selezionare il pulsante **Power BI** nella parte superiore della schermata nel portale di ricerca log.
>
> Gli utenti dovranno accedere alla risorsa dell'area di lavoro in Azure per usare la funzionalità di esportazione di Power BI nelle aree di lavoro aggiornate. Senza l'accesso, gli utenti visualizzeranno un messaggio di errore al momento di importare una query in Power BI desktop a cui non hanno accesso.



[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) è un servizio di analisi business basato sul cloud di Microsoft che fornisce visualizzazioni dettagliate e report per l'analisi di differenti set di dati.  Log Analytics può esportare automaticamente dati dal repository OMS in Power BI per poterne sfruttare gli strumenti di analisi e le visualizzazioni.

Quando si configura Power BI con Log Analytics, si creano query di log che esportano i risultati nei set di dati corrispondenti in Power BI.  La query e l'esportazione continuano a essere eseguite automaticamente in base a una pianificazione definita dall'utente per mantenere aggiornato il set di dati con gli ultimi dati raccolti da Log Analytics.

![Log Analytics in Power BI](media/log-analytics-powerbi/overview.png)

## <a name="power-bi-schedules"></a>Pianificazioni di Power BI
Una *pianificazione di Power BI* include una ricerca dei log che esporta un set di dati dal repository OMS per un set di dati corrispondente in Power BI e una pianificazione che definisce la frequenza con cui viene eseguita la ricerca per mantenere aggiornato il set di dati.

I campi nel set di dati corrisponderanno alle proprietà dei record restituiti dalla ricerca dei log.  Se la ricerca restituisce i record di tipi diversi, il set di dati includerà tutte le proprietà di ognuno dei tipi di record inclusi.  

> [!NOTE]
> La procedura consigliata consiste nell'usare una query di ricerca dei log che restituisce dati non elaborati invece di eseguire una qualsiasi operazione di consolidamento usando comandi come [Measure](log-analytics-search-reference.md#measure).  È possibile eseguire qualsiasi aggregazione e calcoli in Power BI da dati non elaborati.
>
>

## <a name="connecting-oms-workspace-to-power-bi"></a>Connessione dell'area di lavoro di OMS a Power BI
Prima di poter esportare da Log Analytics in Power BI, è necessario connettersi l'area di lavoro di OMS all'account Power BI usando la procedura seguente.  

1. Nella console di OMS selezionare il riquadro **Impostazioni** .
2. Selezionare **Account**.
3. Nella sezione **Informazioni area di lavoro** fare clic su **Connetti all'account Power BI**.
4. Immettere le credenziali per l'account Power BI.

## <a name="create-a-power-bi-schedule"></a>Creare una pianificazione di Power BI
Creare una pianificazione di Power BI per ogni set di dati usando la procedura seguente.

1. Nella console di OMS selezionare il riquadro **Ricerca log** .
2. Digitare una nuova query o selezionare una ricerca salvata che restituisca i dati da esportare in **Power BI**.  
3. Fare clic sul pulsante **Power BI** nella parte superiore della pagina per aprire la finestra di dialogo **Power BI**.
4. Fornire le informazioni nella tabella seguente e fare clic su **Salva**.

| Proprietà | Descrizione |
|:--- |:--- |
| Nome |Nome per identificare la pianificazione quando si visualizza l'elenco di pianificazioni di Power BI. |
| Ricerca salvata |Ricerca dei log da eseguire.  È possibile selezionare la query corrente o selezionare una ricerca salvata dalla casella a discesa. |
| Pianificazione |Frequenza con cui eseguire la ricerca salvata ed esportare nel set di dati di Power BI.  Il valore deve essere compreso tra 15 minuti e 24 ore. |
| Nome del set di dati |Nome del set di dati in Power BI.  Verrà creato se non esiste e aggiornato se esiste. |

## <a name="viewing-and-removing-power-bi-schedules"></a>Visualizzazione e rimozione di pianificazioni di Power BI
Visualizzare l'elenco di pianificazioni di Power BI esistenti con la procedura seguente.

1. Nella console di OMS selezionare il riquadro **Impostazioni** .
2. Selezionare **Power BI**.

Oltre ai dettagli della pianificazione, vengono visualizzati il numero di volte in cui è stata eseguita la pianificazione nella settimana precedente e lo stato dell'ultima sincronizzazione.  Se la sincronizzazione ha rilevato errori, è possibile fare clic sul collegamento per eseguire una ricerca dei log e trovare i record con i dettagli dell'errore.

È possibile rimuovere una pianificazione facendo clic su **X** nella colonna **Rimuovi**.  È possibile disabilitare una pianificazione selezionando **No**.  Per modificare una pianificazione è necessario rimuoverla e ricrearla con le nuove impostazioni.

![Pianificazioni di Power BI](media/log-analytics-powerbi/schedules.png)

## <a name="sample-walkthrough"></a>Procedura dettagliata di esempio
La sezione seguente illustra un esempio di creazione di una pianificazione di Power BI e dell'uso del relativo set di dati per creare un report semplice.  In questo esempio tutti i dati sulle prestazioni per un gruppo di computer vengono esportati in Power BI e viene creato un grafico a linee per visualizzare l'utilizzo del processore.

### <a name="create-log-search"></a>Creare una ricerca dei log
Si inizierà dalla creazione di una ricerca dei log per trovare i dati da inviare al set di dati.  In questo esempio si userà una query che restituisce tutti i dati sulle prestazioni per i computer con un nome che inizia con *srv*.  

![Pianificazioni di Power BI](media/log-analytics-powerbi/walkthrough-query.png)

### <a name="create-power-bi-search"></a>Creare una ricerca di Power BI
Fare clic sul pulsante **Power BI** per aprire la finestra di dialogo di Power BI e fornire le informazioni necessarie.  Questa ricerca dovrà essere eseguita una volta ogni ora e sarà necessario creare un set di dati denominato *Contoso Perf*.  Poiché è già aperta una ricerca che crea i dati desiderati, si manterrà il valore predefinito di *Usa query di ricerca corrente* per **Ricerca salvata**.

![Ricerca di Power BI](media/log-analytics-powerbi/walkthrough-schedule.png)

### <a name="verify-power-bi-search"></a>Verificare la ricerca di Power BI
Per verificare che la pianificazione sia stata creata correttamente, visualizzare l'elenco delle ricerche di Power BI nel riquadro **Impostazioni** nel dashboard di OMS.  Attendere alcuni minuti e aggiornare la visualizzazione fino a quando non segnalerà che la sincronizzazione è stata eseguita.

![Ricerca di Power BI](media/log-analytics-powerbi/walkthrough-schedules.png)

### <a name="verify-the-dataset-in-power-bi"></a>Verificare il set di dati in Power BI
Accedere con il proprio account a [powerbi.microsoft.com](http://powerbi.microsoft.com/) e scorrere fino alla **et di dati** nella parte inferiore del riquadro a sinistra.  È possibile notare che il set di dati *Contoso Perf* è elencato e indica che l'esportazione è stata eseguita correttamente.

![Set di dati di Power BI](media/log-analytics-powerbi/walkthrough-datasets.png)

### <a name="create-report-based-on-dataset"></a>Creare report basato sul set di dati
Selezionare il set di dati **Contoso Perf** e quindi fare clic su **Risultati** nel riquadro **Campi** a destra per visualizzare i campi che fanno parte di questo set di dati.  Per creare un grafico a linee che mostra l'utilizzo del processore per ogni computer, eseguire queste azioni.

1. Selezionare la visualizzazione Grafico a linee.
2. Trascinare **ObjectName** in **Filtri a livello di report** e selezionare **Processore**.
3. Trascinare **CounterName** in **Filtri a livello di report** e selezionare **% tempo processore**.
4. Trascinare **CounterValue** in **Valori**.
5. Trascinare **Computer** in **Legenda**.
6. Trascinare **TimeGenerated** in **Asse**.

Si noterà che il grafico risultante viene visualizzato con i dati dal set di dati.

![Grafico a linee di Power BI](media/log-analytics-powerbi/walkthrough-linegraph.png)

### <a name="save-the-report"></a>Salvare il report
Per salvare il report fare clic sul pulsante Salva nella parte superiore della schermata e verificare che sia elencato nella sezione Report nel riquadro a sinistra.

![Report di Power BI](media/log-analytics-powerbi/walkthrough-report.png)

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su [ricerche dei log](log-analytics-log-searches.md) per compilare query che possono essere esportate in Power BI.
* Altre informazioni su [Power BI](http://powerbi.microsoft.com) per generare visualizzazioni basate sulle esportazioni di Log Analytics.
