---
title: Esportare dati in Power BI da Azure Application Insights | Microsoft Docs
description: Le query di Analisi possono essere visualizzate in Power BI.
services: application-insights
documentationcenter: 
author: noamben
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: mbullwin
ms.openlocfilehash: 19595983ba49a88d9139c85afbf38d3106d4a81d
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="feed-power-bi-from-application-insights"></a>Feed di Power BI da Application Insights
[Power BI](http://www.powerbi.com/) è un insieme di strumenti aziendali che consente di analizzare i dati e condividere informazioni dettagliate e significative. Dashboard completi sono disponibili in tutti i dispositivi. È possibile combinare dati provenienti da diverse origini, incluse le query di Analytics di [Azure Application Insights](app-insights-overview.md).

Esistono tre metodi consigliati per esportare i dati di Application Insights in Power BI. Possono essere usati insieme o separatamente.

* [**Adattatore Power BI**](#power-pi-adapter). Configurare un dashboard di dati di telemetria completo dall'app. Il set di tabelle è predefinito, ma è possibile aggiungere query da qualsiasi altra origine.
* [**Esportare query di Analisi**](#export-analytics-queries). Scrivere le query desiderate ed esportarle in Power BI. Per scrivere una query è possibile usare Analisi o le visualizzazioni Imbuto. La query può essere inserita in un dashboard, insieme a tutti gli altri dati.
* [**Esportazione continua e Analisi di flusso di Azure**](app-insights-export-stream-analytics.md). Questo metodo è utile se i dati devono essere mantenuti a lungo. In caso contrario, usare uno degli altri metodi, poiché questo richiede un maggiore lavoro di configurazione.

## <a name="power-bi-adapter"></a>Adattatore Power BI
Con questo metodo si crea un dashboard di dati di telemetria completo per l'utente. Il set di dati iniziale è predefinito, ma è possibile aggiungere altri dati.

### <a name="get-the-adapter"></a>Scaricare l'adattatore
1. Accedere a [Power BI](https://app.powerbi.com/).
2. Aprire **Recupera dati**, **Servizi** e quindi **Application Insights**.
   
    ![Screenshot per il recupero di dati dall'origine dati di Application Insights](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Specificare i dettagli della risorsa di Application Insights.
   
    ![Screenshot per il recupero di dati dall'origine dati di Application Insights](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Attendere uno o due minuti per il completamento dell'importazione dei dati.
   
    ![Screenshot dell'adattatore Power BI](./media/app-insights-export-power-bi/010.png)

È possibile modificare il dashboard unendo i grafici di Application Insights con i grafici di altre origini e con le query di Analisi. Nella raccolta di visualizzazioni sono disponibili più grafici, ciascuno dei quali include parametri che è possibile impostare.

Dopo l'importazione iniziale, il dashboard e i report continuano a essere aggiornati ogni giorno. È possibile controllare la pianificazione dell'aggiornamento nel set di dati.

## <a name="export-analytics-queries"></a>Esportare query di Analisi
Questo metodo consente di scrivere tutte le query di Analisi desiderate, o di esportarle dalle visualizzazioni Imbuto, e quindi di inserirle in un dashboard di Power BI. È possibile aggiungerle al dashboard creato dall'adattatore.

### <a name="one-time-install-power-bi-desktop"></a>Operazione da eseguire una sola volta: installazione di Power BI Desktop
Per importare la query di Application Insights, usare la versione desktop di Power BI. Sarà quindi possibile pubblicarla sul Web o nell'area di lavoro cloud di Power BI. 

Installare [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Esportare una query di Analisi
1. [Aprire Analisi e scrivere la query](app-insights-analytics-tour.md).
2. Testare e affinare la query fino a quando non si è soddisfatti dei risultati. Verificare che la query venga eseguita correttamente in Analisi prima di esportarla.
3. Nel menu **Esporta** scegliere **Power BI (M)**. Salvare il file di testo.
   
    ![Screenshot di Analisi con il menu Esporta evidenziato](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. In Power BI Desktop selezionare **Recupera dati** > **Query vuota**. Nella scheda **Visualizza** dell'editor di query selezionare **Editor avanzato**.

    Nell'Editor avanzato incollare lo script in linguaggio M esportato.

    ![Screenshot di Power BI Desktop, con l'Editor avanzato evidenziato](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Per consentire a Power BI di accedere ad Azure può essere necessario specificare le credenziali. Scegliere **Account aziendale** per accedere con l'account Microsoft personale.
   
    ![Screenshot della finestra di dialogo Impostazioni query di Power BI](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    Se è necessario verificare le credenziali, usare il comando di menu **Impostazioni origine dati** nell'editor di query. Assicurarsi di specificare le credenziali usate per Azure, che potrebbero essere diverse da quelle di Power BI.
2. Scegliere una visualizzazione per la query e selezionare i campi per le assi X e Y e per le dimensioni di segmentazione.
   
    ![Screenshot delle opzioni di visualizzazione di Power BI Desktop](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Pubblicare il report nell'area di lavoro cloud di Power BI. Da qui è possibile incorporare una versione sincronizzata in altre pagine Web.
   
    ![Screenshot di Power BI Desktop, con il pulsante Pubblica evidenziato](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Aggiornare manualmente il report a intervalli oppure impostare un aggiornamento pianificato nella pagina Opzioni.

### <a name="export-a-funnel"></a>Esportare una visualizzazione Imbuto
1. [Creare una visualizzazione Imbuto](usage-funnels.md).
2. Selezionare **Power BI**. 

   ![Screenshot del pulsante Power BI](./media/app-insights-export-power-bi/button.png)
   
3. In Power BI Desktop selezionare **Recupera dati** > **Query vuota**. Nella scheda **Visualizza** dell'editor di query selezionare **Editor avanzato**.

   ![Screenshot di Power BI Desktop, con il pulsante Query vuota evidenziato](./media/app-insights-export-power-bi/blankquery.png)

   Nell'Editor avanzato incollare lo script in linguaggio M esportato. 

   ![Screenshot di Power BI Desktop, con l'Editor avanzato evidenziato](./media/app-insights-export-power-bi/advancedquery.png)

4. Selezionare gli elementi dalla query e scegliere una visualizzazione Imbuto.

   ![Screenshot delle opzioni di visualizzazione di Power BI Desktop](./media/app-insights-export-power-bi/selectsequence.png)

5. Modificare il titolo specificando un testo significativo e pubblicare il report nell'area di lavoro cloud di Power BI. 

   ![Screenshot di Power BI Desktop, con la modifica del titolo evidenziata](./media/app-insights-export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi

È possibile che vengano restituiti errori relativi alle credenziali o alla dimensione del set di dati. Ecco alcune informazioni su come gestirli.

### <a name="unauthorized-401-or-403"></a>Non autorizzato (401 o 403)
Questo errore può verificarsi se il token di aggiornamento non è stato aggiornato. Provare a eseguire questi passaggi per verificare di avere ancora i diritti di accesso:

1. Accedere al portale di Azure e verificare di poter accedere alla risorsa.
2. Provare ad aggiornare le credenziali per il dashboard.

 Se si ha accesso e non è possibile aggiornare le credenziali, aprire un ticket di supporto.

### <a name="bad-gateway-502"></a>Gateway non valido (502)
Questo errore è in genere causato da una query di Analisi che restituisce troppi dati. Provare a usare un intervallo di tempo più breve per la query. 

Se la riduzione del set di dati risultante dalla query di Analisi non è una soluzione adeguata alle proprie esigenze, valutare l'opportunità di usare l'[API](https://dev.applicationinsights.io/documentation/overview) per estrarre un set di dati di dimensioni maggiori. Per convertire l'esportazione della query M per l'uso dell'API, seguire questa procedura.

1. Creare una [chiave API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Aggiornare lo script M di Power BI esportato da Analisi sostituendo l'URL di Azure Resource Manager con l'API di Application Insights.
   * Sostituire **https://management.azure.com/subscriptions/...**
   * con **https://api.applicationinsights.io/beta/apps/...**
3. Aggiornare infine le credenziali in credenziali di base e usare la chiave API.
  

**Script esistente**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Script aggiornato**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Informazioni sul campionamento
Se l'applicazione invia una grande quantità di dati, può essere opportuno usare la funzionalità di campionamento adattivo, che invia solo una percentuale dei dati di telemetria. La stessa considerazione vale se il campionamento è stato impostato manualmente nell'SDK o durante l'inserimento. [Altre informazioni sul campionamento](app-insights-sampling.md).


## <a name="next-steps"></a>Passaggi successivi
* [Power BI - Informazioni](http://www.powerbi.com/learning/)
* [Esercitazione su Analisi](app-insights-analytics-tour.md)

