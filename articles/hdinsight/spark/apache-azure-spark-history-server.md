---
title: Server cronologia Spark esteso per il debug di app-Azure HDInsight
description: Usare il Server cronologia Spark esteso per il debug e la diagnosi di applicazioni Spark - Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 7e9ab0e41086a4c9478f95c5a56754640feeab4e
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561812"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Usare il Server cronologia Apache Spark esteso per il debug e la diagnosi di applicazioni Apache Spark

Questo articolo illustra come usare il Server cronologia Apache Spark esteso per il debug e la diagnosi di applicazioni Spark completate e in esecuzione. L'estensione include scheda dati e scheda grafico e scheda diagnosi. Nella scheda **dati** gli utenti possono controllare i dati di input e di output del processo Spark. Nella scheda **Grafo** gli utenti possono controllare il flusso di dati e riprodurre il grafico del processo. Nella scheda **diagnosi** , l'utente può fare riferimento a **asimmetria dei dati**, **sfasamento dell'ora**e analisi dell'utilizzo dell' **Executor**.

## <a name="get-access-to-apache-spark-history-server"></a>Ottenere l'accesso al Server cronologia Apache Spark

Server cronologia Apache Spark è l'interfaccia utente Web per le applicazioni Spark completate e in esecuzione.

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>Aprire l'interfaccia utente Web di Server cronologia Apache Spark dal portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/) aprire il cluster Spark. Per altre informazioni, vedere [Elencare e visualizzare i cluster](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Da **Dashboard cluster**selezionare **Server cronologia Spark**. Quando richiesto, immettere le credenziali di amministratore per il cluster di Spark.

    ![Server cronologia Spark di avvio del portale](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Server cronologia Spark")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Aprire l'interfaccia utente Web di Server cronologia Spark dall'URL

Aprire il server cronologia Spark passando a `https://CLUSTERNAME.azurehdinsight.net/sparkhistory` dove CLUSTERname è il nome del cluster Spark.

L'interfaccia utente Web del server cronologia Spark può essere simile alla seguente:

![Server cronologia Spark in HDInsight](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="data-tab-in-spark-history-server"></a>Scheda dati nel Server cronologia Spark

Selezionare ID processo, quindi scegliere **dati** dal menu strumento per ottenere la visualizzazione dati.

+ Esaminare gli **input**, gli **output**e le **operazioni su tabella** selezionando le schede separatamente.

    ![Dati per le schede dell'applicazione Spark](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Copiare tutte le righe selezionando il pulsante **copia**.

    ![Dati per la copia dell'applicazione Spark](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Salvare tutti i dati come file CSV selezionando il pulsante **CSV**.

    ![Dati per il salvataggio dell'applicazione Spark](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Eseguire ricerche immettendo parole chiave nel campo **Search** (Cerca). I risultati della ricerca verranno visualizzati immediatamente.

    ![Dati per la ricerca di applicazioni Spark](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Selezionare l'intestazione di colonna per ordinare la tabella, selezionare il segno più per espandere una riga per visualizzare altri dettagli oppure selezionare il segno meno per comprimere una riga.

    ![Dati per la tabella dell'applicazione Spark](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Scaricare un singolo file selezionando il pulsante **download parziale** che si trova a destra, il file selezionato verrà scaricato in locale. se il file non esiste più, verrà aperta una nuova scheda per visualizzare i messaggi di errore.

    ![Dati per la riga di download dell'applicazione Spark](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Copiare il percorso completo o relativo selezionando **Copy Full Path** (Copia percorso completo) o **Copy Relative Path** (Copia percorso relativo) dall'elenco di opzioni visualizzate espandendo il menu di download. Per i file di archiviazione di Azure Data Lake, **aprire in Azure Storage Explorer** avvierà Azure Storage Explorer e individuare la cartella al momento dell'accesso.

    ![Dati per il percorso di copia dell'applicazione Spark](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Selezionare il numero sotto la tabella per spostarsi tra le pagine quando sono presenti troppe righe da visualizzare in una pagina.

    ![Pagina dei dati per l'applicazione Spark](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Posizionare il puntatore del mouse sul punto interrogativo accanto ai dati per visualizzare la descrizione comando oppure selezionare il punto interrogativo per ottenere altre informazioni.

    ![Ulteriori informazioni per l'applicazione Spark](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Per inviare feedback sui problemi, fare clic su **Provide us feedback** (Invia feedback).

    ![Spark Graph fornisce commenti e suggerimenti](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Scheda del grafo nel Server cronologia Apache Spark

Selezionare l'ID di processo, quindi fare clic su **Graph** (Grafico)nel menu degli strumenti per ottenere la visualizzazione del grafico del processo.

+ Esaminare la panoramica del processo nel grafico del processo generato.

+ Per impostazione predefinita vengono mostrati tutti i processi ed è possibile filtrare la visualizzazione per **ID processo**.

    ![ID del processo dell'applicazione Spark e del grafico del processo](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ Per impostazione predefinita è selezionato **Progress** (Avanzamento). È anche possibile controllare il flusso di dati selezionando **Read/Written** (Letti/Scritti) nell'elenco a discesa **Display** (Visualizza).

    ![Visualizzazione dell'applicazione Spark e del grafico del processo](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    Il nodo del grafico viene visualizzato in colori che rappresentano la mappa termica.

    ![Applicazione Spark e grafo del processo mappa termica](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Riprodurre il processo selezionando il pulsante **riproduzione** e arrestarsi in qualsiasi momento selezionando il pulsante Interrompi. Durante la riproduzione, le attività vengono visualizzate in colori che indicano lo stato:

    |Colore |Description |
    |---|---|
    |Verde|il processo è stato completato correttamente.|
    |Orange|Istanze delle attività non riuscite, ma non influiscono sul risultato finale del processo. Queste attività avevano istanze duplicate o ripetute che potrebbero essere completate in un secondo momento.|
    |Blu|l'attività è in esecuzione.|
    |Bianco|l'attività è in attesa di esecuzione o la fase è stata ignorata.|
    |Rosso|l'attività non è stata completata.|

    ![Esempio di colore dell'applicazione Spark e del grafico del processo, in esecuzione](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

    La fase ignorata viene visualizzata in bianco.
    ![esempio di colore dell'applicazione Spark e del grafico del processo, ignorare](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Esempio di colore dell'applicazione Spark e del grafico del processo non riuscito](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

    > [!NOTE]  
    > È possibile riprodurre qualsiasi processo. Per un processo non completato, la riproduzione non è supportata.

+ Usare la rotellina del mouse per ingrandire/ridurre il grafico del processo o fare clic su **Zoom to fit** (Adatta alla finestra) per adattarlo alle dimensioni dello schermo.

    ![Adatta allo zoom del grafico del processo e dell'applicazione Spark](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Passare il puntatore sul nodo del grafico per visualizzare la descrizione comando quando sono presenti attività non riuscite e fare clic sulla fase per aprire la pagina della fase.

    ![Descrizione comando dell'applicazione Spark e del grafico del processo](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Nella scheda del grafo del processo per le fasi verranno visualizzate una descrizione comando e una piccola icona se hanno attività che soddisfano le condizioni seguenti:
  + Asimmetria dei dati: dimensioni dei dati letti > dimensioni medie dei dati letti di tutte le attività in questa fase * 2 e dimensioni dei dati letti > 10 MB.
  + Sfasamento dell'ora: tempo di esecuzione > tempo di esecuzione medio di tutte le attività in questa fase * 2 e tempo di esecuzione > 2 min.

    ![Icona dell'applicazione Spark e della distorsione del grafico del processo](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ Il nodo del grafico del processo visualizzerà le informazioni seguenti per ogni fase:
  + ID.
  + Nome o descrizione.
  + Numero totale di attività.
  + Dati letti: la somma delle dimensioni di input e delle dimensioni dei dati casuali letti.
  + Dati scritti: la somma delle dimensioni di output e delle dimensioni dei dati casuali scritti.
  + Tempo di esecuzione: il tempo trascorso dall'ora di inizio del primo tentativo all'ora di completamento dell'ultimo tentativo.
  + Conteggio delle righe: la somma dei record di input, dei record di output, dei record di letture casuali letti e dei record di scritture casuali.
  + Avanzamento.

    > [!NOTE]  
    > Per impostazione predefinita, il nodo del grafico del processo mostrerà informazioni dall'ultimo tentativo di ogni fase (ad eccezione del tempo di esecuzione delle fasi), ma durante la riproduzione il nodo del grafico mostrerà informazioni su ogni tentativo.

    > [!NOTE]  
    > Per le dimensioni dei dati di lettura e scrittura viene usato 1 MB = 1000 KB = 1000 * 1000 byte.

+ Inviare commenti e suggerimenti con problemi selezionando Invia **commenti e suggerimenti**.

    ![Commenti e suggerimenti sull'applicazione Spark e sui grafici dei processi](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="diagnosis-tab-in-apache-spark-history-server"></a>Scheda della diagnosi nel Server cronologia Apache Spark

Selezionare ID processo, quindi selezionare **diagnosi** dal menu strumento per ottenere la visualizzazione diagnosi processo. La scheda della diagnosi include **Asimmetria dei dati**, **Sfasamento dell'ora** ed **Executor Usage Analysis** (Analisi utilizzo executor).

+ Esaminare l' **asimmetria dei dati**, lo **sfasamento dell'ora**e l'analisi dell'utilizzo dell' **Executor** selezionando le schede rispettivamente.

    ![SparkUI di nuovo la scheda asimmetria dati diagnosi](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Asimmetria dei dati

Selezionare la scheda **asimmetria dati** . le attività inclinate corrispondenti vengono visualizzate in base ai parametri specificati.

+ **Specificare i parametri** : la prima sezione Visualizza i parametri, che vengono usati per rilevare l'asimmetria dei dati. La regola predefinita è: i dati delle attività lette sono maggiori di tre volte i dati medi dell'attività letti e i dati dell'attività letti sono maggiori di 10 MB. Per definire una regola personalizzata per le attività asimmetriche, è possibile scegliere i parametri e le sezioni **Fase asimmetrica** e **Skew Char** (Grafico asimmetrico) verranno aggiornate di conseguenza.

+ **Fase asimmetrica** : la seconda sezione Visualizza le fasi, che hanno attività inclinate che soddisfano i criteri specificati in precedenza. Se in una fase è presente più di un'attività inclinata, nella tabella della fase asimmetrica viene visualizzata solo l'attività più inclinata, ad esempio i dati più grandi per l'asimmetria dei dati.

    ![scheda asimmetria dati diagnosi sparkui](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **Skew Chart** (Grafico asimmetrico): quando una riga della tabella in Fase asimmetrica viene selezionata, il grafico asimmetrico visualizza altri dettagli sulle distribuzioni dell'attività in base alla quantità di dati letti e al tempo di esecuzione. Le attività asimmetriche vengono contrassegnate in rosso, quelle normali in blu. Per motivi di prestazioni, il grafico visualizza solo un massimo di 100 attività di esempio. Nel pannello in basso a destra vengono visualizzati i dettagli dell'attività.

    ![grafico di asimmetria sparkui per la fase 10](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Sfasamento dell'ora

La scheda **Sfasamento dell'ora** visualizza le attività asimmetriche in base al tempo di esecuzione delle attività.

+ **Specificare i parametri** : la prima sezione Visualizza i parametri, che vengono usati per rilevare lo sfasamento dell'ora. I criteri predefiniti per il rilevamento dello sfasamento dell'ora sono: il tempo di esecuzione dell'attività è maggiore di tre volte rispetto al tempo medio di esecuzione e il tempo di esecuzione dell'attività è maggiore di 30 secondi. È possibile modificare i parametri in base alle esigenze. **Fase asimmetrica** e **Skew Chart** (Grafico asimmetrico) visualizzano le informazioni corrispondenti sulle fasi e sulle attività, come la scheda **Asimmetria dei dati** precedente.

+ Selezionare **sfasamento dell'ora**, quindi il risultato filtrato viene visualizzato nella sezione **fase inclinata** in base ai parametri impostati nella sezione **specificare i parametri**. Selezionare un elemento nella sezione **fase inclinata** , quindi il grafico corrispondente viene redatto in sezione3 e i dettagli dell'attività vengono visualizzati nel pannello in basso a destra.

    ![sezione sfasamento dell'ora diagnosi sparkui](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Executor Usage Analysis (Analisi utilizzo executor)

Il grafico Executor Usage (Utilizzo executor) visualizza l'allocazione di executor effettiva del processo Spark e lo stato di esecuzione.  

+ Selezionare l' **analisi di utilizzo dell'executor**, quindi quattro tipi di curve sull'utilizzo dell'executor, inclusi Executor **allocati**, esecutori **in esecuzione**, **esecutori inattivi**e **istanze di Executor max**. Per quanto riguarda gli executor allocati, ogni evento di aggiunta o di rimozione di un executor aumenterà o diminuirà il numero di executor allocati. Per un ulteriore confronto, è possibile controllare la sequenza temporale di eventi nella scheda "Processi".

    ![scheda esecutori diagnosi sparkui](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ Selezionare l'icona del colore per selezionare o deselezionare il contenuto corrispondente in tutte le bozze.

    ![sparkui diagnosi selezione grafico](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>FAQ

### <a name="1-revert-to-community-version"></a>1. ripristinare la versione della community

Per tornare alla versione Community, procedere come segue:

1. Aprire il cluster in Ambari.
1. Passare a **Spark2** > **configs** > **Custom Spark2-defaults**.
1. Selezionare **Aggiungi proprietà...** , Aggiungi **Spark. UI. Enhancement. Enabled = false**, Salva.
1. La proprietà viene impostata su **false**.
1. Selezionare **Salva** per salvare la configurazione.

    ![La funzionalità Apache Ambari si disattiva](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. Selezionare **Spark2** nel riquadro sinistro, nella scheda **Riepilogo** , selezionare **Spark2 Cronologia server**.

    ![Visualizzazione di riepilogo di Apache Ambari Spark2](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Riavviare il server di cronologia selezionando **Riavvia** il **server della cronologia Spark2**.

    ![Riavvio della cronologia di Apache Ambari Spark2](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  
1. Aggiornare l'interfaccia utente Web Server cronologia Spark. Verrà ripristinata la versione Community.

### <a name="2-upload-history-server-event"></a>2. caricare un evento del server di cronologia

Se si verificano errori del server cronologia, procedere come segue per recuperare l'evento:

1. Scaricare l'evento selezionando **Scarica** nell'interfaccia utente Web di Cronologia server.

    ![Download del server della cronologia Spark2](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Selezionare Invia **commenti e suggerimenti** dalla scheda dati/Graph.

    ![Spark Graph fornisce commenti e suggerimenti](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Specificare il titolo e la descrizione dell'errore, trascinare il file ZIP nel campo di modifica, quindi fare clic su **Submit new issue** (Invia nuovo problema).

    ![esempio di problema di file Apache Spark](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. aggiornare il file jar per uno scenario di hotfix

Se si vuole eseguire l'aggiornamento tramite hotfix, usare lo script seguente per aggiornare spark-enhancement.jar*.

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

**Utilizzo**:

`upgrade_spark_enhancement.sh https://${jar_path}`

**Esempio**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

**Per usare il file Bash dal portale di Azure**

1. Avviare [portale di Azure](https://ms.portal.azure.com)e selezionare il cluster.
2. Completare un' [azione script](../hdinsight-hadoop-customize-cluster-linux.md) con i parametri seguenti:

    |Proprietà |Value |
    |---|---|
    |Tipo di script|- Personalizzato|
    |name|UpgradeJar|
    |URI script Bash|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Tipo/i di nodo|Head, ruolo di lavoro|
    |parameters|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![portale di Azure invia azione script](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Problemi noti

+ Attualmente funziona solo per i cluster Spark 2,3 e 2,4.

+ I dati di input/output con RDD non vengono visualizzati nella scheda dati.

## <a name="next-steps"></a>Passaggi successivi

+ [Gestire le risorse di un cluster Apache Spark in HDInsight](apache-spark-resource-manager.md)
+ [Configurare le impostazioni di Apache Spark](apache-spark-settings.md)

## <a name="contact-us"></a>Contattaci

In caso di commenti o problemi durante l'uso di questo strumento, inviare un messaggio di posta elettronica all'indirizzo ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)).
