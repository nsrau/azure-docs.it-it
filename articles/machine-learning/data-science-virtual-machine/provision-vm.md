---
title: Effettuare il provisioning di una macchina virtuale data science Windows di Azure | Microsoft Docs
description: Configurare e creare una macchina virtuale di Data Science in Azure per l'analisi dei dati e l'apprendimento automatico.
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: d71d8e44d0327515ed302c5c902ce87587e36c7d
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>Effettuare il provisioning di una macchina virtuale data science Windows di Azure
La macchina virtuale di Data Science di Microsoft è un'immagine di macchina virtuale (VM) di Windows Azure pre-installata e configurata con diversi strumenti diffusi comunemente usati per l'analisi dei dati e l'apprendimento automatico. Gli strumenti inclusi sono:

* [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning-services/) Workbench
* [Microsoft ML Server](https://docs.microsoft.com/machine-learning-server/index) Developer Edition
* Distribuzione Anaconda Python
* Notebook Jupyter (con kernel R, Python, PySpark)
* Visual Studio Community Edition
* Power BI Desktop
* SQL Server 2017 Developer Edition
* Istanza di Spark autonoma per sviluppo e test locali
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Strumenti di apprendimento automatico e analisi dei dati
  * Framework di apprendimento avanzato: nella VM è inclusa un'ampia gamma di framework di intelligenza artificiale, tra cui [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet, Keras.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): sistema di apprendimento automatico rapido che supporta tecniche come hash, allreduce, reduction, learning2search, nonché apprendimento online, attivo e interattivo.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): strumento che consente un'implementazione dell'albero con boosting rapida e accurata.
  * [Rattle](http://rattle.togaware.com/) (R Analytical Tool To Learn Easily): strumento che rende semplice la fase introduttiva all'analisi dei dati e al Machine Learning in R, grazie a funzionalità di esplorazione e modellazione dei dati basate su GUI con generazione automatica di codice R.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/): software di apprendimento automatico e data mining visivo in Java.
  * [Apache Drill](https://drill.apache.org/): motore di query SQL senza schema per Hadoop, NoSQL e Cloud Storage.  Supporta le interfacce ODBC e JDBC per consentire l'esecuzione di query in NoSQL e in file di strumenti di Business Intelligence standard come Power BI, Excel e Tableau.
* Librerie in R e Python da usare in Azure Machine Learning e altri servizi di Azure
* Git, che include Git Bash, per lavorare con i repository di codice sorgente fra cui GitHub e Visual Studio Team Services
* Porte Windows di diverse comuni utilità della riga di comando di Linux (tra cui awk, sed, perl, grep, find, wget, curl e così via) accessibili dal prompt dei comandi. 

L'esecuzione dell'analisi scientifica dei dati comporta l'iterazione di una sequenza di attività quali:

1. Ricerca, caricamento e pre-elaborazione dei dati
2. Compilazione e test di modelli
3. Distribuzione dei modelli per l'uso in applicazioni intelligenti

I data scientist usano un'ampia gamma di strumenti per completare queste attività. Trovare le versioni software appropriate e quindi scaricarle e installarle può essere un'operazione molto dispersiva in termini di tempo. La macchina virtuale per l'analisi scientifica dei dati di Microsoft facilita questo compito fornendo un'immagine pronta da usare di cui si può eseguire il provisioning in Azure con tutti i più comuni strumenti pre-installati e configurati. 

La macchina virtuale per l'analisi scientifica dei dati di Microsoft avvia rapidamente il progetto di analisi e consente di svolgere attività in vari linguaggi, ad esempio R, Python, SQL e C#. Visual Studio fornisce un IDE facile da usare per sviluppare e testare il codice. Azure SDK incluso nella VM consente di compilare le applicazioni usando vari servizi sulla piattaforma cloud di Microsoft. 

Per questa immagine di VM per l'analisi scientifica dei dati non sono previsti costi per il software. Si pagano solo le spese di utilizzo di Azure in base alle dimensioni della macchina virtuale di cui viene eseguito il provisioning. Per informazioni dettagliate sui costi di calcolo, vedere la sezione Dettagli prezzi nella pagina relativa alla [macchina virtuale per l'analisi scientifica dei dati](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) . 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Altre versioni della macchina virtuale per l'analisi scientifica dei dati
È disponibile anche un'immagine [Ubuntu](dsvm-ubuntu-intro.md), con molti strumenti simili, oltre ad alcuni framework aggiuntivi di apprendimento avanzato. È anche disponibile un'immagine [CentOS](linux-dsvm-intro.md). Viene poi offerta un'[edizione Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) della macchina virtuale per l'analisi scientifica dei dati anche se alcuni strumenti sono disponibili solo nell'edizione Windows Server 2016.  In caso contrario, questo articolo si applica anche all'edizione Windows Server 2012.

## <a name="prerequisites"></a>Prerequisiti
Prima di creare una macchina virtuale per l'analisi scientifica dei dati di Microsoft, è necessario quanto segue:

* **Sottoscrizione di Azure**: per ottenerne una, vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.com/free).


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Creare la macchina virtuale per l'analisi scientifica dei dati di Microsoft
Di seguito sono riportati i passaggi necessari per creare un'istanza della macchina virtuale per l'analisi scientifica dei dati di Microsoft:

1. Passare alla macchina virtuale nel [portale di Azure](https://portal.azure.com/#create/microsoft-ads.windows-data-science-vmwindows2016).
2. Fare clic sul pulsante **Crea** in basso per visualizzare una procedura guidata.![configure-data-science-vm](./media/provision-vm/configure-data-science-virtual-machine.png)
3. La procedura guidata usata per creare la macchina virtuale per l'analisi scientifica dei dati di Microsoft richiede **input** per ognuno dei **quattro passaggi** elencati in ordine numerico sul lato destro della figura. Di seguito sono riportati gli input necessari per configurare ciascuno di questi passaggi:
   
   1. **Nozioni di base**
      
      1. **Name**: nome del server di analisi scientifica dei dati che si sta creando.
      2. **Tipo di disco della macchina virtuale**: scegliere tra SSD o HDD. Per GPU (serie NC), scegliere **HDD** come tipo di disco. 
      3. **Nome utente**: ID di accesso dell'account amministratore.
      4. **Password**: password dell'account amministratore.
      5. **Sottoscrizione**: se si ha più di una sottoscrizione, selezionare quella in cui viene creata e fatturata la macchina virtuale.
      6. **Gruppo di risorse**: è possibile creare un nuovo gruppo di risorse o usarne uno esistente.
      7. **Location**: selezionare la posizione del data center più appropriata. In genere è il data center contenente la maggior parte dei dati o più vicino alla posizione fisica per garantire la massima velocità di accesso alla rete.
   2. **Dimensione**: selezionare un tipo di server che soddisfa i requisiti funzionali e i vincoli di costo. Per accedere ad altre opzioni di dimensioni delle VM, selezionare "Visualizza tutto".
   3. **Impostazioni**:
      
      1. **Usa il servizio Managed Disks**: scegliere Gestito se si vuole che Azure gestisca i dischi per la VM.  In caso contrario, è necessario specificare un account di archiviazione nuovo o esistente. 
      2. **Altri parametri**: in genere si usano semplicemente i valori predefiniti. Nel caso in cui si desideri usare valori non predefiniti, è possibile passare il puntatore sul collegamento informativo per visualizzare informazioni su campi specifici.
   4. **Riepilogo**: verificare che tutte le informazioni immesse siano corrette e fare clic su **Crea**. **NOTA**: la macchina virtuale non prevede costi aggiuntivi oltre a quelli per il calcolo delle dimensioni del server scelto nel passaggio **Dimensioni**. 

> [!NOTE]
> Per il provisioning sono necessari circa 10-20 minuti. Lo stato del provisioning viene visualizzato nel portale di Azure.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Come accedere a una macchina virtuale per l'analisi scientifica dei dati di Microsoft
Dopo aver creato la VM, è possibile connettersi tramite desktop remoto con le credenziali dell'account amministratore configurato nella sezione **Nozioni di base** precedente. 

Una volta creata la VM ed effettuato il provisioning, si è pronti per iniziare a usare gli strumenti installati e configurati nella VM. Sono disponibili riquadri del menu di avvio e icone del desktop per molti strumenti. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Strumenti installati nella macchina virtuale per l'analisi scientifica dei dati di Microsoft

### <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench

Azure Machine Learning Workbench è un'applicazione desktop e un'interfaccia della riga di comando. Il workbench ha la preparazione incorporata dei dati che apprende le procedure di preparazione dei dati mentre le si esegue. Fornisce anche la gestione dei progetti, la cronologia di esecuzione e l'integrazione di notebook per incrementare la produttività. È possibile sfruttare i vantaggi dei migliori framework open source, tra cui TensorFlow, Cognitive Toolkit, Spark ML e scikit-learn per sviluppare i modelli. Nella DSVM è disponibile un'icona del desktop (InstallAMLFromLocal) per estrarre in locale Azure Machine Learning Workbench nella directory %LOCALAPPDATA% di ogni utente. Ogni utente che deve usare il workbench deve fare doppio clic sull'icona del desktop InstallAMLFromLocal una sola volta per installare l'istanza del workbench. Azure Machine Learning inoltre crea e usa per ogni utente un ambiente Python che viene estratto in %LOCALAPPDATA%\amlworkbench\python.

### <a name="microsoft-ml-server-developer-edition"></a>Microsoft ML Server Developer Edition
Se si vogliono usare le librerie Enterprise Microsoft per R o Python scalabile per l'analisi, nella VM è installato Microsoft ML Server Developer Edition (in precedenza Microsoft R Server). Microsoft ML Server è una piattaforma di analisi di livello aziendale ampiamente distribuibile, disponibile sia per R che per Python, scalabile, supportata a livello commerciale e sicura. Grazie al supporto di diverse funzionalità per statistiche di Big Data, modellazione predittiva e apprendimento automatico, ML Server supporta l'intera gamma dell'analisi: esplorazione, analisi, visualizzazione e modellazione. Usando ed estendendo i linguaggi R e Python open source, Microsoft ML Server è completamente compatibile con gli script e le funzioni R/Python e con i pacchetti CRAN/pip/Conda, per analizzare i dati a livello aziendale. Risolve anche le limitazioni in memoria di R open source aggiungendo l'elaborazione parallela e in blocchi dei dati, consentendo così di eseguire analisi su dati con volumi molto più elevati rispetto a quelli che possono essere contenuti nella memoria principale. Visual Studio Community Edition incluso nella VM contiene l'estensione Strumenti R per Visual Studio che fornisce un IDE completo per l'uso di R.  Visual Studio Community Edition incluso nella VM contiene R Tools per Visual Studio e l'estensione Python Tools for Visual Studio che fornisce un IDE completo per l'uso di R o Python. Nella VM vengono forniti anche altri IDE, ad esempio [RStudio](http://www.rstudio.com) e [PyCharm Community edition](https://www.jetbrains.com/pycharm/). 

### <a name="python"></a>Python
Per lo sviluppo tramite Python, è installata la distribuzione Anaconda Python 2.7 e 3.5. Questa distribuzione contiene il linguaggio Python di base con circa 300 dei più diffusi pacchetti di matematica, ingegneria e analisi dei dati. È possibile usare Python Tools per Visual Studio (PTVS), installato con Visual Studio 2015 Community Edition, o uno degli ambienti IDE inclusi in Anaconda, ad esempio IDLE o Spyder. È possibile avviare uno di questi IDE eseguendo una ricerca sulla barra di ricerca, con la combinazione di tasti **Win** + **S**.

> [!NOTE]
> Per usare Python Tools for Visual Studio in Anaconda Python 2.7 e 3.5, è necessario creare ambienti personalizzati per ogni versione. Per impostare questi percorsi per gli ambienti in Visual Studio 2015 Community Edition, passare a **Strumenti** -> **Python Tools (Strumenti Python)** -> **Python Environments (Ambienti Python)** e quindi fare clic su **+ Custom** (+ Personalizza). 
> 
> 

Anaconda Python 2.7 viene installato in C:\Anaconda e Anaconda Python 3.5 viene installato in C:\Anaconda\envs\py35. Per la procedura dettagliata, vedere la [documentazione di PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) . 

### <a name="jupyter-notebook"></a>Notebook Jupyter
La distribuzione Anaconda include anche Jupyter Notebook, un ambiente per la condivisione di codice e analisi. Un server Jupyter Notebook è stato preconfigurato con Python 2.7, Python 3.5, PySpark, Julia e i kernel R. Sul desktop è disponibile un'icona denominata "Notebook di Jupyter" per avviare il server Jupyter e aprire il browser per accedere al server Notebook. 

> [!NOTE]
> Se vengono visualizzati avvisi relativi al certificato, scegliere di continuare. 
> 
> 

Nel pacchetto sono inclusi vari notebook di esempio in Python e in R. I notebook di Jupyter illustrano come usare Microsoft ML Server, SQL Server ML Services (analisi In-Database), Python, Microsoft Cognitive ToolKit, Tensorflow e altre tecnologie di Azure dopo aver eseguito l'accesso a Jupyter. È possibile visualizzare il collegamento agli esempi nella home page del notebook dopo aver eseguito l'autenticazione a Jupyter Notebook con la password creata nel passaggio precedente. 

### <a name="visual-studio-2017-community-edition"></a>Visual Studio 2017 Community Edition
Visual Studio Community Edition è installato nella VM. È una versione gratuita del diffuso IDE di Microsoft che è possibile usare a scopo di valutazione e per team di dimensioni ridotte. È possibile consultare le condizioni di licenza [qui](https://www.visualstudio.com/support/legal/mt171547).  Aprire Visual Studio facendo doppio clic sull'icona del desktop o sul menu **Start** . È anche possibile cercare i programmi usando la combinazione di tasti **Win** + **S** e immettendo "Visual Studio". È quindi possibile creare progetti in linguaggi come C#, Python, R e node.js. Vengono anche installati plug-in che semplificano l'uso di servizi Azure come Azure Data Catalog, Azure HDInsight (Hadoop, Spark) e Azure Data Lake. 

> [!NOTE]
> Potrebbe essere visualizzato un messaggio indicante che il periodo di valutazione è scaduto. Per accedere a Visual Studio Community Edition è necessario immettere le credenziali di un account Microsoft o creare un nuovo account gratuito. 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition
Una versione per sviluppatori di SQL Server 2017 con ML Services per eseguire analisi nel database è fornita nella VM in R o Python. ML Services offre una piattaforma per lo sviluppo e la distribuzione di applicazioni intelligenti. È possibile usare questi linguaggi avanzati e potenti e i numerosi pacchetti della community per creare modelli e generare stime per i dati di SQL Server. È possibile mantenere le analisi insieme ai dati perché ML Services (In-Database) integra entrambi i linguaggi R e Python con SQL Server. Vengono così eliminati i costi e i rischi per la sicurezza associati allo spostamento dei dati.

> [!NOTE]
> È possibile usare SQL Server Developer Edition solo a scopo di sviluppo e test. Per l'esecuzione nell'ambiente di produzione è necessaria una licenza. 
> 
> 

È possibile accedere a SQL Server avviando **SQL Server Management Studio**. Il nome della VM viene inserito come Nome server. Usare l'autenticazione di Windows durante l'accesso come amministratore a Windows. Una volta eseguito l'accesso a SQL Server Management Studio è possibile creare altri utenti, creare database, importare i dati ed eseguire query SQL. 

Per abilitare l'analisi nel database con SQL ML Services, usare il comando seguente come azione una tantum in SQL Server Management Studio dopo aver eseguito l'accesso come amministratore del server. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
Diversi strumenti di Azure vengono installati nella macchina virtuale:

* Sul desktop c'è un collegamento per accedere alla documentazione di Azure SDK. 
* **AzCopy**: consente di spostare i dati da e verso l'account di archiviazione di Microsoft Azure. Per visualizzare l'utilizzo, digitare **Azcopy** al prompt dei comandi. 
* **Microsoft Azure Storage Explorer**: consente di esplorare gli oggetti archiviati nell'account di archiviazione di Azure e di trasferire dati da e verso Archiviazione di Azure. È possibile digitare **Esplora archivi** nella casella di ricerca oppure individuarlo e selezionarlo dal menu Start di Windows per accedere allo strumento. 
* **Adlcopy**: consente di spostare i dati in Azure Data Lake. Per visualizzare l'utilizzo, digitare **adlcopy** nel prompt dei comandi. 
* **dtui**: consente di spostare dati da e verso Azure Cosmos DB, un database NoSQL nel cloud. Digitare **dtui** al prompt dei comandi. 
* **Azure Data Factory Integration Runtime**: consente lo spostamento di dati tra origini dati locali e il cloud. Viene usato all'interno di strumenti come Data Factory di Azure. 
* **Microsoft Azure Powershell**: nella VM viene installato anche uno strumento che consente di amministrare le risorse di Azure nel linguaggio di scripting di PowerShell. 

### <a name="power-bi"></a>Power BI
È stato installato **Power BI Desktop** per facilitare la creazione di dashboard e interessanti visualizzazioni. Usare questo strumento per estrarre dati da origini diverse, per creare dashboard e report e pubblicarli nel cloud. Per informazioni, visitare il sito di [Power BI](http://powerbi.microsoft.com) . Power BI Desktop è disponibile nel menu Start. 

> [!NOTE]
> Per accedere a Power BI è necessario un account Office 365. 
> 
> 

## <a name="additional-microsoft-development-tools"></a>Altri strumenti di sviluppo Microsoft
È possibile usare [**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) per trovare e scaricare altri strumenti di sviluppo Microsoft. È disponibile anche un collegamento allo strumento sul desktop della macchina virtuale per l'analisi scientifica dei dati di Microsoft.  

## <a name="important-directories-on-the-vm"></a>Directory importanti nella VM
| Elemento | Directory |
| --- | --- |
| Configurazioni del server Jupyter Notebook |C:\ProgramData\jupyter |
| Home directory degli esempi di Jupyter Notebook |c:\dsvm\notebooks |
| Altri esempi |c:\dsvm\samples |
| Anaconda (impostazione predefinita: Python 2.7) |c:\Anaconda |
| Ambiente Anaconda Python 3.5 |c:\Anaconda\envs\py35 |
| Python per l'istanza di Microsoft ML Server autonoma  | C:\Programmi\Microsoft\ML Server\PYTHON_SERVER |
| Istanza predefinita di R (istanza di ML Server autonoma) |C:\Programmi\Microsoft\ML Server\R_SERVER |
| Directory dell'istanza In-Database di SQL ML Services |C:\Programmi\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Azure Machine Learning Workbench (per utente) | %localappdata%\amlworkbench | 
| Strumenti vari |c:\dsvm\tools |

> [!NOTE]
> Le istanze della macchina virtuale per l'analisi scientifica dei dati di Microsoft create prima della versione 1.5.0 (ovvero prima del 3 settembre 2016) usano una struttura di directory leggermente diversa rispetto a quella specificata nella tabella precedente. 
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Ecco alcuni passaggi successivi per continuare l'apprendimento e l'esplorazione. 

* Esaminare i vari strumenti di analisi scientifica dei dati nella VM per l'analisi scientifica dei dati facendo clic sul menu Start e verificando gli strumenti elencati nel menu.
* Per informazioni su Azure Machine Learning Services e Workbench, visitare la [pagina delle guide introduttive e delle esercitazioni](https://docs.microsoft.com/azure/machine-learning/preview/) del prodotto. 
* Per esempi d'uso della libreria RevoScaleR in R che supporta l'analisi dei dati a livello aziendale, passare a **C:\Programmi\Microsoft ML Server\R_SERVER\library\RevoScaleR\demoScripts**.  
* Leggere l'articolo [Dieci cose da fare con la macchina virtuale per l'analisi scientifica dei dati](http://aka.ms/dsvmtenthings)
* Informazioni su come creare sistematicamente soluzioni analitiche end-to-end usando il [processo di analisi scientifica dei dati per i team](https://azure.microsoft.com/documentation/learning-paths/data-science-process/).
* Per esempi di apprendimento avanzato e di analisi dei dati che usano Azure Machine Learning e i servizi dati correlati in Azure, visitare la [Raccolta di intelligenza artificiale per Azure](http://gallery.cortanaintelligence.com). Per questa raccolta è disponibile anche un'icona nel menu **Start** e sul desktop della macchina virtuale.

