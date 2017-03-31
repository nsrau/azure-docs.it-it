---
title: Utilizzare un servizio Web di Machine Learning con un modello di app Web | Documentazione Microsoft
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
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 5d587618dd885ec30e35b13c5fd543c1a8853200
ms.lasthandoff: 03/22/2017


---
# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>Utilizzare un servizio Web di Azure Machine Learning con un modello di app Web

Dopo aver sviluppato il modello predittivo e averlo distribuito come un servizio web di Azure mediante Machine Learning Studio o mediante strumenti come R o Python, è possibile accedere al modello operazionalizzato utilizzando un'API REST.

Ci sono diversi modi per utilizzare l'API REST e accedere al servizio Web. Ad esempio, è possibile scrivere un'applicazione in C#, R o Python usando il codice di esempio generato quando è stato distribuito il servizio Web, disponibile nel [portale del servizio Web di Machine Learning](https://services.azureml.net/quickstart) o nel dashboard del servizio Web in Machine Learning Studio. In alternativa è possibile usare la cartella di lavoro Microsoft Excel di esempio creata nello stesso momento.

Il modo più rapido e semplice per accedere al servizio Web consiste però nell'usare i modelli di app Web disponibili nel [Marketplace delle app Web di Azure](https://azure.microsoft.com/marketplace/web-applications/all/).

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>Modelli di app Web di Azure Machine Learning
I modelli di app Web disponibili in Azure Marketplace consentono di compilare un'app Web personalizzata che riconosce i dati di input del servizio Web e i risultati previsti. È sufficiente concedere all'app Web l'accesso al proprio servizio Web e ai dati e il modello farà il resto.

Sono disponibili due modelli:

* [Modello di app Web del servizio di richiesta/risposta di Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Modello di app Web del servizio di esecuzione batch di Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Ogni modello crea un'applicazione ASP.NET di esempio, usando l'URI dell'APIe la chiave per il servizio Web, e lao distribuisce come sito Web in Azure. Il modello di richiesta-risposta del servizio (RRS) crea un'app Web che consente di inviare una singola riga di dati al servizio Web per ottenere un singolo risultato. Il modello di servizio di esecuzione batch (BES) crea un'app Web che consente di inviare numerose righe di dati per ottenere più risultati.

Per usare questi modelli non è necessario alcuna codifica. È sufficiente implementare l'URI e la chiave API per ottenere la compilazione automatica dell'applicazione da parte del modello.

Per ottenere la chiave API e l'URI della richiesta per un servizio web:

1. Nel [portale dei servizi Web](https://services.azureml.net/quickstart), per un nuovo servizio Web, fare clic su **Servizi Web** nella parte superiore. Per un servizio Web classico, fare clic su **Servizi Web classici**.
2. Selezionare il servizio Web a cui si desidera accedere.
3. Per un servizio Web classico, fare clic sull'endpoint a cui si desidera accedere.
4. Fare clic su **Consumo** nella parte superiore.
5. Copiare la chiave **primaria** o **secondaria** e salvarla.
6. Se si sta creando un modello di Servizio di richiesta-risposta (RRS), copiare l'URI **richiesta-risposta** e salvarlo. Se si sta creando un modello di Servizio Esecuzione batch (BES), copiare l'URI delle **richieste Batch** e salvarlo.


## <a name="how-to-use-the-request-response-service-rrs-template"></a>Come usare il modello di servizio di richiesta-risposta (RRS)
Seguire questa procedura per usare il modello di app Web di RRS, come illustrato nella figura seguente.

![Processo per l'uso del modello Web RRS][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Passare al [portale di Azure](https://portal.azure.com), fare clic su **Accedi** e quindi su **Nuovo**, cercare e selezionare **Azure ML Request-Response Service Web App** (App Web del Servizio di richiesta-risposta di Azure ML) e quindi fare clic su **Crea**. 
   
   * Assegnare all'app Web un nome univoco. L'URL dell'app Web sarà il nome seguito da `.azurewebsites.net.` Ad esempio, `http://carprediction.azurewebsites.net.`
   * Selezionare la sottoscrizione di Azure e servizi in cui è in esecuzione il servizio Web.
   * Fare clic su **Crea**.
     
     ![Crea app Web][image5]

4. Quando Azure ha terminato la distribuzione dell'app Web, fare clic su **URL** nella pagina delle impostazioni dell'app Web in Azure o immettere l'URL in un browser web. Ad esempio, `http://carprediction.azurewebsites.net.`
5. Quando l'app Web viene eseguita per la prima volta, verranno richiesti i valori di **API Post URL** (URL post API) e **API Key** (Chiave API).
   Immettere i valori salvati in precedenza (rispettivamente l'**URI della richiesta** e la **chiave API**).
     
     Fare clic su **Submit**.
     
     ![Immettere Post URI e API Key][image6]

6. L'app Web visualizza la propria pagina **Configurazione app Web** con le impostazioni del servizio Web correnti. Qui è possibile apportare modifiche alle impostazioni usate dall'app Web.
   
   > [!NOTE]
   > La modifica delle impostazioni in questa pagina si applicano solo a questa app Web. Non vengono modificate le impostazioni predefinite del servizio Web. Ad esempio, se si modifica la voce **Description** qui, non viene modificata la descrizione indicata nel dashboard del servizio Web in Machine Learning Studio.
   > 
   > 
   
    Al termine, fare clic su **Save changes** (Salva modifiche) e quindi fare clic su **Go to Home Page** (Vai a home page).

7. Dalla home page è possibile immettere i valori da inviare al servizio Web. Al termine fare clic su **Invia** e verrà restituito il risultato.

Se si vuole ritornare alla pagina **Configuration** (Configurazione), andare alla pagina `setting.aspx` dell'app Web. Ad esempio: `http://carprediction.azurewebsites.net/setting.aspx.` Verrà richiesto di immettere di nuovo la chiave dell'API richiesta per accedere alla pagina e aggiornare le impostazioni.

È possibile arrestare, riavviare o eliminare l'app Web nel portale di Azure come qualsiasi altra app Web. Mentre è in esecuzione, è possibile accedere all'indirizzo Web della home page e immettere nuovi valori.

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>Come usare il modello di servizio di esecuzione batch (BES)
È possibile usare il modello di app Web BES così come si usa il modello RRS, ad eccezione del fatto che l'app Web creata consentirà di inviare più righe di dati e ricevere più risultati.

I valori di input per un servizio Web di esecuzione batch possono provenire da Archiviazione di Azure o da un file locale. I risultati vengono archiviati in un contenitore di Archiviazione di Azure.
È quindi necessario un contenitore di archiviazione di Azure per contenere i risultati restituiti dall'app Web e sarà necessario preparare i dati di input.

![Processo per l'uso del modello Web BES][image2]

1. Seguire la stessa procedura per creare l'app Web BES come modello RRS, oppure passare al [Azure ML Batch Execution Service Web App Template](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) (Modello di app Web del servizio di esecuzione batch di Azure ML) per aprire il modello BES in Azure Marketplace e fare clic su **Crea app Web**.

2. Per specificare dove archiviare i risultati, immettere le informazioni relative al contenitore di destinazione nella home page dell'app Web. Specificare anche dove l'app Web può ottenere i valori di input, ad esempio in un file locale o in un contenitore di archiviazione di Azure.
   Fare clic su **Submit**.
   
    ![Informazioni sull'archiviazione][image7]

L'app Web visualizzerà una pagina con lo stato del processo.
Una volta completato il processo, verrà indicato il percorso dei risultati nell'archivio BLOB di Azure. È possibile scegliere di scaricare i risultati in un file locale.

## <a name="for-more-information"></a>Per altre informazioni
Per altre informazioni su...

* creazione di un esperimento di apprendimento automatico con Machine Learning Studio, vedere [Esercitazione di Machine Learning: Creare il primo esperimento in Azure Machine Learning Studio](machine-learning-create-experiment.md)
* come distribuire l'esperimento di Machine Learning come servizio Web, vedere [Distribuire un servizio Web di Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md)
* altre modalità di accesso al servizio Web, vedere [Come utilizzare un servizio Web di Azure Machine Learning pubblicato da un esperimento di Machine Learning](machine-learning-consume-web-services.md)

[image1]: media/machine-learning-consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/machine-learning-consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/machine-learning-consume-web-service-with-web-app-template/api-key.png
[image4]: media/machine-learning-consume-web-service-with-web-app-template/post-uri.png
[image5]: media/machine-learning-consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/machine-learning-consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/machine-learning-consume-web-service-with-web-app-template/storage.png

