<properties 
	pageTitle="Monitorare e gestire le pipeline di Data factory di Azure" 
	description="Informazioni sull'uso dell'app di monitoraggio e gestione per monitorare e gestire le data factory e le pipeline di Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/12/2016" 
	ms.author="spelluru"/>

# Monitorare e gestire le pipeline di Azure Data Factory con la nuova app di monitoraggio e gestione
> [AZURE.SELECTOR]
- [Using Azure Portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
- [Using Monitoring and Management App](data-factory-monitor-manage-app.md)

Questo articolo descrive come monitorare, gestire ed eseguire il debug delle pipeline usando l'**app di monitoraggio e gestione**. Contiene anche informazioni su come creare avvisi e ricevere notifiche sugli errori tramite l'applicazione.
      
## Avvio dell'app di monitoraggio e gestione 
Per avviare l'app di monitoraggio e gestione, fare clic sul riquadro dell'**app di monitoraggio** nel pannello **DATA FACTORY** della data factory.

![Riquadro di monitoraggio nella home page di Data Factory](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

L'app di monitoraggio e gestione verrà avviata in una scheda o finestra separata.

![App di monitoraggio e gestione](./media/data-factory-monitor-manage-app/AppLaunched.png)

## Informazioni sull'app di monitoraggio e gestione
A sinistra sono disponibili tre schede, **Resource Explorer**, **Monitoring Views** e **Alerts**. La scheda Resource Explorer è selezionata per impostazione predefinita.

### Scheda Resource Explorer
Nel riquadro sinistro è disponibile la **visualizzazione albero** di Resource Explorer, nella parte superiore del pannello centrale è disponibile la vista **Diagram View**, nella parte inferiore si trova l'elenco **Activity Windows** e nel riquadro destro si trovano le schede **Properties** e **Activity Window Explorer**.

È possibile visualizzare tutte le risorse della data factory, ovvero le pipeline, i set di dati e i servizi collegati, in una visualizzazione albero. Quando si seleziona un oggetto in Resource Explorer, si noti quanto segue:

- L'entità di Data Factory associata viene evidenziata nella vista diagramma.
- Le finestre attività associate vengono evidenziate nell'elenco Activity Windows, nella parte inferiore del pannello centrale. Fare clic [qui](data-factory-scheduling-and-execution.md) per informazioni sulle finestre attività.  
- Le proprietà dell'oggetto selezionato vengono visualizzate nella finestra Properties nel riquadro destro. 

![Scheda Resource Explorer](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Per informazioni dettagliate sulle finestre attività, vedere l'articolo [Pianificazione ed esecuzione con Data Factory](data-factory-scheduling-and-execution.md).

### Vista Diagramma
La vista diagramma di una data factory fornisce un'unica console da cui monitorare e gestire la data factory e i relativi asset. Quando si seleziona un'entità di Data Factory, ovvero un set di dati o una pipeline, nella vista diagramma, si noti quanto segue:
 
- L'entità di Data Factory viene selezionata nella visualizzazione albero.
- Le finestre attività associate vengono evidenziate nell'elenco Activity Windows.
- Le proprietà dell'oggetto selezionato vengono visualizzate nella finestra Properties.

Quando la pipeline è abilitata, vale a dire non sospesa, viene visualizzata con una linea verde, come illustrato di seguito.

![Pipeline di esecuzione](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Nella vista diagramma sono presenti tre pulsanti per la pipeline. Il secondo pulsante può essere usato per sospendere l'esecuzione della pipeline. Questa azione non termina le attività attualmente in esecuzione e le lascia continuare fino al completamento. Il terzo pulsante sospende l'esecuzione della pipeline e termina le attività esistenti in esecuzione. Il primo pulsante riprende l'esecuzione della pipeline sospesa. Quando l'esecuzione della pipeline viene sospesa, il colore del relativo riquadro cambia come illustrato di seguito.

![Sospensione/ripresa nel riquadro](./media/data-factory-monitor-manage-app/SuspendResumeOnTile.png)

È possibile selezionare due o più pipeline con il tasto CTRL e usare i pulsanti della barra dei comandi per sospendere o riprendere l'esecuzione di più pipeline contemporaneamente.

![Sospensione/ripresa nella barra dei comandi](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

È possibile visualizzare tutte le attività della pipeline facendo clic con il pulsante destro del mouse sul relativo riquadro e scegliendo **Open pipeline**.

![Menu Open Pipeline](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

Nella visualizzazione pipeline aperta vengono visualizzate tutte le attività della pipeline. In questo esempio è presente soltanto l'attività di copia. Per tornare alla visualizzazione precedente, fare clic sul nome della data factory nel menu di navigazione nella parte superiore.

![Pipeline aperta](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Quando si fa clic o si passa il mouse su un set di dati di output nella visualizzazione pipeline aperta o chiusa, viene visualizzato il popup delle finestre attività per tale set di dati.

![Popup Activity Windows](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

È possibile fare clic su una finestra attività per visualizzarne i dettagli nella finestra **Properties** nel riquadro destro.

![Proprietà della finestra attività](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

Nel riquadro destro passare alla scheda **Activity Window Explorer** per visualizzare altri dettagli.

![Activity Window Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Le finestre attività vengono visualizzate in tre posizioni:

- Popup Activity Windows nella vista diagramma, nel riquadro centrale.
- Activity Window Explorer nel riquadro destro.
- Elenco Activity Windows nel riquadro inferiore.

Nel popup Activity Windows e in Activity Window Explorer è possibile scorrere fino alla settimana precedente e quella successiva usando i pulsanti con la freccia sinistra e destra.

![Freccia sinistra/destra in Activity Window Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Nella parte inferiore della vista diagramma sono disponibili pulsanti per eseguire lo zoom avanti, zoom indietro e zoom 100%, adattare alla finestra e bloccare il layout. Quest'ultimo permette di evitare di spostare accidentalmente tabelle e pipeline nella vista diagramma. Il blocco del layout è abilitato per impostazione predefinita. È possibile disabilitarlo e spostare le entità nel diagramma. Quando il blocco viene disabilitato, è possibile usare l'ultimo pulsante per posizionare automaticamente pipeline e tabelle. È anche possibile eseguire lo zoom avanti o indietro usando la rotellina del mouse.

![Comandi di zoom nella vista diagramma](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)


### Elenco Activity Windows
L'elenco delle finestre attività nella parte inferiore del riquadro centrale riporta tutte le finestre attività per il set di dati selezionato in Resource Explorer o nella vista diagramma. Per impostazione predefinita, l'elenco è in ordine decrescente. Ciò significa che la finestra attività più recente viene visualizzata in alto.

![Elenco Activity Windows](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

L'elenco non viene aggiornato automaticamente. Deve essere aggiornato manualmente usando il relativo pulsante nella barra degli strumenti.


Di seguito sono riportati gli stati possibili per le finestre attività:

<table>
<tr>
	<th align="left">Stato</th><th align="left">Stato secondario</th><th align="left">Descrizione</th>
</tr>
<tr>
	<td rowspan="8">Waiting</td><td>ScheduleTime</td><td>Non è ancora il momento di eseguire la finestra attività.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Le dipendenze upstream non sono pronte.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Le risorse di calcolo non sono disponibili.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Tutte le istanze di attività sono occupate nell'esecuzione di altre finestre attività.</td>
</tr>
<tr>
<td>ActivityResume</td><td>L'attività è sospesa e non è possibile eseguire le finestre attività fino a quando non viene ripresa.</td>
</tr>
<tr>
<td>Retry</td><td>L'esecuzione dell'attività verrà ritentata.</td>
</tr>
<tr>
<td>Validation</td><td>La convalida non è ancora stata avviata.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>In attesa della riesecuzione della convalida.</td>
</tr>
<tr>
&lt;tr
<td rowspan="2">InProgress</td><td>Validating</td><td>Convalida in corso.</td>
</tr>
<td></td>
<td>È in corso l'elaborazione della finestra attività.</td>
</tr>
<tr>
<td rowspan="4">Failed</td><td>TimedOut</td><td>L'esecuzione ha richiesto più tempo di quello consentito dall'attività.</td>
</tr>
<tr>
<td>Canceled</td><td>Annullato dall'utente.</td>
</tr>
<tr>
<td>Validation</td><td>Convalida non riuscita.</td>
</tr>
<tr>
<td></td><td>Non è stato possibile generare e/o convalidare la finestra attività.</td>
</tr>
<td>Ready</td><td></td><td>La finestra attività è pronta per l'utilizzo.</td>
</tr>
<tr>
<td>Skipped</td><td></td><td>La finestra attività non viene elaborata.</td>
</tr>
<tr>
<td>None</td><td></td><td>Finestra attività esistente che in precedenza aveva un altro stato e che ora è stata reimpostata.</td>
</tr>
</table>


Quando si fa clic su una finestra attività nell'elenco, i relativi dettagli vengono visualizzati nella finestra Properties o Activity Window Explorer a destra.

![Activity Window Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### Aggiornare le finestre attività  
I dettagli non vengono aggiornati automaticamente. L'elenco delle finestre attività deve essere aggiornato manualmente usando il pulsante di **aggiornamento**, vale a dire il secondo pulsante nella barra dei comandi.
 

### Finestra Properties
La finestra Properties si trova nel riquadro destro dell'app di monitoraggio e gestione.

![Finestra Properties](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Qui vengono visualizzate le proprietà dell'elemento selezionato nella visualizzazione albero di Resource Explorer, nella vista diagramma o nell'elenco delle finestre attività.

### Activity Window Explorer

La finestra Activity Window Explorer si trova nel riquadro destro dell'app di monitoraggio e gestione. Qui vengono visualizzati i dettagli relativi alla finestra attività selezionata nel popup o nell'elenco delle finestre attività.

![Activity Window Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Per passare a una finestra attività diversa, fare clic su di essa nella visualizzazione calendario in alto. È anche possibile usare i pulsanti con la freccia **sinistra** o **destra** nella parte superiore per visualizzare le finestre attività della settimana precedente o successiva.

I pulsanti della barra degli strumenti nel riquadro inferiore permettono di **rieseguire** la finestra attività o di **aggiornare** i dettagli nel riquadro.


## Uso delle viste di sistema
L'app di monitoraggio e gestione include viste di sistema predefinite, **Recent activity windows**, **Failed activity windows** e **In-Progress activity windows**, che permettono di visualizzare le finestre attività recenti, non riuscite e in corso della data factory.

Fare clic per passare alla scheda **Monitoring Views** a sinistra.

![Scheda Monitoring Views](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Al momento sono disponibili tre viste di sistema supportate. Selezionare un'opzione per visualizzare le finestre attività recenti, non riuscite o in corso nell'elenco Activity Windows, nella parte inferiore del riquadro centrale.

Quando si seleziona l'opzione **Recent activity windows**, le finestre attività recenti vengono visualizzate in ordine decrescente in base all'**ora dell'ultimo tentativo**.

L'opzione **Failed activity windows** permette di visualizzare tutte le finestre attività non riuscita nell'elenco. Selezionare una finestra attività non riuscita dall'elenco per visualizzarne i dettagli nella finestra **Properties** o in **Activity Window Explorer**. È anche possibile scaricare i log relativi a una finestra attività non riuscita.


## Ordinamento e filtro delle finestre attività
Modificare le impostazioni relative all'**ora di inizio** e **ora di fine** nella barra dei comandi per filtrare le finestre attività. Dopo aver modificato queste impostazioni, fare clic sul pulsante accanto all'ora di fine per aggiornare l'elenco delle finestre attività.

![Ora di inizio e ora di fine](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [AZURE.NOTE] Al momento, nell'app di monitoraggio e gestione le ore sono in formato UTC.

Nell'elenco **Activity Windows** fare clic sul nome di una colonna, ad esempio Status.

![Menu della colonna nell'elenco Activity Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

A questo punto è possibile eseguire le attività seguenti:

- Applicare l'ordinamento crescente.
- Applicare l'ordinamento decrescente.
- Filtrare in base a uno o più valori, ad esempio Ready, Waiting e così via.

Quando si specifica un filtro in una colonna, il pulsante filtro è abilitato per la colonna a indicare che i valori nella colonna sono filtrati.

![Filtro della colonna dell'elenco Activity Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Per cancellare i filtri è possibile usare la stessa finestra popup. Per cancellare tutti i filtri per l'elenco delle finestre attività, fare clic sul pulsante filtro nella barra dei comandi.

![Cancellare tutti i filtri nell'elenco Activity Windows](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)


## Esecuzione di azioni batch

### Rieseguire finestre attività selezionate
Selezionare una finestra attività, fare clic sulla freccia a discesa del primo pulsante nella barra dei comandi e selezionare **Rerun** o **Rerun with upstream in pipeline**. L'opzione **Rerun with upstream in pipeline** permette di rieseguire anche tutte le finestre attività upstream. ![Rieseguire una finestra attività](./media/data-factory-monitor-manage-app/ReRunSlice.png)

È anche possibile selezionare più finestre attività nell'elenco e rieseguirle contemporaneamente. È possibile filtrare le finestre attività in base allo stato, ad esempio **Failed**, e quindi rieseguire le finestre attività non riuscite dopo aver corretto il problema che ne causava l'errore. Vedere la sezione seguente per informazioni dettagliate sull'applicazione di filtri alle finestre attività nell'elenco.

### Sospendere o riprendere l'esecuzione di più pipeline
È possibile selezionare due o più pipeline con il tasto CTRL e usare i pulsanti della barra dei comandi, evidenziati dal rettangolo rosso nell'immagine seguente, per sospendere o riprendere l'esecuzione di più pipeline contemporaneamente.

![Sospensione/ripresa nella barra dei comandi](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

## Creazione di avvisi 
La pagina Alerts consente di creare nuovi avvisi e di visualizzare, modificare ed eliminare quelli esistenti. Permette anche di abilitare o disabilitare un avviso. Fare clic sulla scheda Alerts per visualizzare la pagina.

![Scheda Alerts](./media/data-factory-monitor-manage-app/AlertsTab.png)

### Per creare un avviso

1. Fare clic su **Add Alert** per aggiungere un avviso. Verrà visualizzata la pagina dei Details. 

	![Creazione di avvisi: pagina Details](./media/data-factory-monitor-manage-app/CreateAlertDetailsPage.png)
1. Specificare il **nome** e la **descrizione** dell'avviso e fare clic su **Next**. Verrà visualizzata la pagina dei **Filters**.

	![Creazione di avvisi: pagina Filters](./media/data-factory-monitor-manage-app/CreateAlertFiltersPage.png)

	È anche possibile **aggregare** gli eventi di avviso, come illustrato di seguito:

	![Avvisi aggregati](./media/data-factory-monitor-manage-app/AggregateAlerts.png)
2. Selezionare l'**evento**, lo **stato** e lo **stato secondario** (facoltativo) per cui si vuole ricevere un avviso dal servizio Data Factory e fare clic su **Next**. Verrà visualizzata la pagina dei **Recipients**.

	![Creazione di avvisi: pagina Recipients](./media/data-factory-monitor-manage-app/CreateAlertRecipientsPage.png) 
3. Selezionare l'opzione **Email subscription admins** e/o immettere un valore per **Additional administrator email** e quindi fare clic su **Finish**. L'avviso verrà visualizzato nell'elenco. 
	
	![Elenco degli avvisi](./media/data-factory-monitor-manage-app/AlertsList.png)

Nell'elenco degli avvisi, usare i pulsanti associati a un avviso per modificare, eliminare, disabilitare o abilitare l'avviso.

### Evento, stato e stato secondario
La tabella seguente fornisce l'elenco degli eventi, stati e stati secondari disponibili.

Nome evento | Stato | Stato secondario
-------------- | ------ | ----------
Esecuzione attività avviata | Started | Avvio in corso
Esecuzione attività terminata | Succeeded | Succeeded 
Esecuzione attività terminata | Failed| Failed Resource Allocation<p>Failed Execution</p><p>Timed Out</p><p>Failed Validation</p><p>Abandoned</p>
Creazione cluster HDI su richiesta avviata | Started | &nbsp; |
Creazione cluster HDI su richiesta completata | Succeeded | &nbsp; |
Cluster HDI su richiesta eliminato | Succeeded | &nbsp; |
### Per modificare, eliminare o disabilitare un avviso


![Pulsanti degli avvisi](./media/data-factory-monitor-manage-app/AlertButtons.png)



    
 

<!---HONumber=AcomDC_0218_2016-->