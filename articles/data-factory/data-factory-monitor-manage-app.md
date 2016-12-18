---
title: Monitorare e gestire le pipeline di Data factory di Azure
description: Informazioni sull&quot;uso dell&quot;app di monitoraggio e gestione per monitorare e gestire le data factory e le pipeline di Azure.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 0bb9269d20c157221faa5604e68acc3410a5247b
ms.openlocfilehash: d8e5e8e99d40af959f8fa1dd4bf7b636a9f2343b


---
# <a name="monitor-and-manage-azure-data-factory-pipelines-using-new-monitoring-and-management-app"></a>Monitorare e gestire le pipeline di Azure Data Factory con la nuova app di monitoraggio e gestione
> [!div class="op_single_selector"]
> * [Con il portale di Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Con l'app di monitoraggio e gestione](data-factory-monitor-manage-app.md)
> 
> 

Questo articolo descrive le procedure di monitoraggio, gestione e debug delle pipeline e indica come creare avvisi per ricevere notifiche sugli errori usando l' **app di monitoraggio e gestione**. L'uso dell'app di monitoraggio e gestione è illustrato anche nel video riportato di seguito.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
> 
> 

## <a name="launching-the-monitoring-and-management-app-a"></a>Avvio dell'app di monitoraggio e gestione
Per avviare l'app di monitoraggio e gestione, fare clic sul riquadro **Monitoring & Manage** (Monitoraggio e gestione) nel pannello **DATA FACTORY** della data factory.

![Riquadro di monitoraggio nella home page di Data Factory](./media/data-factory-monitor-manage-app/MonitoringAppTile.png) 

L'app di monitoraggio e gestione verrà avviata in una scheda o finestra separata.  

![app di monitoraggio e gestione](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Se il Web browser è bloccato su "Concessione autorizzazioni in corso...", disabilitare/deselezionare l'impostazione di **blocco dei cookie di terze parti e dei dati dei siti** oppure lasciarla abilitata e creare un'eccezione per **login.microsoftonline.com** quindi provare di nuovo ad avviare la procedura guidata.
> 
> 

Se non vengono visualizzate finestre attività nell’elenco nella parte inferiore, fare clic sul pulsante **Refresh** nella barra degli strumenti per aggiornare l'elenco. Impostare anche i valori corretti per i filtri **Start time** e **End time**.  

## <a name="understanding-the-monitoring-and-management-app"></a>Informazioni sull'app di monitoraggio e gestione
A sinistra sono disponibili tre schede, **Esplora inventario risorse**, **Visualizzazioni monitoraggio** e **Avvisi**. La scheda Esplora inventario risorse è selezionata per impostazione predefinita. 

### <a name="resource-explorer"></a>Scheda Resource Explorer
Saranno visualizzate le informazioni illustrate nell'immagine seguente: 

* **Visualizzazione albero** di Esplora inventario risorse nel pannello a sinistra.
* **Visualizzazione diagramma** in alto.
* **Finestre attività** in basso al riquadro nella parte centrale.
* **Proprietà**/**Esplora finestre attività** nel riquadro destro. 

In Esplora inventario risorse è possibile visualizzare tutte le risorse della data factory, ovvero le pipeline, i set di dati e i servizi collegati, in una visualizzazione albero. Quando si seleziona un oggetto in Esplora inventario risorse, si noti quanto segue: 

* L'entità di Data Factory associata viene evidenziata nella vista diagramma.
* Le finestre attività associate vengono evidenziate nell'elenco Finestre attività, nella parte inferiore del pannello centrale. Fare clic [qui](data-factory-scheduling-and-execution.md) per informazioni sulle finestre attività.  
* Le proprietà dell'oggetto selezionato vengono visualizzate nella finestra Properties nel riquadro destro. 
* La definizione JSON dell’oggetto selezionato se applicabile. Ad esempio un servizio collegato, un set di dati o una pipeline. 

![Scheda Resource Explorer](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Per informazioni dettagliate sulle finestre attività, vedere l'articolo [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) . 

### <a name="diagram-view"></a>Visualizzazione diagramma
La vista diagramma di una data factory fornisce un'unica console da cui monitorare e gestire la data factory e i relativi asset. Quando si seleziona un'entità di Data Factory, ovvero un set di dati o una pipeline, nella visualizzazione diagramma, si noti quanto segue:

* L'entità di Data Factory viene selezionata nella visualizzazione albero.
* Le finestre attività associate vengono evidenziate nell'elenco Activity Windows.
* Le proprietà dell'oggetto selezionato vengono visualizzate nella finestra Properties.

Quando la pipeline è abilitata, vale a dire non sospesa, viene visualizzata con una linea verde. 

![Pipeline di esecuzione](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Nella vista diagramma sono presenti tre pulsanti per la pipeline. Il secondo pulsante può essere usato per sospendere l'esecuzione della pipeline. Questa azione non termina le attività attualmente in esecuzione e le lascia continuare fino al completamento. Il terzo pulsante sospende l'esecuzione della pipeline e termina le attività esistenti in esecuzione. Il primo pulsante riprende l'esecuzione della pipeline. Quando l'esecuzione della pipeline viene sospesa, il colore del relativo riquadro cambia come illustrato di seguito.

![Sospensione/ripresa nel riquadro](./media/data-factory-monitor-manage-app/SuspendResumeOnTile.png)

È possibile selezionare due o più pipeline con il tasto CTRL e usare i pulsanti della barra dei comandi per sospendere o riprendere l'esecuzione di più pipeline contemporaneamente.

![Sospensione/ripresa nella barra dei comandi](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

È possibile visualizzare tutte le attività della pipeline facendo clic con il pulsante destro del mouse sul relativo riquadro e scegliendo **Apri pipeline**.

![Menu Open Pipeline](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

Nella visualizzazione pipeline aperta vengono visualizzate tutte le attività della pipeline. In questo esempio è presente soltanto l'attività di copia. Per tornare alla visualizzazione precedente, fare clic sul nome della data factory nel menu di navigazione nella parte superiore.

![Pipeline aperta](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Quando si fa clic o si passa il mouse su un set di dati di output nella visualizzazione pipeline, viene visualizzato il popup delle finestre attività per tale set di dati.

![Popup Activity Windows](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

È possibile fare clic su una finestra attività per visualizzarne i dettagli nella finestra **Proprietà** nel riquadro destro. 

![Proprietà della finestra attività](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

Nel riquadro destro passare alla scheda **Esplora finestre attività** per visualizzare altri dettagli.

![Esplora finestre attività](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png) 

Vengono visualizzate anche le **variabili risolte** per ciascun tentativo di esecuzione di attività nella sezione **Tentativi**. 

![Variabili risolte](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Passare alla scheda **Script** per vedere la definizione dello script JSON per l'oggetto selezionato.   

![Scheda Script](./media/data-factory-monitor-manage-app/ScriptTab.png)

Le finestre attività vengono visualizzate in tre posizioni:

* Popup Finestre attività nella visualizzazione diagramma, nel riquadro centrale.
* Activity Window Explorer nel riquadro destro.
* Elenco Activity Windows nel riquadro inferiore.

Nel popup Finestre attività e in Esplora finestre attività è possibile scorrere fino alla settimana precedente e quella successiva usando i pulsanti con la freccia sinistra e destra.

![Freccia sinistra/destra in Activity Window Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Nella parte inferiore della visualizzazione diagramma, sono presenti i pulsanti Zoom avanti, Zoom indietro, Adatta alla finestra, Zoom 100% e Blocca layout. Il pulsante Blocca layout impedisce di spostare accidentalmente tabelle e pipeline nella visualizzazione diagramma ed è attivo per impostazione predefinita. È possibile disabilitarlo e spostare le entità nel diagramma. Quando il blocco viene disabilitato, è possibile usare l'ultimo pulsante per posizionare automaticamente pipeline e tabelle. È anche possibile eseguire lo zoom avanti o indietro usando la rotellina del mouse.

![Comandi di zoom nella vista diagramma](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Elenco Activity Windows
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
<td>Retry</td><td>L'esecuzione dell'attività viene ritentata.</td>
</tr>
<tr>
<td>Convalida</td><td>La convalida non è ancora stata avviata.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>In attesa della riesecuzione della convalida.</td>
</tr>
<tr>
<tr
<td rowspan="2">InProgress</td><td>Convalida in corso.</td><td>Convalida in corso.</td>
</tr>
<td>-</td>
<td>È in corso l'elaborazione della finestra attività.</td>
</tr>
<tr>
<td rowspan="4">Operazione non riuscita</td><td>TimedOut</td><td>L'esecuzione ha richiesto più tempo di quello consentito dall'attività.</td>
</tr>
<tr>
<td>Canceled</td><td>Annullato dall'utente.</td>
</tr>
<tr>
<td>Convalida</td><td>Convalida non riuscita.</td>
</tr>
<tr>
<td>-</td><td>Non è stato possibile generare e/o convalidare la finestra attività.</td>
</tr>
<td>Ready</td><td>-</td><td>La finestra attività è pronta per l'utilizzo.</td>
</tr>
<tr>
<td>Skipped</td><td>-</td><td>La finestra attività non viene elaborata.</td>
</tr>
<tr>
<td>None</td><td>-</td><td>Una finestra attività esistente che in precedenza aveva un altro stato e che ora è stata reimpostata.</td>
</tr>
</table>


Quando si fa clic su una finestra attività nell'elenco, i relativi dettagli vengono visualizzati nella finestra **Esplora finestre attività** o **Proprietà** a destra.

![Esplora finestre attività](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Aggiornare le finestre attività
I dettagli non vengono aggiornati automaticamente. L'elenco delle finestre attività deve essere aggiornato manualmente usando il pulsante di **aggiornamento**, vale a dire il secondo pulsante sulla barra dei comandi.  

### <a name="properties-window"></a>Finestra Properties
La finestra Properties si trova nel riquadro destro dell'app di monitoraggio e gestione. 

![Finestra Properties](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Qui vengono visualizzate le proprietà dell'elemento selezionato nella visualizzazione albero di Resource Explorer, nella vista diagramma o nell'elenco delle finestre attività. 

### <a name="activity-window-explorer"></a>Esplora finestre attività
La finestra **Esplora finestre attività** si trova nel riquadro destro dell'app di monitoraggio e gestione. Qui vengono visualizzati i dettagli relativi alla finestra attività selezionata nel popup o nell'elenco delle finestre attività. 

![Esplora finestre attività](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Per passare a un'altra finestra attività, fare clic su di essa nella visualizzazione calendario in alto. È anche possibile usare i pulsanti con la freccia **sinistra**/**destra** nella parte superiore per visualizzare le finestre attività della settimana precedente o successiva.

I pulsanti della barra degli strumenti nel riquadro inferiore consentono di **rieseguire** la finestra attività o di **aggiornare** i dettagli nel riquadro. 

### <a name="script"></a>Script
È possibile usare la scheda **Script** per visualizzare la definizione JSON dell'entità Data Factory selezionata (servizio collegato, set di dati e pipeline). 

![Scheda Script](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="using-system-views"></a>Uso delle viste di sistema
L'app di monitoraggio e gestione include viste di sistema predefinite, **Finestre attività recenti**, **Finestre attività non riuscite** e **Finestre attività in corso**, che consentono di visualizzare le finestre attività recenti, non riuscite e in corso della data factory. 

Fare clic per passare alla scheda **Visualizzazioni monitoraggio** a sinistra. 

![Scheda Monitoring Views](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Al momento sono disponibili tre viste di sistema supportate. Selezionare un'opzione per visualizzare le finestre attività recenti, non riuscite o in corso nell'elenco Activity Windows, nella parte inferiore del riquadro centrale. 

Quando si seleziona l'opzione **Finestre attività recenti**, le finestre attività recenti vengono visualizzate in ordine decrescente in base all'**ora dell'ultimo tentativo**. 

L'opzione **Finestre attività non riuscite** consente di visualizzare tutte le finestre attività non riuscite nell'elenco. Selezionare una finestra attività non riuscita dall'elenco per visualizzarne i dettagli nella finestra **Proprietà** o in **Esplora finestre attività**. È anche possibile scaricare i log relativi a una finestra attività non riuscita. 

## <a name="sorting-and-filtering-activity-windows"></a>Ordinamento e filtro delle finestre attività
Modificare le impostazioni relative all'**ora di inizio** e all'**ora di fine** nella barra dei comandi per filtrare le finestre attività. Dopo aver modificato queste impostazioni, fare clic sul pulsante accanto all'ora di fine per aggiornare l'elenco delle finestre attività.

![Ora di inizio e ora di fine](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Al momento, nell'app di monitoraggio e gestione le ore sono in formato UTC. 
> 
> 

Nell' **elenco Finestre attività**fare clic sul nome di una colonna, ad esempio Stato. 

![Menu della colonna nell'elenco Activity Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

A questo punto è possibile eseguire le attività seguenti:

* Applicare l'ordinamento crescente.
* Applicare l'ordinamento decrescente.
* Filtrare in base a uno o più valori, ad esempio Pronto, In attesa e così via.

Quando si specifica un filtro in una colonna, il pulsante filtro è abilitato per la colonna a indicare che i valori nella colonna sono filtrati. 

![Filtro della colonna dell'elenco Activity Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Per cancellare i filtri è possibile usare la stessa finestra popup. Per cancellare tutti i filtri per l'elenco delle finestre attività, fare clic sul pulsante filtro nella barra dei comandi. 

![Cancellare tutti i filtri nell'elenco Activity Windows](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="performing-batch-actions"></a>Esecuzione di azioni batch
### <a name="rerun-selected-activity-windows"></a>Rieseguire finestre attività selezionate
Selezionare una finestra attività, fare clic sulla freccia giù del primo pulsante nella barra dei comandi e selezionare **Riesegui** / **Riesegui con upstream nella pipeline**. L'opzione **Riesegui con upstream nella pipeline** consente di rieseguire anche tutte le finestre attività upstream. 
    ![Rieseguire una finestra attività](./media/data-factory-monitor-manage-app/ReRunSlice.png)

È anche possibile selezionare più finestre attività nell'elenco e rieseguirle contemporaneamente. È possibile filtrare le finestre attività in base allo stato, ad esempio **Non riuscito**, quindi rieseguire le finestre attività non riuscite dopo aver corretto il problema che ne causa l'errore. Vedere la sezione seguente per informazioni dettagliate sull'applicazione di filtri alle finestre attività nell'elenco.  

### <a name="pauseresume-multiple-pipelines"></a>Sospendere o riprendere l'esecuzione di più pipeline
È possibile selezionare due o più pipeline con il tasto CTRL e usare i pulsanti della barra dei comandi, evidenziati dal rettangolo rosso nell'immagine seguente, per sospendere o riprendere l'esecuzione di più pipeline contemporaneamente.

![Sospensione/ripresa nella barra dei comandi](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

## <a name="creating-alerts"></a>Creazione di avvisi
La pagina Avvisi consente di creare nuovi avvisi e di visualizzare, modificare ed eliminare quelli esistenti. Permette anche di abilitare o disabilitare un avviso. Fare clic sulla scheda Avvisi per visualizzare la pagina.

![Scheda Alerts](./media/data-factory-monitor-manage-app/AlertsTab.png)

### <a name="to-create-an-alert"></a>Per creare un avviso
1. Fare clic su **Aggiungi avviso** per aggiungere un avviso. Verrà visualizzata la pagina dei dettagli. 
   
    ![Creazione di avvisi: pagina Details](./media/data-factory-monitor-manage-app/CreateAlertDetailsPage.png)
2. Specificare il **nome** e la **descrizione** dell'avviso e fare clic su **Avanti**. Viene visualizzata la pagina **Filtri** .
   
    ![Creazione di avvisi: pagina Filters](./media/data-factory-monitor-manage-app/CreateAlertFiltersPage.png)
3. Selezionare l'**evento**, lo **stato** e lo **stato secondario** (facoltativo) per cui si vuole ricevere un avviso dal servizio Data Factory e fare clic su **Avanti**. Dovrebbe essere visualizzata la pagina **Destinatari** .
   
    ![Creazione di avvisi: pagina Recipients](./media/data-factory-monitor-manage-app/CreateAlertRecipientsPage.png) 
4. Selezionare l'opzione **Invia email agli amministratori della sottoscrizione** e/o immettere un valore per l'**email degli amministratori aggiuntivi**, quindi fare clic su **Fine**. L'avviso verrà visualizzato nell'elenco. 
   
    ![Elenco degli avvisi](./media/data-factory-monitor-manage-app/AlertsList.png)

Nell'elenco degli avvisi, usare i pulsanti associati a un avviso per modificare, eliminare, disabilitare o abilitare l'avviso. 

### <a name="eventstatussubstatus"></a>Evento, stato e stato secondario
La tabella seguente fornisce l'elenco di eventi, stati e stati secondari disponibili.

| Nome evento | Stato | Stato secondario |
| --- | --- | --- |
| Esecuzione attività avviata |Started |Avvio in corso |
| Esecuzione attività terminata |Operazione completata |Operazione completata |
| Esecuzione attività terminata |Non riuscito |Allocazione risorse non riuscita<br/><br/>Esecuzione non riuscita<br/><br/>Timed Out<br/><br/>Failed Validation<br/><br/>Abbandonato |
| Creazione cluster HDI su richiesta avviata |Started |-|
| Creazione cluster HDI su richiesta completata |Operazione completata |-|
| Cluster HDI su richiesta eliminato |Operazione completata |-|

### <a name="to-editdeletedisable-an-alert"></a>Per modificare, eliminare o disabilitare un avviso
![Pulsanti degli avvisi](./media/data-factory-monitor-manage-app/AlertButtons.png)




<!--HONumber=Nov16_HO3-->


