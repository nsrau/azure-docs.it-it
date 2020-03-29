---
title: 'Usare le funzionalità estese nel server di cronologia di Apache Spark per eseguire il debug delle app: Azure HDInsightUse the extended features in the Apache Spark History Server to debug apps - Azure HDInsight'
description: Usare le funzionalità estese nel server di cronologia Apache Spark per eseguire il debug e diagnosticare le applicazioni Spark - Azure HDInsight.Use the extended features in the Apache Spark History Server to debug and diagnose Spark applications - Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 5cf1986711479f7330b0cd477744d9f4e2ac6459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548935"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Utilizzare le funzionalità estese di Apache Spark History Server per eseguire il debug e diagnosticare le applicazioni Spark

In questo articolo viene illustrato come utilizzare le funzionalità estese di Apache Spark History Server per eseguire il debug e diagnosticare le applicazioni Spark completate o in esecuzione. L'estensione include una scheda **Dati,** una scheda Grafico e una scheda **Diagnosi.The** extension includes a Data tab, a **Graph** tab, and a Diagnosis tab. Nella scheda **Dati** è possibile controllare i dati di input e output del processo Spark. Nella scheda **Grafico** è possibile controllare il flusso di dati e riprodurre il grafico dei processi. Nella scheda **Diagnosi** è possibile fare riferimento alle funzionalità **Inclinazione dati,** **Inclinazione ora**ed **Analisi utilizzo esecutore.**

## <a name="get-access-to-the-spark-history-server"></a>Ottenere l'accesso al server di cronologia Spark

Il server Spark History è l'interfaccia utente Web per le applicazioni Spark completate ed in esecuzione. È possibile aprirlo dal portale di Azure o da un URL.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>Aprire l'interfaccia utente Web del server della cronologia di Spark dal portale di AzureOpen the Spark History Server web UI from the Azure portal

1. Nel [portale di Azure](https://portal.azure.com/) aprire il cluster Spark. Per altre informazioni, vedere [Elencare e visualizzare i cluster](../hdinsight-administer-use-portal-linux.md#showClusters).
2. In **Dashboard cluster**selezionare Server cronologia **spark**. Quando richiesto, immettere le credenziali di amministratore per il cluster di Spark.

    ![Avviare il server di cronologia Spark dal portale di Azure.Launch the Spark History Server from the Azure portal.](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Server cronologia Spark")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Aprire l'interfaccia utente Web di Spark History Server in base all'URL

Aprire Spark History Server `https://CLUSTERNAME.azurehdinsight.net/sparkhistory`accedendo a , dove **CLUSTERNAME** è il nome del cluster Spark.

L'interfaccia utente Web di Spark History Server potrebbe essere simile a questa immagine:

![La pagina Server cronologia Spark.](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>Utilizzare la scheda Dati nel server di cronologia Spark

Selezionare l'ID processo, quindi selezionare **Dati** dal menu degli strumenti per visualizzare la visualizzazione dati.

+ Esaminare **Gli input,** **gli output**e le operazioni **di tabella** selezionando le singole schede.

    ![Schede Dati nella pagina Data for Spark Application.](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Copiare tutte le righe selezionando il pulsante **Copia.**

    ![Copiare i dati nella pagina dell'applicazione Spark.](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Salvare tutti i dati come file . CSV selezionando il pulsante **csv.**

    ![Salvare i dati come file . CSV dalla pagina Applicazione dati per Spark.](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Eseguire la ricerca dei dati immettendo parole chiave nel campo **Cerca.** I risultati della ricerca verranno visualizzati immediatamente.

    ![Cercare i dati nella pagina Data for Spark Application.](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Selezionare l'intestazione di colonna per ordinare la tabella. Selezionare il segno più per espandere una riga e visualizzare ulteriori dettagli. Selezionare il segno meno per comprimere una riga.

    ![Tabella dati nella pagina Data for Spark Application.](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Scaricare un singolo file selezionando il pulsante **Download parziale** a destra. Il file selezionato verrà scaricato localmente. Se il file non esiste più, si aprirà una nuova scheda per visualizzare i messaggi di errore.

    ![Riga di download dei dati nella pagina Data for Spark Application.](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Copiare un percorso completo o relativo selezionando l'opzione **Copia percorso completo** o Copia percorso **relativo,** che si espande dal menu di download. Per i file di Archiviazione Data Lake di Azure selezionare **Apri in Azure Storage Explorer** per avviare Azure Storage Explorer e individuare la cartella dopo l'accesso.

    ![Opzioni Copia percorso completo e Copia percorso relativo nella pagina Dati per applicazione Spark.](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Se sono presenti troppe righe da visualizzare in una singola pagina, selezionare i numeri di pagina nella parte inferiore della tabella per spostarsi.

    ![Numeri di pagina nella pagina Applicazione dati per Spark.](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Per ulteriori informazioni, passare il puntatore del mouse o selezionare il punto interrogativo accanto a **Data per Spark Application** per visualizzare la descrizione comando.

    ![Ottenere ulteriori informazioni dalla pagina Applicazione dati per Spark.Get more information from the Data for Spark Application page.](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  Per inviare commenti e suggerimenti sui problemi, seleziona **Invia feedback**.

    ![Fornire commenti e suggerimenti dalla pagina Data per applicazione Spark.Provide feedback from the Data for Spark Application page.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>Utilizzare la scheda Grafico nel server di cronologia Spark

+ Selezionare l'ID processo, quindi selezionare **Grafico** nel menu degli strumenti per visualizzare il grafico del processo. Per impostazione predefinita, il grafico mostrerà tutti i processi. Filtrare i risultati utilizzando il menu a discesa **ID processo.**

    ![Menu a discesa ID processo nella pagina Spark Application & Job Graph.](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ **L'opzione Stato** di avanzamento è selezionata per impostazione predefinita. Controllare il flusso di dati selezionando **Lettura** o **Scritto** nel menu a discesa **Schermo.**

    ![Controllare il flusso di dati nella pagina Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ Il colore di sfondo di ogni attività corrisponde a una mappa termica.

   ![Mappa termica nella pagina Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |Colore |Descrizione |
    |---|---|
    |Green|il processo è stato completato correttamente.|
    |Arancione|L'attività non è riuscita, ma ciò non influisce sul risultato finale del processo. Queste attività hanno istanze duplicate o ritentate che possono avere esito positivo in un secondo momento.|
    |Blu|l'attività è in esecuzione.|
    |bianco|l'attività è in attesa di esecuzione o la fase è stata ignorata.|
    |Rosso|l'attività non è riuscita.|

     ![Esecuzione di un'attività nella pagina Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     Le fasi saltate vengono visualizzate in bianco.
    ![Un'attività ignorata nella pagina Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Un'attività non riuscita nella pagina Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > La riproduzione è disponibile per i lavori completati. Selezionare il pulsante **Riproduzione** per riprodurre il lavoro. Interrompere il lavoro in qualsiasi momento selezionando il pulsante di arresto. Quando un lavoro viene riprodotto, ogni attività visualizzerà il relativo stato in base al colore. La riproduzione non è supportata per i lavori incompleti.

+ Scorrere per ingrandire o ridurre il grafico del lavoro oppure selezionare **Ingrandisci per adattarlo** allo schermo.

    ![Selezionare Ingrandisci per adattarsi alla pagina Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Quando le attività hanno esito negativo, passare il puntatore del mouse sul nodo del grafico per visualizzare la descrizione comando e quindi selezionare la fase per aprirla in una nuova pagina.

    ![Visualizzare la descrizione comando nella pagina Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Nella pagina Spark Application & Job Graph, le fasi visualizzano le descrizioni comandi e le icone piccole se le attività soddisfano queste condizioni:
  + Inclinazione dei dati: dimensione dei dati di lettura > dimensione media dei dati di lettura di tutte le attività all'interno di questa fase 2 *e* dimensioni di lettura dei dati > 10 MB.
  + Asimmetria temporale: il tempo di esecuzione > tempo medio di esecuzione di tutte le attività all'interno di questa fase n. 2 *e* il tempo di esecuzione > 2 min.

    ![Icona dell'attività distorta nella pagina Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ Nel nodo del grafico dei processi verranno visualizzate le seguenti informazioni su ogni fase:
  + ID
  + Nome o descrizione
  + Numero totale attività
  + Dati letti: la somma delle dimensioni di input e della dimensione di lettura casuale
  + Scrittura dei dati: la somma delle dimensioni di output e della dimensione di scrittura casuale
  + Tempo di esecuzione: il tempo tra l'ora di inizio del primo tentativo e l'ora di completamento dell'ultimo tentativo
  + Conteggio righe: la somma dei record di input, dei record di output, dei record di lettura casuale e della scrittura casuale
  + Progress

    > [!NOTE]  
    > Per impostazione predefinita, il nodo del grafico dei processi visualizzerà le informazioni dell'ultimo tentativo di ogni fase (ad eccezione del tempo di esecuzione dello stage). Ma durante la riproduzione, il nodo del grafico del processo mostrerà informazioni su ogni tentativo.

    > [!NOTE]  
    > Per le dimensioni di lettura e scrittura dei dati, utilizziamo 1 MB , 1000 KB e 1000 x 1000 byte.

+ Inviare commenti e suggerimenti sui problemi selezionando **Invia commenti e suggerimenti**.

    ![L'opzione di feedback nella pagina Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>Utilizzare la scheda Diagnosi nel server di cronologia Spark

Selezionare l'ID processo, quindi selezionare **Diagnosi** dal menu degli strumenti per visualizzare la visualizzazione Diagnosi processo. La scheda **Diagnosi** include **inclinazione dei dati,** **inclinazione dell'ora**e **analisi dell'utilizzo dell'esecutore.**

+ Esaminare **l'inclinazione dei dati,** **l'inclinazione**temporale e **l'analisi dell'utilizzo dell'esecutore** selezionando rispettivamente le schede.

    ![Scheda Inclinazione dati all'interno della scheda Diagnosi.](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Asimmetria dei dati

Selezionare la scheda **Inclinazione dati.** Le attività distorte corrispondenti vengono visualizzate in base ai parametri specificati.

#### <a name="specify-parameters"></a>Specificare i parametri

Nella sezione **Specifica parametri** vengono visualizzati i parametri utilizzati per rilevare l'inclinazione dei dati. La regola predefinita è: i dati dell'attività letti sono maggiori di tre volte della media dei dati dell'attività letti e i dati dell'attività letti sono più di 10 MB. Se si desidera definire una regola personalizzata per le attività distorte, è possibile scegliere i parametri desiderati. Le sezioni Skewed Stage e **Skew Chart** verranno aggiornate di **conseguenza.**

#### <a name="skewed-stage"></a>Fase distorta

Nella sezione **Fase distorta** vengono visualizzate le fasi con attività distorte che soddisfano i criteri specificati. Se in una fase sono presenti più attività distorte, nella sezione **Fase distorta** viene visualizzata solo l'attività più distorta, ovvero i dati più grandi per l'inclinazione dei dati.

![Visualizzazione più ampia della scheda Inclinazione dati all'interno della scheda Diagnosi.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>Grafico ad inclinazione

Quando si seleziona una riga nella tabella Fase di **inclinazione,** il **grafico di inclinazione** visualizza più dettagli di distribuzione delle attività in base al tempo di lettura e di esecuzione dei dati. Le attività distorte sono contrassegnate in rosso e le normali in blu. Per tenere conto delle prestazioni, nel grafico vengono visualizzate fino a 100 attività di esempio. I dettagli dell'attività vengono visualizzati nel pannello in basso a destra.

![Grafico di inclinazione per la fase 10 nell'interfaccia utente di Spark.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Sfasamento dell'ora

La scheda **Sfasamento dell'ora** visualizza le attività asimmetriche in base al tempo di esecuzione.

#### <a name="specify-parameters"></a>Specificare i parametri

Nella sezione **Specifica parametri** vengono visualizzati i parametri utilizzati per rilevare l'inclinazione del tempo. La regola predefinita è: il tempo di esecuzione dell'attività è maggiore di tre volte del tempo medio di esecuzione e il tempo di esecuzione dell'attività è maggiore di 30 secondi. È possibile modificare i parametri in base alle esigenze. Il grafico **Scandite e** **inclinazione** inclinati visualizza le informazioni sulle fasi e sulle attività corrispondenti, proprio come nella scheda **Inclinazione dati.**

Quando si seleziona **Inclinazione ora**, il risultato filtrato viene visualizzato nella sezione **Fase inclinata,** in base ai parametri impostati nella sezione **Specifica parametri.** Quando selezionate un elemento nella sezione **Fase inclinata,** il grafico corrispondente viene redatto nella terza sezione e i dettagli dell'attività vengono visualizzati nel pannello in basso a destra.

![Scheda Inclinazione temporale all'interno della scheda Diagnosi.](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>Grafici di analisi dell'utilizzo dell'executor

Il **grafico di utilizzo dell'esecutore** visualizza lo stato effettivo di allocazione e di esecuzione dell'esecutore del processo.  

Quando si seleziona **Analisi utilizzo esecuzionetori**, vengono redatte quattro diverse curve relative all'utilizzo dell'esecutore: **Esecutori allocati**, **Esecutori**in esecuzione, **Esecutori inattivi**e **Istanze max executor**. Ogni evento **Executor added** o **Executor removed** aumenterà o diminuirà gli esecutori allocati. È possibile controllare **Sequenza temporale** evento nella scheda **Processi** per ulteriori confronti.

![La scheda Analisi utilizzo executor all'interno della scheda Diagnosi.](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

Selezionare l'icona del colore per selezionare o deselezionare il contenuto corrispondente in tutte le bozze.

 ![Selezionare il grafico nella scheda Analisi esecutore utilizzo.](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>Domande frequenti

### <a name="how-do-i-revert-to-the-community-version"></a>Come faccio a tornare alla versione della community?

Per ripristinare la versione della community, procedere come segue.

1. Aprire il cluster in Ambari.
1. Passare a**Configurazioni** **Spark2** > .
1. Selezionare **Custom spark2-defaults**.
1. Selezionare **Aggiungi proprietà...**.
1. Aggiungere **spark.ui.enhancement.enabled,false**, quindi salvarlo.
1. La proprietà viene impostata su **false**.
1. Selezionare **Salva** per salvare la configurazione.

    ![Disattivare una funzione in Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. Selezionare **Spark2** nel pannello a sinistra. Nella scheda **Riepilogo** selezionare **Server cronologia Spark2**.

    ![La visualizzazione di riepilogo in Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Per riavviare il server della cronologia Spark, selezionare il pulsante **Avviato** a destra di **Server cronologia Spark2**, quindi scegliere **Riavvia** dal menu a discesa.

    ![Riavviare Spark History Server in Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. Aggiornare l'interfaccia utente Web di Spark History Server. Verrà ripristinata la versione della community.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Come faccio a caricare un evento Spark History Server per segnalarlo come un problema?

Se si verifica un errore in Server cronologia Spark, eseguire la procedura seguente per segnalare l'evento.

1. Scaricare l'evento selezionando **Scarica** nell'interfaccia utente Web di Spark History Server.

    ![Scaricare l'evento nell'interfaccia utente del server di Cronologia Spark.Download the event in the Spark History Server UI.](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Selezionare **Invia commenti** e suggerimenti nella pagina **Spark Application & Job Graph.**

    ![Inviare commenti e suggerimenti nella pagina Spark Application & Job Graph](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Fornire il titolo e una descrizione dell'errore. Quindi, trascinare il file .zip nel campo di modifica e selezionare **Invia nuovo problema**.

    ![Carica e invia un nuovo problema.](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>Come si aggiorna un file JAR in uno scenario di hotfix?

Se si desidera eseguire l'aggiornamento con un hotfix, utilizzare lo script seguente, che aggiornerà. `spark-enhancement.jar*`

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

#### <a name="usage"></a>Uso

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>Esempio

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>Usare il file bash dal portale di AzureUse the bash file from the Azure portal

1. Avviare il portale di [Azure](https://ms.portal.azure.com)e quindi selezionare il cluster.
2. Completare [un'azione di script](../hdinsight-hadoop-customize-cluster-linux.md) con i parametri seguenti.

    |Proprietà |valore |
    |---|---|
    |Tipo di script|- Personalizzato|
    |Nome|AggiornaJar|
    |URI script Bash|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Tipo/i di nodo|Testa, Lavoratore|
    |Parametri|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Azione di invio script del portale di AzureAzure portal submit script action](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Problemi noti

+ Attualmente, Spark History Server funziona solo per Spark 2.3 e 2.4.

+ I dati di input e output che utilizzano RDD non verranno visualizzati nella scheda **Dati.**

## <a name="next-steps"></a>Passaggi successivi

+ [Gestire le risorse di un cluster Apache Spark in HDInsight](apache-spark-resource-manager.md)
+ [Configurare le impostazioni di Apache Spark](apache-spark-settings.md)

## <a name="feedback"></a>Commenti e suggerimenti

Se si dispone di commenti o suggerimenti o si verificano[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)problemi durante l'utilizzo di questo strumento, inviare un messaggio di posta elettronica a ( ).
