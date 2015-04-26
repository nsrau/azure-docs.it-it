<properties 
	pageTitle="Come usare la diagnostica" 
	description="Informazioni su come configurare la diagnostica per le risorse in Azure." 
	authors="stepsic-microsoft-com" 
	manager="kamrani" 
	editor="" 
	services="application-insights" 
	documentationCenter=""/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2014-11-04" 
	ms.author="stepsic"/>

# Configurazione della diagnostica

Nel portale di anteprima di Azure è ora possibile configurare rich data di monitoraggio e diagnostica frequenti relativi alle macchine virtuali Windows e agli account di archiviazione in uso.

## Raccolta di rich data dalle macchine virtuali
1. Nell'[anteprima del portale di Azure](https://portal.azure.com/)fare clic su **Sfoglia**, quindi su **Macchine virtuali**. Selezionare la macchina virtuale che si desidera monitorare.
2. La sezione **Monitoraggio** contiene alcune metriche predefinite, ad esempio **Percentuale CPU**, **Lettura e scrittura disco** e **In ingresso e in uscita dalla rete**. Facendo clic su uno qualsiasi di questi elementi viene visualizzato il pannello **Metrica**.  
    ![Monitoring lens](./media/insights-how-to-use-diagnostics/Insights_VMMonitoringLens.png)
3. Il pannello **Metrica** visualizza i dettagli di metrica selezionati. La parte superiore del pannello riporta un grafico, sotto il quale viene visualizzata una tabella contenente le aggregazioni delle metriche come media, minimo e massimo. Sotto la tabella viene visualizzato un elenco degli avvisi definiti dall'utente, filtrati in base alle metriche riportate nel pannello.  
    ![Metric blade](./media/insights-how-to-use-diagnostics/Insights_VMMetricBlade.png)
4. Per abilitare la diagnostica avanzata, fare clic sul pulsante **Impostazioni** per visualizzare il pannello **Diagnostica**. Scegliere **ATTIVA**:  
    ![Diagnostics blade](./media/insights-how-to-use-diagnostics/Insights_VMDiagnosticsBlade.png)
    - **Basic metrics**: metriche sull'integrità relative alla macchina virtuale in uso, ad esempio il processore e la memoria 
    - **Per disk metrics**: metriche relative a tutti i dischi collegati alla macchina virtuale
    - **.NET metrics**: metriche relative alle applicazioni .NET e ASP.NET in esecuzione sulla macchina virtuale in uso
    - **Network metrics**: metriche relative alle connessioni di rete e ai servizi Web
    - **Windows event application logs**: eventi Windows inviati al canale delle applicazioni
    - **Windows event system logs**: eventi Windows inviati al canale di sistema, inclusi tutti gli eventi [Microsoft Antimalware](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409). 
    - **Windows event security logs**: eventi Windows inviati al canale di sicurezza
    - **Diagnostics infrastructure logs**: registrazioni relative all'infrastruttura per la raccolta della diagnostica
    - **IIS logs**: log relativi al server IIS in uso
    Tutte le metriche e i log verranno registrati a intervalli di un minuto per far sì che l'utente disponga sempre delle informazioni più aggiornate sulla macchina in uso.

Una volta abilitate le informazioni di diagnostica per un account di archiviazione, a tale account verranno addebitati i normali costi di archiviazione, transazione e uscita. Tuttavia queste funzioni non creano un grande volume di dati, tranne probabilmente i log IIS. Per ridurre al minimo i costi di uscita, l'utente deve selezionare un account di archiviazione nella stessa area della macchina virtuale.

Pochi minuti dopo aver fatto clic su **OK** i dati inizieranno a essere visualizzati nell'account di archiviazione. Non è possibile abilitare la diagnostica per le macchine virtuali in cui è in esecuzione Linux. Inoltre, per abilitare la diagnostica è necessario avere installato l'agente guest.

## Raccolta di rich data dagli account di archiviazione

La raccolta di alcuni dati dagli account di archiviazione è sempre stata possibile; tuttavia nell'anteprima del portale di Azure ora è possibile raccogliere dati con granularità pari al minuto per comprendere veramente che cosa accade all'interno degli account di archiviazione. I passaggi per abilitare le metriche al minuto sono analoghi a quelli usati per le macchine virtuali:

1. Passare al pannello **Metrica** facendo clic su qualsiasi grafico nel pannello **Account di archiviazione**.
2. Fare clic sul pulsante **Diagnostica** nella barra dei comandi.
3. Selezionare i dati che si desidera raccogliere dall'account di archiviazione:  
    ![Storage diagnostics](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)
4. Fare clic su **OK**. Potrebbero trascorrere alcuni minuti prima che i dati vengano visualizzati per la prima volta.

## Visualizzazione dei dati di diagnostica 

Una volta abilitata la diagnostica, per visualizzare l'elenco completo delle metriche disponibili è sufficiente fare clic con il pulsante destro del mouse su qualsiasi grafico e passare quindi a **Modifica query**:

![Edit query](./media/insights-how-to-use-diagnostics/Insights_VMEditQuery.png)

È possibile tracciare queste metriche e fare zoom avanti per visualizzare il valore di **Ora precedente**, fare zoom indietro per visualizzare il valore di **Settimana precedente** o perfino scegliere un intervallo di tempo **Personalizzato**:
 
![Custom timerange](./media/insights-how-to-use-diagnostics/Insights_VMCustomTime.png)

Notare che queste metriche sono di gran lunga più granulari rispetto ai dati che erano disponibili prima, mentre il tempo di ritardo è minimo.

Al momento non vi è modo di tracciare le metriche con più istanze, quali le metriche per processo o per disco. Per altre informazioni sulla personalizzazione dei grafici di monitoraggio, vedere [Come personalizzare il monitoraggio](http://go.microsoft.com/fwlink/?LinkID=394523&clcid=0x409).

## Invio di avvisi sui dati di diagnostica

Oltre a visualizzare le metriche, nell'anteprima del portale è possibile inviare avvisi su qualsiasi metrica. Innanzitutto, scorrere verso il basso fino alla sezione **Regole di avviso** del pannello della macchina virtuale o dell'archiviazione, quindi fare clic su **Aggiungi avviso**:

![Add alert](./media/insights-how-to-use-diagnostics/Insights_VMAlerts.png)

Selezionare quindi una qualsiasi delle metriche che sono state abilitate per la diagnostica:

![JIT alert](./media/insights-how-to-use-diagnostics/Insights_VMJITAlert.png)

Nel grafico viene mostrata un'anteprima della soglia di avviso raffrontata alla metrica del giorno precedente. Pochi minuti dopo aver fatto clic su **Salva**, all'utente verrà comunicato se la metrica prescelta supera la soglia. 

Si noti che le metriche visualizzate solo nel portale di anteprima non possono essere oggetto di avvisi nel portale completo. Di conseguenza, alcune regole relative agli avvisi del portale di anteprima non sono visibili nel portale completo.

<!--HONumber=46--> 

<!--HONumber=46--> 
