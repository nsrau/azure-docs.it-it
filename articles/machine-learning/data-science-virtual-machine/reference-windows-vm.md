---
title: 'Riferimenti: DSVM Windows'
description: Informazioni dettagliate sugli strumenti inclusi nel Data Science VM Windows
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: be4397de477891f2a698c9f7dcb131da79479529
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200022"
---
# <a name="reference-windows-data-science-virtual-machine"></a>Riferimenti: Data Science Virtual Machine di Windows

Per un elenco degli strumenti disponibili in Windows Data Science Virtual Machine, vedere di seguito. 

## <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer Edition

Per l'analisi è possibile usare Microsoft Enterprise Library perché Machine Learning Server Developer Edition è installato nella VM. Machine Learning Server, in precedenza noto come Microsoft R Server, è una piattaforma di analisi su vasta scala. È scalabile e supportata commercialmente.

Machine Learning Server supporta un'ampia gamma di statistiche di Big Data, modellazione predittiva e attività di apprendimento automatico. Supporta la gamma completa di soluzioni di analisi: esplorazione, analisi, visualizzazione e modellazione. Usando ed estendendo R e Python open source, Machine Learning Server è compatibile con le funzioni e gli script R e Python. È anche compatibile con i pacchetti CRAN, pip e Conda per l'analisi dei dati a livello aziendale.

Machine Learning Server risolve le limitazioni in memoria di R open source aggiungendo l'elaborazione parallela e in blocchi dei dati. È quindi possibile eseguire l'analisi su dati con volumi molto più elevati di quelli che possono essere contenuti nella memoria principale. 

Visual Studio Community è incluso nella VM. Contiene R Tools per Visual Studio e le estensioni Python Tools per Visual Studio (PTVS) che forniscono un IDE completo per l'uso di R o Python. Nella VM vengono forniti anche altri IDE, ad esempio [RStudio](https://www.rstudio.com) e [PyCharm Community Edition](https://www.jetbrains.com/pycharm/).

## <a name="python"></a>Python

Per lo sviluppo tramite Python, sono installate le distribuzioni Anaconda Python 2.7 e 3.6. Queste distribuzioni contengono il linguaggio Python di base con circa 300 dei più diffusi pacchetti di matematica, ingegneria e analisi dei dati. È possibile usare PTVS, installato in Visual Studio Community 2017, oppure uno degli IDE inclusi con Anaconda, ad esempio IDLE o Spyder. Cercare e aprire uno di questi pacchetti (tasto WINDOWS + S).

> [!NOTE]
> Per usare Python Tools for Visual Studio in Anaconda Python 2.7, è necessario creare ambienti personalizzati per ogni versione. Per impostare questi percorsi per gli ambienti in Visual Studio 2017 Community, passare a **Strumenti** > **Strumenti Python** > **Ambienti Python**. Selezionare quindi **+ Personalizzato**.

Anaconda Python 3.6 è installato in C:\Anaconda. Anaconda Python 2.7 è installato in C:\Anaconda\envs\python2. Per la procedura dettagliata, vedere la [documentazione di PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

## <a name="the-jupyter-notebook"></a>Jupyter Notebook

La distribuzione Anaconda include anche Jupyter Notebook, un ambiente per la condivisione di codice e analisi. Il server Jupyter Notebook è preconfigurato con i kernel Python 2.7, Python 3.x, PySpark, Julia e R. Per avviare il server Jupyter e aprire il browser per accedere al server notebook, usare l'icona del desktop **Jupyter Notebook**.

In Python e R viene creato il pacchetto di diversi notebook di esempio. Dopo l'accesso a Jupyter, i notebook illustrano come usare le tecnologie seguenti:

* Machine Learning Server
* SQL Server Machine Learning Services, nell'analisi dei database
* Python
* Microsoft Cognitive Toolkit
* TensorFlow
* Altre tecnologie di Azure

Il collegamento agli esempi viene visualizzato nella home page del notebook dopo aver eseguito l'autenticazione con Jupyter Notebook usando la password creata in precedenza.

## <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

La DSVM include Visual Studio Community. È una versione gratuita del diffuso IDE di Microsoft che è possibile usare a scopo di valutazione e per team di dimensioni ridotte. Vedere le [condizioni di licenza software Microsoft](https://www.visualstudio.com/support/legal/mt171547).

Aprire Visual Studio tramite l'icona del desktop o sul menu **Start**. Cercare i programmi premendo il tasto WINDOWS + S e poi **Visual Studio**. È quindi possibile creare progetti in linguaggi come C#, Python, R e Node.js. I plug-in installati semplificano l'uso dei servizi di Azure seguenti:

* Azure Data Catalog
* Azure HDInsight per Hadoop e Spark
* Azure Data Lake

Anche un plug-in denominato Azure Machine Learning per Visual Studio Code si integra con Azure Machine Learning e consente di creare rapidamente applicazioni di intelligenza artificiale.

> [!NOTE]
> Potrebbe essere visualizzato un messaggio indicante che il periodo di valutazione è scaduto. Immettere le credenziali dell'account Microsoft oppure creare un nuovo account gratuito per poter accedere a Visual Studio Community.

## <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition

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

## <a name="azure"></a>Azure

Diversi strumenti di Azure vengono installati nella macchina virtuale:

* Un collegamento sul desktop consente di passare alla documentazione di Azure SDK.
* Usare AzCopy per copiare i dati in e dall'account di archiviazione di Azure. Per visualizzare l'utilizzo, immettere **Azcopy** in un prompt dei comandi.
* Usare Azure Storage Explorer per esplorare gli oggetti archiviati nell'account di archiviazione di Azure. Trasferisce anche dati da e verso Archiviazione di Azure. Per accedere a questo strumento, immettere **Storage Explorer** nel campo **Cerca**. o cercarlo nel menu **Start** di Windows.
* AdlCopy copia i dati in Azure Data Lake. Per visualizzare l'utilizzo, immettere **adlcopy** nel prompt dei comandi.
* Lo strumento dtui copia i dati in e da Azure Cosmos DB, un database NoSQL nel cloud. Immettere **dtui** in un prompt dei comandi.
* Il runtime di integrazione copia i dati tra origini dati locali e il cloud. Viene usato all'interno di strumenti come Azure Data Factory.
* Usare Microsoft Azure PowerShell per amministrare le risorse di Azure nel linguaggio di scripting PowerShell. Viene anche installato nella VM.

## <a name="power-bi"></a>Power BI

La DSVM viene fornita con Power BI Desktop installato per facilitare la creazione di dashboard e visualizzazioni. Usare questo strumento per estrarre dati da origini diverse, per creare dashboard e report e pubblicarli nel cloud. Per altre informazioni, visitare il sito di [Power BI](https://powerbi.microsoft.com). Power BI Desktop è disponibile nel menu **Start**.

> [!NOTE]
> Per accedere a Power BI, è necessario un account Microsoft Office 365.

## <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning SDK per Python

I data scientist e gli sviluppatori per intelligenza artificiale usano SDK di Azure Machine Learning per Python al fine di compilare ed eseguire flussi di lavoro di machine learning con il [servizio di Azure Machine Learning](../service/overview-what-is-azure-ml.md). È possibile interagire con il servizio nei notebook di Jupyter o in un altro ambiente di sviluppo integrato preferito di Python, usando i framework open source, ad esempio TensorFlow e scikit-learn.

Python SDK è preinstallato nella macchina virtuale di data science di Microsoft. Per iniziare a usare Python SDK, vedere [Iniziare a usare Azure Machine Learning con Python](../service/quickstart-create-workspace-with-python.md).

## <a name="more-microsoft-development-tools"></a>Altri strumenti di sviluppo Microsoft

È possibile usare [Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) per trovare e scaricare altri strumenti di sviluppo Microsoft. È disponibile anche un collegamento allo strumento sul desktop di Microsoft Data Science Virtual Machine.  

## <a name="important-directories-on-the-virtual-machine"></a>Directory importanti della macchina virtuale

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

Per altre domande, Prendere in considerazione la creazione di un [ticket di supporto](https://azure.microsoft.com/support/create-ticket/).
