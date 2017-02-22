---
title: Informazioni sulle macchine virtuali per l&quot;analisi scientifica dei dati | Documentazione Microsoft
description: "Informazioni su scenari chiave, funzionalità e installazione di macchine virtuali per l&quot;analisi scientifica dei dati, un ambiente e un toolkit pronti per l&quot;analisi."
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l&quot;analisi scientifica dei dati, strumenti per l&quot;analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 6c664a055e577881d4fcccd5b0ba4047d88aa9ef
ms.openlocfilehash: 64b413451c6ce47cc3aa14322b2aa0342e1e3ffe


---
# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>Introduzione alla macchina virtuale per l'analisi scientifica dei dati basata su cloud per Linux e Windows
La macchina virtuale per l'analisi scientifica dei dati è un'immagine VM personalizzata nel cloud di Microsoft Azure creata in modo specifico per l'analisi scientifica dei dati. Include diversi strumenti comuni per l'analisi scientifica dei dati e altri strumenti preinstallati e preconfigurati per implementare rapidamente la creazione di applicazioni intelligenti per l'analisi avanzata. È disponibile in Windows Server 2012 o in versioni Linux basate su CentOS OpenLogic 7.2. 

Questo argomento illustra le operazioni possibili con la VM per l'analisi scientifica dei dati, descrive alcuni degli scenari chiave per l'uso della VM, indica in modo dettagliato le funzionalità principali disponibili nelle versioni per Windows e Linux e include le istruzioni su come iniziare a usarle.

## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>Usi della macchina virtuale per l'analisi scientifica dei dati
L'obiettivo della macchina virtuale per l'analisi scientifica dei dati è offrire ai professionisti dei dati, in tutti i livelli e i ruoli, un ambiente di analisi scientifica dei dati privo di problemi. La VM consente di risparmiare una notevole quantità di tempo che sarebbe necessario se si volesse implementare un ambiente analogo in autonomia. Questa soluzione consente invece di avviare immediatamente il progetto di analisi scientifica dei dati in una nuova istanza di VM. 

La VM per l'analisi scientifica dei dati è progettata e configurata per l'uso in un'ampia gamma di scenari di utilizzo. È possibile aumentare e ridurre le prestazioni dell'ambiente a seconda delle esigenze del progetto. Può essere usato il linguaggio preferito per programmare le attività di analisi scientifica dei dati. È inoltre possibile installare altri strumenti e personalizzare il sistema in base alle esigenze specifiche.

## <a name="key-scenarios"></a>Scenari chiave
Questa sezione suggerisce alcuni scenari chiave per i quali può essere distribuita la VM per l'analisi scientifica dei dati.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Desktop di analisi preconfigurato nel cloud
La VM per l'analisi scientifica dei dati offre una configurazione di base per i team di analisi scientifica dei dati che vogliono sostituire i desktop locali con un desktop cloud gestito. Questa configurazione di base garantisce che tutti gli scienziati dei dati presenti in un team abbiano una configurazione coerente mediante la quale verificare gli esperimenti e promuovere la collaborazione. Anche i costi diminuiscono, grazie alla riduzione del carico lavorativo per gli amministratori di sistema e del tempo richiesto per valutare, installare e gestire i vari pacchetti di software necessari per eseguire analisi avanzate.  

### <a name="data-science-training-and-education"></a>Preparazione e formazione sull'analisi scientifica dei dati
Gli istruttori e i formatori aziendali che insegnano l'analisi scientifica dei dati in genere forniscono un'immagine di macchina virtuale per garantire che gli studenti abbiano una configurazione coerente e che gli esempi abbiano un comportamento prevedibile. La VM per l'analisi scientifica dei dati consente di creare un ambiente su richiesta con una configurazione coerente che semplifica i problemi relativi a incompatibilità e supporto. Esistono vantaggi sostanziali per i casi in cui tali ambienti devono essere compilati di frequente, in particolare per i corsi di formazione più brevi.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacità elastica su richiesta per progetti su larga scala
Gli hackathon e i concorsi di analisi scientifica dei dati o la modellazione e l'esplorazione di dati su larga scala richiedono una maggiore capacità hardware, in genere per brevi periodi di tempo. La VM per l'analisi scientifica dei dati consente di replicare rapidamente e su richiesta l'ambiente di analisi scientifica dei dati su server con maggiore capacità, che consentono di eseguire esperimenti che richiedono l'esecuzione di risorse di calcolo con potenza elevata.

### <a name="short-term-experimentation-and-evaluation"></a>Valutazione e sperimentazione a breve termine
La VM per l'analisi scientifica dei dati può essere usata per valutare o imparare a usare strumenti quali Microsoft R Server, SQL Server, strumenti di Visual Studio, Jupyter, toolkit di deep learning/ML e i nuovi strumenti popolari nella comunità con il minimo sforzo di installazione. La VM per l'analisi scientifica dei dati può essere configurata rapidamente, pertanto può essere usata anche in altri scenari di utilizzo a breve termine, ad esempio nella replica di esperimenti pubblicati, nell'esecuzione di demo e di procedure dettagliate in sessioni online, nonché in esercitazioni in conferenza.

## <a name="whats-included-in-the-data-science-vm"></a>Funzionalità incluse nella VM per l'analisi scientifica dei dati
La macchina virtuale per l'analisi scientifica dei dati ha già installati e configurati diversi strumenti comuni per l'analisi scientifica dei dati. Include inoltre strumenti che semplificano l'uso di vari prodotti di Azure per l'analisi e per i dati. È possibile esplorare e creare modelli predittivi in set di dati su larga scala usando Microsoft R Server o SQL Server 2016. Sono inclusi anche una serie di altri strumenti della community open source e di Microsoft, nonché esempi di codice e blocchi appunti. La tabella seguente indica in modo dettagliato e confronta i componenti principali inclusi nelle edizioni per Windows e Linux della macchina virtuale per l'analisi scientifica dei dati.

| **Edizione per Windows** | **Edizione per Linux** |
| -- | --|
| Microsoft R Open con i pacchetti più diffusi pre-installati | Microsoft R Open con i pacchetti più diffusi pre-installati |
| Microsoft R Server Developer Edition con gli algoritmi di MicrosoftML e la messa in funzione di Microsoft R  |Microsoft R Server Developer Edition |
| Anaconda Python 2.7, 3.5 |Anaconda Python 2.7, 3.5 con i pacchetti più diffusi pre-installati|
| JuliaPro con i pacchetti più diffusi pre-installati | JuliaPro con i pacchetti più diffusi pre-installati |
| Server Notebook Jupyter (R, Python, Julia) |JupyterHub: notebook Jupyter multiutente (R, Python, Julia, PySpark) |
| SQL Server 2016 SP1 Developer Edition: analisi database scalabile con R Services |PostgreSQL, SQuirreL SQL (strumento di database), driver di SQL Server e riga di comando (bcp, sqlcmd) |
|- Visual Studio Community Edition 2015 (IDE) </br> - Azure HDInsight (Hadoop), Data Lake, SQL Server Data Tools </br> - Strumenti di Node.js, Python e R per Visual Studio (RTVS 0.5) </br>- R Studio Desktop|IDE ed editor </br> - Eclipse con plug-in di Azure Toolkit </br> - Emacs (con ESS, auctex) gedit </br> - IntelliJ IDEA</br> - PyCharm</br> - Atom</br> - Visual Studio Code|
| Power BI Desktop |-- |
| Strumenti di Machine Learning </br> - Integrazione con Azure Machine Learning </br> - Deep learning/AI di Microsoft Cognitive Toolkit (CNTK 2.0) </br> - Xgboost (strumento ML comune nei concorsi di analisi scientifica dei dati) </br> - Vowpal Wabbit (apprendimento rapido online) </br> - Rattle (strumento visivo di analisi e dati di avvio rapido) </br> - Mxnet (deep learning/AI) </br> - TensorFlow  |Strumenti di Machine Learning </br> - Integrazioni con Azure Machine Learning </br> - CNTK (deep learning/AI) </br> - Xgboost (strumento ML comune nei concorsi di analisi scientifica dei dati) </br> - Vowpal Wabbit (apprendimento rapido online) </br> - Rattle (strumento visivo di analisi e dati di avvio rapido) </br> - Mxnet (deep learning/AI)|
| SDK per accedere alla suite di servizi di Cortana Intelligence e di Azure |SDK per accedere alla suite di servizi di Cortana Intelligence e di Azure |
| Strumenti per lo spostamento dei dati e la gestione delle risorse di Azure e Big Data: Azure Storage Explorer, interfaccia della riga di comando, PowerShell, AdlCopy (Azure Data Lake), AzCopy, dtui (per DocumentDB), Gateway di gestione dati di Microsoft |Strumenti per lo spostamento dei dati e la gestione delle risorse di Azure e Big Data: Azure Storage Explorer, interfaccia della riga di comando |
| Git, plug-in di Visual Studio Team Services |Git |
| Porta Windows delle più comuni utilità della riga di comando Linux/Unix accessibile mediante il prompt dei comandi o GitBash |-- |
| Weka | Weka |
| Drill | Drill |
| --| Spark locale |

## <a name="how-to-get-started-with-the-windows-data-science-vm"></a>Introduzione alla VM Windows per l'analisi scientifica dei dati
* Creare un'istanza della VM in Windows, passando a [questa pagina](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) e selezionando il pulsante verde **Crea macchina virtuale**.
* Accedere alla VM dal desktop remoto usando le credenziali specificate durante la creazione della VM.
* Per individuare e avviare gli strumenti disponibili, fare clic sul menu **Start**.

## <a name="get-started-with-the-linux-data-science-vm"></a>Introduzione alla VM Linux per l'analisi scientifica dei dati
* Creare un'istanza della VM in Linux (basata su CentOS OpenLogic) passando a [questa pagina](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) e selezionando il pulsante **Crea macchina virtuale**.
* Accedere alla VM da un client SSH, ad esempio Putty o SSH Command, usando le credenziali specificate durante la creazione della VM.
* Nel prompt della shell immettere dsvm-more-info.
* Per un desktop grafico scaricare [qui](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) il client X2Go per la piattaforma client e seguire le istruzioni riportate nel documento relativo alla VM Linux per l'analisi scientifica dei dati [Effettuare il provisioning di una macchina virtuale Linux per l'analisi scientifica dei dati](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client).

## <a name="next-steps"></a>Passaggi successivi
### <a name="for-the-windows-data-science-vm"></a>Per la VM Windows per l'analisi scientifica dei dati
* Per altre informazioni sull'esecuzione degli strumenti specifici disponibili nella versione per Windows, vedere [Eseguire il provisioning di una macchina virtuale per l'analisi scientifica dei dati di Microsoft](machine-learning-data-science-provision-vm.md).
* Per altre informazioni sull'esecuzione di varie attività necessarie per il progetto di analisi scientifica dei dati sulla VM Windows, vedere [Dieci cose da fare con la macchina virtuale per l'analisi scientifica dei dati](machine-learning-data-science-vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>Per la VM Linux per l'analisi scientifica dei dati
* Per altre informazioni sull'esecuzione degli strumenti specifici disponibili nella versione per Linux, vedere [Effettuare il provisioning di una macchina virtuale Linux per l'analisi scientifica dei dati](machine-learning-data-science-linux-dsvm-intro.md).
* Per una procedura dettagliata che illustra come eseguire diverse attività comuni di analisi scientifica dei dati con la VM Linux, vedere [Analisi scientifica dei dati in una macchina virtuale Linux per l'analisi scientifica dei dati](machine-learning-data-science-linux-dsvm-walkthrough.md).




<!--HONumber=Feb17_HO1-->


