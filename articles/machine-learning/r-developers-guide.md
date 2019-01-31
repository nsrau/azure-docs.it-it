---
title: 'Guida di Azure per sviluppatori di R: Programmazione R | Microsoft Docs'
description: Questo articolo offre una panoramica dei vari metodi con cui i data scientist possono sfruttare le proprie competenze di base insieme al linguaggio di programmazione R di Azure. Azure offre numerosi servizi che gli sviluppatori di R possono usare per estendere i carichi di lavoro di data science nel cloud.
services: machine-learning
documentationcenter: ''
author: AnalyticJeremy
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: R
ms.topic: article
ms.date: 09/12/2018
ms.author: jepeach
ms.openlocfilehash: 102191b885d2a4a9234b7783b0a51b09903d3abd
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54807457"
---
# <a name="r-developers-guide-to-azure"></a>Guida di Azure per sviluppatori di R
<img src="media/r-developers-guide/logo_r.svg" alt="R logo" align="right" width="200" />

Molti data scientist che lavorano con volumi crescenti di dati sono alla ricerca di metodi per sfruttare la potenza del cloud computing per svolgere analisi.  Questo articolo offre una panoramica dei vari metodi con cui i data scientist possono sfruttare le proprie competenze di base insieme al [linguaggio di programmazione R](https://www.r-project.org) di Azure.

Microsoft ha adottato il linguaggio di programmazione R come strumento di prima classe per i data scientist.  Offrendo molte opzioni diverse agli sviluppatori di R per l'esecuzione del codice di Azure, la società consente ai data scientist di estendere al cloud il carico di lavoro di data science quando si affrontano progetti su larga scala.

Si esamino le varie opzioni e gli scenari più interessanti caso per caso.

## <a name="azure-services-with-r-language-support"></a>Servizi di Azure con supporto del linguaggio R
Questo articolo tratta i seguenti servizi di Azure che supportano il linguaggio R:

|Service                                                          |DESCRIZIONE                                                                       |
|-----------------------------------------------------------------|----------------------------------------------------------------------------------|
|[Macchina virtuale di data science](#data-science-virtual-machine)    |una VM personalizzata da usare come workstation di data science o come destinazione di calcolo personalizzata|
|[ML Services in HDInsight](#ml-services-on-hdinsight)            |sistema basato su cluster per l'esecuzione di analisi R su grandi set di dati attraverso molti nodi   |
|[Azure Databricks](#azure-databricks)                            |ambiente di collaborazione Spark che supporta linguaggi R e vari altri               |
|[Azure Machine Learning Studio](#azure-machine-learning-studio)  |esecuzione di script R personalizzati con esperimenti di apprendimento automatico di Azure                      |
|[Azure Batch](#azure-batch)                                      |offre una varietà di opzioni per l'esecuzione economica del codice R attraverso diversi nodi di un cluster|
|[Azure Notebooks](#azure-notebooks)                              |una versione di Jupyter Notebook a costo zero basata sul cloud                  |
|[Database SQL di Azure](#azure-sql-database)                        |esecuzione degli script R all'interno del motore di database di SQL Server                            |

## <a name="data-science-virtual-machine"></a>Macchina virtuale di data science
La [macchina virtuale di data science](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) (DSVM) è un'immagine di VM personalizzata sulla piattaforma cloud di Microsoft Azure creata in modo specifico per i data science. Dispone di molteplici strumenti comuni di data science, tra cui:
* [Microsoft R Open](https://mran.microsoft.com/open/)
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)
* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop)
* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server)

Il provisioning della DSVM può essere effettuato con Windows o con Linux a seconda del sistema operativo.  È possibile usare la DSVM in due modi diversi: come workstation interattiva o come una piattaforma di calcolo per un cluster personalizzato.

### <a name="as-a-workstation"></a>Come workstation
Se si desidera iniziare a usare R nel cloud in modo semplice e veloce, questa è l'opzione migliore.  L'ambiente sarà familiare a chiunque abbia lavorato con R su una workstation locale.  Tuttavia, invece di usare le risorse locali, l'ambiente R si esegue su una macchina virtuale nel cloud.  Se i dati sono già archiviati in Azure, questo approccio offre l'ulteriore vantaggio di consentire agli script R di essere eseguiti "più vicino ai dati." Anziché trasferire i dati attraverso la rete Internet, è possibile accedere ai dati dalla rete interna di Azure che ottimizza i tempi di accesso.

La DSVM può essere particolarmente utile per i team ristretti di sviluppatori R.  Anziché investire su workstation potenti per ogni sviluppatore e richiedere ai membri del team di sincronizzarsi su una versione dei vari pacchetti software a disposizione, ogni sviluppatore può far girare un'istanza della DSVM quando necessario.

### <a name="as-a-compute-platform"></a>Come piattaforma di calcolo
Oltre a essere usata come workstation, la DSVM viene anche usata anche come piattaforma di calcolo scalabile elasticamente per i progetti R.  Usando il pacchetto R <code>[AzureDSVM](https://github.com/Azure/AzureDSVM)</code>, è possibile controllare in modo programmatico la creazione ed eliminazione di istanze DSVM.  È possibile creare le istanze in un cluster e distribuire un'analisi distribuita da eseguire nel cloud.  L'intero processo può essere controllato da un codice R in esecuzione nella workstation locale.

Per altre informazioni su DSVM, consultare ["Introduzione alla macchina virtuale di data science di Azure per Linux e Windows."](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)

## <a name="ml-services-on-hdinsight"></a>ML Services in HDInsight
[Microsoft ML Services](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-overview) offre ai data scientist, statistici e programmatori R accesso su richiesta a metodi di analisi scalabile e distribuita su HDInsight.  Questa soluzione offre le ultime funzionalità per analisi basate su R di set di dati praticamente di qualsiasi dimensione, caricati su Azure BLOB o sulla risorsa di archiviazione Data Lake.

Si tratta di una soluzione di livello aziendale che consente di ridimensionare il codice R in un cluster.  Sfruttando le funzioni del pacchetto
<code>[RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)</code> di Microsoft, gli script R in HDInsight possono eseguire funzioni di elaborazione dei dati in parallelo attraverso diversi nodi in un cluster.  Questo consente a R di raccogliere dati su una scala molto più ampia rispetto all'esecuzione di un thread singolo R in una workstation.

La possibilità di ridimensionare rende i servizi di Machine Learning in HDInsight un'ottima opzione per gli sviluppatori R che gestiscono enormi set di dati.  Fornisce una piattaforma flessibile e scalabile per l'esecuzione di script R nel cloud.

Per una procedura dettagliata sulla creazione di un cluster di servizi di Machine Learning, vedere l'articolo ["Introduzione ai servizi di Machine Learning di Azure HDInsight"](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-get-started).

## <a name="azure-databricks"></a>Azure Databricks
[Azure Databricks](https://azure.microsoft.com/services/databricks/) è una piattaforma di analisi basata su Apache Spark ottimizzata per la piattaforma dei servizi cloud di Microsoft Azure.  Progettato con i fondatori di Apache Spark, Databricks è integrato con Azure per offrire l'installazione con un clic, i flussi di lavoro semplificati e un'area di lavoro interattiva che consente la collaborazione tra data scientist, ingegneri dei dati e business analyst.

La collaborazione con Databricks viene abilitata dal sistema di notebook della piattaforma.  Gli utenti possono creare, condividere e modificare i notebook con altri utenti dei sistemi.  Questi notebook consentono agli utenti di scrivere codici da eseguire contro i cluster di Spark gestiti dall'ambiente Databricks.  Questi notebook supportano R e consentono agli utenti di accedere a Spark tramite entrambi i pacchetti `SparkR` e `sparklyr`.

Poichè Databricks è costruito su Spark e si concentra in modo particolare sulla collaborazione, la piattaforma è spesso usata dai team di data scientist che lavorano insieme su analisi complesse di grandi set di dati.  Poiché i notebook di Databricks supportano altri linguaggi oltre a R, è particolarmente utile per i team in cui gli analisti usano linguaggi diversi nel lavoro principale.

L'articolo ["Cos'è Azure Databricks?"](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks)
può fornire altri dettagli sulla piattaforma e acquisire familiarità con essa.

## <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[Azure Machine Learning Studio](https://azure.microsoft.com/services/machine-learning-studio/) è uno strumento di trascinamento collaborativo che consente di compilare, testare e distribuire soluzioni di analisi predittiva nel cloud.  Consente ai data scientist emergenti di creare e distribuire modelli di machine learning senza la necessità di scrivere tutto il codice.

Machine Learning Studio supporta sia R che Python.  È possibile usare R con Machine Learning Studio in due modi.

### <a name="custom-r-scripts-in-your-experiments"></a>Personalizza gli script R negli esperimenti
In primo luogo, è possibile estendere la manipolazione dei dati e le funzionalità di machine learning di ML Studio scrivendo script R personalizzati.
Anche se ML Studio comprende un'ampia gamma di moduli per la preparazione e l'analisi dei dati, può non essere compatibile con le funzionalità di un linguaggio avanzato come quello R. Pertanto, il servizio è stato progettato per consentire l'introduzione degli script R personalizzati nei casi in cui i moduli forniti non soddisfino le proprie esigenze.

Per sfruttare questa funzionalità, trascinare un modulo "Esegui Script R"nell'esperimento.  Quindi usare il codice editor nel riquadro "Proprietà" per scrivere un nuovo script R o incollare uno script esistente.  All'interno dello script, è possibile fare riferimento ai pacchetti R esterni.  È possibile usare lo script per modificare i dati o eseguire il training di modelli di Machine Learning complessi che non fanno parte della raccolta del modello di Machine Learning Studio standard.

Per un'introduzione completa all'uso di R all'interno di esperimenti di Machine Learning Studio, consultare ["Esercitazione con guida rapida al linguaggio di programmazione R di Azure Machine Learning"](https://docs.microsoft.com/azure/machine-learning/studio/r-quickstart).

### <a name="create-manage-and-deploy-experiments-from-your-local-r-environment"></a>Creare, gestire e distribuire gli esperimenti dall'ambiente R locale
L'altro modo per usare R con Machine Learning Studio consiste nell'usare il
pacchetto <code>[AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html)</code> per monitorare e controllare il processo di sperimentazione con l'ambiente di programmazione R.  Questo pacchetto gestito da Microsoft, consente di caricare e scaricare i set di dati da e verso Azure Machine Learning, interrogare gli esperimenti, pubblicare funzioni di R come i servizi web di Azure Machine Learning ed eseguire i dati R tramite i servizi web esistenti e recuperare l'output.

Questo pacchetto rende molto più semplice l'utilizzo di Azure Machine Learning come piattaforma di distribuzione scalabile per il codice R.  Invece di selezionare e trascinare nell'interfaccia utente, è possibile automatizzare l'intero processo di distribuzione usando lo strumento che già si conosce.

## <a name="azure-batch"></a>Azure Batch
Per i processi R su larga scala, è possibile usare [Azure Batch](https://azure.microsoft.com/services/batch/).  Questo servizio consente la gestione di calcolo e pianificazione del processo su scala cloud in modo da poter ridimensionare il carico di lavoro R tra decine, centinaia o migliaia di macchine virtuali.  Poichè si tratta di una piattaforma di calcolo generalizzata, esistono alcune opzioni per l'esecuzione di processi R con Azure Batch.

Un'opzione consiste nell'usare il pacchetto di <code>[doAzureParallel](https://github.com/Azure/doAzureParallel)</code> Microsoft.  Questo pacchetto R è un back-end parallelo al `foreach` pacchetto.  Consente l'esecuzione in parallelo di ogni iterazione del ciclo `foreach` su un nodo all'interno del cluster di Azure Batch.  Per un'introduzione al pacchetto, leggere il post di blog ["doAzureParallel: Take advantage of Azure’s flexible compute directly from your R session"](https://azure.microsoft.com/blog/doazureparallel/) (Sfruttare i vantaggi delle risorse del calcolo flessibile di Azure direttamente dalla sessione R).

Un'altra opzione per l'esecuzione di uno script R di Azure Batch consiste nell'aggregare il codice "RScript.exe" come un'App di Batch nel portale di Azure.  Per una procedura dettagliata, consultare ["Carichi di lavoro R su Azure Batch"](https://azure.microsoft.com/blog/r-workloads-on-azure-batch/).

Una terza opzione consiste nell'usare la [Azure Distributed Data Engineering Toolkit](https://github.com/Azure/aztk) (AZTK), che consente di eseguire il provisioning di cluster di Spark su richiesta usando i contenitori Docker di Azure Batch.  Ciò costituisce un modo economico per eseguire i processi Spark di Azure.  Usando [SparklyR con AZTK](https://github.com/Azure/aztk/wiki/SparklyR-on-Azure-with-AZTK), gli script R possono essere aumentati sul cloud in modo semplice ed economico.

## <a name="azure-notebooks"></a>Notebook di Azure

[Azure Notebooks](https://notebooks.azure.com) è un metodo economico e a basso attrito per gli sviluppatori di R che preferiscono lavorare con i notebook per portare il proprio codice ad Azure.  È un servizio gratuito per tutti coloro che vogliono sviluppare ed eseguire un codice nel proprio browser usando [Jupyter](https://jupyter.org/), ovvero un progetto open source che consente di combinare markdown della prosa, codice eseguibile e grafica in una canvas singola.

Il livello di servizio gratuito di Azure Notebooks è un'opzione valida per i progetti su scala ridotta, poiché limita il processo di ogni notebook a 4 GB di memoria e 1 GB di set di dati. Se sono necessari più dati e potenza di calcolo rispetto a queste limitazioni, tuttavia, è possibile eseguire i notebook in un'istanza di Data Science Virtual Machine. Per altre informazioni, vedere [Manage and configure Azure Notebooks projects - Compute tier](/azure/notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) (Gestire e configurare i progetti di Azure Notebooks - Livello di calcolo).

## <a name="azure-sql-database"></a>Database SQL di Azure
[Database SQL di Azure](https://azure.microsoft.com/services/sql-database/) è un servizio di database cloud relazionale completamente gestito e intelligente.  Consente di usare tutta la potenza di SQL Server senza alcun problema di configurazione dell'infrastruttura.  Questo comprende [Servizi di Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning?view=sql-server-2017), ovvero una delle aggiunte più recenti al servizio SQL.

Questa funzionalità offre un'analisi predittiva e incorporata e un motore di data science che può eseguire un codice R all'interno di un database di SQL Server come stored procedure, come script T-SQL contenenti le istruzioni di R o come codice R che contiene T-SQL.  Invece di estrarre i dati dal database e caricarli nell'ambiente R, si carica il codice R direttamente nel database e si lascia in esecuzione accanto ai dati.

Anche se Machine Learning Services fa parte del SQL Server locale dal 2016, è relativamente nuovo al Database SQL di Azure.  Al momento si trova in [anteprima limitata](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#azure-sql-database-roadmap) ma continua a evolversi.


### <a name="next-steps"></a>Passaggi successivi
* [Esecuzione del codice R in Azure con mrsdeploy](http://blog.revolutionanalytics.com/2017/03/running-your-r-code-azure.html)
* [Machine Learning Server nel Cloud](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-in-the-cloud)
* [Risorse aggiuntive per Machine Learning Server e Microsoft R](https://docs.microsoft.com/machine-learning-server/resources-more)
* [R in Azure](https://github.com/yueguoguo/r-on-azure): una panoramica dei pacchetti, strumenti e case study per l'uso di R con Azure

---

<sub>Il logo di R è &copy; 2016 La Fondazione R e viene usato secondo le condizioni per la [Licenza internazionale Creative Commons Attribution-ShareAlike 4.0](https://creativecommons.org/licenses/by-sa/4.0/).</sub>
