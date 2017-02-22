---
title: Correlare gli eventi nel tempo con Storm e HBase in HDInsight
description: Informazioni su come correlare gli eventi che giungono in momenti diversi tramite Storm e HBase in HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 17d11479-2d02-4790-8d29-05fb38351479
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/18/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 94e09583ef8070a7e98fd2b30648996648ce3c41
ms.openlocfilehash: 87d3b5ef8989984420f1d0fe7d4188698a68dd0d


---
# <a name="correlate-events-over-time-with-storm-and-hbase-on-hdinsight"></a>Correlare gli eventi nel tempo con Storm e HBase in HDInsight

Utilizzando un archivio dati permanente con Apache Storm, è possibile correlare le voci di dati che arrivano in momenti diversi. Ad esempio, il collegamento degli eventi di accesso e disconnessione di una sessione utente per calcolare il tempo di esecuzione della sessione.

In questo documento si apprenderà come creare una topologia di Storm C# di base che tenga traccia degli eventi di accesso e disconnessione per le sessioni utente e che calcoli la durata della sessione. La topologia utilizza HBase come archivio dati permanente. HBase consente di eseguire query in batch su dati cronologici per ottenere informazioni aggiuntive, ad esempio il numero di sessioni utente avviate o terminate durante un periodo di tempo specifico.

## <a name="prerequisites"></a>Prerequisiti

* Visual Studio e gli strumenti HDInsight per Visual Studio. Per informazioni sull'installazione, vedere [Introduzione all'uso di HDInsight Hadoop Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

* Cluster Apache Storm in HDInsight, basato su Windows. Esegue la topologia Storm, che elabora i dati in ingresso e li archivia in HBase.
  
  > [!IMPORTANT]
  > Anche se le topologie SCP.NET sono supportate nei cluster Storm basati su Linux creati dopo il 28/10/2016, il pacchetto HBase SDK per .NET disponibile dal 28/10/2016 non funziona correttamente in Linux.

* Cluster Apache HBase in HDInsight, basato su Linux o su Windows. È l'archivio dati per questo esempio.

* [Java](https://java.com) 1.7 o versione successiva nell'ambiente di sviluppo. Java viene usato per creare il pacchetto della topologia per l'invio al cluster HDInsight.

  * La variabile di ambiente **JAVA_HOME** deve puntare alla directory contenente Java.
  * La directory **%JAVA_HOME%/bin** deve essere inclusa nel percorso.

## <a name="architecture"></a>Architettura

![Diagramma del flusso di dati tramite la topologia](./media/hdinsight-storm-correlation-topology/correlationtopology.png)

La correlazione di eventi richiede un identificatore comune per l'origine dell'evento. Ad esempio, un ID utente, un ID di sessione o altro dato che sia a) univoco e b) incluso in tutti i dati inviati a Storm. In questo esempio viene utilizzato un valore GUID per rappresentare un ID sessione.

Questo esempio è costituito da due cluster di HDInsight:

* HBase: archivio dati permanente per i dati cronologici
* Storm: utilizzato per inserire i dati in ingresso

I dati vengono generati casualmente dalla topologia di Storm e sono costituiti dai seguenti elementi:

* ID di sessione: un GUID che identifica in modo univoco ogni sessione
* Evento: un evento START o END. Per questo esempio, l'evento START si verifica sempre prima dell'evento END
* Ora: l'ora dell'evento.

Questi dati vengono elaborati e archiviati in HBase.

### <a name="storm-topology"></a>Topologia Storm

Quando si avvia una sessione, un evento **START** viene ricevuto dalla topologia e registrato in HBase. Quando viene ricevuto un evento **END**, la topologia recupera l'evento **START** e calcola il tempo tra i due eventi. Questo valore relativo alla **durata** viene quindi archiviato in HBase con le informazioni sull'evento **END**.

> [!IMPORTANT]
> Mentre con questa topologia viene illustrato il modello di base, per una soluzione di produzione potrebbe essere necessaria la progettazione per gli scenari seguenti:
> 
> * Eventi che arrivano nell'ordine errato
> * Eventi duplicati
> * Eventi eliminati

La topologia di esempio è costituita dai seguenti componenti:

* Session.cs: simula una sessione utente tramite la creazione di un ID di sessione casuale, l'ora di inizio e la durata della sessione.

* Spout.cs: crea 100 sessioni, genera un evento START, attende il timeout casuale per ogni sessione, quindi genera un evento END. Successivamente, ricicla le sessioni terminate per generarne di nuove.

* HBaseLookupBolt.cs: utilizza l'ID di sessione per cercare le informazioni di sessione da HBase. Quando viene elaborato un evento END, consente di trovare l'evento START corrispondente e calcola la durata della sessione.

* HBaseBolt.cs: archivia le informazioni in HBase.

* TypeHelper.cs: consente la conversione di tipo durante la lettura/scrittura in HBase.

### <a name="hbase-schema"></a>Schema HBase

In HBase, i dati vengono archiviati in una tabella con lo schema e le impostazioni seguenti:

* Chiave di riga: la sessione ID viene utilizzato come chiave per le righe in questa tabella.

* Famiglia di colonne: il nome di famiglia è 'cf'. Le colonne memorizzate in questo gruppo sono le seguenti:
  
  * event: START o END.

  * time: il tempo in millisecondi in cui si è verificato l'evento.

  * duration: la lunghezza del periodo di tempo che intercorre tra gli eventi START ed END.

* VERSIONS: la famiglia 'cf' è impostata per conservare 5 versioni di ogni riga.
  
  > [!NOTE]
  > Le versioni sono un registro dei valori precedentemente memorizzati per una chiave di riga specifica. Per impostazione predefinita, HBase restituisce solo il valore relativo alla versione più recente di una riga. In questo caso, la stessa riga viene utilizzata per tutti gli eventi (START, END) ogni versione di una riga viene identificata dal valore di timestamp. Fornisce una visualizzazione cronologica di eventi registrati per un ID specifico.

## <a name="download-the-project"></a>Scaricare il progetto

Il progetto di esempio può essere scaricato da [https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation](https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation).

Il download contiene i seguenti progetti C#:

* CorrelationTopology: topologia Storm di C# che casualmente genera eventi di inizio e di fine per le sessioni utente. La durata di ogni sessione è compresa tra 1 e 5 minuti.

* SessionInfo: applicazione console C# che crea la tabella HBase e fornisce query di esempio per restituire informazioni sui dati della sessione archiviati.

## <a name="create-the-table"></a>Creare la tabella

1. Aprire il progetto **SessionInfo** in Visual Studio.

2. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **SessionInfo**, quindi scegliere **Proprietà**.
   
    ![Schermata del menu con le proprietà selezionate](./media/hdinsight-storm-correlation-topology/selectproperties.png)

3. Selezionare **Impostazioni**, quindi impostare i valori seguenti:
   
   * HBaseClusterURL: l'URL del cluster HBase. Ad esempio, https://myhbasecluster.azurehdinsight.net.

   * HBaseClusterUserName: l'account amministratore/utente HTTP per il cluster

   * HBaseClusterPassword: la password per l'account amministratore/utente HTTP

   * HBaseTableName: il nome della tabella da utilizzare in questo esempio

   * HBaseTableColumnFamily: il nome di famiglia di colonne
     
     ![Finestra di dialogo delle impostazioni di connessione](./media/hdinsight-storm-correlation-topology/settings.png)

4. Eseguire la soluzione. Quando richiesto, selezionare il tasto 'C' per creare la tabella nel cluster HBase.

## <a name="build-and-deploy-the-storm-topology"></a>Compilare e distribuire la topologia di Storm

1. Aprire la soluzione **CorrelationTopology** in Visual Studio.

2. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **CorrelationTopology**, quindi scegliere Proprietà.

3. Nella finestra Proprietà selezionare **Impostazioni** e fornire le informazioni seguenti. I primi 5 devono essere gli stessi valori utilizzati per il progetto **SessionInfo** :
   
   * HBaseClusterURL: l'URL del cluster HBase. Ad esempio, https://myhbasecluster.azurehdinsight.net.

   * HBaseClusterUserName: l'account amministratore/utente HTTP per il cluster.

   * HBaseClusterPassword: la password per l'account amministratore/utente HTTP.

   * HBaseTableName: il nome della tabella da utilizzare in questo esempio. Deve contenere lo stesso nome di tabella utilizzato nel progetto SessionInfo.

   * HBaseTableColumnFamily: il nome di famiglia di colonne. Deve contenere lo stesso nome di famiglia di colonne utilizzato nel progetto SessionInfo.
   
   > [!IMPORTANT]
   > Non modificare HBaseTableColumnNames, poiché i valori predefiniti sono i nomi utilizzati da **SessionInfo** per recuperare i dati.

4. Salvare le proprietà, quindi compilare il progetto.

5. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e selezionare **Submit to Storm on HDInsight**. Se richiesto, immettere le credenziali per la sottoscrizione di Azure.
   
   ![Immagine della voce di menu Submit to Storm](./media/hdinsight-storm-correlation-topology/submittostorm.png)

6. Nella finestra di dialogo **Submit Topology** , selezionare il cluster Storm che eseguirà questa topologia.
   
   > [!NOTE]
   > La prima volta che si invia una topologia potrebbe richiedere alcuni secondi per recuperare il nome dei cluster HDInsight.

7. Una volta la topologia è stata caricata e inviata al cluster, verrà visualizzata **Storm Topology View** e la topologia in esecuzione. Selezionare **CorrelationTopology** e utilizzare il pulsante Aggiorna nella parte superiore destra della pagina per aggiornare le informazioni di topologia.
   
   ![Immagine della visualizzazione topologia](./media/hdinsight-storm-correlation-topology/topologyview.png)
   
   Quando la topologia inizia la generazione di dati, il valore nella colonna **Emitted** verrà incrementato.
   
   > [!NOTE]
   > Se la finestra **Storm Topology View** non veniva visualizzato automaticamente, utilizzare i passaggi seguenti per aprirlo:
   > 
   > 1. Da **Esplora soluzioni** espandere **Azure**, quindi **HDInsight**.
   > 2. Fare clic sul cluster di Storm la topologia è in esecuzione e quindi selezionare **View Storm Topologies**

## <a name="query-the-data"></a>Eseguire query sui dati

Una volta generati i dati, utilizzare la procedura seguente per eseguire query sui dati.

1. Tornare al progetto **SessionInfo** . Se non in esecuzione, avviare una nuova istanza.

2. Quando richiesto, selezionare **S** per la ricerca dell'evento START. Verrà richiesto di immettere un'ora di inizio e fine per definire un intervallo di tempo; verranno restituiti solo gli eventi che si sono verificati all'interno di questo intervallo.
   
    Utilizzare i seguenti formati quando si immette l'ora di inizio e fine: HH:MM e 'M'' e 'am' o 'pm'. Ad esempio, 11:20 pm.
   
    Poiché la topologia è stata appena avviata, utilizzare una data di inizio precedente alla distribuzione e l'attuale ora di fine. In questo modo dovrebbero essere acquisiti la maggior parte degli eventi START generati durante l'avvio. Quando viene eseguita la query, dovrebbe essere visualizzato un elenco di voci simili al seguente:
   
        Session e6992b3e-79be-4991-afcf-5cb47dd1c81c started at 6/5/2015 6:10:15 PM. Timestamp = 1433527820737

La ricerca degli eventi END funziona come gli eventi START. Tuttavia, gli eventi END vengono generati in modo casuale in un tempo compreso tra 1 e 5 minuti dopo l'evento START. Pertanto, potrebbe essere necessario provare alcuni intervalli di tempo per trovare gli eventi END. Gli eventi END conterranno inoltre la durata della sessione, ovvero la differenza tra l'ora dell'evento START e l'ora dell'evento END. Di seguito è riportato un esempio di dati per gli eventi END:

    Session fc9fa8e6-6892-4073-93b3-a587040d892e lasted 2 minutes, and ended at 6/5/2015 6:12:15 PM

> [!NOTE]
> Sebbene i valori orari immessi siano indicati nell'ora locale, l'ora restituita dalla query sarà UTC.

## <a name="stop-the-topology"></a>Arrestare la topologia

Quando si è pronti per arrestare la topologia, tornare al progetto **CorrelationTopology** in Visual Studio. Nella finestra **Storm Topology View** selezionare la topologia quindi utilizzare il pulsante **Kill** nella parte superiore della visualizzazione topologia.

## <a name="delete-your-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di Storm, vedere [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md).




<!--HONumber=Nov16_HO4-->


