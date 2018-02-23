---
title: "Dashboard di Power BI sull'integrità dei veicoli e sulle abitudini di guida - Azure | Microsoft Docs"
description: "Usare le funzionalità di Cortana Intelligence per ottenere informazioni dettagliate predittive e in tempo reale sullo stato di integrità del veicolo e sulle abitudini di guida."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: bradsev
ms.openlocfilehash: 626987ec0648f9e770499b4a48bc4ca2d175d2b4
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Istruzioni di configurazione del dashboard di Power BI per il modello di soluzione per l'analisi dei dati di telemetria del veicolo
Questo menu contiene i collegamenti alle sezioni dello studio: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

La soluzione per l'analisi dei dati di telemetria del veicolo illustra come le concessionarie, i produttori di automobili e le compagnie assicurative possono sfruttare le funzionalità di Cortana Intelligence per ottenere informazioni dettagliate predittive e in tempo reale sull'integrità del veicolo e sulle abitudini di guida in modo da migliorare l'esperienza cliente, la ricerca e lo sviluppo e le campagne di marketing. Queste istruzioni dettagliate mostrano come configurare i report e il dashboard di Power BI dopo la distribuzione della soluzione nella sottoscrizione. 

## <a name="prerequisites"></a>prerequisiti
* Distribuire la soluzione per l'[analisi dei dati di telemetria del veicolo](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90). 
* [Installare Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331).
* Ottenere una [sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/). Se non si dispone di una sottoscrizione di Azure, iniziare con la sottoscrizione gratuita di Azure.
* Aprire un account di Power BI.

## <a name="cortana-intelligence-suite-components"></a>Componenti di Cortana Intelligence Suite
Come parte del modello di soluzione per l'analisi dei dati di telemetria del veicolo, nella sottoscrizione vengono distribuiti i servizi di Cortana Intelligence seguenti:

* **Hub eventi di Azure** inserisce milioni di eventi di telemetria del veicolo in Azure.
* **Analisi di flusso di Azure** fornisce informazioni dettagliate in tempo reale sull'integrità del veicolo e salva i dati in modo permanente in un archivio a lungo termine per un'analisi batch avanzata.
* **Azure Machine Learning** rileva le anomalie in tempo reale e usa l'elaborazione batch per offrire informazioni dettagliate predittive.
* **Azure HDInsight** trasforma i dati su larga scala.
* **Azure Data Factory** gestisce l'orchestrazione, la pianificazione, la gestione delle risorse e il monitoraggio della pipeline di elaborazione batch.

**Power BI** offre alla soluzione un dashboard completo per la visualizzazione di dati in tempo reale e di analisi predittiva. 

La soluzione usa due origini dati diverse:

* Set di dati di diagnostica e segnali simulati del veicolo
* Catalogo dei veicoli

Nella soluzione è incluso un simulatore di dati telematici relativi al veicolo. Il simulatore genera informazioni di diagnostica e segnali che corrispondono allo stato del veicolo e agli stili di guida in un determinato momento. 

Il catalogo dei veicoli è un set di dati di riferimento che esegue il mapping dei numeri di telaio (VIN, Vehicle Identification Number) ai modelli.

## <a name="power-bi-dashboard-preparation"></a>Preparazione del dashboard di Power BI
### <a name="set-up-the-power-bi-real-time-dashboard"></a>Configurare il dashboard in tempo reale di Power BI

#### <a name="start-the-real-time-dashboard-application"></a>Avviare l'applicazione dashboard in tempo reale
Dopo avere completato la distribuzione, seguire le istruzioni per il funzionamento manuale.

1. Scaricare l'applicazione dashboard in tempo reale RealtimeDashboardApp.zip e decomprimere il file.

2.  Nella cartella non compressa aprire il file di configurazione dell'app RealtimeDashboardApp.exe.config. Sostituire appSettings per Hub eventi, Archiviazione BLOB di Azure e connessioni al servizio Azure Machine Learning con i valori indicati nelle istruzioni per il funzionamento manuale. Salvare le modifiche.

3. Eseguire l'applicazione RealtimeDashboardApp.exe. Nella finestra di accesso specificare le credenziali di Power BI valide. 

   ![Finestra di accesso di Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
4. Selezionare **Accetto**. Viene avviata l'esecuzione dell'app.

   ![Autorizzazioni per il dashboard di Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

5. Accedere al sito Web di Power BI e creare un dashboard in tempo reale.

A questo punto si è pronti per configurare il dashboard di Power BI.  

### <a name="configure-power-bi-reports"></a>Configurare i report di Power BI
Il completamento dei report in tempo reale e del dashboard richiede circa 30-45 minuti. 

1. Passare alla pagina Web di [Power BI](http://powerbi.com) ed eseguire l'accesso.

    ![Pagina di accesso di Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

2. Viene generato un nuovo set di dati in Power BI. Selezionare il set di dati **ConnectedCarsRealtime**.

    ![Set di dati ConnectedCarsRealtime](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

3. Per salvare il report vuoto, premere CTRL+S.

    ![Report vuoto](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

4. Immettere il nome del report **Analisi dei dati di telemetria del veicolo in tempo reale - Report**.

    ![Nome del report](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Report in tempo reale
Questa soluzione include tre report in tempo reale:

* Veicoli operativi
* Veicoli che richiedono manutenzione
* Statistiche sull'integrità dei veicoli

È possibile configurare tutti e tre i report in tempo reale o interrompere la procedura dopo ogni fase e passare alla sezione successiva di configurazione dei report in batch. È consigliabile creare tutti e tre i report per visualizzare le informazioni dettagliate complete del percorso in tempo reale della soluzione.  

### <a name="vehicles-in-operation-report"></a>Report Veicoli operativi
1. Fare doppio clic su **Pagina 1** e rinominarla **Veicoli operativi**.

    ![Veicoli operativi](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

2. Nella scheda **Campi** selezionare **vin**. Nella scheda **Visualizzazioni** selezionare la visualizzazione **Scheda**.  

    Viene creata una visualizzazione di tipo **Scheda** come illustrato nella figura seguente:

    ![Selezionare vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

3. Selezionare l'area vuota per aggiungere una nuova visualizzazione.  

4. Nella scheda **Campi** selezionare **city** e **vin**. Nella scheda **Visualizzazioni** selezionare la visualizzazione **Mappa**. Trascinare **vin** nell'area **Valori**. Trascinare **city** nell'area **Legenda**. 

    ![Visualizzazione Scheda](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

5. Nella scheda **Visualizzazioni** selezionare la sezione **Formato**. Selezionare **Titolo** e modificare **Testo** in **Veicoli operativi in base alla città**.

    ![Veicoli operativi in base alla città](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

    La visualizzazione finale avrà un aspetto simile al seguente:

    ![Visualizzazione finale](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

6. Selezionare l'area vuota per aggiungere una nuova visualizzazione.  

7. Nella scheda **Campi** selezionare **city** e **vin**. Nella scheda **Visualizzazioni** selezionare la visualizzazione **Istogramma a colonne raggruppate**. Trascinare **city** nell'area **Asse**. Trascinare **vin** nell'area **Valore**.

8. Ordinare il grafico in base a **Conteggio di vin**.

    ![Count of vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

9. Modificare il campo **Titolo** del grafico in **Veicoli operativi in base alla città**. 

10. Selezionare la sezione **Formato** e quindi selezionare **Colori dati**. In **Mostra tutto** fare clic su **Attiva**.

    ![Colori dati](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

11. Modificare il colore delle singole città facendo clic sull'icona del colore.

    ![Modifica del colore](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

12. Selezionare l'area vuota per aggiungere una nuova visualizzazione.  

13. Nella scheda **Visualizzazioni** selezionare la visualizzazione **Istogramma a colonne raggruppate**. Nella scheda **Campi** trascinare **city** nell'area **Asse**. Trascinare **Model** nell'area **Legenda**. Trascinare **vin** nell'area **Valore**.

    ![Istogramma a colonne raggruppate](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)

    Il grafico ha un aspetto simile all'immagine seguente:

    ![Rendering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

14. Ridisporre tutte le visualizzazioni in modo che la pagina abbia un aspetto analogo al seguente:

    ![Dashboard con visualizzazioni](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Il report in tempo reale "Veicoli operativi" è stato configurato correttamente. È possibile creare il report in tempo reale successivo o interrompere la procedura e configurare il dashboard. 

### <a name="vehicles-requiring-maintenance-report"></a>Report Veicoli che richiedono manutenzione

1. Selezionare ![Aggiungi](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) per aggiungere un nuovo report. Rinominarlo **Veicoli che richiedono manutenzione**.

    ![Veicoli che richiedono manutenzione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

2. Nella scheda **Campi** selezionare **vin**. Nella scheda **Visualizzazioni** selezionare la visualizzazione **Scheda**.

    ![Visualizzazione Scheda vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

    Il set di dati contiene un campo denominato **MaintenanceLabel**. Questo campo può avere un valore pari a "0" o "1". Il valore è impostato dal modello di Machine Learning fornito come parte della soluzione ed è integrato nel percorso in tempo reale. Il valore "1" indica che un veicolo richiede manutenzione. 

3. Per aggiungere un **filtro a livello di pagina** per visualizzare i dati relativi ai veicoli che richiedono manutenzione: 

   a. Trascinare il campo **MaintenanceLabel** in **Filtri a livello di pagina**.
  
      ![Filtri a livello di pagina](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

    b. Nella parte inferiore di **Filtri a livello di pagina MaintenanceLabel** selezionare **Filtro di base**.

      ![Filtro di base](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png) 

    c. Impostare il valore del filtro su **1**.

      ![Valore del filtro](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

4. Selezionare l'area vuota per aggiungere una nuova visualizzazione.  

5. Nella scheda **Visualizzazioni** selezionare la visualizzazione **Istogramma a colonne raggruppate**. 

    ![Scheda vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)

    ![Istogramma a colonne raggruppate](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

6. Nella scheda **Campi** trascinare **Model** nell'area **Asse**. Trascinare **vin** nell'area **Valore**. Ordinare quindi la visualizzazione in base a **Conteggio di vin**. Modificare il campo **Titolo** del grafico in **Veicoli che richiedono manutenzione in base al modello**. 

7. Nella sezione **Campi** ![Campi](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) della scheda **Visualizzazioni** trascinare **vin** in **Saturazione colore**.

    ![Saturazione colore](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

8. Nella sezione **Formato** modificare **Colori dati** nella visualizzazione: 

    a. Cambiare il colore **Minimo** in **F2C812**.

    b. Cambiare il colore **Massimo** in **FF6300**.

    ![Nuovi colori dati](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)

    I colori della nuova visualizzazione saranno simili all'esempio seguente:

    ![Nuovi colori di visualizzazione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

9. Selezionare l'area vuota per aggiungere una nuova visualizzazione.  

10. Nella scheda **Visualizzazioni** selezionare **Istogramma a colonne raggruppate**. Trascinare **vin** nell'area **Valore**. Trascinare **city** nell'area **Asse**. Ordinare il grafico in base a **Conteggio di vin**. Modificare il campo **Titolo** del grafico in **Veicoli che richiedono manutenzione in base alla città**.

    ![Veicoli che richiedono manutenzione in base alla città](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

11. Selezionare l'area vuota per aggiungere una nuova visualizzazione.  

12. Nella scheda **Visualizzazioni** selezionare la visualizzazione  **Scheda con più righe**. Trascinare **Model** e **vin** nell'area **Campi**.

    ![Scheda con più righe](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

13. Ridisporre tutte le visualizzazioni in modo che il report finale abbia un aspetto analogo al seguente: 

    ![Report finale ridisposto](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Il report in tempo reale "Veicoli che richiedono manutenzione" è stato configurato correttamente. È possibile creare il report in tempo reale successivo o interrompere la procedura e configurare il dashboard. 

### <a name="vehicle-health-statistics-report"></a>Report Statistiche sull'integrità dei veicoli

1. Selezionare ![Aggiungi](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) per aggiungere un nuovo report. Rinominarlo **Statistiche sull'integrità dei veicoli**. 

2. Nella scheda **Visualizzazioni** selezionare la visualizzazione **Misuratore**. Trascinare **speed** nelle aree **Valore**, **Valore minimo** e **Valore massimo**.

   ![Statistiche sull'integrità dei veicoli](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

3. Nell'area **Valore** modificare l'aggregazione predefinita di **speed** impostandola su **Media**.

4. Nell'area **Valore minimo** modificare l'aggregazione predefinita di **speed** impostandola su **Minima**.

5. Nell'area **Valore massimo** modificare l'aggregazione predefinita di **speed** impostandola su **Massima**.

   ![Valori della velocità](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

6. Rinominare il **Titolo misuratore** in **Velocità media**.

   ![Misuratore](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

7. Selezionare l'area vuota per aggiungere una nuova visualizzazione.  

    Allo stesso modo, aggiungere un **Misuratore** per **Media olio motore**, **Media carburante** e **Media temperatura motore**.  

8. Modificare l'aggregazione predefinita dei campi in ogni misuratore come già fatto nei passaggi precedenti per il misuratore **Velocità media**.

    ![Misuratori aggiuntivi](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

9. Selezionare l'area vuota per aggiungere una nuova visualizzazione.

10. Nella scheda **Visualizzazioni** selezionare la visualizzazione **Grafico a linee e istogramma a colonne raggruppate**. Trascinare **city** in **Asse condiviso**. Trascinare **tirepressure**, **engineoil** e **speed** nell'area **Valori colonna**. Modificare il tipo di aggregazione impostandola su **Media**. 

11. Trascinare **engineTemperature** nell'area **Valori riga**. Modificare il tipo di aggregazione impostandola su **Media**. 

    ![Valori del grafico a linee e istogramma](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

12. Modificare il campo **Titolo** del grafico in **Media velocità, pressione pneumatici, temperatura dell'olio e del motore**.  

    ![Titolo del grafico a linee e istogramma a colonne raggruppate](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

13. Selezionare l'area vuota per aggiungere una nuova visualizzazione.

14. Nella scheda **Visualizzazioni** selezionare la visualizzazione **Mappa ad albero**. Trascinare **Model** nell'area **Gruppo**. Trascinare **MaintenanceProbability** nell'area **Valori**.

15. Modificare il campo **Titolo** del grafico in **Modelli di veicolo che richiedono manutenzione**.

    ![Titolo della mappa ad albero](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

16. Selezionare l'area vuota per aggiungere una nuova visualizzazione.

17. Nella scheda **Visualizzazioni** selezionare la visualizzazione **Grafico a barre in pila 100%**. Trascinare **city** nell'area **Asse**. Trascinare **MaintenanceProbability** e **RecallProbability** nell'area **Valore**.

    ![Aree Asse e Valore](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

18. Nella sezione **Formato** selezionare **Colori dati**. Impostare il colore di **MaintenanceProbability** sul valore **F2C80F**.

19. Modificare il campo **Titolo** del grafico in **Probabilità di manutenzione e richiamo del veicolo in base alla città**.

    ![Titolo del grafico a barre in pila 100%](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

20. Selezionare l'area vuota per aggiungere una nuova visualizzazione.

21. Nella scheda **Visualizzazioni** selezionare la visualizzazione **Grafico ad aree**. Trascinare **Model** nell'area **Asse**. Trascinare **engineOil**, **tirepressure**, **speed** e **MaintenanceProbability** nell'area **Valori**. Modificare il tipo di aggregazione impostandola su **Media**. 

    ![Tipo di aggregazione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

22. Modificare il campo **Titolo** del grafico in **Media per olio motore, pressione pneumatici, velocità e probabilità di manutenzione in base al modello**.

    ![Titolo del grafico ad aree](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

23. Selezionare l'area vuota per aggiungere una nuova visualizzazione.

24. Nella scheda **Visualizzazioni** selezionare la visualizzazione **Grafico a dispersione**. Trascinare **Model** nelle aree **Dettagli** e **Legenda**. Trascinare **fuel** nell'area **Asse X**. Modificare l'aggregazione impostandola su **Media**. Trascinare **engineTemperature** nell'area **Asse Y**. Modificare l'aggregazione impostandola su **Media**. Trascinare **vin** nell'area **Dimensioni**.

    ![Aree Dettagli, Legenda, Asse e Dimensioni](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

25. Modificare il campo **Titolo** del grafico in **Medie per carburante, temperatura del motore e conteggio di vin in base al modello e modello**.

    ![Titolo del grafico a dispersione](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

    Il report finale avrà un aspetto simile al seguente:

    ![Report finale](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Aggiungere visualizzazioni dai report al dashboard in tempo reale
1. Creare un dashboard vuoto facendo clic sul segno più (+) accanto a **Dashboard**. Immettere il nome **Dashboard di analisi dei dati di telemetria del veicolo**.

    ![Segno più (+) in Dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

2. Aggiungere le visualizzazioni dei report precedenti al dashboard. 

    ![Icona puntina in Dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

    Una volta aggiunti tutti e tre i report al dashboard, l'aspetto dovrebbe essere simile al seguente. Se non sono stati creati tutti i report, il dashboard potrebbe apparire diverso. 

    ![Dashboard con report](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Il dashboard in tempo reale è stato creato correttamente. Continuando a eseguire CarEventGenerator.exe e RealtimeDashboardApp.exe, gli aggiornamenti vengono visualizzati in tempo reale nel dashboard. Il completamento dei passaggi seguenti richiede circa 10-15 minuti.

## <a name="set-up-the-power-bi-batch-processing-dashboard"></a>Configurare il dashboard di elaborazione batch di Power BI
> [!NOTE]
> Occorrono circa due ore dal completamento della distribuzione perché la pipeline di elaborazione batch end-to-end completi l'esecuzione ed elabori un anno di dati generati. Attendere il completamento prima di procedere con i passaggi seguenti. 
> 
> 

### <a name="download-the-power-bi-designer-file"></a>Scaricare il file di progettazione Power BI

1. Un file di progettazione Power BI preconfigurato è incluso con le istruzioni di funzionamento manuale della distribuzione. Cercare "2. Configurare il dashboard di elaborazione batch di Power BI".

2. Scaricare il modello di Power BI per il dashboard di elaborazione batch denominato **ConnectedCarsPbiReport.pbix**.

3. Salvarlo in locale.

### <a name="configure-power-bi-reports"></a>Configurare i report di Power BI

1. Aprire il file di progettazione **ConnectedCarsPbiReport.pbix** tramite Power BI Desktop. Se non lo si è ancora fatto, installare Power BI Desktop dal sito Web per l'[installazione di Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331).

2. Selezionare **Modifica query**.

    ![Modifica query](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

3. Fare doppio clic sull'**origine**.

    ![Sorgente](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

4. Aggiornare la stringa di connessione del server con il server di Azure SQL di cui è stato effettuato il provisioning come parte della distribuzione. Esaminare le istruzioni di funzionamento manuale nel database SQL di Azure:

    * Server: somethingsrv.database.windows.net
    * Database: connectedcar
    * Nome utente: nome utente
    * Password: è possibile gestire la password di SQL Server dal portale di Azure.

5. Lasciare **Database** come **connectedcar**.

    ![Database](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

6. Selezionare **OK**.

7. La scheda **Credenziale Windows** è selezionata per impostazione predefinita. Modificarla in **Credenziali di database** selezionando la scheda **Database** a destra.

8. Immettere **Nome utente** e **Password** del database SQL di Azure specificato durante la relativa configurazione della distribuzione.

    ![Credenziali di database](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

9. Selezionare **Connessione**.

10. Ripetere i passaggi precedenti per ognuna delle tre query rimanenti presenti nel riquadro di destra e quindi aggiornare i dettagli di connessione dell'origine dati.

11. Fare clic su **Chiudi e carica**. I set di dati del file di Power BI Desktop sono connessi alle tabelle del database SQL.

12. Selezionare **Chiudi** per chiudere il file di Power BI Desktop.

    ![Chiudi](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

13. Selezionare **Salva** per salvare le modifiche. 

Sono stati configurati tutti i report corrispondenti al percorso di elaborazione batch nella soluzione. 

## <a name="upload-to-powerbicom"></a>Caricare in powerbi.com
1. Passare al [portale Web di Power BI](http://powerbi.com) ed eseguire l'accesso.

2. Selezionare **Recupera dati**.

3. Caricare il file di Power BI Desktop. Selezionare **Recupera dati** > **Recupera file** > **File locale**.

4. Andare a **ConnectedCarsPbiReport.pbix**.

5. Dopo che il file è stato caricato, tornare all'area di lavoro di Power BI. Sono stati creati automaticamente un set di dati, un report e un dashboard vuoto.  

6. Aggiungere grafici a un nuovo dashboard denominato **Dashboard di analisi dei dati di telemetria del veicolo** in Power BI. Selezionare il dashboard vuoto creato in precedenza e quindi passare alla sezione **Report**. Selezionare il report appena caricato.  

    ![Nuovo dashboard di Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

    Il report include sei pagine:

    Pagina 1: Densità veicolo  
    Pagina 2: Integrità veicolo in tempo reale  
    Pagina 3: Veicoli guidati in modo aggressivo   
    Pagina 4: Veicoli richiamati  
    Pagina 5: Veicoli con consumo di carburante efficiente  
    Pagina 6: Logo Contoso Motors  

    ![Report di Power BI con sei pagine](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

7. Dalla **pagina 3** aggiungere il contenuto seguente:  

    a. **Conteggio di vin**  

   ![Pagina 3 Conteggio di vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

    b. **Veicoli guidati in modo aggressivo in base al modello: grafico a cascata** 

   ![Pagina 3 grafico 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

8. Dalla **pagina 5** aggiungere il contenuto seguente: 

    a. **Conteggio di vin**

   ![Pagina 5 grafico 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)

    b. **Veicoli guidati con un consumo efficiente del carburante in base al modello: istogramma a colonne raggruppate**

   ![Pagina 5 grafico 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

9. Dalla **pagina 4** aggiungere il contenuto seguente:  

    a. **Conteggio di vin** 

   ![Pagina 4 grafico 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

    b. **Veicoli richiamati in base alla città e al modello: mappa ad albero**

   ![Pagina 4 grafico 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

10. Dalla **pagina 6** aggiungere il contenuto seguente:  

    * **Logo Contoso Motors**

    ![Logo Contoso Motors ](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

### <a name="organize-the-dashboard"></a>Organizzare il dashboard  

1. Andare al dashboard.

2. Passare il mouse su ciascun grafico e rinominarlo in base alla denominazione specificata nell'esempio seguente di dashboard completato:

   ![Organizzazione del dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) 
   
3. Spostare i grafici come illustrato nell'esempio di dashboard seguente:

    ![Dashboard ridisposto](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

4. Dopo avere creato tutti i report indicati in questo documento, il dashboard finale completato ha un aspetto simile al seguente: 

   ![Dashboard finale](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Sono stati creati i report e il dashboard per ottenere in tempo reale informazioni dettagliate in batch e predittive sullo stato di integrità dei veicoli e sulle abitudini di guida.  
