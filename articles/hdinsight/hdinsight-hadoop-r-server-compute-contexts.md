<properties
   pageTitle="Opzioni del contesto di calcolo per R Server su HDInsight (anteprima) | Microsoft Azure"
   description="Informazioni sulle diverse opzioni del contesto di calcolo disponibili per gli utenti con R Server su HDInsight (anteprima)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="07/07/2016"
   ms.author="jeffstok"
/>

# Opzioni del contesto di calcolo per R Server su HDInsight (anteprima)

Microsoft R Server su Azure HDInsight (anteprima) fornisce le funzionalità più recenti per l'analisi basata su R. Usa i dati archiviati in HDFS in un contenitore nell'account di archiviazione[BLOB di Azure](../storage/storage-introduction.md "Archivio BLOB di Azure") o nel file system locale di Linux. Poiché R Server si basa su R open source, le applicazioni basate su R compilate dall'utente possono sfruttare gli oltre 8000 pacchetti R open source. Possono inoltre sfruttare le routine di [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "ScaleR di Revolution Analytics"), il pacchetto di analisi dei Big Data di Microsoft incluso in R Server.

Il nodo perimetrale di un cluster Premium offre una posizione pratica per connettersi al cluster ed eseguire gli script R. Con un nodo perimetrale è possibile eseguire funzioni distribuite parallelizzate di ScaleR nei core del server del nodo perimetrale. È anche possibile eseguire tali funzioni tra i nodi del cluster usando contesti di calcolo Hadoop MapReduce o Spark di ScaleR.

## Contesti di calcolo per un nodo perimetrale

In generale, uno script R eseguito in R Server nel nodo perimetrale viene eseguito all'interno dell'interprete R in tale nodo. L'eccezione è costituita dai passaggi che chiamano una funzione ScaleR. Le chiamate ScaleR vengono eseguite in un ambiente di calcolo determinato dall'impostazione del contesto di calcolo di ScaleR. Quando si esegue lo script R da un nodo perimetrale, i valori possibili del contesto di calcolo sono sequenziale locale ('local'), parallelo locale ('localpar'), MapReduce e Spark, come indicato di seguito:

| Contesto di calcolo | Come impostarlo | Contesto di esecuzione |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| Sequenziale locale | rxSetComputeContext('local') | Esecuzione sequenziale (non parallelizzata) nel server del nodo edge |
| Parallelo locale | rxSetComputeContext('localpar') | Esecuzione parallelizzata tra i core del server del nodo edge |
| Spark | RxSpark() | Esecuzione parallelizzata distribuita tramite Spark tra i nodi del cluster HDInsight |
| MapReduce | RxHadoopMR() | Esecuzione parallelizzata distribuita tramite MapReduce tra i nodi del cluster HDInsight |


Supponendo che si preferisca un'esecuzione parallelizzata per motivi di prestazioni, sono disponibili tre opzioni. L'opzione scelta dipende dalla natura dell'analisi e dalle dimensioni e posizione dei dati.

## Linee guida per la scelta di un contesto di calcolo

Attualmente non esiste una formula che indichi quale contesto di calcolo usare. Esistono tuttavia alcuni principi guida che consentono di effettuare la scelta appropriata o, almeno, consentono di limitare le scelte prima di eseguire un benchmark. Ecco alcuni dei principi guida:

1.	Il file system locale di Linux è più veloce rispetto ad HDFS.
2.	Le analisi ripetute risultano più veloci se i dati sono locali e in formato XDF.
3.	È preferibile eseguire lo streaming di piccole quantità di dati da un'origine dati di testo. Se la quantità di dati è maggiore, è necessario convertirli in formato XDF prima dell'analisi.
4.	Il sovraccarico dovuto alla copia o allo streaming dei dati nel nodo perimetrale per l'analisi diventa ingestibile per quantità di dati molto grandi.
5.	Spark è più veloce rispetto a Map Reduce per l'analisi in Hadoop.

Dati questi principi, ecco alcune regole generali per la selezione di un contesto di calcolo:

### Parallelo locale

- Se la quantità di dati da analizzare è limitata e non sono richieste analisi ripetute, eseguirne lo streaming direttamente in una routine di analisi e usare 'localpar'.
- Se la quantità di dati da analizzare è limitata o media e richiede analisi ripetute, copiare i dati nel file system locale, importarli in XDF e analizzarli con 'localpar'.

### Hadoop Spark

- Se la quantità di dati da analizzare è grande, importare i dati in HDFS in formato XDF, a meno che lo spazio di archiviazione non sia un problema, e analizzarli usando 'Spark'.

### Hadoop MapReduce

- Da usare solo se si riscontra un problema insormontabile riguardo all'utilizzo del contesto di calcolo Spark, poiché di norma risulta essere più lento.

## Guida in linea su rxSetComputeContext

Per altre informazioni ed esempi di contesti di calcolo di ScaleR, vedere la guida in linea di R sul metodo rxSetComputeContext, ad esempio:

    > ?rxSetComputeContext

È anche possibile vedere la guida all'elaborazione distribuita di ScaleR disponibile nella pagina relativa a [Server R](https://msdn.microsoft.com/library/mt674634.aspx "Server R in MSDN") in MSDN Library.


## Passaggi successivi

In questo articolo si è appreso come creare un nuovo cluster HDInsight che include R Server. Si sono anche apprese le nozioni di base per l'suo della console R da una sessione SSH. A questo punto è possibile leggere gli articoli seguenti per scoprire altre modalità di utilizzo di R Server in HDInsight:

- [Panoramica: R Server su HDInsight (anteprima)](hdinsight-hadoop-r-server-overview.md)
- [Introduzione all'uso di R Server su HDInsight (anteprima)](hdinsight-hadoop-r-server-get-started.md)
- [Aggiungere RStudio Server a HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Opzioni di Archiviazione di Azure per R Server su HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

<!---HONumber=AcomDC_0713_2016-->