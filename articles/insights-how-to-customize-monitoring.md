<properties title="How to customize monitoring" pageTitle="How to customize monitoring" description="Learn how to customize monitoring charts in Azure." authors="stepsic" />

Personalizzazione del monitoraggio
==================================

Nel portale di gestione di Azure è ora possibile visualizzare i dati di monitoraggio in modalità più numerose rispetto al passato, che comprendono la personalizzazione dell'intervallo di tempo e la scelta di un numero maggiore di metriche.

1.  Nel [portale di gestione di Azure](https://portal.azure.com/) fare clic su **Browse**, quindi su **Web Sites** e quindi fare clic sul nome di un sito Web per aprire il blade.

2.  Il filtro **Monitoring** consente di selezionare richieste, errori, [test Web](http://go.microsoft.com/fwlink/?LinkID=394528&clcid=0x409) e [analisi](http://go.microsoft.com/fwlink/?LinkID=394529&clcid=0x409). Facendo clic su **Requests and errors today** verrà visualizzato il blade delle **metriche**.

   ![Filtro di monitoraggio](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3.  Il blade delle **metriche** visualizza i dettagli selezionati. La parte superiore del blade riporta un grafico, sotto il quale viene visualizzata una tabella contenente le aggregazioni delle metriche come media, minimo e massimo. Sotto la tabella viene visualizzato un elenco degli avvisi definiti dall'utente, filtrati in base alle metriche riportate nel blade. In tal modo, in presenza di un gran numero di avvisi, qui è possibile visualizzare solo gli avvisi pertinenti. È sempre possibile visualizzare tutti gli avvisi relativi al sito Web facendo clic su **Alert rules** nel blade **Web site**.

   ![Blade delle metriche](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4.  Per personalizzare le metriche da visualizzare, fare clic con il pulsante destro del mouse sul grafico e scegliere **Edit Query**:

   ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_MetricMenu.png)

5.  Nel blade Edit Query è possibile: modificare l'intervallo di tempo e scegliere metriche diverse.

   ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6.  Per apportare cambiamenti all'intervallo di tempo è sufficiente scegliere un intervallo diverso (come **Past Hour**) e fare clic su **Save** nella parte inferiore del blade. Nel portale di gestione è ora possibile scegliere anche **Custom**:

  ![Intervallo di tempo personalizzato](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7.  È infatti possibile scegliere qualsiasi periodo di tempo nell'arco delle ultime due settimane, ad esempio visualizzare le due settimane intere o solo un'ora del giorno prima. Digitare l'orario diverso nella casella di testo.

8.  Sotto l'intervallo di tempo è possibile scegliere un numero qualsiasi di metriche da visualizzare nel grafico. È possibile visualizzare nuove metriche: **working set della memoria** e **working set medio della memoria**.

9.  Facendo clic su Save, le modifiche verranno salvate fino all'uscita dal blade del sito Web. A un accesso successivo al blade, verranno visualizzate le metriche e l'intervallo di tempo originali.

Monitoraggio del piano di hosting Web
-------------------------------------

Il portale di gestione di Azure consente ora anche di monitorare le metriche delle prestazioni delle istanze relative ai siti Web. Per accedere a queste metriche, fare clic sull'icona del piano di hosting Web nel filtro di riepilogo.

   ![Piano di hosting Web](./media/insights-how-to-customize-monitoring/Insights_WHPSelect.png)

Nel filtro **Monitoring** è possibile visualizzare un grafico che si comporta come il grafico nel blade del sito Web, includendo le nuove metriche: 
- Percentuale CPU 
- Percentuale memoria 
- Profondità coda HTTP 
- Profondità coda del disco

Creazione di grafici affiancati
-------------------------------

Grazie alla notevole personalizzazione da parte dell'utente, nel portale di gestione di Azure è possibile creare grafici affiancati.

1.  Innanzitutto, fare clic con il pulsante destro del mouse sul grafico dal quale si intende partire e scegliere **Customize**

   ![Personalizzazione del grafico](./media/insights-how-to-customize-monitoring/Insights_Customize.png)

2.  Quindi fare clic su **Clone** nel menu **...** per copiare la parte

   ![Clonazione della parte](./media/insights-how-to-customize-monitoring/Insights_ClonePart.png)

3.  Infine, sulla barra degli strumenti nella parte superiore della schermata, fare clic su **Done**. Ora è possibile trattare questa parte come una normale parte delle metriche. Facendo clic con il pulsante destro del mouse e modificando la metrica visualizzata, è possibile visualizzare contemporaneamente due diverse metriche affiancate:

   ![Due metriche affiancate](./media/insights-how-to-customize-monitoring/Insights_SideBySide.png)

Si noti che il grafico relativo all'intervallo di tempo e le metriche selezionate verranno reimpostati nel momento in cui si lascia il portale.

