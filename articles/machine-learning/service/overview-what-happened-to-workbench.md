---
title: Che cosa è successo a Machine Learning Workbench?
titleSuffix: Azure Machine Learning service
description: Informazioni su cosa è successo all'applicazione Machine Learning Workbench, sui cambiamenti introdotti nel servizio Azure Machine Learning e sulla sequenza temporale del supporto.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: ff6b61874363bbc869bd509174e58640a2487f56
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811308"
---
# <a name="whats-happening-to-machine-learning-workbench-in-azure-machine-learning-service"></a>Che cosa è successo a Machine Learning Workbench nel servizio Azure Machine Learning?

L'applicazione Azure Machine Learning Workbench e alcune altre funzionalità di anteprima sono state deprecate e sostituite nella versione di settembre 2018 per introdurre un miglioramento nell'[architettura](concept-azure-machine-learning-architecture.md). Per migliorare l'esperienza dell'utente, questa versione contiene molti aggiornamenti significativi introdotti in base al feedback dei clienti. Le funzionalità di base, dalle esecuzioni sperimentali alla distribuzione del modello, non sono cambiate, ma è ora possibile usare le solide funzionalità dell'<a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> e dell'[interfaccia della riga di comando di Azure](reference-azure-machine-learning-cli.md) per eseguire attività e pipeline di apprendimento automatico.  

Questo articolo illustra cosa è cambiato e come questi cambiamenti possono influire sul lavoro già svolto con Azure Machine Learning Workbench e con le relative API.

## <a name="what-changed"></a>Cosa è cambiato?

La versione più recente del servizio Azure Machine Learning include le funzionalità seguenti:
+ Un [modello semplificato delle risorse di Azure](concept-azure-machine-learning-architecture.md).
+ La [nuova interfaccia utente del portale](how-to-track-experiments.md) per gestire gli esperimenti e le destinazioni di calcolo.
+ Una nuova versione dell'<a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> per Python più completa.
+ Una nuova [estensione ampliata dell'interfaccia della riga di comando di Azure](reference-azure-machine-learning-cli.md) per l'apprendimento automatico.

L'[architettura](concept-azure-machine-learning-architecture.md) è stata riprogettata per una maggiore facilità d'uso. Invece di più risorse e account di Azure, è sufficiente avere un'[area di lavoro del servizio Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace). Le aree di lavoro possono essere create rapidamente nel [portale di Azure](quickstart-get-started.md). Usando un'area di lavoro, più utenti possono archiviare destinazioni di calcolo per training e distribuzione, esperimenti di modello, immagini Docker, modelli distribuiti e così via.

Anche se nella versione corrente sono disponibili interfaccia della riga di comando e client SDK nuovi e migliorati, l'applicazione desktop Workbench stessa è stata deprecata. È ora possibile monitorare gli esperimenti nel [dashboard dell'area di lavoro nel portale di Azure](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal). Attraverso il dashboard è possibile accedere alla cronologia degli esperimenti, gestire le destinazioni di calcolo collegate all'area di lavoro, gestire i modelli e le immagini Docker, nonché distribuire servizi Web.

## <a name="how-do-i-migrate"></a>Come si esegue la migrazione?

La maggior parte degli artefatti creati nella versione precedente del servizio Azure Machine Learning si trova nello spazio di archiviazione locale o cloud. Questi artefatti non verranno mai eliminati. Per eseguire la migrazione, è necessario registrare nuovamente gli artefatti con il servizio Azure Machine Learning aggiornato. Per sapere cosa è possibile includere nella migrazione e come eseguirla, vedere questo [articolo sulla migrazione](how-to-migrate.md).

<a name="timeline"></a>

## <a name="support-timeline"></a>Sequenza temporale del supporto

È possibile continuare a usare gli account di Gestione modelli e Sperimentazione di Machine Learning e l'applicazione Machine Learning Workbench dopo il mese di settembre 2018. Il supporto per le risorse seguenti verrà rimosso progressivamente nel corso dei tre o quattro mesi successivi al rilascio della nuova versione. La documentazione delle funzionalità precedenti resta disponibile nella [sezione Risorse](../desktop-workbench/tutorial-classifying-iris-part-1.md) nella parte inferiore del sommario.

|Fase di&nbsp;ritiro|Dettagli relativi al supporto per le funzionalità precedenti|
|:---:|----------------|
|4 dicembre 2018|Non è più possibile creare account di Gestione modelli e Sperimentazione di Azure Machine Learning nel portale di Azure e dall'interfaccia della riga di comando, né creare ambienti di calcolo di Machine Learning dall'interfaccia della riga di comando. Se si ha già un account, in questa fase l'interfaccia della riga di comando e l'applicazione desktop Machine Learning Workbench continueranno a funzionare.|
|9 gennaio 2019|In questa data termina il supporto per tutto il resto, ad esempio le API rimanenti e l'applicazione desktop Machine Learning Workbench.|

È consigliabile [iniziare la migrazione](how-to-migrate.md) oggi stesso. Tutte le funzionalità più recenti sono disponibili tramite il nuovo <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>, l'[interfaccia della riga di comando](reference-azure-machine-learning-cli.md) e il [portale](quickstart-get-started.md).

## <a name="what-about-run-histories"></a>Che ne sarà delle cronologie di esecuzione?

Le cronologie di esecuzione saranno accessibili per qualche tempo. Quando si è pronti per passare alla versione aggiornata del servizio Azure Machine Learning, è possibile esportare le cronologie di esecuzione se si vuole conservarne una copia.

Nella versione corrente le cronologie di esecuzione sono dette **esperimenti**. È possibile raccogliere gli esperimenti di un modello ed esplorarli usando l'SDK, l'interfaccia della riga di comando o il portale di Azure.

Il dashboard dell'area di lavoro del portale è supportato solo nei browser Microsoft Edge, Chrome e Firefox:

[ ![Portale online](./media/overview-what-happened-to-workbench/image001.png)] (./media/overview-what-happened-to-workbench/image001.png#lightbox)


## <a name="can-i-still-prep-data"></a>È ancora possibile preparare i dati?

I file di preparazione dei dati preesistenti non possono essere portati nella versione più recente perché Machine Learning Workbench non è più disponibile. È comunque sempre possibile preparare i dati per la modellazione.  

Con set di dati di qualsiasi dimensione, è possibile usare [Azure Machine Learning Data Prep SDK](http://aka.ms/data-prep-sdk) per preparare rapidamente i dati prima della modellazione scrivendo codice Python. 

Per altre informazioni su come usare Azure Machine Learning Data Prep SDK è possibile seguire [questa esercitazione](tutorial-data-prep.md).

## <a name="will-projects-persist"></a>I progetti saranno mantenuti?

Non si perderà il codice o il lavoro svolto. Nella versione precedente, i progetti sono entità cloud con una directory locale. Nella versione più recente si collegano le directory locali all'area di lavoro del servizio Azure Machine Learning usando un file di configurazione locale. Vedere un [diagramma dell'architettura più recente](concept-azure-machine-learning-architecture.md).

Gran parte del contenuto del progetto era già nel computer locale. È quindi sufficiente creare un file di configurazione in tale directory e farvi riferimento nel codice per collegarlo all'area di lavoro. Informazioni su come [eseguire la migrazione dei progetti esistenti](how-to-migrate.md#projects).

Informazioni su come iniziare a usare [Python con l'SDK principale](quickstart-create-workspace-with-python.md) o il [portale di Azure](quickstart-get-started.md).

## <a name="what-about-my-registered-models-and-images"></a>Che ne sarà delle immagini e dei modelli registrati?
 
Se si vuole continuare a usare i modelli registrati nel registro dei modelli precedente, è necessario eseguirne la migrazione nella nuova area di lavoro. Per eseguire la migrazione dei modelli, [scaricare i modelli e registrarli nuovamente](how-to-migrate.md) nella nuova area di lavoro. 

Le immagini create nel registro delle immagini precedente devono essere ricreate nella nuova area di lavoro per continuare a usarle. È possibile eseguire questa operazione seguendo le indicazioni nelle sezioni per [configurare e creare un'immagine](how-to-deploy-and-where.md#configureimage). 

## <a name="what-about-deployed-web-services"></a>Che ne sarà dei servizi Web distribuiti?

I modelli distribuiti come servizi Web mediante l'account di Gestione modelli di Machine Learning continueranno a funzionare finché sarà supportato il servizio Azure Container. Tali servizi Web funzioneranno anche dopo il termine del supporto per gli account di Gestione modelli di Machine Learning. Tuttavia, quando terminerà il supporto per l'interfaccia della riga di comando precedente, non sarà più possibile gestire questi servizi Web.

Nella versione più recente, i modelli vengono distribuiti come servizi Web in Istanze di Azure Container o nei cluster del servizio Azure Kubernetes (AKS). È anche possibile distribuire i modelli in dispositivi FPGA e in Azure IoT Edge. Per altre informazioni, vedere l'articolo su [come e dove eseguire la distribuzione](how-to-deploy-and-where.md). Senza la necessità di modificare i file dei punteggi, le dipendenze e gli schemi, è possibile ridistribuire i modelli usando il nuovo SDK o la nuova interfaccia della riga di comando. 

## <a name="what-about-the-old-sdk-and-cli"></a>Che ne sarà dell'SDK e dell'interfaccia della riga di comando precedenti?

Continueranno a funzionare fino al mese di gennaio. Vedere la [sequenza temporale](#timeline) precedente. È consigliabile iniziare a creare i nuovi esperimenti e modelli con l'SDK o l'interfaccia della riga di comando più recente.

Usando il nuovo SDK per Python nella versione più recente, è possibile interagire con il servizio Azure Machine Learning in qualsiasi ambiente Python. Sono disponibili informazioni sull'installazione dell'<a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> più recente. È anche possibile usare l'[estensione aggiornata dell'interfaccia della riga di comando di Azure Machine Learning](reference-azure-machine-learning-cli.md) con l'ampio set di comandi `az ml` per interagire con il servizio in qualsiasi ambiente da riga di comando, incluso Azure Cloud Shell.

## <a name="what-about-azure-machine-learning-for-visual-studio-code"></a>Che ne è di Azure Machine Learning per Visual Studio Code?

In quest'ultima versione, Azure Machine Learning per Visual Studio Code è stato ampliato e migliorato per supportare le nuove funzionalità descritte sopra.

[ ![Azure Machine Learning per Visual Studio Code](./media/overview-what-happened-to-workbench/vscode.png)] (./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>Che ne è dei pacchetti di domini?

I pacchetti di domini relativi a [Visione artificiale, Analisi del testo e Previsione](../desktop-workbench/reference-python-package-overview.md) non possono essere usati con la versione più recente di Azure Machine Learning. È comunque possibile creare ed eseguire il training di modelli di Visione artificiale, Analisi del testo e Previsione con la versione più recente dell'<a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> per Python di Azure Machine Learning. Per informazioni su come eseguire la migrazione di modelli preesistenti realizzati con i pacchetti Visione artificiale, Analisi del testo e Previsione, scrivere all'indirizzo [AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sull'[architettura più recente per il servizio Azure Machine Learning](concept-azure-machine-learning-architecture.md). Provare a eseguire una delle guide di avvio rapido o esercitazioni:

* [Informazioni sul servizio Azure Machine Learning](overview-what-is-azure-ml.md)
* [Avvio rapido: Creare un'area di lavoro con Python](quickstart-get-started.md)
* [Esercitazione: Eseguire il training di un modello](tutorial-train-models-with-aml.md)
