---
title: Usare le funzionalità estese nel server di cronologia Apache Spark per eseguire il debug di app-Azure HDInsight
description: Usare le funzionalità estese nel server di cronologia di Apache Spark per eseguire il debug e la diagnosi delle applicazioni Spark-Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: d8dd9aaeaadf13fa48577cf2853e7bcf58badb41
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86079293"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Usare le funzionalità estese del server di cronologia Apache Spark per eseguire il debug e la diagnosi delle applicazioni Spark

Questo articolo illustra come usare le funzionalità estese del server di cronologia di Apache Spark per eseguire il debug e la diagnosi delle applicazioni Spark completate o in esecuzione. L'estensione include una scheda **dati** , una scheda **grafico** e una scheda **diagnosi** . Nella scheda **dati** è possibile controllare i dati di input e di output del processo Spark. Nella scheda **grafico** è possibile controllare il flusso di dati e riprodurre il grafico del processo. Nella scheda **diagnosi** è possibile fare riferimento alle caratteristiche di **asimmetria dei dati**, **sfasamento dell'ora**e **analisi dell'utilizzo dell'executor** .

## <a name="get-access-to-the-spark-history-server"></a>Ottenere l'accesso al server della cronologia Spark

Il server cronologia Spark è l'interfaccia utente Web per le applicazioni Spark completate ed eseguite. È possibile aprirlo dalla portale di Azure o da un URL.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>Aprire l'interfaccia utente Web del server cronologia Spark dalla portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/) aprire il cluster Spark. Per altre informazioni, vedere [Elencare e visualizzare i cluster](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Da **Dashboard cluster**selezionare **Server cronologia Spark**. Quando richiesto, immettere le credenziali di amministratore per il cluster di Spark.

    ![Avviare il server cronologia Spark dal portale di Azure.](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Server cronologia Spark")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Aprire l'interfaccia utente Web del server cronologia Spark per URL

Aprire il server cronologia Spark passando a `https://CLUSTERNAME.azurehdinsight.net/sparkhistory` , dove **clustername** è il nome del cluster Spark.

L'interfaccia utente Web del server cronologia Spark può essere simile all'immagine seguente:

![Pagina del server cronologia Spark.](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>Usare la scheda dati nel server cronologia Spark

Selezionare l'ID del processo e quindi scegliere **dati** dal menu strumento per visualizzare la vista dati.

+ Esaminare gli **input**, gli **output**e **le operazioni su tabella** selezionando le singole schede.

    ![Schede dati nella pagina dati per l'applicazione Spark.](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Copiare tutte le righe selezionando il pulsante **copia** .

    ![Copiare i dati nella pagina dell'applicazione Spark.](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Salvare tutti i dati come. File CSV selezionando il pulsante **CSV** .

    ![Salvare i dati come. File CSV dalla pagina dati per l'applicazione Spark.](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Per cercare i dati, immettere le parole chiave nel campo di **ricerca** . I risultati della ricerca vengono visualizzati immediatamente.

    ![Cercare i dati nella pagina dati per l'applicazione Spark.](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Selezionare l'intestazione di colonna per ordinare la tabella. Selezionare il segno più per espandere una riga per visualizzare altri dettagli. Selezionare il segno meno per comprimere una riga.

    ![Tabella di dati nella pagina dati per l'applicazione Spark.](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Scaricare un singolo file selezionando il pulsante **download parziale** a destra. Il file selezionato viene scaricato localmente. Se il file non esiste più, verrà aperta una nuova scheda per visualizzare i messaggi di errore.

    ![Riga di download dei dati nella pagina dati per l'applicazione Spark.](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Copiare un percorso completo o relativo selezionando l'opzione **Copia percorso completo** o **Copia percorso relativo** , che è possibile espandere dal menu di download. Per Azure Data Lake Storage file, selezionare **Apri in Azure Storage Explorer** per avviare Azure Storage Explorer e individuare la cartella dopo l'accesso.

    ![Copiare le opzioni percorso completo e copia percorso relativo nella pagina dati per l'applicazione Spark.](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Se sono presenti troppe righe da visualizzare in una singola pagina, selezionare i numeri di pagina nella parte inferiore della tabella da esplorare.

    ![Numeri di pagina nella pagina dati per l'applicazione Spark.](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Per altre informazioni, passare il puntatore del mouse o selezionare il punto interrogativo accanto ai **dati per l'applicazione Spark** per visualizzare la descrizione comando.

    ![Ottenere altre informazioni dalla pagina dati per l'applicazione Spark.](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  Per inviare commenti e suggerimenti sui problemi, selezionare Invia **commenti e suggerimenti**.

    ![Inviare commenti e suggerimenti dalla pagina dati per l'applicazione Spark.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>Usare la scheda Graph nel server cronologia Spark

+ Selezionare l'ID del processo e quindi scegliere **grafico** dal menu strumento per visualizzare il grafico del processo. Per impostazione predefinita, il grafico visualizzerà tutti i processi. Filtrare i risultati usando il menu a discesa **ID processo** .

    ![Menu a discesa ID processo nella pagina grafico del processo & applicazione Spark.](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ Lo **stato di avanzamento** è selezionato per impostazione predefinita. Controllare il flusso di dati selezionando **lettura** o **scrittura** nel menu a discesa **Visualizza** .

    ![Controllare il flusso di dati nella pagina del grafico del processo & applicazione Spark.](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ Il colore di sfondo di ogni attività corrisponde a una mappa termica.

   ![Mappa termica nella pagina del grafico del processo & applicazione Spark.](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |Colore |Descrizione |
    |---|---|
    |Green|il processo è stato completato correttamente.|
    |Orange|L'attività non è riuscita, ma ciò non influisce sul risultato finale del processo. Queste attività hanno istanze duplicate o tentativi che possono avere esito positivo in un secondo momento.|
    |Blu|l'attività è in esecuzione.|
    |bianco|l'attività è in attesa di esecuzione o la fase è stata ignorata.|
    |Rosso|l'attività non è riuscita.|

     ![Esecuzione di un'attività nella pagina del grafico del processo & applicazione Spark.](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     Le fasi ignorate vengono visualizzate in bianco.
    ![Un'attività ignorata nella pagina del grafico del processo & applicazione Spark.](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Un'attività non riuscita nella pagina del grafico del processo & applicazione Spark.](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > La riproduzione è disponibile per i processi completati. Selezionare il pulsante **riproduzione** per rieseguire il processo. Arrestare il processo in qualsiasi momento selezionando il pulsante Interrompi. Quando viene riprodotto un processo, ogni attività visualizzerà lo stato in base al colore. La riproduzione non è supportata per i processi incompleti.

+ Scorrere per ingrandire o ridurre il grafico del processo oppure selezionare **zoom per adattarlo** allo schermo.

    ![Selezionare Zoom per adattarlo alla pagina del grafico del processo & applicazione Spark.](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Quando le attività hanno esito negativo, passare il puntatore del mouse sul nodo del grafo per visualizzare la descrizione comando, quindi selezionare la fase per aprirla in una nuova pagina.

    ![Visualizzare la descrizione comando nella pagina del grafico del processo & applicazione Spark.](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Nella pagina del grafico del processo & applicazione Spark, le fasi visualizzeranno descrizioni comando e icone piccole se le attività soddisfano le condizioni seguenti:
  + Asimmetria dati: dimensioni lettura dati > dimensione media lettura dati di tutte le attività all'interno di questa fase * 2 *e* dimensioni lettura dati > 10 MB.
  + Sfasamento dell'ora: tempo di esecuzione > tempo medio di esecuzione di tutte le attività all'interno di questa fase * 2 *e* tempo di esecuzione > 2 minuti.

    ![Icona dell'attività inclinata nella pagina del grafico del processo & applicazione Spark.](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ Il nodo del grafico del processo visualizzerà le informazioni seguenti su ogni fase:
  + ID
  + Nome o descrizione
  + Numero totale attività
  + Dati letti: somma delle dimensioni di input e delle dimensioni di lettura casuale
  + Scrittura dei dati: somma delle dimensioni di output e della dimensione di scrittura casuale
  + Tempo di esecuzione: l'ora di inizio del primo tentativo e l'ora di completamento dell'ultimo tentativo
  + Conteggio righe: la somma dei record di input, dei record di output, dei record di lettura casuale e dei record di scrittura shuffle
  + Avanzamento

    > [!NOTE]  
    > Per impostazione predefinita, il nodo del grafico del processo visualizzerà le informazioni dell'ultimo tentativo di ogni fase, ad eccezione del tempo di esecuzione della fase. Durante la riproduzione, tuttavia, nel nodo del grafico del processo vengono visualizzate informazioni su ogni tentativo.

    > [!NOTE]  
    > Per le dimensioni di lettura e scrittura dei dati, si usa 1 MB = 1000 KB = 1000 * 1000 byte.

+ Inviare commenti e suggerimenti sui problemi selezionando Invia **commenti e suggerimenti**.

    ![L'opzione feedback nella pagina del grafico del processo & applicazione Spark.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>Usare la scheda diagnosi nel server cronologia Spark

Selezionare l'ID del processo, quindi selezionare **diagnosi** dal menu strumento per visualizzare la visualizzazione diagnosi processo. La scheda **diagnosi** include l'analisi dell'utilizzo di **dati**, **sfasamento dell'ora**e dell' **Executor**.

+ Esaminare l' **asimmetria dei dati**, lo **sfasamento dell'ora**e l'analisi dell'utilizzo dell' **Executor** selezionando le schede rispettivamente.

    ![Scheda asimmetria dati nella scheda diagnosi.](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Asimmetria dei dati

Selezionare la scheda **asimmetria dati** . Le attività inclinate corrispondenti vengono visualizzate in base ai parametri specificati.

#### <a name="specify-parameters"></a>Specificare i parametri

Nella sezione **Specifica parametri** vengono visualizzati i parametri utilizzati per rilevare l'asimmetria dei dati. La regola predefinita è: la lettura dei dati dell'attività è maggiore di tre volte la lettura dei dati dell'attività media e i dati dell'attività letti sono maggiori di 10 MB. Se si vuole definire una regola personalizzata per le attività inclinate, è possibile scegliere i parametri. Le sezioni della fase e del **grafico di inclinazione** **inclinate** vengono aggiornate di conseguenza.

#### <a name="skewed-stage"></a>Fase asimmetrica

Nella sezione **fase asimmetrica** sono visualizzate le fasi con attività inclinate che soddisfano i criteri specificati. Se in una fase è presente più di un'attività inclinata, nella sezione relativa alla **fase asimmetrica** viene visualizzata solo l'attività più inclinata, ovvero i dati più grandi per l'asimmetria dei dati.

![Visualizzazione più ampia della scheda asimmetria dati nella scheda diagnosi.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>Inclina grafico

Quando si seleziona una riga nella tabella della **fase di inclinazione** , il **grafico di inclinazione** Visualizza più dettagli di distribuzione delle attività in base al tempo di lettura e di esecuzione dei dati. Le attività inclinate sono contrassegnate in rosso e le attività normali sono contrassegnate come blu. Per la valutazione delle prestazioni, nel grafico vengono visualizzate fino a 100 attività di esempio. I dettagli dell'attività vengono visualizzati nel riquadro in basso a destra.

![Grafico di inclinazione per la fase 10 nell'interfaccia utente di Spark.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Sfasamento dell'ora

La scheda **Sfasamento dell'ora** visualizza le attività asimmetriche in base al tempo di esecuzione.

#### <a name="specify-parameters"></a>Specificare i parametri

Nella sezione **Specifica parametri** vengono visualizzati i parametri utilizzati per rilevare lo sfasamento dell'ora. La regola predefinita è: il tempo di esecuzione dell'attività è maggiore di tre volte del tempo medio di esecuzione e il tempo di esecuzione dell'attività è maggiore di 30 secondi. È possibile modificare i parametri in base alle esigenze. Il grafico a fasi e **inclinazioni** **inclinate** Visualizza le informazioni relative a fasi e attività corrispondenti, esattamente come nella scheda **asimmetria dei dati** .

Quando si seleziona **sfasamento dell'ora**, il risultato filtrato viene visualizzato nella sezione **fase inclinata** , in base ai parametri impostati nella sezione **Specifica parametri** . Quando si seleziona un elemento nella sezione della **fase inclinata** , il grafico corrispondente viene disegnato nella terza sezione e i dettagli dell'attività vengono visualizzati nel riquadro in basso a destra.

![Scheda sfasamento dell'ora nella scheda diagnosi.](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>Grafici di analisi utilizzo Executor

Il **grafico sull'utilizzo dell'executor** Visualizza lo stato effettivo di allocazione e esecuzione del processo.  

Quando si seleziona l' **analisi dell'utilizzo dell'executor**, vengono elaborate quattro curve diverse sull'utilizzo dell'Executor: **esecutori allocati**, esecutori **in esecuzione**, esecutori **inattivi**e **istanze di Executor max**. Ogni evento dell' **Executor aggiunto** o **rimosso dall'executor** aumenterà o ridurrà gli esecutori allocati. Per altri confronti, è possibile controllare la **sequenza temporale degli eventi** nella scheda **processi** .

![Scheda Analisi utilizzo Executor nella scheda diagnosi.](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

Selezionare l'icona del colore per selezionare o deselezionare il contenuto corrispondente in tutte le bozze.

 ![Selezionare il grafico nella scheda Analisi utilizzo Executor.](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>Domande frequenti

### <a name="how-do-i-revert-to-the-community-version"></a>Ricerca per categorie ripristinare la versione community?

Per ripristinare la versione community, seguire questa procedura.

1. Aprire il cluster in Ambari.
1. Passare a **Spark2**  >  **configs**.
1. Selezionare **Custom spark2-defaults**.
1. Selezionare **Aggiungi proprietà...**.
1. Aggiungere **Spark. UI. Enhancement. Enabled = false**e quindi salvarlo.
1. La proprietà viene impostata su **false**.
1. Selezionare **Save (Salva** ) per salvare la configurazione.

    ![Disabilitare una funzionalità in Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. Selezionare **Spark2** nel riquadro sinistro. Quindi, nella scheda **Riepilogo** selezionare **Spark2 Cronologia server**.

    ![Visualizzazione di riepilogo in Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Per riavviare il server cronologia Spark, selezionare il pulsante **avviato** a destra di **Spark2 cronologia Server**, quindi scegliere **Riavvia** dal menu a discesa.

    ![Riavviare il server della cronologia Spark in Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. Aggiornare l'interfaccia utente Web del server cronologia Spark. Verrà ripristinata la versione community.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Ricerca per categorie caricare un evento del server della cronologia Spark per segnalarlo come problema?

Se si è eseguito un errore nel server della cronologia Spark, seguire questa procedura per segnalare l'evento.

1. Scaricare l'evento selezionando **Scarica** nell'interfaccia utente Web del server cronologia Spark.

    ![Scaricare l'evento nell'interfaccia utente del server cronologia Spark.](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Selezionare Invia **commenti e suggerimenti** nella pagina del grafico del **processo & applicazione Spark** .

    ![Inviare commenti e suggerimenti sulla pagina del grafico del processo & applicazione Spark](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Specificare il titolo e una descrizione dell'errore. Trascinare quindi il file con estensione zip nel campo modifica e selezionare **Invia nuovo problema**.

    ![Caricare e inviare un nuovo problema.](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>Ricerca per categorie aggiornare un file con estensione jar in uno scenario di hotfix?

Se si vuole eseguire l'aggiornamento con un hotfix, usare lo script seguente, che aggiornerà `spark-enhancement.jar*` .

**upgrade_spark_enhancement.sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

#### <a name="usage"></a>Utilizzo

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>Esempio

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>Usare il file bash dalla portale di Azure

1. Avviare il [portale di Azure](https://ms.portal.azure.com)e quindi selezionare il cluster.
2. Completare un' [azione script](../hdinsight-hadoop-customize-cluster-linux.md) con i seguenti parametri.

    |Proprietà |valore |
    |---|---|
    |Tipo di script|- Personalizzato|
    |Nome|UpgradeJar|
    |URI script Bash|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Tipo/i di nodo|Head, ruolo di lavoro|
    |Parametri|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![portale di Azure invia azione script](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Problemi noti

+ Attualmente, il server della cronologia Spark funziona solo per Spark 2,3 e 2,4.

+ I dati di input e di output che usano RDD non verranno visualizzati nella scheda **dati** .

## <a name="next-steps"></a>Passaggi successivi

+ [Gestire le risorse di un cluster Apache Spark in HDInsight](apache-spark-resource-manager.md)
+ [Configurare le impostazioni di Apache Spark](apache-spark-settings.md)

## <a name="suggestions"></a>Suggerimenti

In caso di commenti o problemi durante l'uso di questo strumento, inviare un messaggio di posta elettronica a ( [hdivstool@microsoft.com](mailto:hdivstool@microsoft.com) ).
