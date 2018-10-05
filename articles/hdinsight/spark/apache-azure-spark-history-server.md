---
title: Usare il Server cronologia Spark esteso per eseguire il debug e la diagnosi di applicazioni Spark - Azure HDInsight
description: Usare il Server cronologia Spark esteso per il debug e la diagnosi di applicazioni Spark - Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 4627593e4ab96c63423a7afd6152f3a004bc6c3f
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042364"
---
# <a name="use-extended-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Usare il Server cronologia Spark esteso per il debug e la diagnosi di applicazioni Spark

Questo articolo illustra come usare il Server cronologia Spark esteso per il debug e la diagnosi di applicazioni Spark completate e in esecuzione. L'estensione include una scheda dati, una scheda del grafo e una scheda diagnosi. Nella scheda **Dati** gli utenti possono controllare i dati di input e output del processo Spark. Nella scheda **Grafo** gli utenti possono controllare il flusso di dati e riprodurre il grafico del processo. Nella scheda **Diagnosi** l'utente può fare riferimento ad **Asimmetria dei dati**, **Sfasamento dell'ora** ed **Executor Usage Analysis** (Analisi utilizzo executor).

## <a name="get-access-to-spark-history-server"></a>Ottenere l'accesso al Server cronologia Spark

Server cronologia Spark è l'interfaccia utente Web per le applicazioni Spark completate e in esecuzione. 

### <a name="open-the-spark-history-server-web-ui-from-azure-portal"></a>Aprire l'interfaccia utente Web di Server cronologia Spark dal portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/) aprire il cluster Spark. Per altre informazioni, vedere [Elencare e visualizzare i cluster](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. In **Collegamenti rapidi** fare clic su **Dashboard cluster** e quindi su **Server cronologia Spark**. Quando richiesto, immettere le credenziali di amministratore per il cluster di Spark. 

    ![Server cronologia Spark](./media/apache-azure-spark-history-server/launch-history-server.png "Server cronologia Spark")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Aprire l'interfaccia utente Web di Server cronologia Spark dall'URL
Aprire il Server cronologia Spark passando all'URL seguente, sostituendo <ClusterName> con il nome del cluster Spark del cliente.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

L'interfaccia utente Web Server cronologia Spark è simile all'immagine seguente:

![Server cronologia Spark in HDInsight](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="data-tab-in-spark-history-server"></a>Scheda dati nel Server cronologia Spark
Selezionare l'ID di processo, quindi fare clic su **Data** (Dati) nel menu degli strumenti per ottenere la visualizzazione dati.

+ Controllare **Inputs** (Input), **Outputs** (Output) e **Table Operations** (Operazioni tabella) selezionando le schede separatamente.

    ![Schede dati](./media/apache-azure-spark-history-server/sparkui-data-tabs.png)

+ Copiare tutte le righe facendo clic sul pulsante **Copy** (Copia).

    ![Copia dati](./media/apache-azure-spark-history-server/sparkui-data-copy.png)

+ Salvare tutti i dati come file con estensione CSV facendo clic sul pulsante **csv**.

    ![Salvataggio dati](./media/apache-azure-spark-history-server/sparkui-data-save.png)

+ Eseguire ricerche immettendo parole chiave nel campo **Search** (Cerca). I risultati della ricerca verranno visualizzati immediatamente.

    ![Ricerca dati](./media/apache-azure-spark-history-server/sparkui-data-search.png)

+ Fare clic sull'intestazione di colonna per ordinare la tabella, fare clic sul segno più per espandere una riga e visualizzare altri dettagli o fare clic sul segno meno per comprimere una riga.

    ![Tabella dati](./media/apache-azure-spark-history-server/sparkui-data-table.png)

+ Scaricare un singolo file facendo clic sul pulsante **Partial Download** (Download parziale) sulla destra. Il file selezionato verrà scaricato in locale e, se non esiste più, si aprirà una nuova scheda con i messaggi di errore.

    ![Riga download dati](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Copiare il percorso completo o relativo selezionando **Copy Full Path** (Copia percorso completo) o **Copy Relative Path** (Copia percorso relativo) dall'elenco di opzioni visualizzate espandendo il menu di download. Per i file di Azure Data Lake Storage, facendo clic su **Open in Azure Storage Explorer** (Apri in Azure Storage Explorer) si aprirà Azure Storage Explorer e all'accesso verrà visualizzata la cartella.

    ![Percorso di copia dati](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Fare clic sul numero sotto la tabella per spostarsi tra le pagine quando in un'unica pagina compaiono troppe righe. 

    ![Pagina dati](./media/apache-azure-spark-history-server/sparkui-data-page.png)

+ Passare il puntatore sul punto interrogativo accanto a Data (Dati) per visualizzare la descrizione comando oppure fare clic sul punto interrogativo per ottenere altre informazioni.

    ![Altre informazioni sui dati](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Per inviare feedback sui problemi, fare clic su **Provide us feedback** (Invia feedback).

    ![Feedback nella scheda grafico](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="graph-tab-in-spark-history-server"></a>Scheda del grafo nel Server cronologia Spark
Selezionare l'ID di processo, quindi fare clic su **Graph** (Grafico)nel menu degli strumenti per ottenere la visualizzazione del grafico del processo.

+ Controllare la panoramica del processo tramite il grafico del processo generato. 

+ Per impostazione predefinita vengono mostrati tutti i processi ed è possibile filtrare la visualizzazione per **ID processo**.

    ![ID processo del grafico](./media/apache-azure-spark-history-server/sparkui-graph-jobid.png)

+ Per impostazione predefinita è selezionato **Progress** (Avanzamento). È anche possibile controllare il flusso di dati selezionando **Read/Written** (Letti/Scritti) nell'elenco a discesa **Display** (Visualizza).

    ![Visualizzazione del grafico](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    Il nodo del grafico viene visualizzato in colori che rappresentano la mappa termica.

    ![Mappa termica del grafico](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Riprodurre il processo facendo sul pulsante **Playback** (Riproduci) e arrestare la riproduzione in qualsiasi momento facendo clic sul pulsante di arresto. Durante la riproduzione, le attività vengono visualizzate in colori che indicano lo stato:

    + Il verde indica l'esito positivo: il processo è stato completato correttamente.
    + L'arancione indica i tentativi ripetuti: istanze di attività non riuscite, ma che non influiscono sul risultato finale del processo. Queste attività avevano istanze duplicate o ripetute che potrebbero essere completate in un secondo momento.
    + Il blu indica lo stato di esecuzione: l'attività è in esecuzione.
    + Il bianco indica un processo in attesa o ignorato: l'attività è in attesa di esecuzione o la fase è stata ignorata.
    + Il rosso indica l'esito negativo: l'attività non è stata completata.

    ![Esempio di colore del grafico, in esecuzione](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    La fase ignorata viene visualizzata in bianco.
    ![Esempio di colore del grafo, ignorato](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Esempio di colore del grafico, non riuscito](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]
    > È possibile riprodurre qualsiasi processo. Per un processo non completato, la riproduzione non è supportata.


+ Usare la rotellina del mouse per ingrandire/ridurre il grafico del processo o fare clic su **Zoom to fit** (Adatta alla finestra) per adattarlo alle dimensioni dello schermo.
 
    ![Adattamento del grafico alla finestra](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Passare il puntatore sul nodo del grafico per visualizzare la descrizione comando quando sono presenti attività non riuscite e fare clic sulla fase per aprire la pagina della fase.

    ![Descrizione comando del grafico](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Nella scheda del grafo del processo per le fasi verranno visualizzate una descrizione comando e una piccola icona se hanno attività che soddisfano le condizioni seguenti:
    + Asimmetria dei dati: dimensioni dei dati letti > dimensioni medie dei dati letti di tutte le attività in questa fase * 2 e dimensioni dei dati letti > 10 MB
    + Sfasamento dell'ora: tempo di esecuzione > tempo di esecuzione medio di tutte le attività in questa fase * 2 e tempo di esecuzione > 2 min

    ![Icona dello sfasamento nel grafo](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

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

+ Per inviare feedback sui problemi, fare clic su **Provide us feedback** (Invia feedback).

    ![Feedback nella scheda grafico](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="diagnosis-tab-in-spark-history-server"></a>Scheda della diagnosi nel Server cronologia Spark
Selezionare l'ID di processo, quindi fare clic su **Diagnosi** nel menu degli strumenti per ottenere la visualizzazione della diagnosi del processo. La scheda della diagnosi include **Asimmetria dei dati**, **Sfasamento dell'ora** ed **Executor Usage Analysis** (Analisi utilizzo executor).
    
+ Controllare **Asimmetria dei dati**, **Sfasamento dell'ora** ed **Executor Usage Analysis** (Analisi utilizzo executor) selezionando le rispettive schede.

    ![Schede della diagnosi](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Asimmetria dei dati
Fare clic sulla scheda **Asimmetria dei dati** per visualizzare le attività asimmetriche corrispondenti in base ai parametri specificati. 

+ **Specificare i parametri**: la prima sezione visualizza i parametri usati per rilevare l'asimmetria dei dati. La regola predefinita è: la quantità di dati attività letti è superiore al triplo della quantità media di dati attività letti e la quantità di dati attività letti è superiore a 10 MB. Per definire una regola personalizzata per le attività asimmetriche, è possibile scegliere i parametri e le sezioni **Fase asimmetrica** e **Skew Char** (Grafico asimmetrico) verranno aggiornate di conseguenza.

+ **Fase asimmetrica**: la seconda sezione visualizza le fasi con attività asimmetriche che soddisfano i criteri specificati sopra. Se in una fase sono presenti più attività asimmetriche, la tabella in Fase asimmetrica visualizza l'attività più asimmetrica (ad esempio, la quantità di dati maggiore per l'asimmetria dei dati).

    ![Sezione 2 di Asimmetria dei dati](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **Skew Chart** (Grafico asimmetrico): quando una riga della tabella in Fase asimmetrica viene selezionata, il grafico asimmetrico visualizza altri dettagli sulle distribuzioni dell'attività in base alla quantità di dati letti e al tempo di esecuzione. Le attività asimmetriche vengono contrassegnate in rosso, quelle normali in blu. Per motivi di prestazioni, il grafico visualizza solo un massimo di 100 attività di esempio. Nel pannello in basso a destra vengono visualizzati i dettagli dell'attività.

    ![Sezione 3 di Asimmetria dei dati](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Sfasamento dell'ora
La scheda **Sfasamento dell'ora** visualizza le attività asimmetriche in base al tempo di esecuzione delle attività. 

+ **Specificare i parametri**: la prima sezione visualizza i parametri usati per rilevare lo sfasamento dell'ora. I criteri predefiniti per rilevare lo sfasamento dell'ora sono: il tempo di esecuzione dell'attività è superiore al triplo del tempo di esecuzione medio e il tempo di esecuzione dell'attività è superiore a 30 secondi. È possibile modificare i parametri in base alle esigenze. **Fase asimmetrica** e **Skew Chart** (Grafico asimmetrico) visualizzano le informazioni corrispondenti sulle fasi e sulle attività, come la scheda **Asimmetria dei dati** precedente.

+ Fare clic su **Sfasamento dell'ora** per visualizzare il risultato filtrato nella sezione **Fase asimmetrica** in base ai parametri impostati nella sezione **Specificare i parametri**. Fare clic su un elemento nella sezione **Fase asimmetrica** per creare una bozza del grafico corrispondente nella sezione 3 e visualizzare i dettagli dell'attività nel pannello in basso a destra.

    ![Sezione 2 di Sfasamento dell'ora](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Executor Usage Analysis (Analisi utilizzo executor)
Il grafico Executor Usage (Utilizzo executor) visualizza l'allocazione di executor effettiva del processo Spark e lo stato di esecuzione.  

+ Fare clic su **Executor Usage Analysis** (Analisi utilizzo executor) per tracciare quattro tipi di curve sull'utilizzo degli executor, tra cui **Allocated Executors** (Executor allocati), **Running Executors** (Executor in esecuzione), **idle Executors** (Executor inattivi) e **Max Executor Instances** (Numero massimo di istanze di executor). Per quanto riguarda gli executor allocati, ogni evento di aggiunta o di rimozione di un executor aumenterà o diminuirà il numero di executor allocati. Per un ulteriore confronto, è possibile controllare la sequenza temporale di eventi nella scheda "Processi".

    ![Scheda degli executor](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ Fare clic sull'icona del colore per selezionare o deselezionare il contenuto corrispondente in tutte le bozze.

    ![Selezionare il grafico](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)


## <a name="faq"></a>Domande frequenti

### <a name="1-revert-to-community-version"></a>1. Tornare alla versione Community

Per tornare alla versione Community, procedere come segue:

1. Aprire il cluster in Ambari. Fare clic su **Spark2** nel pannello a sinistra.
2. Fare clic sulla scheda **Configs** (Configurazioni).
3. Espandere il gruppo **Custom spark2-defaults** (Impostazioni predefinite Spark2 personalizzate).
4. Fare clic su **Add Property** (Aggiungi proprietà), aggiungere **spark.ui.enhancement.enabled=false** e salvare.
5. La proprietà viene impostata su **false**.
6. Fare clic su **Salva** per salvare la configurazione.

    ![Funzionalità disattivata](./media/apache-azure-spark-history-server/sparkui-turn-off.png)

7. Fare clic su **Spark2** nel pannello a sinistra e nella scheda **Summary** (Riepilogo) fare clic su **Spark2 History Server** (Server cronologia Spark2).

    ![Riavvio del server 1](./media/apache-azure-spark-history-server/sparkui-restart-1.png) 

8. Riavviare il server cronologia facendo clic su **Restart** (Riavvia) per **Spark2 History Server** (Server cronologia Spark2).

    ![Riavvio del server 2](./media/apache-azure-spark-history-server/sparkui-restart-2.png)  

9. Aggiornare l'interfaccia utente Web Server cronologia Spark. Verrà ripristinata la versione Community.

### <a name="2-upload-history-server-event"></a>2. Caricare eventi del server cronologia

Se si verificano errori del server cronologia, procedere come segue per recuperare l'evento:
1. Scaricare l'evento facendo clic su **Download** nell'interfaccia utente Web Server cronologia.

    ![Download dell'evento](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Fare clic su **Provide us feedback** (Invia feedback) nella scheda dei dati o del grafico.

    ![Feedback nella scheda grafico](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Specificare il titolo e la descrizione dell'errore, trascinare il file ZIP nel campo di modifica, quindi fare clic su **Submit new issue** (Invia nuovo problema).

    ![Problema file](./media/apache-azure-spark-history-server/sparkui-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. Aggiornare il file JAR per lo scenario di aggiornamento rapido

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

1. Avviare il [Portale di Azure](https://ms.portal.azure.com) e selezionare il cluster.
2. Fare clic su **Azioni script**, quindi su **Invia nuova**. Completare il modulo **Invia azione script**, quindi fare clic sul pulsante **Crea**.
    
    + **Tipo di script**: selezionare **Personalizzato**.
    + **Nome**: specificare un nome per lo script.
    + **URI script Bash**: caricare il file Bash nel cluster privato, quindi copiare l'URL qui. In alternativa, usare l'URI fornito.
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

    + Selezionare **Head** e **Lavoro**.
    + **Parametri**: impostare i parametri per l'utilizzo del file Bash.

    ![Caricamento log o hotfix di aggiornamento](./media/apache-azure-spark-history-server/sparkui-upload2.png)


## <a name="known-issues"></a>Problemi noti

1.  Attualmente la procedura funziona solo per i cluster Spark 2.3.

2.  I dati di input/output in formato RDD non verranno visualizzati nella scheda dati.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire le risorse del cluster Spark in HDInsight](apache-spark-resource-manager.md)
* [Configurare le impostazioni di Spark](apache-spark-settings.md)


## <a name="contact-us"></a>Contatti

Per eventuali commenti oppure se si riscontrano problemi nell'uso di questo strumento, inviare un messaggio di posta elettronica all'indirizzo ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)).
