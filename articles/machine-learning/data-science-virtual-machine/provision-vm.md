---
title: 'Guida introduttiva: Creare una Data Science Virtual Machine Windows'
titleSuffix: Azure Data Science Virtual Machine
description: Configurare e creare una macchina virtuale di Data Science in Azure per l'analisi dei dati e l'apprendimento automatico.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 02/22/2019
ms.openlocfilehash: ac4b9b4d32d05083ceabd41207243eb483648baa
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278603"
---
# <a name="quickstart-set-up-a-windows-data-science-virtual-machine-on-azure"></a>Guida introduttiva: Configurare una Data Science Virtual Machine Windows in Azure

La Data Science Virtual Machine (DSVM) è un'immagine di macchina virtuale (VM) Windows Server 2016 in Azure. È preinstallata e configurata con strumenti per l'analisi dei dati e Machine Learning.

## <a name="included-data-science-tools"></a>Strumenti di data science inclusi

In una DSVM sono inclusi gli strumenti seguenti:

* Python SDK per il [servizio Azure Machine Learning](../index.yml).
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer Edition.
* Distribuzione Anaconda Python.
* Notebook Jupyter con kernel R, Python e PySpark.
* Microsoft Visual Studio Community.
* Microsoft Power BI Desktop.
* Microsoft SQL Server 2017 Developer Edition.
* Un'istanza di Apache Spark autonoma per sviluppo e test locali.
* [JuliaPro](https://juliacomputing.com/products/juliapro.html).
* Strumenti di apprendimento automatico e analisi dei dati:
  * Framework di Deep Learning: nella VM sono inclusi i framework di intelligenza artificiale [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), MXNet e Keras.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): Sistema di apprendimento automatico rapido che supporta tecniche come hash online, allreduce, reduction, learning2search e apprendimento attivo e interattivo.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): Strumento che consente un'implementazione di albero con boosting rapida e accurata.
  * [Rattle](https://togaware.com/rattle/): lo strumento analitico R che consente di iniziare a eseguire analisi dei dati e Machine Learning in R. Include funzionalità di esplorazione e modellazione dei dati basate su GUI con generazione automatica di codice R.
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/): Software di apprendimento automatico e data mining visivo in Java.
  * [Apache Drill](https://drill.apache.org/): Motore di query SQL senza schema per Apache Hadoop, NoSQL e Cloud Storage. Supporta le interfacce ODBC e JDBC per l'esecuzione di query in NoSQL e in file di strumenti di Business Intelligence standard come Power BI, Microsoft Excel e Tableau.
* Librerie in R e Python da usare in Azure Machine Learning e altri servizi di Azure.
* Git, che include Git Bash, per lavorare con i repository di codice sorgente come GitHub e Azure DevOps. Git fornisce diversi strumenti da riga di comando comuni di Linux, accessibili sia da Git Bash che da un prompt dei comandi, ad esempio awk, sed, perl, grep, find, wget e curl.
* Editor e strumenti di sviluppo (RStudio, PyCharm).

### <a name="about-data-science"></a>Informazioni su data science

La data science comporta l'iterazione di una sequenza di attività quali:

1. Trovare, caricare e pre-elaborare i dati.
1. Compilare e testare i modelli.
1. Distribuire i modelli per l'uso in applicazioni intelligenti.

I data scientist usano diversi strumenti per queste attività. Trovare le versioni software appropriate e quindi scaricarle e installarle può essere un'operazione dispersiva in termini di tempo. La DSVM consente di risparmiare tempo fornendo un'immagine pronta per l'uso di cui è possibile effettuare il provisioning in Azure con diversi strumenti comuni preinstallati e configurati.

DSVM avviare rapidamente il progetto di analisi. È possibile eseguire attività in vari linguaggi, tra cui R, Python, SQL e C#. Visual Studio fornisce un ambiente di sviluppo integrato (IDE) semplice da usare per sviluppare e testare il codice. Azure SDK è incluso nella VM per consentire di creare applicazioni usando i servizi nella piattaforma Microsoft Cloud.

Per questa immagine di DSVM non sono previsti costi per il software. Si pagano solo le spese di utilizzo di Azure, che dipendono dalle dimensioni della macchina virtuale di cui viene effettuato il provisioning. Per altre informazioni, vedere la pagina della [Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows).

### <a name="other-dsvm-versions"></a>Altre versioni di DSVM

* Un'immagine [Ubuntu](dsvm-ubuntu-intro.md). Include strumenti simili alla DSVM e alcuni framework di Deep Learning.
* Un'immagine [Linux CentOS](linux-dsvm-intro.md).
* L'[edizione Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) di Data Science Virtual Machine. Alcuni strumenti sono disponibili solo nell'edizione Windows Server 2016. In caso contrario, questo articolo si applica anche all'edizione Windows Server 2012.

## <a name="prerequisite"></a>Prerequisito

Per creare una Microsoft Data Science Virtual Machine, è necessaria una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.com/free).

## <a name="create-your-dsvm"></a>Creare la propria DSVM

Per creare un'istanza di DSVM:

1. Andare all'elenco di macchine virtuali nel [portale di Azure](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Se non è stato ancora eseguito l'accesso all'account Azure, verrà probabilmente chiesto di farlo.
1. Selezionare quindi il pulsante **Crea** nella parte inferiore.

   ![Elenco di macchine virtuali nel portale di Azure con il pulsante Crea](./media/provision-vm/configure-data-science-virtual-machine.png)

1. Immettere le informazioni seguenti per configurare ognuno dei passaggi illustrati nel riquadro destro della schermata:

   1. **Nozioni di base**:
      * **Nome**: immettere il nome della DSVM.
      * **Tipo di disco VM**: selezionare **SSD** o **HDD**. Per un'istanza di GPU NC_v1 basata su Nvidia Tesla K80, come tipo di disco scegliere **HDD**.
      * **Nome utente**: immettere l'ID dell'account amministratore.
      * **Password**: immettere la password dell'account amministratore.
      * **Sottoscrizione** se si hanno più sottoscrizioni, selezionare quella in cui verrà creata e fatturata la macchina virtuale.
      * **Gruppo di risorse**: creare un nuovo gruppo di risorse o selezionarne uno esistente.
      * **Località**: selezionare il data center più appropriato. Per l'accesso più veloce alla rete, in genere è il data center che include la maggior parte dei dati o è più vicino alla propria posizione fisica.
   1. **Dimensione**: selezionare il tipo di server che soddisfa i requisiti funzionali e i vincoli di costo. Per visualizzare altre opzioni per le dimensioni delle VM, selezionare **Visualizza tutto**.
   1. **Impostazioni**:  
      * **Usa il servizio Managed Disks**. Scegliere **Gestito** se si vuole che Azure gestisca i dischi per la VM. In caso contrario, è necessario specificare un account di archiviazione nuovo o esistente.  
      * **Altri parametri**. È possibile usare i valori predefiniti. Se si vuole usare valori non predefiniti, è possibile passare il puntatore sul collegamento informativo per visualizzare informazioni sui campi specifici.
   1. **Riepilogo**: Verificare che tutte le informazioni immesse siano corrette. Selezionare **Create** (Crea).

> [!NOTE]
> * La VM non prevede costi aggiuntivi oltre a quelli delle risorse di calcolo per le dimensioni del server scelte nel passaggio **Dimensioni**.
> * Il provisioning richiede da 10 a 20 minuti. È possibile visualizzare lo stato della macchina virtuale nel portale di Azure.

## <a name="access-the-dsvm"></a>Accedere alla DSVM

Dopo la creazione e il provisioning della VM, è possibile accedervi tramite una connessione Desktop remoto. Usare le credenziali dell'account amministratore configurate nel passaggio **Informazioni di base** per la creazione di una macchina virtuale. 

Si è pronti per iniziare a usare gli strumenti installati e configurati nella VM. Molti strumenti sono accessibili tramite i riquadri del menu **Start** e le icone del desktop.

È anche possibile collegare una DSVM ad Azure Notebooks per eseguire notebook Jupyter nella VM e ignorare le limitazioni del livello di servizio gratuito. Per altre informazioni, vedere [Gestire e configurare i progetti di Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

<a name="tools"></a>

## <a name="tools-installed-on-the-dvsm"></a>Strumenti installati nella DVSM

Nelle sezioni seguenti sono disponibili altre informazioni sugli strumenti installati nella Data Science Virtual Machine.

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer Edition

Per l'analisi è possibile usare Microsoft Enterprise Library perché Machine Learning Server Developer Edition è installato nella VM. Machine Learning Server, in precedenza noto come Microsoft R Server, è una piattaforma di analisi su vasta scala. È scalabile e supportata commercialmente.

Machine Learning Server supporta un'ampia gamma di statistiche di Big Data, modellazione predittiva e attività di apprendimento automatico. Supporta la gamma completa di soluzioni di analisi: esplorazione, analisi, visualizzazione e modellazione. Usando ed estendendo R e Python open source, Machine Learning Server è compatibile con le funzioni e gli script R e Python. È anche compatibile con i pacchetti CRAN, pip e Conda per l'analisi dei dati a livello aziendale.

Machine Learning Server risolve le limitazioni in memoria di R open source aggiungendo l'elaborazione parallela e in blocchi dei dati. È quindi possibile eseguire l'analisi su dati con volumi molto più elevati di quelli che possono essere contenuti nella memoria principale. 

Visual Studio Community è incluso nella VM. Contiene R Tools per Visual Studio e le estensioni Python Tools per Visual Studio (PTVS) che forniscono un IDE completo per l'uso di R o Python. Nella VM vengono forniti anche altri IDE, ad esempio [RStudio](https://www.rstudio.com) e [PyCharm Community Edition](https://www.jetbrains.com/pycharm/).

### <a name="python"></a>Python

Per lo sviluppo tramite Python, sono installate le distribuzioni Anaconda Python 2.7 e 3.6. Queste distribuzioni contengono il linguaggio Python di base con circa 300 dei più diffusi pacchetti di matematica, ingegneria e analisi dei dati. È possibile usare PTVS, installato in Visual Studio Community 2017, oppure uno degli IDE inclusi con Anaconda, ad esempio IDLE o Spyder. Cercare e aprire uno di questi pacchetti (tasto WINDOWS + S).

> [!NOTE]
> Per usare Python Tools for Visual Studio in Anaconda Python 2.7, è necessario creare ambienti personalizzati per ogni versione. Per impostare questi percorsi per gli ambienti in Visual Studio 2017 Community, passare a **Strumenti** > **Strumenti Python** > **Ambienti Python**. Selezionare quindi **+ Personalizzato**.

Anaconda Python 3.6 è installato in C:\Anaconda. Anaconda Python 2.7 è installato in C:\Anaconda\envs\python2. Per la procedura dettagliata, vedere la [documentazione di PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>Jupyter Notebook

La distribuzione Anaconda include anche Jupyter Notebook, un ambiente per la condivisione di codice e analisi. Il server Jupyter Notebook è preconfigurato con i kernel Python 2.7, Python 3.x, PySpark, Julia e R. Per avviare il server Jupyter e aprire il browser per accedere al server notebook, usare l'icona del desktop **Jupyter Notebook**.

In Python e R viene creato il pacchetto di diversi notebook di esempio. Dopo l'accesso a Jupyter, i notebook illustrano come usare le tecnologie seguenti:

* Machine Learning Server
* SQL Server Machine Learning Services, nell'analisi dei database
* Python
* Microsoft Cognitive Toolkit
* TensorFlow
* Altre tecnologie di Azure

Il collegamento agli esempi viene visualizzato nella home page del notebook dopo aver eseguito l'autenticazione con Jupyter Notebook usando la password creata in precedenza.

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

La DSVM include Visual Studio Community. È una versione gratuita del diffuso IDE di Microsoft che è possibile usare a scopo di valutazione e per team di dimensioni ridotte. Vedere le [condizioni di licenza software Microsoft](https://www.visualstudio.com/support/legal/mt171547).

Aprire Visual Studio tramite l'icona del desktop o sul menu **Start**. Cercare i programmi premendo il tasto WINDOWS + S e poi **Visual Studio**. È quindi possibile creare progetti in linguaggi come C#, Python, R e Node.js. I plug-in installati semplificano l'uso dei servizi di Azure seguenti:

* Azure Data Catalog
* Azure HDInsight per Hadoop e Spark
* Azure Data Lake

Anche un plug-in denominato Azure Machine Learning per Visual Studio Code si integra con Azure Machine Learning e consente di creare rapidamente applicazioni di intelligenza artificiale.

> [!NOTE]
> Potrebbe essere visualizzato un messaggio indicante che il periodo di valutazione è scaduto. Immettere le credenziali dell'account Microsoft oppure creare un nuovo account gratuito per poter accedere a Visual Studio Community.

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition

La DSVM viene fornita con una versione per sviluppatori di SQL Server 2017 con Machine Learning Services. Questa edizione di SQL Server è disponibile in R o Python e può eseguire analisi nel database. 

Machine Learning Services offre una piattaforma per lo sviluppo e la distribuzione di applicazioni intelligenti. È possibile usare questi linguaggi e numerosi pacchetti della community per creare modelli e generare stime per i dati di SQL Server. È possibile mantenere le analisi insieme ai dati perché Machine Learning Services, In-Database, integra entrambi i linguaggi R e Python con SQL Server. Questa integrazione elimina i costi e i rischi per la sicurezza associati allo spostamento dei dati.

> [!NOTE]
> Usare SQL Server Developer Edition solo a scopo di sviluppo e test. Per l'esecuzione nell'ambiente di produzione è necessaria una licenza.

È possibile accedere a SQL Server aprendo Microsoft SQL Server Management Studio. Il nome della VM viene inserito come **Nome server**. Usare l'autenticazione di Windows durante l'accesso come amministratore a Windows. Dopo aver eseguito l'accesso a SQL Server Management Studio, è possibile creare altri utenti, creare database, importare i dati ed eseguire query SQL.

Per abilitare l'analisi nel database usando SQL Server Machine Learning Services, eseguire il comando seguente come azione una tantum in SQL Server Management Studio dopo aver effettuato l'accesso come amministratore del server:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Sostituire `%COMPUTERNAME%` con il nome della macchina virtuale.

### <a name="azure"></a>Azure

Diversi strumenti di Azure vengono installati nella macchina virtuale:

* Un collegamento sul desktop consente di passare alla documentazione di Azure SDK.
* Usare AzCopy per copiare i dati in e dall'account di archiviazione di Azure. Per visualizzare l'utilizzo, immettere **Azcopy** in un prompt dei comandi.
* Usare Azure Storage Explorer per esplorare gli oggetti archiviati nell'account di archiviazione di Azure. Trasferisce anche dati da e verso Archiviazione di Azure. Per accedere a questo strumento, immettere **Storage Explorer** nel campo **Cerca**. o cercarlo nel menu **Start** di Windows.
* AdlCopy copia i dati in Azure Data Lake. Per visualizzare l'utilizzo, immettere **adlcopy** nel prompt dei comandi.
* Lo strumento dtui copia i dati in e da Azure Cosmos DB, un database NoSQL nel cloud. Immettere **dtui** in un prompt dei comandi.
* Il runtime di integrazione copia i dati tra origini dati locali e il cloud. Viene usato all'interno di strumenti come Azure Data Factory.
* Usare Microsoft Azure PowerShell per amministrare le risorse di Azure nel linguaggio di scripting PowerShell. Viene anche installato nella VM.

### <a name="power-bi"></a>Power BI

La DSVM viene fornita con Power BI Desktop installato per facilitare la creazione di dashboard e visualizzazioni. Usare questo strumento per estrarre dati da origini diverse, per creare dashboard e report e pubblicarli nel cloud. Per altre informazioni, visitare il sito di [Power BI](https://powerbi.microsoft.com). Power BI Desktop è disponibile nel menu **Start**.

> [!NOTE]
> Per accedere a Power BI, è necessario un account Microsoft Office 365.

### <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning SDK per Python

I data scientist e gli sviluppatori per intelligenza artificiale usano SDK di Azure Machine Learning per Python al fine di compilare ed eseguire flussi di lavoro di machine learning con il [servizio di Azure Machine Learning](../service/overview-what-is-azure-ml.md). È possibile interagire con il servizio nei notebook di Jupyter o in un altro ambiente di sviluppo integrato preferito di Python, usando i framework open source, ad esempio TensorFlow e scikit-learn.

Python SDK è preinstallato nella macchina virtuale di data science di Microsoft. Per iniziare a usare Python SDK, vedere [Iniziare a usare Azure Machine Learning con Python](../service/quickstart-create-workspace-with-python.md).

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
| Microsoft Machine Learning Server (autonomo) per Python | C:\Programmi\Microsoft\ML Server\PYTHON_SERVER |
| Istanza predefinita di R, Machine Learning Server (autonomo) | C:\Programmi\Microsoft\ML Server\R_SERVER |
| Directory dell'istanza in database di SQL Server Machine Learning Services | C:\Programmi\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Strumenti vari | C:\dsvm\tools |

> [!NOTE]
> Nelle edizioni per Windows Server 2012 e Windows Server 2016 della DSVM, precedenti a marzo 2018, l'ambiente Anaconda predefinito è Python 2.7. L'ambiente secondario è Python 3.5, nella directory C:\Anaconda\envs\py35.

## <a name="next-steps"></a>Passaggi successivi

* Esplorare gli strumenti disponibili nella DSVM aprendo il menu **Start**.
* Per informazioni sul servizio Azure Machine Learning, leggere [Informazioni sul servizio Azure Machine Learning](../service/overview-what-is-azure-ml.md) e seguire le [esercitazioni](../index.yml).
* In Esplora File passare a C:\Programmi\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts per trovare esempi che usano la libreria RevoScaleR in R che supporta l'analisi dei dati a livello aziendale. 
* Leggere l'articolo [Dieci cose da fare con una Data Science Virtual Machine](https://aka.ms/dsvmtenthings).
* Informazioni su come creare sistematicamente soluzioni analitiche end-to-end usando il [Processo di analisi scientifica dei dati per i team](../team-data-science-process/index.yml).
* Per esempi di apprendimento automatico e di analisi dei dati che usano Azure Machine Learning e i servizi dati correlati in Azure, visitare [Azure AI Gallery](https://gallery.cortanaintelligence.com). Per questa raccolta è disponibile anche un'icona nel menu **Start** e sul desktop della macchina virtuale.
