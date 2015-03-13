<properties 
	pageTitle="Introduzione all'uso di HDInsight Tools per Visual Studio | Azure" 
	description="Informazioni su come installare e usare HDInsight Tools per Visual Studio per connettersi a HDInsight ed eseguire query Hive." 
	services="HDInsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="2/17/2015" 
	ms.author="jgao"/>

# Introduzione all'uso di HDInsight Tools per Visual Studio

Informazioni su come usare HDInsight Tools per Visual Studio per connettersi a cluster HDInsight e inviare query Hive. Per altre informazioni sull'uso di HDInsight, vedere [Introduzione ad HDInsight][hdinsight.introduction] e [Introduzione all'uso di HDInsight][hdinsight.get.started]. Per altre informazioni sulla connessione a un cluster Storm, vedere [Sviluppo di topologie C# per Apache Storm in HDInsight mediante Visual Studio][hdinsight.storm.visual.studio.tools]. 

>[WACOM.NOTE] Tutte le funzionalità di HDInsight Tools disponibili per i cluster Windows funzionano anche in cluster Linux, ad eccezione di alcune funzionalità attualmente non supportate in Humboldt, ad esempio i log YARN di presentazione di un determinato processo Hive. 

>La versione più recente ha introdotto alcune nuove funzionalità, ad esempio il supporto Intellisense dell'editor Hive, la convalida locale di script Hive e l'accesso ai log Yarn.

## Contenuto dell'articolo
+ [Installare] 
+ [Connettersi alla sottoscrizione Azure]
+ [Esplorare le risorse collegate]
+ [Eseguire query Hive]
+ [Passaggi successivi]


## Prerequisiti

- Una workstation con i seguenti elementi:

	- Windows 7, Windows 8 o Windows 8.1
	- Visual Studio con le seguenti versioni:
		- Visual Studio 2012 Professional/Premium/Ultimate con [Update 4](http://www.microsoft.com/it-it/download/details.aspx?id=39305)
		- Visual Studio 2013 Community/Professional/Premium/Ultimate con [Update 4](https://www.microsoft.com/it-it/download/details.aspx?id=44921)
		- Visual Studio 2015 Preview

	>[WACOM.NOTE] Attualmente gli strumenti sono disponibili solo con la versione inglese. 


## Installare

È possibile installare HDInsight Tools per Visual Studio usando [Installazione guidata piattaforma Web](http://go.microsoft.com/fwlink/?LinkId=255386). È possibile eseguire query sugli strumenti usando la parola chiave "hdinsight". Per ogni versione di Visual Studio, è disponibile un pacchetto. È necessario scegliere quello che corrisponde alla versione di Visual Studio in uso.  Il pacchetto installerà anche Microsoft Hive ODBC Driver a 32 bit e a 64 bit.

![HDinsight Tools for Visual Studio Web Platform installer][1]


>[WACOM.NOTE] Se si dispone di Visual Studio 2012 o 2015 e della versione precedente di Azure SDK, è necessario rimuovere manualmente la versione precedente prima di installare quella più recente. Visual Studio 2013 supporta l'aggiornamento diretto.

## Connettersi alla sottoscrizione Azure
HDInsight Tools per Visual Studio consente di connettersi a cluster HDInsight, eseguire alcune operazioni di gestione di base ed eseguire query Hive.

**Per connettersi alla sottoscrizione Azure**

1.	Eseguire Visual Studio.
2.	Scegliere **Esplora server** dal menu **Visualizza** per aprire la finestra Esplora server.
3.	Espandere **Azure** e quindi espandere **HDInsight**. 

	>[WACOM.NOTE]Si noti che la finestra **Elenco attività HDInsight** è aperta. Se tale finestra non è visualizzata, è possibile aprirla scegliendo **Altre finestre** dal menu **VISUALIZZA** e quindi facendo clic su **Finestra elenco attività HDInsight**.  
4.	Immettere le credenziali della sottoscrizione Azure e fare clic su **Accedi**. Questa operazione è necessaria solo se non si è mai eseguita la connessione alla sottoscrizione Azure da Visual Studio sulla workstation.
5.	In Esplora server verrà visualizzato un elenco di cluster HDInsight esistenti. Se non si dispone di cluster, è possibile eseguire il provisioning di un cluster usando il portale di gestione, Azure PowerShell o HDInsight SDK.  Per altre informazioni, vedere [Effettuare il provisioning di cluster Hadoop in HDInsight con opzioni personalizzate][hdinsight-provision].

	![HDInsight Tools for visual studio server explorer cluster list][5]
6.	Espandere un cluster HDInsight. Verranno visualizzati i **database Hive**, l'account di archiviazione predefinito, gli account di archiviazione collegati e il **log del servizio Hadoop**. È possibile espandere ulteriormente le entità. 

Dopo la connessione alla sottoscrizione Azure, è possibile eseguire le operazioni seguenti:

**Per connettersi al portale di gestione da Visual Studio**

- Da Esplora server espandere **Azure** e **HDInsight**, fare clic con il pulsante destro del mouse su un cluster HDInsight, quindi fare clic su **Gestisci cluster nel portale Azure**.

**Per porre domande e fornire commenti e suggerimenti da Visual Studio**

- Nel menu **STRUMENTI** fare clic su **HDInsight**, quindi su **Forum MSDN** per porre domande o **lasciare un commento**.

## Esplorare le risorse collegate 

Da Esplora server è possibile visualizzare l'account di archiviazione predefinito e qualsiasi account di archiviazione collegato. Quando si espande l'account di archiviazione predefinito, è possibile visualizzare i contenitori presenti. L'account di archiviazione predefinito e il contenitore predefinito sono entrambi contrassegnati. Per visualizzare un contenitore, è possibile anche fare clic su di esso con il pulsante destro del mouse.

![HDInsight Tools for visual studio server explorer cluster list][2]

## Eseguire query Hive
[Apache Hive][apache.hive] è un'infrastruttura di data warehouse basata su Hadoop che consente di fornire funzionalità di riepilogo, query e analisi di dati. HDInsight Tools per Visual Studio supporta l'esecuzione di query Hive da Visual Studio. Per altre informazioni su Hive, vedere [Usare Hive con HDInsight][hdinsight.hive].

Testare script Hive in un cluster HDInsight è un'operazione che può richiedere molto tempo, ad esempio alcuni minuti.  HDInsight Tools per Visual Studio consente di eseguire la convalida della sintassi dello script Hive in locale senza necessità di connettersi a un cluster attivo.

HDInsight Tools per Visual Studio consente inoltre di visualizzare i contenuti del processo Hive raccogliendo ed esponendo i log Yarn di alcuni processi Hive.

### Visualizzare la tabella Hive hivesampletable
I cluster HDInsight includono una tabella Hive di esempio denominata *hivesampletable*. Questa tabella verrà usata per mostrare come elencare tabelle Hive, visualizzare gli schemi di tabella ed elencare le righe nella tabella Hive.



**Per elencare tabelle Hive e visualizzare lo schema di tabella Hive**

1.	Da **Esplora server** espandere in sequenza **Azure**, **HDInsight**, un cluster, **Hive Databases**, **Default** e infine **hivesampletable** per visualizzare lo schema della tabella.
4.	Fare clic con il pulsante destro del mouse su **hivesampletable** e quindi scegliere **Visualizza le prime 100 righe** per elencare le righe. Questa operazione equivale ad eseguire la seguente query Hive mediante il driver ODBC di Hive:

		SELECT * FROM hivesampletable LIMIT 100

	È possibile personalizzare il numero delle righe. 
 
	![HDinsight Hive Visual Studio schema query][6]

### Creare tabelle Hive

Per creare una tabella Hive è possibile usare l'interfaccia utente grafica o query Hive. Per usare query Hive, vedere [Eseguire query Hive](#run.queries).

**Per creare una tabella Hive**

1. Da **Esplora server** espandere in sequenza **Azure**, **HDInsight Clusters**, un cluster HDInsight e **Hive Databases**, fare clic con il pulsante destro del mouse su **default**, quindi scegliere **Crea tabella**.
2. Configurare la tabella.
3. Fare clic su **Crea tabella** per inviare il processo per la creazione della nuova tabella Hive.

	![hdinsight visual studio tools create hive table][7]

### <a name="run.queries"></a>Convalidare ed eseguire query Hive
Esistono due modi per creare ed eseguire query Hive:

- Creare query ad hoc
- Creare un'applicazione Hive

**Per creare, convalidare ed eseguire query ad hoc**

1. Da **Esplora server** espandere **Azure**, quindi **HDInsight Clusters**.
2. Fare clic con il pulsante destro del mouse sul cluster in cui si desidera eseguire la query, quindi scegliere **Scrivi una query Hive**. 
3. Immettere le query Hive. Si noti che l'editor Hive supporta Intellisense.
4. (Facoltativo) Fare clic su **Validate Script** per verificare la presenza di eventuali errori di sintassi nello script.

	![hdinsight tools for Visual Studio local validation][10]

4. Fare clic su **Invia** o **Invia (Avanzato)**. L'invio avanzato consente di configurare i dati relativi a **Nome processo**, **Argomenti**, **Configurazioni aggiuntive** e **Directory stato** per lo script:

	![hdinsight hadoop hive query][9]

	Dopo l'invio, è possibile visualizzare una finestra di riepilogo **del processo Hive**.

	![hdinsight hadoop hive query][8]
5. Usare il pulsante **Aggiorna** per aggiornare lo stato del processo finché non risulta **Completato**.
6. Fare clic sui collegamenti presenti nella parte inferiore per visualizzare **query di processo**, **output di processo**, **log di processo** o **log Yarn**.



**Per creare o eseguire una soluzione Hive**

1. Scegliere **Nuovo** dal menu **FILE**, quindi fare clic su **Progetto**.
2. Selezionare **HDInsight** nel riquadro sinistro, selezionare **Applicazione Hive** nel riquadro centrale, immettere le proprietà e fare clic su **OK**.
3. In **Esplora soluzioni** fare doppio clic su **Script.hql** per aprirlo. 
4. Per convalidare lo script Hive, è possibile fare clic sul pulsante per la convalida dello script oppure fare clic con il pulsante destro del mouse sullo script nell'editor di Hive e quindi fare clic sul pulsante per la convalida dal menu di scelta rapida.

 
### Visualizzare processi Hive
È possibile visualizzare query di processo, output di processo, log di processo e log Yarn per i processi Hive.  Vedere la schermata precedente.

La versione più recente dello strumento consente di visualizzare i contenuti dei processi Hive raccogliendo ed esponendo i log Yarn. I log Yarn consentono di analizzare eventuali problemi di prestazioni. Per altre informazioni su come HDInsight raccoglie log YARN, vedere [Accedere ai log dell'applicazione HDInsight a livello di codice][hdinsight.access.application.logs].

**Per visualizzare processi Hive**

1. Da **Esplora server** espandere **Azure**, quindi **HDInsight**. 
2. Fare clic con il pulsante destro del mouse su un cluster HDInsight, quindi scegliere **Visualizza processi Hive**. Verrà visualizzato l'elenco di processi Hive eseguiti nel cluster. 
3. Fare clic su un processo incluso nell'elenco dei processi per selezionarlo, quindi usare la finestra **Hive Job Summary** per aprire **query di processo**, **output di processo**, **log di processo** o **log Yarn**.

## Passaggi successivi
In questo articolo è stato mostrato come connettersi a cluster HDInsight da Visual Studio ed eseguire query Hive. Per altre informazioni, vedere:

- [Usare Hadoop Hive in HDInsight][hdinsight.hive]
- [Introduzione all'uso di Hadoop in HDInsight][hdinsight.get.started]
- [Inviare processi Hadoop in HDInsight][hdinsight.submit.jobs]
- [Analizzare i dati di Twitter con Hadoop in HDInsight][hdinsight.analyze.twitter.data]


<!--Anchors-->
[Installare]: #installation
[Connettersi alla sottoscrizione Azure]: #connect-to-your-azure-subscription
[Esplorare le risorse collegate]: #navigate-the-linked-resources
[Eseguire query Hive]: #run-hive-queries
[Passaggi successivi]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png


<!--Link references-->
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight.introduction]: ../hdinsight-introduction/
[hdinsight.get.started]: ../hdinsight-get-started/
[hdinsight.hive]: ../hdinsight-use-hive/
[hdinsight.submit.jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight.analyze.twitter.data]: ../hdinsight-analyze-twitter-data/
[hdinsight.storm.visual.studio.tools]: ../hdinsight-storm-develop-csharp-visual-studio-topology/
[hdinsight.access.application.logs]: ../hdinsight-hadoop-access-yarn-app-logs/

[apache.hive]: http://hive.apache.org
<!--HONumber=45--> 
