<properties title="How to use diagnostics" pageTitle="How to use diagnostics" description="Learn how to set up diagnostics for your resources in Azure." authors="stepsic"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stepsic"></tags>

# Configurazione della diagnostica

Nel portale di gestione di Azure è ora possibile configurare rich data di monitoraggio e diagnostica frequenti relativi alle macchine virtuali Windows e agli account di archiviazione in uso.

## Raccolta di rich data dalle macchine virtuali

1.  Nel [portale di gestione di Azure](https://portal.azure.com/), fare clic su **Browse**, quindi scegliere **Virtual machines**. Selezionare la macchina virtuale che si desidera monitorare.
2.  Il filtro **Monitoring** contiene alcune metriche predefinite, ad esempio **percentuale CPU**, **lettura dal disco e scrittura sul disco** e **traffico di rete in entrata e in uscita**. Facendo clic su uno qualsiasi di questi elementi viene visualizzato il blade **Metric**.
    ![Filtro di monitoraggio](./media/insights-how-to-use-diagnostics/Insights_VMMonitoringLens.png)
3.  Il blade delle **metriche** visualizza i dettagli selezionati. La parte superiore del blade riporta un grafico, sotto il quale viene visualizzata una tabella contenente le aggregazioni delle metriche come media, minimo e massimo. Sotto la tabella viene visualizzato un elenco degli avvisi definiti dall'utente, filtrati in base alle metriche riportate nel blade.
    ![Blade delle metriche](./media/insights-how-to-use-diagnostics/Insights_VMMetricBlade.png)
4.  Per abilitare la diagnostica avanzata, fare clic sul pulsante **Settings** per visualizzare il blade **Diagnostics**. Scegliere **ON**:
    ![Blade di diagnostica](./media/insights-how-to-use-diagnostics/Insights_VMDiagnosticsBlade.png)

    -   **Basic metrics**: metriche sull'integrità relative alla macchina virtuale in uso, ad esempio il processore e la memoria
    -   **Per disk metrics**: metriche relative a tutti i dischi collegati alla macchina virtuale
    -   **.NET metrics**: metriche relative alle applicazioni .NET e ASP.NET in esecuzione sulla macchina virtuale in uso
    -   **Network metrics**: metriche relative alla connessioni di rete e ai servizi Web
    -   **Windows event application logs**: eventi Windows inviati al canale delle applicazioni
    -   **Windows event system logs**: eventi Windows inviati al canale di sistema, inclusi tutti gli eventi [Microsoft Antimalware](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409)
    -   **Windows event security logs**: eventi Windows inviati al canale di sicurezza
    -   **Diagnostics infrastructure logs**: registrazioni relative all'infrastruttura per la raccolta della diagnostica
    -   **IIS logs**: log relativi al server IIS in uso
        Tutte le metriche e i log verranno registrati a intervalli di un minuto per far sì che l'utente disponga sempre delle informazioni più aggiornate sulla macchina in uso.

Una volta abilitate le informazioni di diagnostica per un account di archiviazione, a tale account verranno addebitati i normali costi di archiviazione, transazione e uscita. Tuttavia queste funzioni non creano un grande volume di dati, tranne probabilmente i log IIS. Per ridurre al minimo i costi di uscita, l'utente deve selezionare un account di archiviazione nella stessa regione della macchina virtuale.

Pochi minuti dopo aver fatto clic su **OK** i dati inizieranno a essere visualizzati nell'account di archiviazione. Non è possibile abilitare la diagnostica per le macchine virtuali in cui è in esecuzione Linux. Inoltre, per abilitare la diagnostica è necessario avere installato l'agente guest.

## Raccolta di rich data dagli account di archiviazione

La raccolta di alcuni dati dagli account di archiviazione è sempre stata possibile; tuttavia nel portale di gestione di Azure ora è possibile raccogliere dati con granularità pari al minuto per comprendere veramente che cosa accade all'interno degli account di archiviazione. I passaggi per abilitare le metriche al minuto sono analoghi a quelli utilizzati per le macchine virtuali:

1.  Passare al blade **Metric** facendo clic su qualsiasi grafico nel blade **Storage account**.
2.  Fare clic sul pulsante **Diagnostics** nella barra dei comandi.
3.  Selezionare i dati che si desidera raccogliere dall'account di archiviazione:
    ![Diagnostica di archiviazione](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)
4.  Fare clic su **OK**. Potrebbero trascorrere alcuni minuti prima che i dati vengano visualizzati per la prima volta.

## Visualizzazione dei dati di diagnostica

Una volta abilitata la diagnostica, per visualizzare l'elenco completo delle metriche disponibili è sufficiente fare clic con il pulsante destro del mouse su qualsiasi grafico e passare quindi a **Edit query**:

![Edit query](./media/insights-how-to-use-diagnostics/Insights_VMEditQuery.png)

È possibile tracciare queste metriche e fare zoom avanti per visualizzare il valore dell'ora precedente, **Past hour**, fare zoom indietro per visualizzare il valore della settimana precedente, **Past week**, o perfino scegliere un intervallo di tempo personalizzato **Custom**:

![Intervallo di tempo personalizzato](./media/insights-how-to-use-diagnostics/Insights_VMCustomTime.png)

Notare che queste metriche sono di gran lunga più granulari rispetto ai dati che erano disponibili prima, mentre il tempo di ritardo è minimo.

Al momento non vi è modo di tracciare le metriche con più istanze, quali le metriche per processo o per disco. Per ulteriori informazioni sulla personalizzazione dei grafici di monitoraggio, vedere [Come personalizzare il monitoraggio](http://go.microsoft.com/fwlink/?LinkID=394523&clcid=0x409).

## Invio di avvisi sui dati di diagnostica

Oltre a visualizzare le metriche, nel portale di gestione è possibile inviare avvisi su qualsiasi metrica. Innanzitutto, scorrere verso il basso fino alla sezione **Alert rules** del blade della macchina virtuale o dell'archiviazione, quindi fare clic su **Add alert**:

![Add alert](./media/insights-how-to-use-diagnostics/Insights_VMAlerts.png)

Selezionare quindi una qualsiasi delle metriche che sono state abilitate per la diagnostica:

![JIT alert](./media/insights-how-to-use-diagnostics/Insights_VMJITAlert.png)

Nel grafico viene mostrata un'anteprima della soglia di avviso raffrontata alla metrica del giorno precedente. Pochi minuti dopo aver fatto clic su **Save**, all'utente verrà comunicato se la metrica prescelta supera la soglia.

Si noti che le metriche visualizzate solo nel portale di gestione non possono essere oggetto di avvisi nel portale completo. Di conseguenza, alcune regole relative agli avvisi del portale di gestione non sono visibili nel portale completo.


