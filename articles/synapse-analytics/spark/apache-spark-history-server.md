---
title: Usare il server di cronologia Spark esteso per eseguire il debug delle app
description: Usare il server di cronologia Spark esteso per eseguire il debug e la diagnosi delle applicazioni Spark in Azure sinapsi Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 75aac74ae5ccf5b52234f1b554dc2a5edefcf32d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91260411"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Usare il server di cronologia Apache Spark esteso per eseguire il debug e la diagnosi delle applicazioni Apache Spark

Questo articolo fornisce indicazioni su come usare il server di cronologia di Apache Spark esteso per eseguire il debug e la diagnosi delle applicazioni Spark completate ed eseguite.

L'estensione include una scheda dati, una scheda grafico e una scheda diagnosi. Usare la scheda **dati** per controllare i dati di input e di output del processo Spark. La scheda **grafico** Mostra il flusso di dati e la riproduzione del grafico del processo. La scheda **diagnosi** Mostra l'  **asimmetria dei dati**, lo **sfasamento dell'ora**e l'analisi dell'utilizzo dell' **Executor**.

## <a name="access-the-apache-spark-history-server"></a>Accedere al server di cronologia Apache Spark

Apache Spark server di cronologia è l'interfaccia utente Web per le applicazioni Spark completate ed eseguite. È possibile aprire l'interfaccia Web del server di cronologia di Apache Spark da Azure sinapsi Analytics.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Aprire l'interfaccia utente Web del server cronologia Spark dal nodo applicazioni Apache Spark

1. Aprire [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Selezionare **monitoraggio**, quindi selezionare **Apache Spark applicazioni**.

    ![Selezionare monitoraggio, quindi selezionare applicazione Spark.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Selezionare un'applicazione, quindi aprire **query di log** selezionandolo.

    ![Aprire la finestra query log.](./media/apache-spark-history-server/open-application-window.png)

4. Selezionare **Server cronologia Spark**, quindi verrà visualizzata l'interfaccia utente Web del server cronologia Spark.

    ![Aprire il server cronologia Spark.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>Aprire l'interfaccia utente Web del server cronologia Spark dal nodo dati

1. Dal notebook di Azure sinapsi Studio selezionare **Server cronologia Spark** dalla cella output esecuzione processo o dal pannello stato nella parte inferiore del documento del notebook. Selezionare **Dettagli sessione**.

   ![Avviare il server della cronologia Spark 1](./media/apache-spark-history-server/launch-history-server2.png "Avviare il server cronologia Spark")

2. Selezionare **Server cronologia Spark** dal pannello di scorrimento.

   ![Avviare il server cronologia Spark 2](./media/apache-spark-history-server/launch-history-server.png "Avviare il server cronologia Spark")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Esplorare la scheda dati nel server cronologia Spark

Selezionare l'ID processo per il processo che si desidera visualizzare. Selezionare quindi **dati** dal menu strumento per ottenere la visualizzazione dati. In questa sezione viene illustrato come eseguire varie attività nella scheda dati.

* Esaminare **Input**, **Output** e **Table Operations** (Operazioni tabella) selezionando le schede separatamente.

    ![Dati per le schede dell'applicazione Spark](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Copiare tutte le righe selezionando **copia**.

    ![Dati per la copia dell'applicazione Spark](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Salvare tutti i dati come file CSV selezionando **CSV**.

    ![Dati per il salvataggio dell'applicazione Spark](./media/apache-spark-history-server/apache-spark-data-save.png)

* Per eseguire la ricerca, immettere le parole chiave nella **ricerca**dei campi. I risultati della ricerca vengono visualizzati immediatamente.

    ![Dati per la ricerca di applicazioni Spark](./media/apache-spark-history-server/apache-spark-data-search.png)

* Selezionare l'intestazione di colonna per ordinare la tabella, selezionare il segno più per espandere una riga per visualizzare altri dettagli oppure selezionare il segno meno per comprimere una riga.

    ![Dati per la tabella dell'applicazione Spark](./media/apache-spark-history-server/apache-spark-data-table.png)

* Scaricare un singolo file selezionando **download parziale**. Il file selezionato viene scaricato in locale. Se il file non esiste più, viene visualizzata una nuova scheda con un messaggio di errore.

    ![Dati per la riga di download dell'applicazione Spark](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Per copiare un percorso completo o relativo, selezionare le opzioni **Copia percorso completo** o **Copia percorso relativo** che si espandono dal menu a discesa. Per Azure Data Lake Storage file, **Apri in Azure Storage Explorer** avvia Azure Storage Explorer e individua la cartella al momento dell'accesso.

    ![Dati per il percorso di copia dell'applicazione Spark](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* Selezionare i numeri di pagina al di sotto della tabella per spostarsi tra le pagine quando sono presenti troppe righe da visualizzare in una pagina.

    ![Pagina dei dati per l'applicazione Spark](./media/apache-spark-history-server/apache-spark-data-page.png)

* Posizionare il puntatore del mouse sul punto interrogativo accanto ai **dati** per visualizzare la descrizione comando oppure selezionare il punto interrogativo per ottenere altre informazioni.

    ![Ulteriori informazioni per l'applicazione Spark](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Inviare commenti e suggerimenti con problemi selezionando Invia **commenti e suggerimenti**.

    ![Spark Graph fornisce commenti e suggerimenti](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Scheda grafico nel server Apache Spark cronologia

Selezionare l'ID processo per il processo che si desidera visualizzare. Selezionare quindi **Graph** nel menu dello strumento per ottenere la visualizzazione del grafico del processo.

### <a name="overview"></a>Panoramica

È possibile visualizzare una panoramica del processo nel grafico del processo generato. Per impostazione predefinita, il grafico Mostra tutti i processi. È possibile filtrare la visualizzazione in base all' **ID del processo**.

![ID del processo dell'applicazione Spark e del grafico del processo](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Schermo

Per impostazione predefinita, è selezionata la visualizzazione **stato** . È possibile controllare il flusso di dati selezionando **Leggi** o **scritto** nell'elenco a discesa **Visualizza** .

![Visualizzazione dell'applicazione Spark e del grafico del processo](./media/apache-spark-history-server/sparkui-graph-display.png)

Il nodo Graph Mostra i colori visualizzati nella legenda mappa termica.

![Applicazione Spark e grafo del processo mappa termica](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Riproduzione

Per riprodurre il processo, selezionare **riproduzione**. È possibile selezionare **Arresta** in qualsiasi momento per arrestare. I colori delle attività mostrano stati diversi durante la riproduzione:

|Colore|Significato|
|-|-|
|Green|Succeeded: il processo è stato completato correttamente.|
|Orange|Tentativi: istanze delle attività non riuscite, ma non influiscono sul risultato finale del processo. Per queste attività sono presenti istanze duplicate o nuovi tentativi che potrebbero riuscire in un secondo momento.|
|Blu|Running: l'attività è in esecuzione.|
|White|In attesa o ignorata: l'attività è in attesa di esecuzione oppure la fase è stata ignorata.|
|Red|Non riuscito: l'attività non è riuscita.|

La figura seguente mostra i colori di stato verde, arancione e blu.

![Esempio di colore dell'applicazione Spark e del grafico del processo, in esecuzione](./media/apache-spark-history-server/sparkui-graph-color-running.png)

La figura seguente mostra i colori di stato verde e bianco.

![Esempio di colore dell'applicazione Spark e del grafico del processo, ignorare](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

La figura seguente mostra i colori di stato rosso e verde.

![Esempio di colore dell'applicazione Spark e del grafico del processo non riuscito](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> È consentita la riproduzione per ogni processo. Per i processi incompleti, la riproduzione non è supportata.

### <a name="zoom"></a>Zoom

Usare lo scorrimento del mouse per eseguire lo zoom avanti e indietro nel grafico del processo oppure selezionare **zoom per adattarlo** allo schermo.

![Adatta allo zoom del grafico del processo e dell'applicazione Spark](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Descrizioni comando

Passare il puntatore del mouse sul nodo del grafo per visualizzare la descrizione comando quando sono presenti attività non riuscite e selezionare una fase per aprire la relativa pagina di gestione temporanea.

![Descrizione comando dell'applicazione Spark e del grafico del processo](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

Nella scheda grafico processo le fasi hanno una descrizione comando e una piccola icona visualizzata se hanno attività che soddisfano le condizioni seguenti:

|Condizione|Descrizione|
|-|-|
|Asimmetria dei dati|dimensioni lettura dati > dimensione media lettura dati di tutte le attività all'interno di questa fase * 2 e dimensioni lettura dati > 10 MB|
|Sfasamento dell'ora|tempo di esecuzione > tempo medio di esecuzione di tutte le attività all'interno di questa fase * 2 e tempo di esecuzione > 2 minuti|
   
![Icona dell'applicazione Spark e della distorsione del grafico del processo](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Descrizione nodo grafico

Nel nodo del grafico del processo vengono visualizzate le informazioni seguenti di ogni fase:

  * ID.
  * Nome o descrizione.
  * Numero totale di attività.
  * Dati letti: la somma delle dimensioni di input e delle dimensioni dei dati casuali letti.
  * Scrittura dei dati: la somma delle dimensioni di output e delle Scritture shuffle.
  * Tempo di esecuzione: tempo tra l'ora di inizio del primo tentativo e l'ora di completamento dell'ultimo tentativo.
  * Conteggio righe: somma di record di input, record di output, record di lettura casuale e record di scrittura casuale.
  * Stato.

    > [!NOTE]  
    > Per impostazione predefinita, il nodo del grafico del processo Visualizza le informazioni dall'ultimo tentativo di ogni fase, ad eccezione del tempo di esecuzione della fase. Tuttavia, durante la riproduzione, il nodo Graph Mostra le informazioni di ogni tentativo.
    >  
    > Le dimensioni dei dati di lettura e scrittura sono pari a 1 MB = 1000 KB = 1000 * 1000 byte.

### <a name="provide-feedback"></a>Inviare feedback

Inviare commenti e suggerimenti con problemi selezionando Invia **commenti e suggerimenti**.

![Commenti e suggerimenti sull'applicazione Spark e sui grafici dei processi](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Esplorare la scheda diagnosi nel server Apache Spark cronologia

Per accedere alla scheda diagnosi, selezionare un ID processo. Selezionare quindi **diagnosi** dal menu strumento per ottenere la visualizzazione diagnosi processo. La scheda relativa alla diagnosi include **Asimmetria dei dati**, **Sfasamento dell'ora** e **Executor Usage Analysis** (Analisi utilizzo executor).

Esaminare i valori di **Asimmetria dei dati**, **Sfasamento dell'ora** e **Executor Usage Analysis** (Analisi utilizzo executor) selezionando le rispettive schede.

![SparkUI di nuovo la scheda asimmetria dati diagnosi](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Asimmetria dei dati

Quando si seleziona la scheda **asimmetria dati** , le attività inclinate corrispondenti vengono visualizzate in base ai parametri specificati.

* **Specificare i parametri** - La prima sezione visualizza i parametri usati per rilevare l'asimmetria dei dati. La regola predefinita è: l'attività lettura dati è maggiore di tre volte della media dei dati attività letti e i dati dell'attività letti sono maggiori di 10 MB. Se si vuole definire una regola personalizzata per le attività inclinate, è possibile scegliere i parametri. Le sezioni della **fase asimmetrica** e del **carattere di inclinazione** vengono aggiornate di conseguenza.

* **Fase asimmetrica** - La seconda sezione visualizza le fasi che hanno attività asimmetriche che soddisfano i criteri specificati in precedenza. Se in una fase è presente più di un'attività asimmetrica, nella tabella delle fasi asimmetriche viene visualizzata solo l'attività con più asimmetria, ad esempio i dati di dimensioni maggiori per l'asimmetria dei dati.

    ![scheda asimmetria dati diagnosi sparkui](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Grafico** a inclinazione: quando viene selezionata una riga nella tabella della fase di inclinazione, il grafico di inclinazione Visualizza più dettagli di distribuzione delle attività in base al tempo di lettura e di esecuzione dei dati. Le attività asimmetriche sono contrassegnate in rosso e le attività normali sono contrassegnate in blu. Il grafico Visualizza fino a 100 attività di esempio e i dettagli dell'attività vengono visualizzati nel pannello inferiore destro.

    ![grafico di asimmetria sparkui per la fase 10](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Sfasamento dell'ora

La scheda **Sfasamento dell'ora** visualizza le attività asimmetriche in base al tempo di esecuzione.

* **Specificare i parametri** - La prima sezione visualizza i parametri, che vengono usati per rilevare lo sfasamento dell'ora. I criteri predefiniti per il rilevamento dello sfasamento dell'ora sono: il tempo di esecuzione dell'attività è maggiore di tre volte rispetto al tempo medio di esecuzione e il tempo di esecuzione dell'attività è maggiore di 30 secondi. È possibile modificare i parametri in base alle esigenze. Le sezioni **Fase asimmetrica** e **Skew Chart** (Grafico asimmetrie) visualizzano le informazioni sulle fasi e sulle attività corrispondenti come nella scheda **Asimmetria dei dati** precedente.

* Selezionare **sfasamento dell'ora**, quindi il risultato filtrato viene visualizzato nella sezione **fase inclinata** in base ai parametri impostati nella sezione **specificare i parametri**. Selezionare un elemento nella sezione **fase inclinata** , quindi il grafico corrispondente viene redatto in sezione3 e i dettagli dell'attività vengono visualizzati nel pannello in basso a destra.

    ![sezione sfasamento dell'ora diagnosi sparkui](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Analisi dell'utilizzo dell'executor

Il grafico sull'utilizzo dell'executor Visualizza lo stato di allocazione e esecuzione del processo Spark.  

1. Selezionare l' **analisi di utilizzo dell'executor**, quindi quattro tipi di curve sull'utilizzo dell'executor, inclusi Executor **allocati**, esecutori **in esecuzione**, **esecutori inattivi**e **istanze di Executor max**. Per gli executor allocati, ogni evento "Executor added" o "Executor removed" aumenta o diminuisce gli esecutori allocati. È possibile esaminare la sequenza temporale degli eventi nella scheda relativa ai processi per ulteriori confronti.

   ![scheda esecutori diagnosi sparkui](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Selezionare l'icona del colore per selezionare o deselezionare il contenuto corrispondente in tutte le bozze.

    ![sparkui diagnostica di selezione grafico](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Problemi noti

I dati di input/output che usano RDD (Resilient Distributed DataSet) non vengono visualizzati nella scheda dati.

## <a name="next-steps"></a>Passaggi successivi

- [Azure Synapse Analytics](../overview-what-is.md)
- [Documentazione di .NET per Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

