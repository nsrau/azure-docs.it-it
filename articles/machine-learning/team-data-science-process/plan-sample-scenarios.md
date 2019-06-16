---
title: Identificare scenari per Azure Machine Learning - Processo di data science per i team
description: Selezionare gli scenari appropriati per eseguire analisi predittive avanzate con il Processo di analisi scientifica dei dati per i team.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6838f4db240a0712eece7a97bc2cfe99efb87215
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61429758"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scenari per l'analisi avanzata in Azure Machine Learning
Questo articolo descrive le varie origini dati di esempio e gli scenari di destinazione che possono essere gestiti con il [Processo di analisi scientifica dei dati per i team (TDSP)](overview.md). Il TDSP offre un approccio sistematico per consentire ai team di collaborare sulla compilazione di applicazioni intelligenti. Gli scenari presentati illustrano le opzioni disponibili nel flusso di lavoro dell'elaborazione dei dati basato su caratteristiche dei dati, posizioni delle origini e repository di destinazione in Azure.

L’ **albero delle decisioni** per la scelta degli scenari di esempio appropriati per i dati dell’utente e l'obiettivo sono presentati nella sezione precedente.

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

Ciascuna delle sezioni seguenti presenta uno scenario di esempio. Per ogni scenario vengono elencati un flusso di dati scientifici o di analisi avanzata possibile e le risorse di Azure di supporto.

> [!NOTE]
> **Per tutti gli scenari seguenti, è necessario:**
> <br/>
> 
> * [Creare un account di archiviazione](../../storage/common/storage-quickstart-create-account.md)
>   <br/>
> * [Creare un'area di lavoro di Machine Learning di Azure](../studio/create-workspace.md)
> 
> 

## <a name="smalllocal"></a>Scenario \#1: Set di dati tabulari medio-piccolo in un file locale
![File locali medio-piccoli][1]

#### <a name="additional-azure-resources-none"></a>Risorse di Azure aggiuntive: Nessuna
1. Accedere a [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Caricare un set di dati.
1. Compilare un flusso di esperimento di Azure Machine Learning iniziando con il set di dati caricato.

## <a name="smalllocalprocess"></a>Scenario \#2: Set di dati medio-piccolo in un file locale che richiede elaborazione
![File locali medio piccoli con elaborazione][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Risorse di Azure aggiuntive: macchina virtuale di Azure (server IPython Notebook)
1. Creare una macchina virtuale di Azure Virtual Machine che esegue IPython Notebook.
1. Caricare i dati in un contenitore di archiviazione di Azure.
1. Pre-elaborare e pulire i dati in IPython Notebook, accedere ai dati dal contenitore di archiviazione di Azure.
1. Trasformare i dati in un formato tabulare pulito.
1. Salvare i dati trasformati in BLOB di Azure.
1. Accedere a [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leggere i dati dai BLOB di Azure usando il modulo [Import Data][import-data] (Importa dati).
1. Compilare un flusso di esperimento di Azure Machine Learning iniziando con il set di dati inserito.

## <a name="largelocal"></a>Scenario \#3: Set di dati di grandi dimensioni in file locali, con BLOB di Azure come destinazione
![File locali di grandi dimensioni][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Risorse di Azure aggiuntive: macchina virtuale di Azure (server IPython Notebook)
1. Creare una macchina virtuale di Azure Virtual Machine che esegue IPython Notebook.
1. Caricare i dati in un contenitore di archiviazione di Azure.
1. Pre-elaborare e pulire i dati in IPython Notebook, accedere ai dati dai BLOB di Azure.
1. Trasformare i dati in un formato tabulare pulito, se necessario.
1. Esaminare i dati e creare le funzionalità necessarie.
1. Estrarre un campione di dati medio-piccolo.
1. Salvare i dati campionati in BLOB di Azure.
1. Accedere a [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leggere i dati dai BLOB di Azure usando il modulo [Import Data][import-data] (Importa dati).
1. Compilare un flusso di esperimento di Azure Machine Learning iniziando con il set di dati inserito.

## <a name="smalllocaltodb"></a>Scenario \#4: Set di dati medio-piccolo in un file locale, con SQL Server in una macchina virtuale di Azure come destinazione
![File locali medio-piccoli al database SQL in Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Risorse di Azure aggiuntive: macchina virtuale di Azure (SQL Server/server IPython Notebook)
1. Creare una macchina virtuale di Azure Virtual Machine che esegue SQL Server e IPython Notebook.
1. Caricare i dati in un contenitore di archiviazione di Azure.
1. Pre-elaborare e pulire i dati nel contenitore di archiviazione di Azure usando IPython Notebook.
1. Trasformare i dati in un formato tabulare pulito, se necessario.
1. Salvare i dati in file nella macchina virtuale locale (IPython Notebook è in esecuzione nella VM, le unità locali fanno riferimento alle unità della VM).
1. Caricare i dati nel database SQL Server in esecuzione in una VM di Azure.
   
   Opzione \#1: Uso di SQL Server Management Studio
   
   * Accedere alla macchina virtuale di SQL Server
   * Eseguire SQL Server Management Studio
   * Creare tabelle di database e di destinazione
   * Utilizzare uno dei metodi di importazione globale per caricare i dati dai file della macchina virtuale locale.
   
   Opzione \#2: Uso di IPython Notebook, non consigliabile per i set di dati di medie e grandi dimensioni
   
   <!-- -->    
   * Utilizzare la stringa di connessione ODBC per accedere a SQL Server sulla macchina virtuale.
   * Creare tabelle di database e di destinazione
   * Utilizzare uno dei metodi di importazione globale per caricare i dati dai file della macchina virtuale locale.
1. Esaminare i dati e creare le funzionalità necessarie. Si noti che le funzionalità non devono essere materializzare nelle tabelle del database. Notare solo la query necessaria per crearle.
1. Scegliere le dimensioni del campione di dati, se necessario e/o lo si desidera.
1. Accedere a [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leggere i dati direttamente da SQL Server usando il modulo [Import Data][import-data] (Importa dati). Incollare la query richiesta che estrae i campi, crea le funzionalità ed esegue il campionamento dei dati, se necessario, direttamente nella query di [Import Data][import-data] (Importa dati).
1. Compilare un flusso di esperimento di Azure Machine Learning iniziando con il set di dati inserito.

## <a name="largelocaltodb"></a>Scenario \#5: Set di dati di grandi dimensioni in file locali, con SQL Server in una macchina virtuale di Azure come destinazione
![File locali di grandi dimensioni al database SQL in Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Risorse di Azure aggiuntive: macchina virtuale di Azure (SQL Server/server IPython Notebook)
1. Creare una macchina virtuale di Azure che esegue SQL Server e il server IPython Notebook.
1. Caricare i dati in un contenitore di archiviazione di Azure.
1. (Facoltativo) Pre-elaborare e pulire i dati.
   
   a.  Pre-elaborare e pulire i dati in IPython Notebook, accedendo ai dati da Azure
   
       blobs.
   
   b.  Trasformare i dati in un formato tabulare pulito, se necessario.
   
   c.  Salvare i dati in file nella macchina virtuale locale (IPython Notebook è in esecuzione nella VM, le unità locali fanno riferimento alle unità della VM).
1. Caricare i dati nel database SQL Server in esecuzione in una VM di Azure.
   
   a.  Accedere alla macchina virtuale di SQL Server.
   
   b.  Se i dati non sono ancora stati salvati, scaricare i file di dati da Azure
   
       storage container to local-VM folder.
   
   c.  Eseguire SQL Server Management Studio
   
   d.  Creare tabelle di database e di destinazione
   
   e.  Usare uno dei metodi di importazione in blocco per caricare i dati.
   
   f.  Se sono richiesti join di tabella, creare gli indici per velocizzarli.
   
   > [!NOTE]
   > Per caricare più velocemente i dati di grandi dimensioni, si consiglia di creare tabelle partizionate e importare in blocco i dati in parallelo. Per altre informazioni, vedere l'articolo relativo all' [importazione parallela di dati in tabelle partizionale di SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Esaminare i dati e creare le funzionalità necessarie. Si noti che le funzionalità non devono essere materializzare nelle tabelle del database. Notare solo la query necessaria per crearle.
1. Scegliere le dimensioni del campione di dati, se necessario e/o lo si desidera.
1. Accedere a [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leggere i dati direttamente da SQL Server usando il modulo [Import Data][import-data] (Importa dati). Incollare la query richiesta che estrae i campi, crea le funzionalità ed esegue il campionamento dei dati, se necessario, direttamente nella query di [Import Data][import-data] (Importa dati).
1. Semplificare il flusso di esperimento di Azure Machine Learning iniziando con il set di dati caricato

## <a name="largedbtodb"></a>Scenario \#6: Set di dati di grandi dimensioni in un database di SQL Server in locale, come destinazione di SQL Server in una macchina virtuale di Azure
![Database SQL locale di grandi dimensioni  al database SQL locale in Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Risorse di Azure aggiuntive: macchina virtuale di Azure (SQL Server/server IPython Notebook)
1. Creare una macchina virtuale di Azure che esegue SQL Server e il server IPython Notebook.
1. Usare uno dei metodi di esportazione dei dati per esportare i dati da SQL Server in file di dump.
   
   > [!NOTE]
   > Se si decide di spostare tutti i dati dal database on premises, un metodo alternativo (più rapido) per spostare il database completo all'istanza di SQL Server in Azure. Ignorare i passaggi per esportare i dati, creare il database e caricare/importare i dati nel database di destinazione e seguire il metodo alternativo.
   > 
   > 
1. Caricare file di dump nel contenitore di archiviazione di Azure.
1. Caricare i dati in un database di SQL Server in esecuzione in una macchina virtuale di Azure.
   
   a.  Accedere alla macchina virtuale di SQL Server.
   
   b.  Scaricare i file di dati dal contenitore di archiviazione di Azure nella cartella della VM locale.
   
   c.  Eseguire SQL Server Management Studio
   
   d.  Creare tabelle di database e di destinazione
   
   e.  Usare uno dei metodi di importazione in blocco per caricare i dati.
   
   f.  Se sono richiesti join di tabella, creare gli indici per velocizzarli.
   
   > [!NOTE]
   > Per caricare più velocemente dati di grandi dimensioni, creare tabelle partizionate e importare in blocco i dati in parallelo. Per altre informazioni, vedere l'articolo relativo all' [importazione parallela di dati in tabelle partizionale di SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Esaminare i dati e creare le funzionalità necessarie. Si noti che le funzionalità non devono essere materializzare nelle tabelle del database. Notare solo la query necessaria per crearle.
1. Scegliere le dimensioni del campione di dati, se necessario e/o lo si desidera.
1. Accedere a [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leggere i dati direttamente da SQL Server usando il modulo [Import Data][import-data] (Importa dati). Incollare la query richiesta che estrae i campi, crea le funzionalità ed esegue il campionamento dei dati, se necessario, direttamente nella query di [Import Data][import-data] (Importa dati).
1. Semplificare il flusso di esperimento di Azure Machine Learning iniziando con il set di dati caricato.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Metodo alternativo per copiare un intero database da un server SQL locale al database SQL di Azure
![Scollegare il database locale e collegarlo al database SQL in Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Risorse di Azure aggiuntive: macchina virtuale di Azure (SQL Server/server IPython Notebook)
Per replicare l'intero database SQL Server nella macchina virtuale di SQL Server, è necessario copiare un database da un percorso/server a un altro, supponendo che il database possa essere portato temporaneamente offline. Eseguire questa operazione in SQL Server Management Studio, oppure utilizzando i comandi Transact-SQL equivalenti.

1. Scollegare il database nel percorso di origine. Per altre informazioni, vedere [Scollegamento di un database](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. In Esplora risorse o nella finestra del prompt dei comandi di Windows  copiare il file o i file del database scollegato nel percorso di destinazione della macchina virtuale di SQL Server in Azure.
1. Collegare i file copiati all'istanza di SQL Server di destinazione. Per altre informazioni, vedere [Collegare un database](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Spostamento di un database tramite la funzionalità di scollegamento e collegamento (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="largedbtohive"></a>Scenario \#7: Big Data nei file locali, con il database Hive in cluster Azure HDInsight Hadoop come destinazione
![Big Data nell'Hive di destinazione locale][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Risorse di Azure aggiuntive: cluster Azure HDInsight Hadoop e macchina virtuale di Azure (server IPython Notebook)
1. Creare una macchina virtuale di Azure che esegue il server IPython Notebook.
1. Creare un cluster Hadoop di Azure HDInsight
1. (Facoltativo) Pre-elaborare e pulire i dati.
   
   a.  Pre-elaborare e pulire i dati in IPython Notebook, accedendo ai dati da Azure
   
       blobs.
   
   b.  Trasformare i dati in un formato tabulare pulito, se necessario.
   
   c.  Salvare i dati in file nella macchina virtuale locale (IPython Notebook è in esecuzione nella VM, le unità locali fanno riferimento alle unità della VM).
1. Caricare i dati nel contenitore predefinito del cluster Hadoop selezionato nel passaggio 2.
1. Caricare i dati nel database di Hive del cluster Hadoop di HDInsight di Azure.
   
   a.  Accedere al nodo head del cluster Hadoop
   
   b.  Aprire la riga di comando di Hadoop.
   
   c.  Immettere la directory radice di Hive con il comando `cd %hive_home%\bin` nella riga di comando di Hadoop.
   
   d.  Eseguire le query Hive per creare tabelle e database e caricare i dati dall'archivio BLOB nelle tabelle Hive.
   
   > [!NOTE]
   > Se i dati sono di grandi dimensioni, gli utenti possono creare la tabella Hive con partizioni. Gli utenti possono quindi usare un ciclo `for` nella riga di comando di Hadoop nel nodo head per caricare dati nella tabella Hive partizionata in base alla partizione.
   > 
   > 
1. Esaminare i dati e creare le funzionalità necessarie nella riga di comando di Hadoop. Si noti che le funzionalità non devono essere materializzare nelle tabelle del database. Notare solo la query necessaria per crearle.
   
   a.  Accedere al nodo head del cluster Hadoop
   
   b.  Aprire la riga di comando di Hadoop.
   
   c.  Immettere la directory radice di Hive con il comando `cd %hive_home%\bin` nella riga di comando di Hadoop.
   
   d.  Eseguire le query Hive nella riga di comando di Hadoop nel nodo head del cluster Hadoop per esplorare i dati e creare le funzionalità necessarie.
1. Se necessario e/o lo si desidera, campionare i dati in modo da adattarli a Azure Machine Learning Studio.
1. Accedere a [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leggere i dati direttamente da `Hive Queries` usando il modulo [Import Data][import-data] (Importa dati). Incollare la query richiesta che estrae i campi, crea le funzionalità ed esegue il campionamento dei dati, se necessario, direttamente nella query di [Import Data][import-data] (Importa dati).
1. Semplificare il flusso di esperimento di Azure Machine Learning iniziando con il set di dati caricato.

## <a name="decisiontree"></a>Albero delle decisioni per la scelta degli scenari
- - -
Nel diagramma seguente sono riepilogati gli scenari descritti in precedenza e il processo di analisi avanzata e le scelte di tecnologia effettuate che conducono l’utente a ciascuno degli scenari elencati. Tenere presente che l'elaborazione dei dati, l'esplorazione, la progettazione delle funzionalità e il campionamento potrebbero avvenire in uno o più metodi/ambienti, ad esempio nell'ambiente di origine, intermedio e/o di destinazione, e potrebbero procedere in modo iterativo, se necessario. Il diagramma è solo un'illustrazione di alcuni dei possibili flussi e non fornisce un'enumerazione completa.

![Scenari della procedura dettagliata del processo di analisi scientifica][8]

### <a name="advanced-analytics-in-action-examples"></a>Analisi avanzata in esempi di azioni
Per procedure dettagliate end-to-end di Azure Machine Learning che usano ADAPT tramite set di dati pubblici, vedere:

* [Processo di analisi scientifica dei dati per i team in azione: uso di SQL Sever](sql-walkthrough.md).
* [Processo di analisi scientifica dei dati per i team in azione: uso dei cluster Hadoop di HDInsight](hive-walkthrough.md).

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
