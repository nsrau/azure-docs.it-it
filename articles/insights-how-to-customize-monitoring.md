<properties title="How to customize monitoring" pageTitle="How to customize monitoring" description="Learn how to customize monitoring charts in Azure." authors="stepsic"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stepsic" />

# Personalizzazione del monitoraggio

Nel portale di gestione di Azure è ora possibile visualizzare i dati di monitoraggio in modalità più numerose rispetto al passato, che comprendono la personalizzazione dell'intervallo di tempo e la scelta di un numero maggiore di metriche.

1.  Nell'[anteprima del portale di Azure][anteprima del portale di Azure] fare clic su **Sfoglia**, quindi sulla risorsa che si desidera monitorare.
2.  La sezione **Monitoraggio** include la metrica più importante per ogni risorsa di Azure. Ad esempio, per i siti Web sono presenti Richiesto, Errori, [test Web][test Web] [Analisi][Analisi]. Facendo clic sulla parte relativa a **richieste ed errori di oggi**, viene visualizzato il pannello **Metrica**.  
    ![Monitoring lens][Monitoring lens]
3.  Il pannello delle **metriche** visualizza i dettagli selezionati. La parte superiore del pannello riporta un grafico, sotto il quale viene visualizzata una tabella contenente le aggregazioni delle metriche come media, minimo e massimo. Sotto la tabella viene visualizzato un elenco degli avvisi definiti dall'utente, filtrati in base alle metriche riportate nel pannello. In tal modo, in presenza di un gran numero di avvisi, qui è possibile visualizzare solo gli avvisi pertinenti. È sempre possibile visualizzare tutti gli avvisi relativi al sito Web facendo clic su **Regole di avviso** nel pannello **Sito Web**.  
    ![Metric blade][Metric blade]
4.  Per personalizzare le metriche da visualizzare, fare clic con il pulsante destro del mouse sul grafico e scegliere **Modifica query**:  
    ![Edit Query][Edit Query]
5.  Nel pannello Edit Query è possibile: modificare l'intervallo di tempo e scegliere una metrica diversa.  
    ![Edit Query][1]
6.  Per apportare cambiamenti all'intervallo di tempo è sufficiente scegliere un intervallo diverso (come **Past Hour**) e fare clic su **Save** nella parte inferiore del pannello. Nel portale di gestione è ora possibile scegliere anche **Personalizzato**:  
    ![Custom time range][Custom time range]
7.  È infatti possibile scegliere qualsiasi periodo di tempo nell'arco delle ultime due settimane, ad esempio visualizzare le due settimane intere o solo un'ora del giorno prima. Digitare l'orario diverso nella casella di testo.
8.  Sotto l'intervallo di tempo è possibile scegliere un numero qualsiasi di metriche da visualizzare nel grafico. È possibile visualizzare nuove metriche: **working set della memoria** e **working set medio della memoria**.

9.  Facendo clic su Save, le modifiche verranno salvate fino all'uscita dal pannello del sito Web. A un accesso successivo al pannello, verranno visualizzate le metriche e l'intervallo di tempo originali.

## Monitoraggio di nuove risorse

Nell'anteprima del portale di Azure è ora possibile monitorare anche la metrica delle prestazioni per molte nuove risorse, ad esempio:

-   Piani di hosting Web
-   Cache Redis
-   Account DocumentDB

I piani di hosting Web sono più complessi di altre risorse, in quando rappresentano le prestazioni delle istanze in cui vengono eseguiti i **siti Web**. Per accedere alla metrica del piano di hosting Web, fare clic sull'icona del piano nella sezione di riepilogo del sito Web.

![Piano di hosting Web][Piano di hosting Web]

Nel filtro **Monitoring** è possibile visualizzare un grafico che si comporta come il grafico nel pannello del sito Web, includendo le nuove metriche:

-   Percentuale di CPU
-   Percentuale memoria
-   Profondità coda HTTP
-   Profondità coda disco

## Creazione di grafici affiancati

Grazie alla notevole personalizzazione da parte dell'utente, nel portale di gestione di Azure è possibile creare grafici affiancati.

1.  Innanzitutto, fare clic con il pulsante destro del mouse sul grafico dal quale si intende partire e scegliere **Personalizza**  
    ![Customize chart][Customize chart]
2.  Fare quindi clic su **Clona** nel menu **...** per copiare la parte  
    ![Clone part][Clone part]
3.  Infine, sulla barra degli strumenti nella parte superiore della schermata, fare clic su **Done**. Ora è possibile trattare questa parte come una normale parte delle metriche. Facendo clic con il pulsante destro del mouse e modificando la metrica visualizzata, è possibile visualizzare contemporaneamente due diverse metriche affiancate:  
    ![Two metrics Side by Side][Two metrics Side by Side]

Si noti che il grafico relativo all'intervallo di tempo e le metriche selezionate verranno reimpostati nel momento in cui si lascia il portale.

  [anteprima del portale di Azure]: https://portal.azure.com/
  [test Web]: http://go.microsoft.com/fwlink/?LinkID=394528&clcid=0x409
  [Analisi]: http://go.microsoft.com/fwlink/?LinkID=394529&clcid=0x409
  [Monitoring lens]: ./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png
  [Metric blade]: ./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png
  [Edit Query]: ./media/insights-how-to-customize-monitoring/Insights_MetricMenu.png
  [1]: ./media/insights-how-to-customize-monitoring/Insights_EditQuery.png
  [Custom time range]: ./media/insights-how-to-customize-monitoring/Insights_CustomTime.png
  [Piano di hosting Web]: ./media/insights-how-to-customize-monitoring/Insights_WHPSelect.png
  [Customize chart]: ./media/insights-how-to-customize-monitoring/Insights_Customize.png
  [Clone part]: ./media/insights-how-to-customize-monitoring/Insights_ClonePart.png
  [Two metrics Side by Side]: ./media/insights-how-to-customize-monitoring/Insights_SideBySide.png
