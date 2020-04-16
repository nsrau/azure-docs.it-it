---
title: Usare il server di cronologia Spark esteso per eseguire il debug delle app - Apache Spark in Azure SynapseUse the extended Spark history server to debug apps - Apache Spark in Azure Synapse
description: Usare il server di cronologia Spark esteso per eseguire il debug e diagnosticare le applicazioni Spark in Azure Synapse Analytics.Use the extended Spark history server to debug and diagnose Spark applications in Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4f03033942517f4778192e0b12f84610df8fd469
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429213"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Utilizzare il server di cronologia Apache Spark esteso per eseguire il debug e diagnosticare le applicazioni Apache Spark

In questo articolo vengono fornite indicazioni su come utilizzare il server di cronologia Apache Spark esteso per eseguire il debug e diagnosticare le applicazioni Spark completate ed eseguite.

L'estensione include una scheda dati, una scheda **Data** grafico e una scheda Diagnosi. La scheda **Grafico** mostra il flusso di dati e la riproduzione del grafico dei processi. La scheda **Diagnosi** mostra **l'inclinazione dei dati,** **l'inclinazione dell'ora**e **l'analisi dell'utilizzo dell'esecutore.**

## <a name="access-the-apache-spark-history-server"></a>Accedere al server della cronologia di Apache Spark

Il server di cronologia Apache Spark è l'interfaccia utente Web per le applicazioni Spark completate ed in esecuzione. È possibile aprire l'interfaccia Web del server di cronologia Di Apache Spark da Analisi di Azure Synapse.You can open the Apache Spark history server web interface from the Azure Synapse Analytics.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Aprire l'interfaccia utente Web di Spark History Server dal nodo delle applicazioni spark Apache

1. Aprire [Analisi synapse](https://web.azuresynapse.net/)di Azure .

2. Fare clic su **Monitoraggio**, quindi selezionare **Applicazioni Spark Apache**.

    ![Fare clic su Monitor quindi selezionare l'applicazione spark.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Selezionare un'applicazione, quindi aprire **Query di** log facendo clic su di essa.

    ![Aprire la finestra di query del log.](./media/apache-spark-history-server/open-application-window.png)

4. Selezionare **Server cronologia Spark**, quindi verrà visualizzata l'interfaccia utente Web del server della cronologia Spark.

    ![Aprire il server di cronologia spark.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>Aprire l'interfaccia utente Web del server della cronologia Spark dal nodo Dati

1. Nel blocco appunti di Azure Synapse Studio selezionare Server di **cronologia Spark** dalla cella di output dell'esecuzione del processo o dal riquadro di stato nella parte inferiore del documento del blocco appunti. Selezionare **Dettagli sessione**.

   ![Avviare il server di cronologia Spark](./media/apache-spark-history-server/launch-history-server2.png "Avviare il server di cronologia Spark")

2. Selezionare **Server cronologia Spark** dal pannello scorrevole.

   ![Avviare il server di cronologia Spark](./media/apache-spark-history-server/launch-history-server.png "Avviare il server di cronologia Spark")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Esplorare la scheda Dati nel server di cronologia Di Spark

Selezionare l'ID processo per il processo che si desidera visualizzare. Quindi selezionare **Dati** nel menu degli strumenti per ottenere la visualizzazione dati. In questa sezione viene illustrato come eseguire varie attività nella scheda Dati.

* Esaminare **Input**, **Output** e **Table Operations** (Operazioni tabella) selezionando le schede separatamente.

    ![Dati per le schede dell'applicazione Spark](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Copiare tutte le righe selezionando **Copia**.

    ![Dati per la copia dell'applicazione Spark](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Salvare tutti i dati come file CSV selezionando **csv**.

    ![Dati per il salvataggio dell'applicazione Spark](./media/apache-spark-history-server/apache-spark-data-save.png)

* Cerca inserendo parole chiave nel campo **Cerca**. I risultati della ricerca vengono visualizzati immediatamente.

    ![Dati per la ricerca di applicazioni Spark](./media/apache-spark-history-server/apache-spark-data-search.png)

* Selezionare l'intestazione di colonna per ordinare la tabella, selezionare il segno più per espandere una riga per visualizzare ulteriori dettagli oppure selezionare il segno meno per comprimere una riga.

    ![Tabella dell'applicazione Dati per Spark](./media/apache-spark-history-server/apache-spark-data-table.png)

* Scaricare un singolo file selezionando **Download parziale**. Il file selezionato viene scaricato in locale. Se il file non esiste più, viene visualizzata una nuova scheda con un messaggio di errore.

    ![Riga di download dell'applicazione Data for Spark](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Per copiare un percorso completo o relativo, selezionate le opzioni **Copia percorso completo** o Copia percorso **relativo** che si espandono dal menu a discesa. Per i file di Archiviazione Data Lake di Azure, **Apri in Azure Storage Explorer** avvia Azure Storage Explorer e individua la cartella quando è stato eseguito l'accesso.

    ![Percorso copia applicazione Spark Data for Spark](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* Selezionare i numeri di pagina sotto la tabella per spostarsi tra le pagine quando sono presenti troppe righe da visualizzare in una pagina.

    ![Pagina dell'applicazione Dati per Spark](./media/apache-spark-history-server/apache-spark-data-page.png)

* Passare il puntatore del mouse sul punto interrogativo accanto a **Dati** per visualizzare la descrizione comando oppure selezionare il punto interrogativo per ottenere ulteriori informazioni.

    ![Informazioni su dati per l'applicazione Spark](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Inviare commenti e suggerimenti sui problemi selezionando **Invia commenti e suggerimenti**.

    ![Spark graph ci fornisce di nuovo un feedback](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Scheda Grafico nel server di cronologia di Apache Spark

Selezionare l'ID processo per il processo che si desidera visualizzare. Quindi, selezionare **Grafico** nel menu degli strumenti per ottenere la visualizzazione del grafico dei processi.

### <a name="overview"></a>Panoramica

È possibile visualizzare una panoramica del processo nel grafico del processo generato. Per impostazione predefinita, il grafico mostra tutti i processi. È possibile filtrare questa visualizzazione in base **all'ID processo.**

![ID processo spark per l'applicazione e il grafico dei processi](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Visualizzazione

Per impostazione predefinita, la visualizzazione **Avanzamento** è selezionata. È possibile controllare il flusso di dati selezionando **Lettura** o **Scritto** nell'elenco a discesa **Visualizzazione.**

![Visualizzazione dell'applicazione Spark e del grafico dei processi](./media/apache-spark-history-server/sparkui-graph-display.png)

Il nodo del grafico visualizza i colori visualizzati nella legenda della mappa termica.

![Applicazione Spark e mappa termica del grafico dei processi](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Riproduzione

Per riprodurre il lavoro, selezionare **Riproduzione**. È possibile selezionare **Interrompi** in qualsiasi momento per interrompere. I colori delle attività mostrano diversi stati durante la riproduzione:

|Colore|Significato|
|-|-|
|Green|Operazione riuscita: il processo è stato completato correttamente.|
|Arancione|Riprova: istanze di attività non riuscite ma che non influiscono sul risultato finale del processo. Per queste attività sono presenti istanze duplicate o nuovi tentativi che potrebbero riuscire in un secondo momento.|
|Blu|In esecuzione: l'attività è in esecuzione.|
|bianco|In attesa o ignorata: l'attività è in attesa di essere eseguita o la fase è saltata.|
|Rosso|Non riuscito: l'attività non è riuscita.|

L'immagine seguente mostra i colori di stato Verde, Arancione e Blu.

![Esempio di colore dell'applicazione Spark e del grafico dei processi, in esecuzione](./media/apache-spark-history-server/sparkui-graph-color-running.png)

L'immagine seguente mostra i colori di stato Verde e Bianco.

![Esempio di colore dell'applicazione Spark e del grafico del processo, salta](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

L'immagine seguente mostra i colori di stato Rosso e Verde.

![Esempio di colore dell'applicazione Spark e del grafico del processo, non riuscito](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> È consentita la riproduzione per ogni processo. Per i lavori incompleti, la riproduzione non è supportata.

### <a name="zoom"></a>Zoom

Usa lo scorrimento del mouse per ingrandire e ridurre il grafico del lavoro oppure seleziona **Ingrandisci per adattarlo** allo schermo.

![Applicazione Spark e zoom grafico dei processi per adattarsi](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Tooltips

Passare il mouse sul nodo del grafico per visualizzare la descrizione comando in caso di attività non riuscite e selezionare una fase per aprire la relativa pagina della fase.

![Descrizione comando dell'applicazione Spark e del grafico dei processi](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

Nella scheda del grafico dei processi, le fasi hanno una descrizione comando e una piccola icona visualizzata se dispongono di attività che soddisfano le seguenti condizioni:

|Condizione|Descrizione|
|-|-|
|Inclinazione dei dati|dimensione della lettura dei dati > dimensione media della lettura dei dati di tutte le attività all'interno di questa fase 2 e dimensioni di lettura dei dati > 10 MB|
|Inclinazione del tempo|tempo di esecuzione > tempo medio di esecuzione di tutte le attività all'interno di questa fase 2 e tempo di esecuzione > 2 minuti|
   
![Icona di inclinazione dell'applicazione Spark e del grafico del processo](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Descrizione nodo grafico

Il nodo del grafico dei processi visualizza le seguenti informazioni di ogni fase:

  * ID.
  * Nome o descrizione.
  * Numero totale di attività.
  * Dati letti: la somma delle dimensioni di input e delle dimensioni dei dati casuali letti.
  * Scrittura dati: la somma delle dimensioni di output e della dimensione di scrittura casuale.
  * Tempo di esecuzione: tempo tra l'ora di inizio del primo tentativo e l'ora di completamento dell'ultimo tentativo.
  * Conteggio righe: somma di record di input, record di output, record di lettura casuale e record di scrittura casuale.
  * Stato.

    > [!NOTE]  
    > Per impostazione predefinita, il nodo del grafico dei processi visualizza le informazioni dell'ultimo tentativo di ogni fase (ad eccezione del tempo di esecuzione dello stage). Tuttavia, durante la riproduzione, il nodo del grafico mostra le informazioni di ogni tentativo.
    >  
    > La dimensione dei dati di lettura e scrittura è di 1 MB , 1000 KB e 1000 x 1000 byte.

### <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Inviare commenti e suggerimenti sui problemi selezionando **Invia commenti e suggerimenti**.

![Commenti e suggerimenti sull'applicazione Spark e sul grafico dei processi](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Esplorare la scheda Diagnosi nel server di cronologia di Apache SparkExplore the Diagnosis tab in Apache Spark history server

Per accedere alla scheda Diagnosi, selezionare un ID processo. Quindi selezionare **Diagnosi** dal menu degli strumenti per ottenere la vista Diagnosi del lavoro. La scheda relativa alla diagnosi include **Asimmetria dei dati**, **Sfasamento dell'ora** e **Executor Usage Analysis** (Analisi utilizzo executor).

Esaminare i valori di **Asimmetria dei dati**, **Sfasamento dell'ora** e **Executor Usage Analysis** (Analisi utilizzo executor) selezionando le rispettive schede.

![SparkUI diagnosi dati skew scheda](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Asimmetria dei dati

Quando si seleziona la scheda **Inclinazione dati,** le attività distorte corrispondenti vengono visualizzate in base ai parametri specificati.

* **Specificare i parametri** - La prima sezione visualizza i parametri usati per rilevare l'asimmetria dei dati. La regola predefinita è: Lettura dati attività è maggiore di tre volte della media dei dati delle attività letti e i dati dell'attività letti sono più di 10 MB. Se si desidera definire una regola personalizzata per le attività distorte, è possibile scegliere i parametri, le sezioni **Fase inclinata** e **Grado di inclinazione** vengono aggiornate di conseguenza.

* **Fase asimmetrica** - La seconda sezione visualizza le fasi che hanno attività asimmetriche che soddisfano i criteri specificati in precedenza. Se in una fase è presente più di un'attività asimmetrica, nella tabella delle fasi asimmetriche viene visualizzata solo l'attività con più asimmetria, ad esempio i dati di dimensioni maggiori per l'asimmetria dei dati.

    ![scheda di inclinazione dei dati di diagnosi sparkui](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Grafico di inclinazione:** quando viene selezionata una riga nella tabella delle fasi di inclinazione, il grafico di inclinazione visualizza più dettagli di distribuzione delle attività in base al tempo di lettura e di esecuzione dei dati. Le attività asimmetriche sono contrassegnate in rosso e le attività normali sono contrassegnate in blu. Il grafico visualizza fino a 100 attività di esempio e i dettagli dell'attività vengono visualizzati nel riquadro inferiore destro.

    ![grafico asfcioso sparkui per la fase 10](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Sfasamento dell'ora

La scheda **Sfasamento dell'ora** visualizza le attività asimmetriche in base al tempo di esecuzione.

* **Specifica parametri** - La prima sezione visualizza i parametri utilizzati per rilevare l'inclinazione temporale. I criteri predefiniti per il rilevamento dello sfasamento dell'ora sono: il tempo di esecuzione dell'attività è maggiore di tre volte rispetto al tempo medio di esecuzione e il tempo di esecuzione dell'attività è maggiore di 30 secondi. È possibile modificare i parametri in base alle esigenze. Le sezioni **Fase asimmetrica** e **Skew Chart** (Grafico asimmetrie) visualizzano le informazioni sulle fasi e sulle attività corrispondenti come nella scheda **Asimmetria dei dati** precedente.

* Selezionare **Inclinazione ora**, quindi il risultato filtrato viene visualizzato nella sezione **Fase inclinata** in base ai parametri impostati nella sezione **Specifica parametri**. Seleziona un elemento nella sezione **Fase inclinata,** quindi il grafico corrispondente viene redatto nella sezione3 e i dettagli dell'attività vengono visualizzati nel pannello in basso a destra.

    ![sparkui diagnosi tempo di inclinazione sezione](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Analisi dell'utilizzo dell'executor

Il grafico di utilizzo dell'esecutore visualizza lo stato di allocazione e di esecuzione dell'esecutore dell'esecutore spark.  

1. Selezionare **Analisi utilizzo esecutore**, quindi vengono redatte quattro tipi di curve relative all'utilizzo dell'esecutore, tra cui **Esecutori allocati**, **Esecutori**in esecuzione, **Esecutori inattivi**e **Istanze max executor**. Per quanto riguarda gli esecutori allocati, ogni evento "Executor added" o "Executor removed" aumenta o diminuisce gli esecutori allocati. È possibile esaminare la sequenza temporale degli eventi nella scheda relativa ai processi per ulteriori confronti.

   ![Scheda Esecutori diagnosi sparkui](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Selezionare l'icona del colore per selezionare o deselezionare il contenuto corrispondente in tutte le bozze.

    ![sparkui diagnostica grafico selezionare](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Problemi noti

I dati di input/output che utilizzano RDD (Resilient Distributed Dataset) non vengono visualizzati nella scheda Dati.

## <a name="next-steps"></a>Passaggi successivi

- [Azure Synapse Analytics](../overview-what-is.md)
- [.NET per la documentazione di Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

