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

1. Nel computer locale passare alla cartella in cui è stato estratto il pacchetto del simulatore di dati telematici relativi al veicolo. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/2-vehicle-telematics-simulator-folder.png)

2.	Eseguire l'applicazione ***CarEventGenerator.exe***.
3.	Il simulatore genera informazioni di diagnostica e segnali corrispondenti allo stato del veicolo e allo schema di guida in un determinato momento. Viene pubblicato in un'istanza di Hub eventi di Azure configurata come parte della distribuzione.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/3-vehicle-telematics-diagnostics.png)
	 
Avviare l'applicazione dashboard in tempo reale Nella soluzione è inclusa un'applicazione per generare il dashboard in tempo reale in PowerBI. Questa applicazione è in attesa di un'istanza di Hub eventi in cui l'analisi di flusso pubblica gli eventi in un modello eseguito in modo continuo. Per ogni evento ricevuto, l'applicazione elabora i dati usando l'endpoint di assegnazione dei punteggi di richiesta-risposta di Machine Learning e il set di dati risultante viene pubblicato nell'API delle notifiche push di PowerBI per la visualizzazione. Per scaricare l'applicazione:

1.	Fare clic sul nodo PowerBI nella visualizzazione diagramma e quindi sul collegamento **Scarica applicazione dashboard in tempo reale** nel riquadro delle proprietà.

2.	Estrarre e salvare in locale

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/4-real-time-dashboard-application.png)

3.	Eseguire l'applicazione "RealtimeDashboardApp.exe"
4.	Fornire credenziali di PowerBI valide, accedere e fare clic su **Accetta**
	
![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
	
![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)
 
## Creare report di PowerBI in tempo reale 

### Configurare report di PowerBI

Verrà generato un nuovo set di dati in PowerBI. Fare clic sul set di dati **ConnectedCarsRealtime**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

Salvare il report vuoto premendo **CTRL+S**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

Specificare il nome del report *Analisi dei dati di telemetria del veicolo in tempo reale: report*.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)


### Creare un report in tempo reale: *Veicoli operativi*
  
Fare doppio clic sulla **Pagina 1** e rinominarla "Veicoli operativi" ![Automobili connesse: veicoli operativi](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)

Selezionare il campo **vin** da **Campi** e scegliere **"Scheda"** come tipo di visualizzazione.

Verrà creata una visualizzazione scheda come illustrato nella figura. ![Automobili connesse: seleziona vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Selezionare **Città** e **vin** dai campi. Modificare la visualizzazione in **"Mappa"**. Trascinare **vin** nell'area valori. Trascinare **città** dai campi nell'area **Legenda**. ![Automobili connesse: visualizzazione scheda](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)
  
Selezionare la sezione **formato** da **Visualizzazioni**, fare clic su **Titolo** e modificare il testo in **"Veicoli operativi in base alla città"**. ![Automobili connesse: veicoli operativi in base alla città](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)

La visualizzazione finale sarà simile a quella illustrata nella figura. ![Automobili connesse: visualizzazione finale](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Selezionare **Città** e **vin**, modificare il tipo di visualizzazione in **Istogramma a colonne raggruppate**. Verificare che il campo **Città** sia nell'**Area asse** e **vin** nell'**Area valore**

Ordinare il grafico in base a **"Conteggio di vin"** ![Automobili connesse: conteggio di vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)

Modificare il **Titolo** del grafico in **"Veicoli operativi in base alla città"**

Fare clic sulla sezione **Formato**, quindi selezionare **Colori dei dati** e in **Mostra tutto** fare clic su **"Attivato"** ![Automobili connesse: mostra tutti i colori dei dati](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)

Modificare il colore delle singole città facendo clic sull'icona del colore. ![Automobili connesse: modifica colori](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Selezionare la visualizzazione **Istogramma a colonne raggruppate** dalle visualizzazioni, trascinare il campo **Città** nell'area **Asse**, **Modello** nell'area **Legenda** e **vin** nell'area **Valore**. ![Automobili connesse: istogramma a colonne raggruppate](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png) ![Automobili connesse: rendering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)
  
Ridisporre la visualizzazione nella pagina, come illustrato nella figura. ![Automobili connesse: visualizzazioni](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

### Aggiungere un nuovo report: *Veicoli che richiedono manutenzione*
  
Fare clic su ![Aggiungi](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) per aggiungere un nuovo report e rinominarlo **"Veicoli che richiedono manutenzione"**

![Automobili connesse: veicoli che richiedono manutenzione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)

Selezionare il campo **vin** e modificare il tipo di visualizzazione in **Scheda**. ![Automobili connesse: visualizzazione scheda vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)

Nel set di dati è presente un campo denominato **"MaintenanceLabel"**. In questo campo il valore presente sarà **"0"** e **"1"**. Il valore "1" indica veicoli che richiedono manutenzione

Per la visualizzazione di dati relativi ai veicoli che richiedono manutenzione, si aggiungerà il filtro **Livello di pagina**.

1. Trascinare il campo **"MaintenanceLabel"** in **Filtri a livello di pagina**. ![Automobili connesse: filtri a livello di pagina](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  

2. Fare clic sul menu **Filtro base** presente nella parte inferiore del filtro a livello di pagina MaintenanceLabel. ![Automobili connesse: filtro base](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)

3.  Impostarne il valore del filtro su **"1"** ![Automobili connesse: valore di filtro](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)
  
>[AZURE.NOTE]È possibile impostare filtri a livello di grafico, denominati **Filtri a livello visivo**. Tali filtri saranno applicabili solo a visualizzazioni specifiche. I Filtri a livello di pagina sono applicabili a tutte le visualizzazioni in un report.

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Selezionare **Istogramma a colonne raggruppate** dalle visualizzazioni ![Automobili connesse: visualizzazione scheda vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png) ![Automobili connesse: istogramma a colonne raggruppate](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

Trascinare il campo **Modello** nell'area **Asse** e **vin** nell'area **Valore**. Ordinare quindi la visualizzazione in base a **Conteggio di vin**. Modificare il **Titolo** del grafico in **"Veicoli che richiedono manutenzione in base al modello"**

Trascinare i campi **vin** in **Saturazione colore** presente nella sezione **Campi** ![Fields](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) della scheda **Visualizzazione** ![Automobili connesse: saturazione dei colori](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)

Modificare **Colori dei dati** nelle visualizzazioni dalla sezione **Formato** Modificare il colore minimo in: **F2C812** Modificare il colore massimo in: **FF6300** ![Automobili connesse: modifiche dei colori](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png) ![Automobili connesse: nuovi colori di visualizzazione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Selezionare **Istogramma a colonne raggruppate** dalle visualizzazioni, trascinare il campo **vin** nell'area **Valore** e il campo **Città** nell'area **Asse**. Ordinare il grafico in base a **"Conteggio di vin"**. Modificare il **Titolo** del grafico in **"Veicoli che richiedono manutenzione in base alla città"** ![Automobili connesse: veicoli che richiedono manutenzione in base alla città](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Selezionare la visualizzazione **Scheda con più righe** dalle visualizzazioni, trascinare **Modello** e **vin** nell'area **Campi**. ![Automobili connesse: scheda con più righe](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)

Ridisporre tutte le visualizzazioni. Il report finale avrà un aspetto simile a quello illustrato di seguito ![Automobili connesse: scheda con più righe](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)

### Aggiungere un nuovo report: *Statistiche sul funzionamento dei veicoli*
  
Fare clic su ![Aggiungi](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) per aggiungere un nuovo report e rinominarlo **"Statistiche sul funzionamento dei veicoli"**

Selezionare la visualizzazione **Misuratore** dalle visualizzazioni, quindi trascinare il campo **Velocità** nelle aree **Valore, Valore minimo, Valore massimo**. ![Automobili connesse: scheda con più righe](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)

Modificare in **Media** l'aggregazione predefinita di **velocità** nell'**Area valore**

Modificare in **Minima** l'aggregazione predefinita di **velocità** nell'**Area minima**

Modificare in **Massima** l'aggregazione predefinita di **velocità** nell'**Area massima**

![Automobili connesse: scheda con più righe](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)

Rinominare il **Titolo del misuratore** in **"Velocità media"**
 
![Automobili connesse: misuratore](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Allo stesso modo, aggiungere un **Misuratore** per **media olio motore**, **media carburante** e **media temperatura motore**.

Modificare l'aggregazione predefinita dei campi in ogni misuratore nel misuratore **"Velocità media"**, come indicato nei passaggi precedenti.

![Automobili connesse: misuratori](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Selezionare **Istogramma a colonne raggruppate** dalle visualizzazioni, quindi trascinare il campo **Città** in **Asse condiviso**, trascinare **velocità** e i **campi pressionegomme e oliomotore** nell'area **Valori colonna** e modificare il relativo tipo di aggregazione in **Media**.

Trascinare il campo **temperaturaMotore** nell'area **Valori linea** e modificare il tipo di aggregazione in **Media**.

![Automobili connesse: campi di visualizzazione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

Modificare il **Titolo** del grafico in **"Media velocità, pressione delle gomme, temperatura dell'olio e del motore"**

![Automobili connesse: campi di visualizzazione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Selezionare la visualizzazione **Grafico ad albero** dalle visualizzazioni, trascinare il campo **Modello** nell'area **Gruppo** e trascinare il campo **ProbabilitàManutenzione** nell'area **Valori**.

Modificare il **Titolo** del grafico in **"Modelli di veicolo che richiedono manutenzione"**

![Automobili connesse: modifica titolo del grafico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Selezionare la visualizzazione **Grafico a barre in pila 100%** dalle visualizzazioni, trascinare il campo **città** nell'area **Asse** e i campi **ProbabilitàManutenzione** e **ProbabilitàRichiamo** nell'area **Valore**

![Automobili connesse: aggiungi nuova visualizzazione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

Fare clic su **Formato**, selezionare **Colori dei dati**, modificare il colore di **ProbabilitàManutenzione** nel valore **"F2C80F"**

Modificare il **Titolo** del grafico in **"Probabilità di manutenzione e richiamo del veicolo in base alla città"**

![Automobili connesse: aggiungi nuova visualizzazione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

Selezionare la visualizzazione **Grafico ad area** dalle visualizzazioni, trascinare il campo **Modello** nell'area **Asse** e i campi **olioMotore, pressionegomme, velocità e ProbabilitàManutenzione** nell'area **Valori**. Modificare il tipo di aggregazione in **"Media"**.

![Automobili connesse: modifica tipo di aggregazione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

Modificare il titolo del grafico in **"Media olio motore, pressione delle gomme, velocità e probabilità di manutenzione in base al modello"**

![Automobili connesse: modifica titolo del grafico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

Fare clic sull'area vuota per aggiungere una nuova visualizzazione.

1. Selezionare la visualizzazione **Grafico a dispersione** dalle visualizzazioni
2. Trascinare il campo **Modello** nelle aree **Dettagli** e **Legenda** 
3. Trascinare il campo **carburante** nell'area **Asse X** e modificare l'aggregazione in Media
4. Trascinare il campo **temperaturaMotore** nell'area **Asse Y** e modificare l'aggregazione in **Media**
5. Trascinare il campo **vin** nell'area **Dimensioni**

![Automobili connesse: aggiungi nuova visualizzazione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

Modificare il **Titolo** del grafico in **"Medie carburante e temperatura del motore in base al modello"**

![Automobili connesse: modifica titolo del grafico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

Il report finale avrà un aspetto simile a quello illustrato di seguito.

![Automobili connesse: report finale](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### Aggiungere visualizzazioni dai report al dashboard in tempo reale
  
Aggiungere la visualizzazione dai report riportati sopra al dashboard in tempo reale creato nella sezione 2: **Configurare il dashboard di PowerBI**.

Il dashboard dovrebbe avere un aspetto simile a quello illustrato di seguito.

![Automobili connesse: dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)


##  Configurare il dashboard di elaborazione batch di PowerBI

>[AZURE.ALERT]Sono necessarie circa 2 ore per il completamento dell'esecuzione della pipeline di elaborazione batch end-to-end. Attendere prima di procedere con i passaggi successivi.

**Scaricare il file di progettazione di PowerBI** • Un file di progettazione di PowerBI è incluso nella distribuzione • Fare clic sul nodo di PowerBI nella visualizzazione diagramma e fare clic sul collegamento "Scarica il file di progettazione di PowerBI" nel riquadro delle proprietà • Salvare in locale

**Configurare i report di PowerBI** • Aprire il file di progettazione "VehicleTelemetryAnalytics - Desktop Report.pbix" tramite PowerBI Desktop. Se non lo si è ancora fatto, installare PowerBI Desktop- http://www.microsoft.com/it-IT/download/details.aspx?id=45331 • Fare clic su **Modifica query**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

[AZURE.NOTE]Il valore **nome/password** del database SQL di Azure è quello fornito durante la configurazione della distribuzione

- Fare doppio clic su **Origine**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

- stringa di connessione date Server
- Impostare **Database** su *autoconnessa*.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

- Fare clic su **OK**.
- La scheda **Credenziali Windows** sarà selezionata per impostazione predefinita, modificarla in **Credenziali database** facendo clic sulla scheda **Database** a destra.
- Specificare **Nome utente** e **Password**

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

- Fare clic su **Connetti**
- Ripetere i passaggi precedenti per ciascuna delle tre query rimanenti presenti nel riquadro destro e aggiornare i dettagli di connessione dell'origine dati.
- Fare clic su **Chiudi e carica**. I set di dati del file PowerBI Desktop sono connessi alle tabelle di database SQL di Azure.
- **Chiudi** il file PowerBI Desktop.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

- Fare clic sul pulsante **Salva** per salvare le modifiche apportate. 
 
**Caricare in *powerbi.com*** Passare a powerbi.com e accedere. ![Telemetria veicolo PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/15-vehicle-telemetry-powerbi-site.png)

1.	Fare clic su Ottieni dati  
2.	Caricare il file PowerBI Desktop.  
3.	Per caricarlo, fare clic su **Ottieni dati -> File -> File locale**  
4.	Passare a **"Automobili connesse: Desktop Report.pbix"**  
5.	Una volta caricato il file, si verrà reindirizzati nuovamente all'area di lavoro di PowerBI.  

	Verranno creati automaticamente un set di dati, un report e un dashboard vuoto.

	>**Nota:** potrebbe essere necessario creare un dashboard vuoto facendo clic sull'icona del segno più accanto a Dashboard.

**Aggiungere grafici al dashboard** In PowerBI fare clic sul dashboard vuoto creato in precedenza, quindi passare alla sezione Report e fare clic sul report appena caricato.

![Telemetria veicolo PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png)


**Il report include sei pagine:** Pagina 1: densità del veicolo Pagina 2: integrità del veicolo in tempo reale Pagina 3: veicoli guidati in modo aggressivo Pagina 4: veicoli richiamati Pagina 5: veicoli guidati con un consumo efficiente del carburante Pagina 6: logo Contoso

![Automobili connesse PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)
 

**Dalla pagina 3** aggiungere quanto segue 1. Conteggio di VIN ![Automobili connesse PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 2. Veicoli guidati in modo aggressivo in base al modello: grafico Waterfall ![Telemetria veicolo: aggiungi grafici 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**Dalla pagina 5** aggiungere quanto segue 1. Conteggio di vin ![Telemetria veicolo: aggiungi grafici 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png) 2. Veicoli guidati con un consumo efficiente del carburante in base al modello: istogramma a colonne raggruppate ![Telemetria veicolo: aggiungi grafici 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**Dalla pagina 4** aggiungere quanto segue

1.	Conteggio di vin ![Telemetria veicolo: aggiungi grafici 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

2.	Veicoli richiamati in base alla città e al modello: grafico ad albero ![Telemetria veicolo: aggiungi grafici 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)

**Dalla pagina 6** aggiungere quanto segue

1.	Logo Contoso Motors ![Telemetria veicolo: aggiungi grafici 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**Organizzare il dashboard**

1.	Passare al dashboard ![Telemetria veicolo: organizza dashboard 1](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard1.png)

2.	Passare il mouse su ciascun grafico e rinominarlo in base alla denominazione specificata nell'immagine seguente del dashboard completo. Spostare inoltre i grafici come illustrato nel dashboard seguente. ![Telemetria veicolo: organizza dashboard 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)

![Telemetria veicolo PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

<!---HONumber=AcomDC_1203_2015-->