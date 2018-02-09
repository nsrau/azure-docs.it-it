---
title: Utilizzare un servizio Web di Machine Learning con un modello di app Web | Microsoft Docs
description: Usare un modello di app Web in Azure Marketplace per utilizzare un servizio Web predittivo in Azure Machine Learning.
keywords: servizio Web,messa in funzione,API REST,Machine Learning
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye;raymondl
ms.openlocfilehash: 93e75a2864d9aef266b16f8dd7dcc3411b24864b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="consume-an-azure-machine-learning-web-service-by-using-a-web-app-template"></a>Utilizzare un servizio Web di Azure Machine Learning con un modello di app Web

È possibile sviluppare un modello predittivo e distribuirlo come servizio Web di Azure tramite:
- Azure Machine Learning Studio.
- Strumenti come R o Python. 

Successivamente, è possibile accedere al modello operativo tramite un'API REST.

Ci sono diversi modi per utilizzare l'API REST e accedere al servizio Web. È ad esempio possibile scrivere un'applicazione in C#, R o Python usando il codice di esempio generato automaticamente quando è stato distribuito il servizio Web. (Il codice di esempio è disponibile nel [portale di servizi Web di Machine Learning](https://services.azureml.net/quickstart) o nel dashboard del servizio Web in Machine Learning Studio.) In alternativa è possibile usare la cartella di lavoro Microsoft Excel di esempio creata nello stesso momento.

Il modo più rapido e semplice per accedere al servizio Web consiste però nell'usare i modelli di app Web disponibili in [Azure Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-machine-learning-web-app-templates"></a>Modelli di app Web di Azure Machine Learning
I modelli di app Web disponibili in Azure Marketplace consentono di compilare un'app Web personalizzata che riconosce i dati di input del servizio Web e i risultati previsti. È sufficiente concedere all'app Web l'accesso al proprio servizio Web e ai dati e il modello farà il resto.

Sono disponibili due modelli:

* [Modello di app Web del servizio di richiesta/risposta di Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Modello di app Web del servizio di esecuzione batch di Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Ogni modello crea un'applicazione ASP.NET di esempio, usando l'URI dell'API e la chiave per il servizio Web. Il modello distribuisce quindi l'applicazione come sito Web in Azure. 

Il modello di servizio di richiesta-risposta (RRS) crea un'app Web che è possibile usare per inviare una singola riga di dati al servizio Web per ottenere un singolo risultato. Il modello di servizio di esecuzione batch (BES) crea un'app Web che è possibile usare per inviare numerose righe di dati per ottenere più risultati.

Per usare questi modelli non è necessario alcuna codifica. È sufficiente specificare l'URI e la chiave API per ottenere la compilazione automatica dell'applicazione da parte del modello.

Per ottenere la chiave API e l'URI della richiesta per un servizio Web:

1. Nel [portale dei servizi Web](https://services.azureml.net/quickstart) selezionare **Servizi Web** nella parte superiore. Per un servizio Web classico selezionare invece **Servizi Web classici**.
2. Selezionare il servizio Web a cui si vuole accedere.
3. Per un servizio Web classico, selezionare l'endpoint a cui si vuole accedere.
4. Selezionare **Consumo** nella parte superiore.
5. Copiare la chiave primaria o secondaria e salvarla.
6. Se si sta creando un modello RRS, copiare l'URI di **richiesta-risposta** e salvarlo. Se si sta creando un modello BES, copiare l'URI delle **richieste batch** e salvarlo.


## <a name="how-to-use-the-request-response-service-template"></a>Come usare il modello di servizio di richiesta-risposta
Seguire questa procedura per usare il modello di app Web di RRS, come illustrato nella figura seguente.

![Processo per l'uso del modello Web RRS][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Nuovo**, cercare e selezionare **Azure ML Request-Response Service Web App** (App Web del Servizio di richiesta-risposta di Azure ML) e quindi fare clic su **Crea**. 
3. Nel riquadro **Crea**:
   
   * Assegnare all'app Web un nome univoco. L'URL dell'app Web sarà il nome seguito da **.azurewebsites.net**. Ad esempio **http://carprediction.azurewebsites.net**.
   * Selezionare la sottoscrizione di Azure e servizi in cui è in esecuzione il servizio Web.
   * Selezionare **Create**.
     
   ![Crea app Web][image5]

4. Quando Azure ha terminato la distribuzione dell'app Web, selezionare l'**URL** nella pagina delle impostazioni dell'app Web in Azure o immettere l'URL in un Web browser. Ad esempio, immettere **http://carprediction.azurewebsites.net**.
5. Quando l'app Web viene eseguita per la prima volta, vengono richiesti i valori di **API Post URL** (URL post API) e **API Key** (Chiave API). Immettere i valori salvati in precedenza (rispettivamente l'URI della richiesta e la chiave API). Selezionare **Submit** (Invia).
     
   ![Immettere l'URI post e la chiave API][image6]

6. L'app Web visualizza la propria pagina **Configurazione app Web** con le impostazioni del servizio Web correnti. Qui è possibile apportare modifiche alle impostazioni usate dall'app Web.
   
   > [!NOTE]
   > La modifica delle impostazioni in questa pagina si applicano solo a questa app Web. Non vengono modificate le impostazioni predefinite del servizio Web. Ad esempio, se si modifica il testo in **Description** qui, non viene modificata la descrizione indicata nel dashboard del servizio Web in Machine Learning Studio.
   > 
   > 
   
    Al termine, selezionare **Salva modifiche** e quindi selezionare **Vai alla home page**.

7. Dalla home page è possibile immettere i valori da inviare al servizio Web. Al termine selezionare **Invia** e verrà restituito il risultato.

Se si vuole tornare alla pagina **Configurazione**, passare alla pagina **setting.aspx** dell'app Web. Ad esempio, passare a **http://carprediction.azurewebsites.net/setting.aspx**. Viene chiesto di immettere di nuovo la chiave API. È necessaria per accedere alla pagina e aggiornare le impostazioni.

È possibile arrestare, riavviare o eliminare l'app Web nel portale di Azure come qualsiasi altra app Web. Mentre è in esecuzione, è possibile accedere all'indirizzo Web della home page e immettere nuovi valori.

## <a name="how-to-use-the-batch-execution-service-template"></a>Come usare il modello di servizio di esecuzione batch
È possibile usare il modello di app Web BES nello stesso modo del modello RRS. La differenza è che è possibile usare l'app Web creata per inviare più righe di dati e ricevere più risultati.

I valori di input per un servizio Web di esecuzione batch possono provenire da Archiviazione di Azure o da un file locale. I risultati vengono archiviati in un contenitore di Archiviazione di Azure. È quindi necessario un contenitore di Archiviazione di Azure per archiviare i risultati restituiti dall'app Web. È anche necessario preparare i dati di input.

![Processo per l'uso del modello Web BES][image2]

1. Seguire la stessa procedura per creare l'app Web BES usata per il modello RRS. In questo caso, però, passare ad [Azure ML Batch Execution Service Web App Template](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) (Modello di app Web del servizio di esecuzione batch di Azure ML) per aprire il modello BES in Azure Marketplace. Selezionare **Crea app Web**.

2. Per specificare dove archiviare i risultati, immettere le informazioni relative al contenitore di destinazione nella home page dell'app Web. Specificare anche dove l'app Web può ottenere i valori di input, ad esempio in un file locale o in un contenitore di archiviazione di Azure.
   Selezionare **Submit** (Invia).
   
   ![Informazioni sull'archiviazione][image7]

L'app Web visualizza una pagina con lo stato del processo. Una volta completato il processo, viene indicato il percorso dei risultati nell'archivio BLOB di Azure. È possibile scegliere di scaricare i risultati in un file locale.

## <a name="for-more-information"></a>Per altre informazioni
Per altre informazioni su:

* Creazione di un esperimento di Machine Learning con Machine Learning Studio, vedere [Esercitazione di Machine Learning: Creare il primo esperimento in Azure Machine Learning Studio](create-experiment.md).
* Come distribuire l'esperimento di Machine Learning come servizio Web, vedere [Distribuire un servizio Web di Azure Machine Learning](publish-a-machine-learning-web-service.md).
* Altre modalità di accesso al servizio Web, vedere [Come usare un servizio Web di Azure Machine Learning](consume-web-services.md).

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
