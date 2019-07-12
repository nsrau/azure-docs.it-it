---
title: Creare una macchina virtuale data science Windows
titleSuffix: Azure
description: Configurare e creare una macchina virtuale di Data Science in Azure per l'analisi dei dati e l'apprendimento automatico.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: gokuma
ms.openlocfilehash: 57768fc463f5ea1d2f1ec386f3f0975758220013
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626391"
---
# <a name="provision-a-windows-data-science-virtual-machine-on-azure"></a>Effettuare un provisioning di una macchina virtuale di Data Science Windows di Azure

La macchina virtuale di Data Science (DSVM) di Microsoft Windows è un'immagine di macchina virtuale (VM) di Windows Server 2016 in Azure, che viene fornita pre-installata e configurata con gli strumenti usati per l'analisi dei dati e l'apprendimento automatico.

## <a name="included-data-science-tools"></a>Strumenti di data science inclusi

In una DSVM sono inclusi gli strumenti seguenti:

* Python SDK del [servizio Azure Machine Learning](../service/index.yml)
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer Edition
* Distribuzione Anaconda Python
* Notebook Jupyter con kernel R, Python e PySpark
* Microsoft Visual Studio Community
* Microsoft Power BI Desktop
* Microsoft SQL Server 2017 Developer Edition
* Un'istanza di Apache Spark autonoma per sviluppo e test locali
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Strumenti di apprendimento automatico e analisi dei dati:
  * Framework per il Deep Learning: la macchina virtuale include un ampio set di framework per intelligenza artificiale: [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet e Keras
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): un sistema di apprendimento automatico rapido che supporta tecniche come hash online, allreduce, reduction, learning2search e apprendimento attivo e interattivo
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): uno strumento che consente un'implementazione di albero con boosting rapida e accurata
  * [Rattle](https://togaware.com/rattle/): lo strumento analitico R che consente di iniziare a usare l'analisi dei dati e l'apprendimento automatico in R. Include funzionalità di esplorazione e modellazione dei dati basate su GUI con generazione automatica di codice R.
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/): software di apprendimento automatico e data mining visivo in Java
  * [Apache Drill](https://drill.apache.org/): motore di query SQL senza schema per Apache Hadoop, NoSQL e Cloud Storage. Supporta le interfacce ODBC e JDBC per l'esecuzione di query in NoSQL e in file di strumenti di Business Intelligence standard come Power BI, Microsoft Excel e Tableau.
* Librerie in R e Python da usare in Azure Machine Learning e altri servizi di Azure
* Git, che comprende Git Bash, per lavorare con i repository di codice sorgente che includono GitHub e Azure DevOps. Git fornisce diverse comuni utilità della riga di comando di Linux accessibili sia da Git Bash che da un prompt dei comandi, ad esempio awk, sed, perl, grep, find, wget e curl.

### <a name="about-data-science"></a>Informazioni su data science

La data science comporta l'iterazione di una sequenza di attività quali:

1. Trovare, caricare e pre-elaborare i dati
1. Compilare e testare i modelli
1. Distribuire i modelli per l'uso in applicazioni intelligenti

I data scientist usano diversi strumenti per queste attività. Trovare le versioni software appropriate e quindi scaricarle e installarle può essere un'operazione dispersiva in termini di tempo. DSVM consente di risparmiare tempo fornendo un'immagine pronta da usare di cui si può effettuare il provisioning in Azure con diversi comuni strumenti pre-installati e configurati.

DSVM avviare rapidamente il progetto di analisi. ed è possibile svolgere attività in vari linguaggi, ad esempio R, Python, SQL e C#. Visual Studio fornisce un ambiente di sviluppo integrato (IDE) semplice da usare per sviluppare e testare il codice. Azure SDK è incluso nella VM per consentire di compilare le applicazioni usando vari servizi sulla piattaforma cloud di Microsoft.

Per questa immagine di VM per l'analisi scientifica dei dati non sono previsti costi per il software. Si pagano solo le spese di utilizzo di Azure, che dipendono dalle dimensioni della macchina virtuale di cui viene effettuato il provisioning. Per informazioni dettagliate sui costi di calcolo, vedere la sezione **Dettagli prezzi** nella pagina [Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows).

### <a name="other-dsvm-versions"></a>Altre versioni di DSVM

* Un'immagine [Ubuntu](dsvm-ubuntu-intro.md). Include molti strumenti simili a DSVM e alcuni altri framework di apprendimento avanzato.
* Un'immagine [Linux CentOS](linux-dsvm-intro.md).
* L'[edizione Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) di Data Science Virtual Machine. Alcuni strumenti sono disponibili solo nell'edizione Windows Server 2016. In caso contrario, questo articolo si applica anche all'edizione Windows Server 2012.

## <a name="prerequisite"></a>Prerequisito

Per creare una Microsoft Data Science Virtual Machine, è necessaria una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.com/free).

## <a name="create-your-dsvm"></a>Creare la propria DSVM

Per creare un'istanza di DSVM:

1. Andare all'elenco di macchine virtuali nel [portale di Azure](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Se non è stato eseguito l'accesso all'account Azure, verrà chiesto di farlo.
1. Selezionare quindi il pulsante **Crea** nella parte inferiore.

   ![configure-data-science-vm](./media/provision-vm/configure-data-science-virtual-machine.png)

1. È necessario immettere le informazioni seguenti per configurare ognuno dei passaggi illustrati nel riquadro destro della schermata:

   1. **Nozioni di base**:
      * **Nome**: nome della DSVM
      * **Tipo di disco della macchina virtuale**: **SSD** o **HDD**. Per un'istanza di GPU NC_v1 (basata su NVidia Tesla K80), come tipo di disco scegliere **HDD**.
      * **Nome utente**: ID dell'account amministratore
      * **Password**: password dell'account amministratore
      * **Sottoscrizione** Se si ha più di una sottoscrizione, selezionare quella in cui viene creata e fatturata la macchina virtuale.
      * **Gruppo di risorse**. È possibile creare un nuovo gruppo di risorse o usare un gruppo esistente.
      * **Località**. Selezionare il data center più appropriato. Per l'accesso più veloce alla rete, in genere è il data center che include la maggior parte dei dati o è più vicino alla posizione fisica.
   1. **Dimensione**: Selezionare uno dei tipi di server che soddisfa i requisiti funzionali e i vincoli di costo. Per visualizzare altre opzioni per le dimensioni delle VM, selezionare **Visualizza tutto**.
   1. **Impostazioni**:  
      * **Usa il servizio Managed Disks**. Scegliere **Gestito** se si vuole che Azure gestisca i dischi per la VM. In caso contrario, è necessario specificare un account di archiviazione nuovo o esistente.  
      * **Altri parametri**. È possibile usare i valori predefiniti. Se si vuole usare valori non predefiniti, è possibile passare il puntatore sul collegamento informativo per visualizzare informazioni sui campi specifici.
   1. **Riepilogo**: Verificare che tutte le informazioni immesse siano corrette. Selezionare **Create**.

> [!NOTE]
> * La macchina virtuale non prevede costi aggiuntivi oltre a quelli per il calcolo delle dimensioni del server scelto nel passaggio **Dimensioni**.
> * Il provisioning richiede circa 10-20 minuti. È possibile visualizzare lo stato della macchina virtuale nel portale di Azure.

## <a name="how-to-access-the-dsvm"></a>Come accedere alla DSVM

Dopo aver creato la VM ed averne effettuare il provisioning, è possibile connettersi tramite desktop remoto usando le credenziali dell'account amministratore configurato nella sezione **Nozioni di base** precedente. Si è pronti per iniziare a usare gli strumenti installati e configurati nella VM. Molti degli strumenti sono accessibili tramite i riquadri del menu di avvio e le icone del desktop.

È anche possibile collegare una macchina virtuale Data Science per i notebook di Azure per eseguire i notebook di Jupyter nella VM e ignorare le limitazioni del livello di servizio gratuito. Per altre informazioni, vedere [gestire e configurare progetti di notebook - livello di calcolo](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Strumenti installati nella macchina virtuale per l'analisi scientifica dei dati di Microsoft

Altre informazioni sugli strumenti già installati nella DSVM:

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer Edition

Per l'analisi è possibile usare Microsoft Enterprise Library per R o Python scalabile perché Machine Learning Server Developer Edition è installato nella VM. Machine Learning Server, precedentemente noto come Microsoft R Server, è una piattaforma di analisi di livello aziendale su vasta scala. È disponibile per R e Python. È anche scalabile, supportato a livello commerciale e sicuro.

Machine Learning Server supporta un'ampia gamma di statistiche di Big Data, modellazione predittiva e attività di apprendimento automatico. Supporta la gamma completa di soluzioni di analisi: esplorazione, analisi, visualizzazione e modellazione. Usando ed estendendo R e Python open source, Machine Learning Server è compatibile con le funzioni e gli script R e Python. È anche compatibile con i pacchetti CRAN, pip e Conda per l'analisi dei dati a livello aziendale.

Machine Learning Server risolve le limitazioni in memoria di R open source aggiungendo l'elaborazione parallela e in blocchi dei dati. È quindi possibile eseguire l'analisi su dati con volumi molto più elevati di quelli che possono essere contenuti nella memoria principale. Visual Studio Community è incluso nella VM. Contiene R Tools per Visual Studio e le estensioni Python Tools per Visual Studio (PTVS) che forniscono un IDE completo per l'uso di R o Python. Nella VM vengono forniti anche altri IDE, ad esempio [RStudio](https://www.rstudio.com) e [PyCharm Community edition](https://www.jetbrains.com/pycharm/).

### <a name="python"></a>Python

Per lo sviluppo tramite Python, sono installate le distribuzioni Anaconda Python 2.7 e 3.6. Queste distribuzioni contengono il linguaggio Python di base con circa 300 dei più diffusi pacchetti di matematica, ingegneria e analisi dei dati. È possibile usare PTVS, installato in Visual Studio Community 2017, oppure uno degli IDE inclusi con Anaconda, ad esempio IDLE o Spyder. Cercare e avviare uno di questi pacchetti (Win+S).

> [!NOTE]
> Per usare Python Tools for Visual Studio in Anaconda Python 2.7, è necessario creare ambienti personalizzati per ogni versione. Per impostare questi percorsi per gli ambienti in Visual Studio 2017 Community, passare a **Strumenti** > **Strumenti Python** > **Ambienti Python**. Selezionare quindi **+ Personalizzato**.

Anaconda Python 3.6 è installato in **C:\Anaconda**. Anaconda Python 2.7 è installato in **c:\Anaconda\envs\python2**. Per la procedura dettagliata, vedere la [documentazione di PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>Jupyter Notebook

La distribuzione Anaconda include anche Jupyter Notebook, un ambiente per la condivisione di codice e analisi. Il server Jupyter Notebook è preconfigurato con i kernel Python 2.7, Python 3.x, PySpark, Julia e R. Per avviare il server Jupyter e aprire il browser per accedere al server Notebook, usare l'icona del desktop **Jupyter Notebook**.

In Python e R viene creato il pacchetto di diversi notebook di esempio. Dopo l'accesso a Jupyter, i notebook illustrano come usare le tecnologie seguenti:

* Machine Learning Server
* SQL Server Machine Learning Services, nell'analisi dei database
* Python
* Microsoft Cognitive Toolkit
* TensorFlow
* Altre tecnologie di Azure

Il collegamento agli esempi viene visualizzato nella home page del notebook dopo aver eseguito l'autenticazione a Jupyter Notebook usando la password creata nel passaggio precedente.

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

La DSVM include Visual Studio Community. È una versione gratuita del diffuso IDE di Microsoft che è possibile usare a scopo di valutazione e per team di dimensioni ridotte. Vedere le [condizioni di licenza](https://www.visualstudio.com/support/legal/mt171547).

Aprire Visual Studio tramite l'icona del desktop o sul menu **Start**. Cercare i programmi (Win+S), seguiti da **Visual Studio**. È quindi possibile creare progetti in linguaggi come C#, Python, R e node.js. I plug-in installati semplificano l'uso dei servizi di Azure seguenti:

* Azure Data Catalog
* Azure HDInsight Hadoop e Spark
* Azure Data Lake

È anche disponibile un plug-in denominato **Azure Machine Learning per Visual Studio Code**, facilmente integrabile in Azure Machine Learning, che consente di creare rapidamente applicazioni di intelligenza artificiale.

> [!NOTE]
> Potrebbe essere visualizzato un messaggio indicante che il periodo di valutazione è scaduto. Immettere le credenziali dell'account Microsoft oppure creare un nuovo account gratuito per poter accedere a Visual Studio Community.

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition

La DSVM viene fornita con una versione per sviluppatori di SQL Server 2017 con Machine Learning Services. Questa edizione di SQL Server è disponibile in R o Python e può eseguire analisi nel database. Machine Learning Services offre una piattaforma per lo sviluppo e la distribuzione di applicazioni intelligenti. È possibile usare questi linguaggi e numerosi pacchetti della community per creare modelli e generare stime per i dati di SQL Server. È possibile mantenere le analisi insieme ai dati perché Machine Learning Services, In-Database, integra entrambi i linguaggi R e Python con SQL Server. Questa integrazione elimina i costi e i rischi per la sicurezza associati allo spostamento dei dati.

> [!NOTE]
> Usare SQL Server Developer Edition solo a scopo di sviluppo e test. Per l'esecuzione nell'ambiente di produzione è necessaria una licenza.

È possibile accedere a SQL Server avviando Microsoft SQL Server Management Studio. Il nome della VM viene inserito come **Nome server**. Usare l'autenticazione di Windows durante l'accesso come amministratore a Windows. Dopo aver eseguito l'accesso a SQL Server Management Studio, è possibile creare altri utenti, creare database, importare i dati ed eseguire query SQL.

Per abilitare l'analisi nel database usando SQL Machine Learning Services, usare il comando seguente come azione una tantum in SQL Server Management Studio dopo aver eseguito l'accesso come amministratore del server:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Sostituire `%COMPUTERNAME%` con il nome della macchina virtuale.

### <a name="azure"></a>Azure

Diversi strumenti di Azure vengono installati nella macchina virtuale:

* Un collegamento sul desktop consente di passare alla documentazione di Azure SDK.
* Usare **AzCopy** per copiare i dati da e verso l'account di archiviazione di Azure. Per visualizzare l'utilizzo, immettere **Azcopy** in un prompt dei comandi.
* Usare **Azure Storage Explorer** per esplorare gli oggetti archiviati nell'account di archiviazione di Azure. Trasferisce anche dati da e verso Archiviazione di Azure. Per accedere a questo strumento, immettere **Storage Explorer** nel campo **Cerca**. Oppure cercarlo nel menu **Start** di Windows.
* **Adlcopy** copia i dati in Azure Data Lake. Per visualizzare l'utilizzo, immettere **adlcopy** nel prompt dei comandi.
* **dtui** copia i dati da e verso Azure Cosmos DB, un database NoSQL nel cloud. Immettere **dtui** in un prompt dei comandi.
* **Azure Data Factory Integration Runtime** copia i dati tra origini dati locali e il cloud. Viene usato all'interno di strumenti come Azure Data Factory.
* Usare **Microsoft Azure PowerShell** per amministrare le risorse di Azure nel linguaggio di scripting di PowerShell. Viene anche installato nella VM.

### <a name="power-bi"></a>Power BI

La DSVM viene fornita con **Power BI Desktop** installato per facilitare la creazione di dashboard e visualizzazioni. Usare questo strumento per estrarre dati da origini diverse, per creare dashboard e report e pubblicarli nel cloud. Per altre informazioni, visitare il sito di [Power BI](https://powerbi.microsoft.com). Power BI Desktop è disponibile nel menu **Start**.

> [!NOTE]
> Per accedere a Power BI, è necessario un account Microsoft Office 365.

### <a name="azure-machine-learning-service-python-sdk"></a>Python SDK del servizio Azure Machine Learning

I data scientist e gli sviluppatori per intelligenza artificiale usano SDK di Azure Machine Learning per Python al fine di compilare ed eseguire flussi di lavoro di machine learning con il [servizio di Azure Machine Learning](../service/overview-what-is-azure-ml.md). È possibile interagire con il servizio nei notebook di Jupyter o in un altro ambiente di sviluppo integrato preferito di Python, usando i framework open source, ad esempio TensorFlow e scikit-learn.

Per iniziare a usare Python SDK, vedere [Iniziare a usare Azure Machine Learning con Python](../service/quickstart-create-workspace-with-python.md).

Python SDK è preinstallato nella macchina virtuale di data science di Microsoft.

## <a name="more-microsoft-development-tools"></a>Altri strumenti di sviluppo Microsoft

È possibile usare [Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) per trovare e scaricare altri strumenti di sviluppo Microsoft. È disponibile anche un collegamento allo strumento sul desktop di Microsoft Data Science Virtual Machine.  

## <a name="important-directories-on-the-vm"></a>Directory importanti nella VM

| Elemento | Directory |
| --- | --- |
| Configurazioni del server Jupyter Notebook | C:\ProgramData\jupyter |
| Home directory degli esempi di Jupyter Notebook | C:\dsvm\notebooks e c:\users\\<username\>\notebooks |
| Altri esempi | C:\dsvm\samples |
| Anaconda, predefinito: Python 3.6 | C:\Anaconda |
| Ambiente Anaconda Python 2.7 | C:\Anaconda\envs\python2 |
| Python per Microsoft Machine Learning Server (autonomo) | C:\Programmi\Microsoft\ML Server\PYTHON_SERVER |
| Istanza predefinita di R, Machine Learning Server (autonomo) | C:\Programmi\Microsoft\ML Server\R_SERVER |
| Directory dell'istanza In-Database di SQL Machine Learning Services | C:\Programmi\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Strumenti vari | C:\dsvm\tools |

> [!NOTE]
> Nell'edizione di DSVM di Windows Server 2012 e Windows Server 2016, prima di marzo 2018, l'ambiente Anaconda predefinito è Python 2.7. L'ambiente secondario è Python 3.5, nella directory **C:\Anaconda\envs\py35**.

## <a name="next-steps"></a>Passaggi successivi

* Esplorare gli strumenti nella VM di data science selezionando il menu **Start**.
* Per informazioni sul servizio di Azure Machine Learning leggere [Informazioni sul servizio Azure Machine Learning](../service/overview-what-is-azure-ml.md) e provando le [guide introduttive e le esercitazioni](../service/index.yml) che sono disponibili.
* In Esplora File, passare a **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** per esempi che usano la libreria RevoScaleR in R che supporta l'analisi dei dati a livello aziendale.  
* Leggere l'articolo [Dieci cose da fare con la macchina virtuale per l'analisi scientifica dei dati](https://aka.ms/dsvmtenthings).
* Informazioni su come creare sistematicamente soluzioni analitiche end-to-end usando il [Processo di analisi scientifica dei dati per i team](../team-data-science-process/index.yml).
* Per esempi di apprendimento automatico e di analisi dei dati che usano Azure Machine Learning e i servizi dati correlati in Azure, visitare [Azure AI Gallery](https://gallery.cortanaintelligence.com). Per questa raccolta è disponibile anche un'icona nel menu **Start** e sul desktop della macchina virtuale.
