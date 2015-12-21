<properties 
	pageTitle="Istruzioni di configurazione del dashboard di PowerBI per il modello di soluzione per l'analisi dei dati di telemetria del veicolo | Microsoft Azure" 
	description="Usare le funzionalità di Cortana Analytics per ottenere informazioni dettagliate predittive e in tempo reale sul funzionamento del veicolo e sulle abitudini di guida." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="bradsev" />


# Istruzioni di configurazione del dashboard di PowerBI per il modello di soluzione per l'analisi dei dati di telemetria del veicolo

Questo **menu** contiene i collegamenti alle sezioni dello studio.

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]


La soluzione per l'analisi dei dati di telemetria del veicolo illustra come le concessionarie d'auto, i produttori di automobili e le compagnie di assicurazione possano sfruttare le funzionalità di Cortana Analytics per ottenere informazioni dettagliate predittive e in tempo reale sul funzionamento del veicolo e sulle abitudini di guida in modo da apportare miglioramenti nelle aree dell'esperienza cliente, di ricerca e sviluppo e delle campagne di marketing. Questo documento contiene istruzioni dettagliate su come configurare i report e il dashboard di PowerBI dopo la distribuzione della soluzione nella sottoscrizione.


## Prerequisiti
1.	Distribuire la soluzione per l'analisi dei dati di telemetria del veicolo passando a [https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3](https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3)  
2.	[Installare Microsoft PowerBI Desktop](http://www.microsoft.com/download/details.aspx?id=45331)
3.	Una [sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/). Se non si dispone di una sottoscrizione di Azure, iniziare con una sottoscrizione gratuita di Azure
4.	Un account di Microsoft PowerBI
	

## Componenti di Cortana Analytics Suite
Come parte del modello di soluzione per l'analisi dei dati di telemetria del veicolo, vengono distribuiti nella sottoscrizione i servizi di Cortana Analytics seguenti.

- **Hub eventi** per l'inserimento di milioni di eventi di telemetria del veicolo in Azure.
- **Analisi di flusso** per ottenere informazioni dettagliate in tempo reale sul funzionamento del veicolo e salvare i dati in modo permanente nello spazio di archiviazione a lungo termine per un'analisi batch avanzata.
- **Machine Learning** per il rilevamento di anomalie in tempo reale e l'elaborazione batch per ottenere informazioni predittive.
- **HDInsight** per trasformare i dati su larga scala
- **Data Factory** gestisce l'orchestrazione, la pianificazione, la gestione delle risorse e il monitoraggio della pipeline di elaborazione batch.

**PowerBI** offre alla soluzione un dashboard completo per la visualizzazione di analisi predittive e di dati in tempo reale.

La soluzione usa due origini dati diverse: **un set di dati di diagnostica e di segnali simulati del veicolo** e **un catalogo del veicolo**.

Nella soluzione è incluso un simulatore di dati telematici relativi al veicolo. Il simulatore genera informazioni di diagnostica e segnali corrispondenti allo stato del veicolo e allo schema di guida in un determinato momento.

Il catalogo del veicolo è un set di dati di riferimento contenente il VIN (Vehicle Identification Number, numero di identificazione del veicolo) per il mapping del modello


## Preparazione del dashboard di PowerBI

### Distribuzione

Dopo aver completato la distribuzione, verrà visualizzato il diagramma seguente con tutti i componenti contrassegnati con il colore verde.

- Fare clic sulla freccia in alto a destra dei nodi verdi per passare ai servizi corrispondenti e verificare se sono stati tutti distribuiti correttamente.
- Fare clic sulla freccia in alto a destra del nodo del simulatore di dati telematici relativi al veicolo per scaricare il pacchetto del simulatore di dati. Salvare ed estrarre i file in locale nel computer. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/1-deployed-components.png)

A questo punto si è pronti per configurare il dashboard di PowerBI con visualizzazioni avanzate per ottenere informazioni dettagliate predittive e in tempo reale sul funzionamento del veicolo e sulle abitudini di guida. Per creare tutti i report e configurare il dashboard sono necessari da circa 45 minuti a un'ora.


### Configurare il dashboard in tempo reale di PowerBI

**Generare dati simulati**

1. Nel computer locale passare alla cartella in cui è stato estratto il pacchetto Vehicle Telematics Simulator.![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/2-vehicle-telematics-simulator-folder.png)
2.	Eseguire l'applicazione ***CarEventGenerator.exe***.
3.	Il simulatore genera informazioni di diagnostica e segnali corrispondenti allo stato del veicolo e allo schema di guida in un determinato momento. Viene pubblicato in un'istanza di Hub eventi di Azure configurata come parte della distribuzione.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/3-vehicle-telematics-diagnostics.png)
	 
**Avviare l'applicazione dashboard in tempo reale**

Nella soluzione è inclusa un'applicazione per generare il dashboard in tempo reale in Power BI. Questa applicazione è in attesa di un'istanza di Hub eventi in cui l'analisi di flusso pubblica gli eventi in un modello eseguito in modo continuo. Per ogni evento ricevuto, l'applicazione elabora i dati usando l'endpoint di assegnazione dei punteggi di richiesta-risposta di Machine Learning e il set di dati risultante viene pubblicato nell'API delle notifiche push di PowerBI per la visualizzazione.

Per scaricare l'applicazione:

1.	Fare clic sul nodo Power BI nella vista diagramma e fare clic sul collegamento **Download Real-time Dashboard Application** nel riquadro delle proprietà.![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard-new1.png)
2.	Estrarre e salvare l'applicazione in locale ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/4-real-time-dashboard-application.png)

3.	Eseguire l'applicazione **RealtimeDashboardApp.exe**
4.	Fornire credenziali di Power BI valide, accedere e fare clic su **Accetta**
	
	![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
	
	![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)


### Configurare report di PowerBI
Il completamento dei report in tempo reale e del dashboard richiede circa 30-45 minuti. Passare a [http://powerbi.com](http://powerbi.com) e accedere.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

Verrà generato un nuovo set di dati in PowerBI. Fare clic sul set di dati **ConnectedCarsRealtime**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

Salvare il report vuoto premendo **CTRL+S**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

Specificare il nome del report *Analisi dei dati di telemetria del veicolo in tempo reale - Report*.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## Report in tempo reale
Questa soluzione include 3 report in tempo reale:

1.	Veicoli operativi
2.	Veicoli che richiedono manutenzione
3.	Statistiche sull'integrità dei veicoli

È possibile scegliere di configurare tutti e 3 i report in tempo o interrompere la procedura dopo ogni fase e passare alla successiva sezione di configurazione dei report in batch. È consigliabile creare tutti e 3 i report per visualizzare le informazioni dettagliate complete del percorso in tempo reale della soluzione.

### 1\. Veicoli operativi
  
Fare doppio clic su **Page 1** e rinominarla in "Veicoli operativi" ![Automobili connesse: veicoli operativi](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)

Selezionare il campo **vin** da **Fields** e scegliere **"Card"** come tipo di visualizzazione.

Verrà creata una visualizzazione di tipo scheda come illustrato nella figura. ![Automobili connesse: seleziona vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Selezionare **City** e **vin** dai campi. Cambiare la visualizzazione in **"Map"**. Trascinare **vin** nell'area dei valori. Trascinare **city** dai campi nell'area **Legend**. ![Automobili connesse: visualizzazione scheda](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)
  
Selezionare la sezione **format** da **Visualizations**, fare clic su **Title** e modificare il testo in **"Veicoli operativi per città"**. ![Automobili connesse: veicoli operativi in base alla città](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)

La visualizzazione finale sarà simile a quella illustrata nella figura. ![Automobili connesse: visualizzazione finale](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Selezionare **City** e **vin**, modificare il tipo di visualizzazione in **Clustered Column Chart**. Assicurarsi che il campo **City** sia in **Axis area** e **vin** in**Value area**.

Ordinare il grafico in base a **"Count of vin"** ![Automobili connesse: conteggio di vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)

Modificare il campo **Title** del grafico in **"Veicoli operativi per città"**

Fare clic sulla sezione **Format**, quindi selezionare **Data Colors** e fare clic su **"On"** in **Show All** ![Automobili connesse: mostra tutti i colori dei dati](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)

Modificare il colore delle singole città facendo clic sull'icona del colore. ![Automobili connesse: modifica colori](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Dalle visualizzazioni selezionare **Clustered Column Chart**, trascinare il campo **city** nell'area **Axis**, **Model** nell'area **Legend** e **vin** nell'area **Value**. ![Automobili connesse: istogramma a colonne raggruppate](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png) ![Automobili connesse: rendering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)
  
Ridisporre la visualizzazione nella pagina come illustrato nella figura. ![Automobili connesse: visualizzazioni](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Il report in tempo reale "Veicoli operativi" è stato configurato correttamente. È possibile procedere per creare il report in tempo reale successivo o interrompere la procedura e configurare il dashboard.

### 2\. Veicoli che richiedono manutenzione
  
Fare clic su ![Aggiungi](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) per aggiungere un nuovo report e rinominarlo in **"Veicoli che richiedono manutenzione"**

![Automobili connesse: veicoli che richiedono manutenzione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)

Selezionare il campo **vin** e modificare il tipo di visualizzazione in **Card**. ![Automobili connesse: visualizzazione scheda vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)

Nel set di dati è presente un campo denominato "MaintenanceLabel". Questo campo può avere un valore pari a "0" o "1". È impostato dal modello di Azure Machine Learning fornito come parte della soluzione e integrato nel percorso in tempo reale. Il valore "1" indica che un veicolo richiede manutenzione.

Per visualizzare i dati relativi ai veicoli che richiedono manutenzione, si aggiungerà il filtro **Page Level**.

1. Trascinare il campo **"MaintenanceLabel"** in **Page Level Filters**. ![Automobili connesse: filtri a livello di pagina](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  

2. Fare clic sul menu **Basic Filtering** presente in basso nella schermata del filtro MaintenanceLabel in Page Level Filter. ![Automobili connesse: filtro base](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)

3.  Impostare il valore del filtro su **"1"** ![Automobili connesse: valore di filtro](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)


Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Dalle visualizzazioni selezionare **Clustered Column Chart** ![Automobili connesse: visualizzazione scheda vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png) ![Automobili connesse: istogramma a colonne raggruppate](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

Trascinare il campo **Model** nell'area **Axis** e **vin** nell'area **Value**. Ordinare quindi la visualizzazione in base a **Count of vin**. Modificare il campo **Title** del grafico in **"Veicoli che richiedono manutenzione in base al modello"**

Trascinare i campi **vin** in **Color Saturation** presente nella sezione **Fields** ![Fields](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) della scheda **Visualization** ![Automobili connesse: saturazione dei colori](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)

Nelle visualizzazioni modificare **Data Colors** dalla sezione **Format** Modificare il colore Minimum in: **F2C812** Modificare il colore Maximum in: **FF6300** ![Automobili connesse: modifiche dei colori](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png) ![Automobili connesse: nuovi colori di visualizzazione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Nelle visualizzazioni selezionare **Clustered column chart** , trascinare il campo **vin** nell'area **Value** e il campo **City** nell'area **Axis**. Ordinare il grafico in base a **"Count of vin"**. Modificare il campo **Title** del grafico in **"Veicoli che richiedono manutenzione in base alla città"** ![Automobili connesse: veicoli che richiedono manutenzione in base alla città](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Nelle visualizzazioni selezionare **Multi Row Card**, trascinare **Model** e **vin** nell'area **Fields**. ![Automobili connesse: scheda con più righe](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)

Ridisporre tutte le visualizzazioni. Il report finale avrà un aspetto simile a quello illustrato di seguito ![Automobili connesse: scheda con più righe](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)

Il report in tempo reale "Veicoli che richiedono manutenzione" è stato configurato correttamente. È possibile procedere per creare il report in tempo reale successivo o interrompere la procedura e configurare il dashboard.

### 3\. Statistiche sull'integrità dei veicoli
  
Fare clic su ![Aggiungi](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) per aggiungere un nuovo report e rinominarlo in **"Statistiche sull'integrità dei veicoli"**

Nelle visualizzazioni selezionare **Gauge**, quindi trascinare il campo **Speed** nelle aree **Value, Minimum Value, Maximum Value**. ![Automobili connesse: scheda con più righe](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)

Modificare l'aggregazione predefinita di **speed** nell'area **Value** su **Average**

Modificare l'aggregazione predefinita di **speed** nell'area **Minimum Value** su **Minimum**

Modificare l'aggregazione predefinita di **speed** nell'area **Maximum Value** su **Minimum**

![Automobili connesse: scheda con più righe](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)

Rinominare il campo **Gauge Title** in **"Velocità media"**
 
![Automobili connesse: misuratore](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Allo stesso modo, aggiungere un campo **Gauge** per **average engine oil**, **average fuel** e **average engine temperature**.

Modificare l'aggregazione predefinita dei campi in ogni misuratore come indicato nei passaggi precedenti per il misuratore **"Velocità media"**.

![Automobili connesse: misuratori](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Nelle visualizzazioni selezionare **Line and Clustered Column Chart**, quindi trascinare il campo **City** in **Shared Axis**, trascinare i campi **speed**, **tirepressure e engineoil** nell'area **Column Values** e modificare il relativo tipo di aggregazione in **Average**.

Trascinare il campo **engineTemperature** nell'area **Line Values** e modificare il tipo di aggregazione in **Average**.

![Automobili connesse: campi di visualizzazione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

Modificare il campo **Title** del grafico in **"Media velocità, pressione pneumatici, temperatura dell'olio e del motore"**

![Automobili connesse: campi di visualizzazione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Nelle visualizzazioni selezionare **Treemap**, trascinare il campo **Model** nell'area **Group** e il campo **MaintenanceProbability** nell'area **Values**.

Modificare il campo **Title** del grafico in **"Modelli di veicolo che richiedono manutenzione"**

![Automobili connesse: modifica titolo del grafico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Nelle visualizzazioni selezionare **100% Stacked Bar Chart**, trascinare il campo **city** nell'area **Axis** e i campi **MaintenanceProbability** e **RecallProbability** nell'area **Value**.

![Automobili connesse: aggiungi nuova visualizzazione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

Fare clic su **Format**, selezionare **Colori dei dati**, modificare il colore di **MaintenanceProbability** e impostarlo sul valore **"F2C80F"**.

Modificare il campo **Title** del grafico in **"Probabilità di manutenzione e richiamo del veicolo in base alla città"**

![Automobili connesse: aggiungi nuova visualizzazione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Nelle visualizzazioni selezionare **Area Chart**, trascinare il campo **Model** nell'area **Axis** e i campi **engineOil, tirepressure, speed e MaintenanceProbability** nell'area **Values**. Modificare il tipo di aggregazione in **"Average"**.

![Automobili connesse: modifica tipo di aggregazione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

Modificare il titolo del grafico in **"Media per olio motore, pressione pneumatici, velocità e probabilità di manutenzione in base al modello"**.

![Automobili connesse: modifica titolo del grafico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

1. Nelle visualizzazioni selezionare **Scatter Chart**
2. Trascinare il campo **Model** nelle aree **Details** e **Legend**. 
3. Trascinare il campo **fuel** nell'area **X Axis** e modificare l'aggregazione in Average.
4. Trascinare il campo **engineTemperature** nell'area **Y Axis** e modificare l'aggregazione in **Average**.
5. Trascinare il campo **vin** nell'area **Size**.

![Automobili connesse: aggiungi nuova visualizzazione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

Modificare il campo **Title** del grafico in **"Medie per carburante e temperatura del motore in base al modello"**.

![Automobili connesse: modifica titolo del grafico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

Il report finale avrà un aspetto simile a quello illustrato di seguito.

![Automobili connesse: report finale](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### Aggiungere visualizzazioni dai report al dashboard in tempo reale
  
Creare un dashboard vuoto facendo clic sull'icona del segno più accanto a Dashboard. È possibile denominarlo "Dashboard di analisi dei dati di telemetria di veicolo".

![Automobili connesse: dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

Aggiungere la visualizzazione dei report precedenti al dashboard.
 
![Automobili connesse: dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

Il dashboard dovrebbe avere l'aspetto illustrato di seguito una volta creati tutti e 3 i report e aggiunte le visualizzazioni corrispondenti al dashboard. Il dashboard potrebbe apparire diverso se non sono stati creati tutti i report.

![Automobili connesse: dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

La procedura è stata completata. Il dashboard in tempo reale è stato creato correttamente. Continuando a eseguire CarEventGenerator.exe e RealtimeDashboardApp.exe, gli aggiornamenti saranno visualizzati in tempo reale nel dashboard. Il completamento dei passaggi seguenti dovrebbe richiedere circa 10-15 minuti .
 
##  Configurare il dashboard di elaborazione batch di PowerBI

Nota: occorrono circa 2 ore dal completamento della distribuzione perché la pipeline di elaborazione batch end-to-end completi l'esecuzione ed elabori un anno di dati generati. Attendere prima di procedere con i passaggi successivi.

**Scaricare il file di progettazione di Power BI** • Un file di progettazione di Power BI preconfigurato è incluso nella distribuzione • Fare clic sul nodo di Power BI nella visualizzazione diagramma e fare clic sul collegamento per scaricare il file di progettazione di Power BI nel riquadro delle proprietà ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9.5-download-powerbi-designer.png)

• Salvare in locale

**Configurare i report di Power BI** • Aprire il file di progettazione "VehicleTelemetryAnalytics - Desktop Report.pbix" con Power BI Desktop. Se non lo si è ancora fatto, installare Power BI Desktop [qui](http://www.microsoft.com/download/details.aspx?id=45331).

• Fare clic su **Modifica query**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

- Fare doppio clic su **Origine**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

- Aggiornare la stringa di connessione del server con il server di Azure SQL di cui è stato effettuato il provisioning come parte della distribuzione. Fare clic sul nodo di Azure SQL nel diagramma e visualizzare il nome del server nel riquadro proprietà.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11.5-view-server-name.png)

- Lasciare **Database** come *connectedcar*.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

- Fare clic su **OK**.
- La scheda **Credenziali di Windows** è selezionata per impostazione predefinita. Modificarla in **Credenziali di database** facendo clic sulla scheda **Database** a destra.
- Specificare **Nome utente** e **Password** del database SQL di Azure specificato durante la relativa configurazione della distribuzione.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

- Fare clic su **Connetti**
- Ripetere i passaggi precedenti per ciascuna delle tre query rimanenti presenti nel riquadro destro e aggiornare i dettagli di connessione dell'origine dati.
- Fare clic su **Chiudi e carica**. I set di dati del file PowerBI Desktop sono connessi alle tabelle di database SQL di Azure.
- Fare clic su **Chiudi** per chiudere il file Power BI Desktop.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

- Fare clic sul pulsante **Salva** per salvare le modifiche apportate. 
 
Sono stati configurati tutti i report corrispondenti al percorso di elaborazione batch nella soluzione.


## Caricare in *powerbi.com*
 
1.	Passare al portale Web di Power BI all'indirizzo http://powerbi.com e accedere.
2.	Fare clic su Ottieni dati  
3.	Caricare il file PowerBI Desktop.  
4.	Per caricarlo, fare clic su **Recupera dati -> Recupera File -> File locale**.  
5.	Passare a **"VehicleTelemetryAnalytics – Desktop Report.pbix"**.  
6.	Una volta caricato il file, si verrà reindirizzati nuovamente all'area di lavoro di PowerBI.  

Verranno creati automaticamente un set di dati, un report e un dashboard vuoto.
 

Aggiungere grafici al dashboard esistente **Vehicle Telemetry Analytics Dashboard** in **Power BI**. In Power BI fare clic sul dashboard vuoto creato in precedenza, quindi passare alla sezione **Report** e fare clic sul report appena caricato.

![Telemetria veicolo PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png)


**Il report include sei pagine:** Pagina 1: Densità di veicoli Pagina 2: Integrità dei veicoli in tempo reale Pagina 3: Veicoli guidati in modo aggressivo Pagina 4: Veicoli richiamati Pagina 5: Veicoli guidati con un consumo efficiente del carburante Pagina 6: Logo Contoso

![Automobili connesse PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)
 

**Dalla pagina 3** aggiungere quanto segue 1. Conteggio del numero identificativo del veicolo ![Automobili connesse PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

2.	Veicoli guidati in modo aggressivo in base al modello: grafico a cascata ![Telemetria veicolo: aggiungi grafici 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**Dalla pagina 5** aggiungere quanto segue 1. Conteggio del numero identificativo del veicolo ![Telemetria veicolo: aggiungi grafici 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png) 2. Veicoli guidati con un consumo efficiente del carburante in base al modello: istogramma a colonne raggruppate ![Telemetria veicolo: aggiungi grafici 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**Dalla pagina 4** aggiungere quanto segue

1.	Conteggio del numero identificativo del veicolo ![Telemetria veicolo: aggiungi grafici 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

2.	Veicoli richiamati in base alla città e al modello: mappa ad albero ![Telemetria veicolo: aggiungi grafici 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)

**Dalla pagina 6** aggiungere quanto segue

1.	Logo Contoso Motors ![Telemetria veicolo: aggiungi grafici 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**Organizzare il dashboard**

1.	Passare al dashboard
2.	Passare il mouse su ciascun grafico e rinominarlo in base alla denominazione specificata nell'immagine seguente del dashboard completo. Spostare anche i grafici come illustrato nel dashboard seguente. ![Telemetria veicolo: organizza dashboard 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) ![Telemetria veicolo PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)
3. Il dashboard completato finale sarà simile al seguente, se tutti i report sono stati creati come indicato in questo documento. 

![Telemetria veicolo: organizza dashboard 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

La procedura è stata completata. Sono stati creati il report e il dashboard per ottenere in tempo reale informazioni dettagliate in batch e predittive sullo stato di integrità dei veicoli e sulle abitudini di guida.

<!---HONumber=AcomDC_1210_2015-->