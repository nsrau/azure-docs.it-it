---
title: Eseguire il provisioning di una macchina virtuale per l&quot;analisi scientifica dei dati di Microsoft | Documentazione Microsoft
description: Configurare e creare una macchina virtuale per l&quot;analisi scientifica dei dati in Azure per l&quot;analisi dei dati e l&quot;apprendimento automatico.
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: e1563db23e687f772eaff2e90ce9853c9d8b87de
ms.openlocfilehash: f87df28a5a44b7ab3c1d9a48d43c8d1354c48830
ms.lasthandoff: 11/23/2016


---
# <a name="provision-the-microsoft-data-science-virtual-machine"></a>Eseguire il provisioning di una macchina virtuale per l'analisi scientifica dei dati di Microsoft
La macchina virtuale per l'analisi scientifica dei dati di Microsoft è un'immagine di macchina virtuale (VM) di Azure pre-installata e configurata con diversi strumenti diffusi comunemente usati per l'analisi dei dati e l'apprendimento automatico. Gli strumenti inclusi sono:

* Microsoft R Server Developer Edition
* Distribuzione Anaconda Python
* Notebook Jupyter (con kernel R o Python)
* Visual Studio Community Edition
* Power BI Desktop
* SQL Server 2016 Developer Edition
* Strumenti di apprendimento automatico e analisi dei dati
  * [Computational Network Toolkit (CNTK)](https://github.com/Microsoft/CNTK): toolkit di software di formazione avanzato sviluppato da Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): sistema di apprendimento automatico rapido che supporta tecniche come hash, allreduce, reduction, learning2search, nonché apprendimento online, attivo e interattivo.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): strumento che consente un'implementazione dell'albero con boosting rapida e accurata.
  * [Rattle](http://rattle.togaware.com/) (R Analytical Tool To Learn Easily): strumento che rende semplice la fase introduttiva all'analisi dei dati e al Machine Learning in R, grazie a funzionalità di esplorazione e modellazione dei dati basate su GUI con generazione automatica di codice R.
  * [mxnet](https://github.com/dmlc/mxnet): un framework di apprendimento avanzato progettato per l'efficienza e la flessibilità
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/): software di apprendimento automatico e data mining visivo in Java.
  * [Apache Drill](https://drill.apache.org/): motore di query SQL senza schema per Hadoop, NoSQL e Cloud Storage.  Supporta le interfacce ODBC e JDBC per consentire l'esecuzione di query in NoSQL e in file di strumenti di Business Intelligence standard come Power BI, Excel e Tableau.
* Librerie in R e Python da usare in Azure Machine Learning e altri servizi di Azure
* Git, che include Git Bash, per lavorare con i repository di codice sorgente fra cui GitHub e Visual Studio Team Services
* Porte Windows di diverse comuni utilità della riga di comando di Linux (tra cui awk, sed, perl, grep, find, wget, curl e così via) accessibili dal prompt dei comandi. 

L'esecuzione di analisi scientifica dei dati implica l'iterazione di una sequenza di attività: ricerca, caricamento e pre-elaborazione dei dati, generazione e test di modelli e distribuzione dei modelli per l'utilizzo in applicazioni intelligenti. I data scientist usano un'ampia gamma di strumenti per completare queste attività. Trovare le versioni software appropriate e quindi scaricarle e installarle può essere un'operazione molto dispersiva in termini di tempo. La macchina virtuale per l'analisi scientifica dei dati di Microsoft facilita questo compito fornendo un'immagine pronta da usare di cui si può eseguire il provisioning in Azure con tutti i più comuni strumenti pre-installati e configurati. 

La macchina virtuale per l'analisi scientifica dei dati di Microsoft avvia rapidamente il progetto di analisi e consente di svolgere attività in vari linguaggi, ad esempio R, Python, SQL e C#. Visual Studio fornisce un IDE facile da usare per sviluppare e testare il codice. Azure SDK incluso nella VM consente di compilare le applicazioni usando vari servizi sulla piattaforma cloud di Microsoft. 

Per questa immagine di VM per l'analisi scientifica dei dati non sono previsti costi per il software. Si pagano solo le spese di utilizzo di Azure in base alle dimensioni della macchina virtuale di cui viene eseguito il provisioning. Per informazioni dettagliate sui costi di calcolo, vedere la sezione Dettagli prezzi nella pagina relativa alla [macchina virtuale per l'analisi scientifica dei dati](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) . 

## <a name="prerequisites"></a>Prerequisiti
Prima di creare una macchina virtuale per l'analisi scientifica dei dati di Microsoft, è necessario quanto segue:

* **Sottoscrizione di Azure**: per ottenerne una, vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Account di archiviazione di Azure**: per crearne uno, vedere [Creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account). In alternativa, se non si vuole usare un account esistente è possibile creare l'account di archiviazione come parte del processo di creazione della VM.

## <a name="create-your-microsoft-data-science-virtual-machine"></a>Creare la macchina virtuale per l'analisi scientifica dei dati di Microsoft
Di seguito sono riportati i passaggi necessari per creare un'istanza della macchina virtuale per l'analisi scientifica dei dati di Microsoft:

1. Passare alla macchina virtuale nel [portale di Azure](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
2. Fare clic sul pulsante **Crea** in basso per visualizzare una procedura guidata.![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3. La procedura guidata usata per creare la macchina virtuale per l'analisi scientifica dei dati di Microsoft richiede **input** per ognuno dei **cinque passaggi** elencati in ordine numerico sul lato destro della figura. Di seguito sono riportati gli input necessari per configurare ciascuno di questi passaggi:
   
   1. **Nozioni di base**
      
      1. **Name**: nome del server di analisi scientifica dei dati che si sta creando.
      2. **Nome utente**: ID di accesso dell'account amministratore.
      3. **Password**: password dell'account amministratore.
      4. **Sottoscrizione**: se si ha più di una sottoscrizione, selezionare quella in cui viene creata e fatturata la macchina virtuale.
      5. **Gruppo di risorse**: è possibile creare un nuovo gruppo di risorse o usarne uno esistente.
      6. **Location**: selezionare la posizione del data center più appropriata. In genere è il data center contenente la maggior parte dei dati o più vicino alla posizione fisica per garantire la massima velocità di accesso alla rete.
   2. **Dimensione**: selezionare un tipo di server che soddisfa i requisiti funzionali e i vincoli di costo. Per accedere ad altre opzioni di dimensioni delle VM, selezionare "Visualizza tutto".
   3. **Impostazioni**:
      
      1. **Tipo di disco**: scegliere Premium se si preferisce un'unità SSD, in caso contrario scegliere "Standard".
      2. **Account di archiviazione**: è possibile creare un nuovo account di archiviazione di Azure nella sottoscrizione o usarne uno esistente nella stessa **località** scelta nel passaggio *Informazioni di base* della procedura guidata.
      3. **Altri parametri**: in genere si usano semplicemente i valori predefiniti. Nel caso in cui si desideri usare valori non predefiniti, è possibile passare il puntatore sul collegamento informativo per visualizzare informazioni su campi specifici.
   4. **Riepilogo**: verificare che tutte le informazioni immesse siano corrette.
   5. **Acquista**: fare clic su **Acquista** per avviare il provisioning. Viene fornito un collegamento alle condizioni della transazione. La macchina virtuale non prevede costi aggiuntivi oltre a quelli per il calcolo delle dimensioni del server scelto nel passaggio **Size** . 

> [!NOTE]
> Per il provisioning sono necessari circa 10-20 minuti. Lo stato del provisioning viene visualizzato nel portale di Azure.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Come accedere a una macchina virtuale per l'analisi scientifica dei dati di Microsoft
Dopo aver creato la VM, è possibile connettersi tramite desktop remoto con le credenziali dell'account amministratore configurato nella sezione **Nozioni di base** precedente. 

Una volta creata la VM ed effettuato il provisioning, si è pronti per iniziare a usare gli strumenti installati e configurati nella VM. Sono disponibili riquadri del menu di avvio e icone del desktop per molti strumenti. 

## <a name="how-to-create-a-strong-password-for-jupyter-and-start-the-notebook-server"></a>Come creare una password complessa per Jupyter e avviare il server notebook
Per impostazione predefinita, il server Jupyter Notebook è preconfigurato ma disabilitato nella macchina virtuale finché non viene impostata una password Jupyter. Per creare una password complessa per il server Jupyter Notebook installato nel computer, eseguire questo comando da un prompt dei comandi nella macchina virtuale per l'analisi scientifica dei dati. In alternativa, fare doppio clic sul collegamento sul desktop denominato **Jupyter Set Password & Start** (Imposta password e avvia Jupyter) da un account amministratore della macchina virtuale locale.

    C:\dsvm\tools\setup\JupyterSetPasswordAndStart.cmd

Seguire le indicazioni dei messaggi e scegliere una password complessa quando viene richiesto.

Lo script precedente genera un hash della password e lo archivia nel file di configurazione di Jupyter disponibile in **C:\ProgramData\jupyter\jupyter_notebook_config.py** con il nome di parametro ***c.NotebookApp.password***.

Lo script abilita ed esegue il server Jupyter in background. Il server Jupyter viene creato come attività di Windows nell'Utilità di pianificazione di Windows con il nome **Start_IPython_Notebook**.  Dopo aver impostato la password, potrebbe essere necessario attendere alcuni secondi prima di aprire il notebook nel browser. Per informazioni su come accedere al server Jupyter Notebook, vedere la sezione **Jupyter Notebook** di seguito. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Strumenti installati nella macchina virtuale per l'analisi scientifica dei dati di Microsoft
### <a name="microsoft-r-server-developer-edition"></a>Microsoft R Server Developer Edition
Per usare R per l'analisi, è necessario che nella macchina virtuale sia installato Microsoft R Server Developer Edition. Microsoft R Server è una piattaforma di analisi di livello aziendale su vasta scala basata su R supportata, scalabile e sicura. Grazie al supporto di diverse funzionalità per statistiche di Big Data, modellazione predittiva e apprendimento automatico, R Server supporta l'intera gamma dell'analisi: esplorazione, analisi, visualizzazione e modellazione. Usando ed estendendo il linguaggio R open source, Microsoft R Server è completamente compatibile con gli script e le funzioni R, nonché i pacchetti CRAN, per analizzare i dati a livello aziendale. Risolve anche le limitazioni in memoria di R open source aggiungendo l'elaborazione parallela e in blocchi dei dati, consentendo così di eseguire analisi su dati con volumi molto più elevati rispetto a quelli che possono essere contenuti nella memoria principale. Visual Studio Community Edition incluso nella VM contiene l'estensione Strumenti R per Visual Studio che fornisce un IDE completo per l'uso di R.  È anche possibile scaricare e usare altri IDE, come [RStudio](http://www.rstudio.com). 

### <a name="python"></a>Python
Per lo sviluppo tramite Python, è installata la distribuzione Anaconda Python 2.7 e 3.5. Questa distribuzione contiene il linguaggio Python di base con circa 300 dei più diffusi pacchetti di matematica, ingegneria e analisi dei dati. È possibile usare Python Tools per Visual Studio (PTVS), installato con Visual Studio 2015 Community Edition, o uno degli ambienti IDE inclusi in Anaconda, ad esempio IDLE o Spyder. È possibile avviare uno di questi IDE eseguendo una ricerca sulla barra di ricerca, con la combinazione di tasti **Win** + **S**.

> [!NOTE]
> Per usare Python Tools for Visual Studio in Anaconda Python 2.7 e 3.5, è necessario creare ambienti personalizzati per ogni versione. Per impostare questi percorsi per gli ambienti in Visual Studio 2015 Community Edition, passare a **Strumenti** -> **Python Tools (Strumenti Python)** -> **Python Environments (Ambienti Python)** e quindi fare clic su **+ Custom** (+ Personalizza). 
> 
> 

Anaconda Python 2.7 viene installato in C:\Anaconda e Anaconda Python 3.5 viene installato in C:\Anaconda\envs\py35. Per la procedura dettagliata, vedere la [documentazione di PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) . 

### <a name="jupyter-notebook"></a>Notebook Jupyter
La distribuzione Anaconda include anche Jupyter Notebook, un ambiente per la condivisione di codice e analisi. Un server Jupyter Notebook è stato preconfigurato con Python 2.7, Python 3.4, Python 3.5 e i kernel R. Sul desktop è disponibile un'icona denominata Notebook di Jupyter per avviare il browser per accedere al server notebook. Se si usa la VM da desktop remoto, è anche possibile visitare il sito [https://localhost:9999/](https://localhost:9999/) per accedere al server Jupyter Notebook quando si è connessi alla VM.

> [!NOTE]
> Se vengono visualizzati avvisi relativi al certificato, scegliere di continuare. 
> 
> 

Nel pacchetto sono inclusi vari notebook di esempio in Python e in R. I Jupyter Notebook illustrano come usare Microsoft R Server, i Servizi R di SQL Server 2016 (analisi nel database), Python, Microsoft Cognitive ToolKit (CNTK) per l'apprendimento avanzato e altre tecnologie di Azure dopo aver eseguito l'accesso a Jupyter. È possibile visualizzare il collegamento agli esempi nella home page del notebook dopo aver eseguito l'autenticazione a Jupyter Notebook con la password creata nel passaggio precedente. 

### <a name="visual-studio-2015-community-edition"></a>Visual Studio 2015 Community Edition
Visual Studio Community Edition è installato nella VM. È una versione gratuita del diffuso IDE di Microsoft che è possibile usare a scopo di valutazione e per team di dimensioni ridotte. È possibile consultare le condizioni di licenza [qui](https://www.visualstudio.com/support/legal/mt171547).  Aprire Visual Studio facendo doppio clic sull'icona del desktop o sul menu **Start** . È anche possibile cercare i programmi usando la combinazione di tasti **Win** + **S** e immettendo "Visual Studio". È quindi possibile creare progetti in linguaggi come C#, Python, R e node.js. Vengono anche installati plug-in che semplificano l'uso di servizi Azure come Azure Data Catalog, Azure HDInsight (Hadoop, Spark) e Azure Data Lake. 

> [!NOTE]
> Potrebbe essere visualizzato un messaggio indicante che il periodo di valutazione è scaduto. Per accedere a Visual Studio Community Edition è necessario immettere le credenziali di un account Microsoft o creare un nuovo account gratuito. 
> 
> 

### <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition
Una versione per sviluppatori di SQL Server 2016 con i R Services per eseguire analisi nel database è fornita nella VM. I servizi R offrono una piattaforma per lo sviluppo e la distribuzione di applicazioni intelligenti. È possibile usare il linguaggio R, avanzato e potente, e i numerosi pacchetti della community per creare modelli e generare stime per i dati SQL Server. È possibile mantenere le analisi insieme ai dati perché i servizi R (nel database) integrano il linguaggio R con SQL Server. Vengono così eliminati i costi e i rischi per la sicurezza associati allo spostamento dei dati.

> [!NOTE]
> È possibile usare SQL Server 2016 Developer Edition solo a scopo di sviluppo e test. Per l'esecuzione nell'ambiente di produzione è necessaria una licenza. 
> 
> 

È possibile accedere a SQL Server avviando **SQL Server Management Studio**. Il nome della VM viene inserito come Nome server. Usare l'autenticazione di Windows durante l'accesso come amministratore a Windows. Una volta eseguito l'accesso a SQL Server Management Studio è possibile creare altri utenti, creare database, importare i dati ed eseguire query SQL. 

Per abilitare l'analisi nel database con Microsoft R, eseguire il comando seguente come azione una tantum in SQL Server Management Studio dopo aver eseguito l'accesso come amministratore del server. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
Diversi strumenti di Azure vengono installati nella macchina virtuale:

* Sul desktop c'è un collegamento per accedere alla documentazione di Azure SDK. 
* **AzCopy**: consente di spostare i dati da e verso l'account di archiviazione di Microsoft Azure. Per visualizzare l'utilizzo, digitare **Azcopy** al prompt dei comandi. 
* **Microsoft Azure Storage Explorer**: consente di esplorare gli oggetti archiviati nell'account di archiviazione di Azure e di trasferire dati da e verso Archiviazione di Azure. È possibile digitare **Esplora archivi** nella casella di ricerca oppure individuarlo e selezionarlo dal menu Start di Windows per accedere allo strumento. 
* **Adlcopy**: consente di spostare i dati in Azure Data Lake. Per visualizzare l'utilizzo, digitare **adlcopy** nel prompt dei comandi. 
* **dtui**: consente di spostare dati da e verso Azure DocumentDB, un database NoSQL nel cloud. Digitare **dtui** al prompt dei comandi. 
* **Gateway di gestione dati di Microsoft**: consente lo spostamento di dati tra origini dati locali e il cloud. Viene usato all'interno di strumenti come Data Factory di Azure. 
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
| Item | Directory |
| --- | --- |
| Configurazioni del server Jupyter Notebook |C:\ProgramData\jupyter |
| Home directory degli esempi di Jupyter Notebook |c:\dsvm\notebooks |
| Altri esempi |c:\dsvm\samples |
| Anaconda (impostazione predefinita: Python 2.7) |c:\Anaconda |
| Ambiente Anaconda Python 3.5 |c:\Anaconda\envs\py35 |
| Directory dell'istanza autonoma di R Server (istanza predefinita di R basata su R3.2.2) |C:\Programmi\Microsoft SQL Server\130\R_SERVER |
| Directory dell'istanza nel database di R Server (R3.2.2) |C:\Programmi\Microsoft SQL Server\MSSQL13. MSSQLSERVER\R_SERVICES |
| Directory dell'istanza di Microsoft R Open (R3.3.1) |C:\Programmi\Microsoft\MRO-3.3.1 |
| Strumenti vari |c:\dsvm\tools |

> [!NOTE]
> Le istanze della macchina virtuale per l'analisi scientifica dei dati di Microsoft create prima della versione 1.5.0 (ovvero prima del 3 settembre 2016) usano una struttura di directory leggermente diversa rispetto a quella specificata nella tabella precedente. 
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Ecco alcuni passaggi successivi per continuare l'apprendimento e l'esplorazione. 

* Esaminare i vari strumenti di analisi scientifica dei dati nella VM per l'analisi scientifica dei dati facendo clic sul menu Start e verificando gli strumenti elencati nel menu.
* Per esempi d'uso della libreria RevoScaleR in R che supporta l'analisi dei dati a livello aziendale, passare a **C:\Programmi\Microsoft SQL Server\130\R_SERVER\library\RevoScaleR\demoScripts**.  
* Leggere l'articolo [Dieci cose da fare con la macchina virtuale per l'analisi scientifica dei dati](http://aka.ms/dsvmtenthings)
* Informazioni su come creare sistematicamente soluzioni analitiche end-to-end usando il [processo di analisi scientifica dei dati per i team](https://azure.microsoft.com/documentation/learning-paths/data-science-process/).
* Visitare [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com) per esempi di Machine Learning e di analisi dei dati con Cortana Intelligence Suite. Per questa raccolta è disponibile anche un'icona nel menu **Start** e sul desktop della macchina virtuale.


