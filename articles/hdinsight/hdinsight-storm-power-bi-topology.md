<properties
 pageTitle="Scrivere dati in Power BI da Apache Storm | Microsoft Azure"
 description="Scrivere i dati in Power BI da una topologia C# in esecuzione in un cluster Apache Storm in HDInsight. Inoltre, creare un report e un dashboard in tempo reale utilizzando Power BI."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="04/28/2015"
 ms.author="larryfr"/>

# Usare Power BI (anteprima) per visualizzare i dati provenienti da una topologia Apache Storm

L'anteprima di Power BI permette di mostrare visivamente i dati sotto forma di report o dashboard. L'API REST di Power BI permette di usare con facilità i dati da una topologia in esecuzione su Apache Storm in un cluster HDInsight in Power BI.

In questo documento verrà illustrato come usare Power BI per creare un report e un dashboard dai dati creati da una topologia Storm.

## Prerequisiti

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un utente di Azure Active Directory con accesso [Power BI](https://powerbi.com)

* Visual Studio (una delle versioni seguenti)

    * Visual Studio 2012 con [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)

    * Visual Studio 2013 con [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) o [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)

    * Visual Studio 2015 [CTP6](http://visualstudio.com/downloads/visual-studio-2015-ctp-vs)

* Gli strumenti HDInsight per Visual Studio. Per informazioni sull'installazione, vedere [Introduzione all'uso di HDInsight Tools per Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

## Funzionamento

Questo esempio include una topologia C# Storm che genera in modo casuale una frase, la suddivide in parole, conta le parole e invia il conteggio delle parole all'API REST di Power BI. Il pacchetto NuGet [PowerBi.Api.Client](https://github.com/Vtek/PowerBI.Api.Client) viene usato per comunicare con Power BI.

I file seguenti nel progetto implementano la funzionalità specifica di Power BI:

* **PowerBiBolt.cs**: implementa il Bolt di Storm, che invia dati a Power BI.

* **Data.cs**: descrive l'oggetto dati o la riga da inviare a Power BI.

> [AZURE.WARNING]Sembra che Power BI permetta la creazione di più set di dati con lo stesso nome. È possibile che ciò si verifichi se il set di dati non esiste e la topologia crea più istanze del Bolt di Power BI. Per evitare questo problema, impostare l'hint di parallelismo del Bolt su 1 (come illustrato in questo esempio) oppure creare il set di dati prima di distribuire la topologia.
>
> L'applicazione console **CreateDataset** inclusa in questa soluzione viene fornita come esempio relativo a come creare il set di dati all'esterno della topologia.

## Registrare un'applicazione Power BI

1. Per iscriversi a Power BI, eseguire i passaggi in [Guida introduttiva a Power BI](https://msdn.microsoft.com/it-it/library/dn931989.aspx).

2. Per creare una registrazione dell'applicazione, eseguire i passaggi in [Registrare un'app](https://msdn.microsoft.com/it-it/library/dn877542.aspx). L'app verrà usata durante l'accesso all'API REST di Power BI.

    > [AZURE.IMPORTANT]Salvare l'**ID client** per la registrazione dell'applicazione.

## Scaricare l'esempio

Scaricare l'[esempio di Power BI per C# Storm in HDInsight](https://github.com/Blackmist/hdinsight-csharp-storm-powerbi). Per scaricarlo, biforcarlo o clonarlo mediante [git](http://git-scm.com/) oppure usare il collegamento **Download** per scaricare un file con estensione zip dell'archivio.

## Configurare l'esempio

1. Aprire l'esempio in Visual Studio. In **Esplora soluzioni** aprire il file **SCPHost.exe.config** e quindi individuare l'elemento **<OAuth .../>**. Immettere i valori per le proprietà seguenti dell'elemento.

    * **Client**: ID client della registrazione dell'applicazione creata in precedenza.

    * **Utente**: account Azure Active Directory in grado di accedere a Power BI.

    * **Password**: password per l'account Azure Active Directory.

2. (Facoltativo). Il nome predefinito per il set di dati usato da questo progetto è **Words**. Per cambiare il nome, fare clic con il pulsante destro del mouse sul progetto **WordCount** in **Esplora soluzioni**, scegliere **Proprietà** e quindi selezionare **Impostazioni**. Modificare la voce **DatasetName** specificando il valore desiderato.

2. Salvare e chiudere i file.

## Distribuire l'esempio

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **WordCount**, quindi scegliere **Invia a Storm in HDInsight**. Selezionare il cluster HDInsight dall'elenco a discesa **Cluster Storm**.

    > [AZURE.NOTE]Il popolamento dell'elenco a discesa **Cluster Storm** con i nomi dei server potrebbe richiedere alcuni minuti.
    >
    > Se richiesto, immettere le credenziali di accesso per la sottoscrizione di Azure. Se si dispone di più di una sottoscrizione, accedere a quella che contiene il cluster Storm in HDInsight.

2. Dopo che la topologia è stata inviata correttamente, verrà visualizzato l'elenco Topologie Storm relativo al cluster. Selezionare dall'elenco la topologia WordCount per visualizzare le informazioni sulla topologia in esecuzione.

    ![Topologie con la topologia WordCount selezionata](./media/hdinsight-storm-power-bi-topology/topologysummary.png)

    > [AZURE.NOTE]È possibile visualizzare Topologie Storm anche da Esplora server, espandendo Azure, HDInsight e quindi facendo clic su un cluster Storm in HDInsight e selezionando Visualizza topologie Storm.

3. Quando viene visualizzato il **Riepilogo topologia**, scorrere fino a visualizzare la sezione **Bolt**. In questa sezione si noti la colonna **Eseguito** per il Bolt **PowerBI**. Usare il pulsante di aggiornamento nella parte superiore della pagina per aggiornare la visualizzazione fino a quando il valore non viene impostato su un valore diverso da zero. Quando questo numero comincia ad aumentare, indica che gli elementi vengono scritti in Power BI.

## Creare un report

1. In un browser passare a [https://PowerBI.com](https://powerbi.com). Accedere con il proprio account.

2. Sul lato sinistro della pagina espandere **Set di dati**. Selezionare la voce **Words**, ovvero il set di dati creato dalla topologia di esempio.

    ![Voce del set di dati Words](./media/hdinsight-storm-power-bi-topology/words.png)

3. Dall'area **Campi** espandere **WordCount**. Trascinare le voci **Count** e **Word** nella parte centrale della pagina. Verrà creato un nuovo grafico che mostra una barra per ogni parola che indica il numero di occorrenze della parola.

    ![Grafico WordCount](./media/hdinsight-storm-power-bi-topology/wordcountchart.png)

4. Dall'angolo superiore sinistro della pagina selezionare **Salva** per creare un nuovo report. Immettere **Conteggio parole** come nome del report.

5. Selezionare il logo di Power BI per tornare al dashboard. Il report **Conteggio parole** è ora disponibile in **Report**.

## Creare un dashboard attivo

1. Accanto a **Dashboard** selezionare l'icona **+** per creare un nuovo dashboard. Assegnare al nuovo dashboard il nome **Conteggio parole attivo**.

2. Selezionare il report **Conteggio parole** creato in precedenza. Quando viene visualizzato, selezionare il grafico e quindi selezionare l'icona a forma di puntina da disegno nell'angolo superiore destro del grafico. Verrà visualizzata una notifica relativa al fatto che il grafico è stato bloccato sul dashboard.

    ![Grafico con icona a forma di puntina da disegno visualizzata](./media/hdinsight-storm-power-bi-topology/pushpin.png)

2. Selezionare il logo di Power BI per tornare al dashboard. Selezionare il dashboard **Conteggio parole attivo**. Include ora il grafico Conteggio parole e il grafico viene aggiornato ogni volta che nuove voci vengono inviate a Power BI dalla topologia WordCount in esecuzione su HDInsight.

    ![Dashboard attivo](./media/hdinsight-storm-power-bi-topology/dashboard.png)

## Arrestare la topologia WordCount

La topologia rimarrà in esecuzione fino all'arresto o all'eliminazione del cluster Storm in HDInsight. Per arrestare la topologia, eseguire la procedura seguente.

1. In Visual Studio aprire la finestra **Riepilogo topologia** per la topologia WordCount. Se il Riepilogo topologia non è già aperto, passare a **Esplora server**, espandere le voci **Azure** e **HDInsight**, fare clic con il pulsante destro del mouse sul cluster Storm in HDInsight e quindi scegliere **Visualizza topologie Storm**. Selezionare infine la topologia **WordCount**.

2. Selezionare il pulsante **Termina** per arrestare la topologia **WordCount**.

    ![Pulsante Termina nel riepilogo della topologia](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## Passaggi successivi

In questo documento è stato illustrato come inviare dati da una topologia Storm a Power BI mediante REST. Per informazioni su come usare altre tecnologie di Azure, vedere l'articolo seguente:

* [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)
 

<!---HONumber=62-->