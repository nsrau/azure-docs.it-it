<properties
   pageTitle="Opzioni del contesto di calcolo per R Server su HDInsight (anteprima) | Azure"
   description="Informazioni sulle varie opzioni di contesto di calcolo disponibili per gli utenti con Server R su HDInsight (anteprima)"
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
   ms.date="03/28/2016"
   ms.author="jeffstok"
/>

#Opzioni del contesto di calcolo per R Server su HDInsight (anteprima)

Server R su HDInsight (anteprima) offre le funzionalità più recenti per l'analisi basata su R usando i dati archiviati in HDFS in un contenitore nell'account di archiviazione [BLOB di Azure](../storage/storage-introduction.md "Archivio BLOB di Azure") o nel file system locale di Linux. Dato che Server R si basa su R open source, le applicazioni basate su R che vengono compilate possono sfruttare gli oltre 8000 pacchetti R open source disponibili, nonché le routine in [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "ScaleR di Revolution Analytics"), il pacchetto di analisi dei Big Data di Microsoft incluso in Server R. Il nodo edge dei cluster Premium offre una comoda destinazione per la connessione al cluster e l'esecuzione degli script R. Con un nodo edge è possibile eseguire funzioni distribuite parallelizzate di ScaleR nei core del server del nodo edge o tra i nodi del cluster tramite l'uso di contesti di calcolo Hadoop MapReduce o Spark di ScaleR.

## Contesti di calcolo per un nodo edge

In generale, uno script R eseguito nel nodo edge in Server R viene eseguito all'interno dell'interprete R su tale nodo, ad eccezione dei passaggi che chiamano una funzione ScaleR. Le chiamate a ScaleR verranno eseguite in un ambiente di calcolo determinato dall'impostazione del contesto di calcolo di ScaleR. I valori possibili del contesto di calcolo quando si esegue lo script R da un nodo edge sono locale sequenziale ('local'), locale parallelo ('localpar'), MapReduce e Spark, come illustrato nella tabella seguente:

| Contesto di calcolo | Come impostarlo | Contesto di esecuzione |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| Locale sequenziale | rxSetComputeContext('local') | Esecuzione sequenziale (non parallelizzata) nel server del nodo edge |
| Locale parallelo | rxSetComputeContext('localpar') | Esecuzione parallelizzata tra i core del server del nodo edge |
| Spark | RxSpark() | Esecuzione parallelizzata distribuita tramite Spark tra i nodi del cluster HDInsight |
| MapReduce | RxHadoopMR() | Esecuzione parallelizzata distribuita tramite MapReduce tra i nodi del cluster HDInsight |


Supponendo che si preferisca un'esecuzione parallelizzata per motivi di prestazioni, sono disponibili tre opzioni. La scelta dipenderà dalla natura dell'analisi e dalle dimensioni e posizione dei dati.

## Scelta di un contesto di calcolo

Attualmente non sono disponibili formule per la determinazione del contesto di calcolo da usare, ma esistono alcuni principi guida che possono indirizzare verso la scelta giusta o almeno restringere il campo prima di effettuare un benchmark, se è necessaria una soluzione ottimale. Ecco alcuni dei principi guida:

1.	Il file system locale di Linux è più veloce rispetto a HDFS.
2.	Le analisi ripetute risultano più veloci se i dati sono in locale e in formato XDF. 
3.	Eseguire lo streaming da un'origine dati di testo per i dati di piccole dimensioni o convertirli in formato XDF prima dell'analisi. 
4.	Il sovraccarico dovuto alla copia o allo streaming dei dati nel nodo edge per l'analisi diventa insostenibile per i dati di dimensioni molto grandi. 
5.	Spark è più veloce di MapReduce per l'analisi in Hadoop, ma se i dati raggiungono dimensioni molto grandi non sono più compatibili con la memoria distribuita.

Dati questi principi, ecco alcune regole generali per la selezione di un contesto di calcolo:

### Locale parallelo

- Se i dati da analizzare sono di piccole dimensioni e non richiedono analisi ripetute, eseguirne lo streaming direttamente in una routine di analisi e usare 'localpar'. 
- Se i dati da analizzare sono di dimensioni modeste oppure di piccole dimensioni e richiedono analisi ripetute, copiarli nel file system locale, importarli in XDF e analizzarli usando 'localpar'. 

### Hadoop Spark

- Se i dati da analizzare sono di grandi dimensioni, importarli in HDFS in formato XDF, a meno che lo spazio di archiviazione non sia un problema, e analizzarli usando 'Spark'. 

### Hadoop MapReduce

- Se i dati da analizzare sono di dimensioni molto grandi e le prestazioni di Spark iniziano a peggiorare, provare a eseguire l'analisi usando 'MapReduce'.

## Guida in linea su rxSetComputeContext

Per altre informazioni ed esempi di contesti di calcolo di ScaleR, vedere la guida in linea di R sul metodo rxSetComputeContext, ad esempio:

    > ?rxSetComputeContext 

Oppure vedere la guida all'elaborazione distribuita di ScaleR disponibile nella pagina relativa a [Server R](https://msdn.microsoft.com/library/mt674634.aspx "Server R in MSDN") in MSDN Library.


## Passaggi successivi

Dopo aver visto come creare un nuovo cluster HDInsight che include Server R e le nozioni di base sull'uso della console di R da una sessione SSH, usare le risorse seguenti per trovare altre modalità di utilizzo di Server R in HDInsight.

- [Articolo contenente una panoramica su Server R in Hadoop](hdinsight-hadoop-r-server-overview.md).
- [Articolo introduttivo relativo a Server R su Hadoop](hdinsight-hadoop-r-server-get-started.md).
- [Articolo relativo all'installazione di RStudio Server in HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md).
- [Opzioni di Archiviazione di Azure per R Server in HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

<!---HONumber=AcomDC_0420_2016-->