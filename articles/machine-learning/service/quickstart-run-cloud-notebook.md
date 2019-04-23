---
title: 'Avvio rapido: eseguire un notebook nel cloud'
titleSuffix: Azure Machine Learning service
description: Introduzione al servizio Azure Machine Learning. Usare un server notebook gestito nel cloud per provare l'area di lavoro.  L'area di lavoro rappresenta l'elemento fondamentale nel cloud per eseguire esperimenti, training e distribuzione di modelli di Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 03/21/2019
ms.custom: seodec18
ms.openlocfilehash: 0672d90a25bc4c879d28512ab212f98f29efbf3b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59358211"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Guida introduttiva: Usare un server notebook basato sul cloud per iniziare a usare Azure Machine Learning

In questo articolo si usa Azure Notebooks per eseguire il codice che viene registrato nell'[area di lavoro](concept-azure-machine-learning-architecture.md) del servizio Machine Learning. L'area di lavoro rappresenta l'elemento fondamentale nel cloud per eseguire esperimenti, training e distribuzione di modelli di Machine Learning con il servizio Machine Learning. 

Questa guida introduttiva usa le risorse cloud e non richiede installazione. Per usare invece il proprio ambiente, vedere [Avvio rapido: Usare il proprio server notebook per iniziare a usare Azure Machine Learning](quickstart-run-local-notebook.md).  
 
In questo argomento di avvio rapido si eseguono le operazioni seguenti:

* Connettersi alla propria area di lavoro con Python in un Jupyter Notebook. Il notebook contiene un codice per stimare il pi greco e registra gli errori a ogni iterazione. 
* Visualizzare i valori di errore registrati nell'area di lavoro.

Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisite"></a>Prerequisito

1. Se non è presente, [creare un'area di lavoro di Azure Machine Learning](setup-create-workspace.md#portal).

1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com/).  Vedere come [trovare l'area di lavoro](how-to-manage-workspace.md#view).

## <a name="use-your-workspace"></a>Usare l'area di lavoro

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]



Informazioni su come un'area di lavoro consente di gestire gli script di apprendimento automatico. In questa sezione si eseguono i passaggi seguenti:

* Aprire un notebook in Azure Notebooks.
* Eseguire il codice che crea alcuni valori registrati.
* Visualizzare i valori registrati nell'area di lavoro.

Questo esempio mostra come l'area di lavoro consente di tenere traccia delle informazioni generate in uno script. 

### <a name="open-a-notebook"></a>Aprire un notebook 

[Azure Notebooks](https://notebooks.azure.com) offre una piattaforma cloud gratuita per i notebook di Jupyter preconfigurati con tutto il necessario per eseguire Machine Learning. Dall'area di lavoro è possibile avviare questa piattaforma per iniziare a usare l'area di lavoro del servizio Azure Machine Learning.

1. Nella pagina Panoramica dell'area di lavoro, selezionare **Inizia a usare Azure Notebooks** per provare il primo esperimento in Azure Notebooks.  Azure Notebooks è un servizio separato che consente di eseguire gratuitamente i notebook di Jupyter nel cloud.  Quando si usa questo collegamento al servizio, le informazioni su come connettersi all'area di lavoro verranno aggiunte alla libreria creata in Azure Notebooks.

   ![Esplorare l'area di lavoro](./media/quickstart-run-cloud-notebook/explore-aml.png)

1. Accedere ad Azure Notebooks.  Assicurarsi di accedere con lo stesso account usato per accedere al portale di Azure. L'organizzazione potrebbe richiedere [il consenso dell'amministratore](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent) per poter accedere.

1. Dopo l'accesso, si apre una nuova scheda e viene visualizzata la richiesta `Clone Library`. La clonazione di questa libreria caricherà un set di notebook e altri file nell'account di Azure Notebooks.  Questi file consentono di esplorare le funzionalità di Azure Machine Learning.

1. Deselezionare l'opzione **Pubblica** in modo da non condividere le informazioni dell'area di lavoro con altri utenti.

1. Selezionare **Clona**.

   ![Clonare una libreria](./media/quickstart-run-cloud-notebook/clone.png)

1. Se lo stato del progetto è arrestato, fare clic su **Esegui in ambiente di calcolo gratuito** per usare il server notebook gratuito.

    ![Eseguire un progetto in un ambiente di calcolo gratuito](./media/quickstart-run-cloud-notebook/run-project.png)

### <a name="run-the-notebook"></a>Eseguire il notebook

Nell'elenco di file per questo progetto viene visualizzato il file `config.json`. Questo file di configurazione contiene informazioni sull'area di lavoro creata nel portale di Azure.  Questo file consente al codice di connettersi e di aggiungere informazioni nell'area di lavoro.

1. Selezionare **01.run experiment.ipynb** per aprire il notebook.

1. L'area di notifica indica di attendere fino a quando il kernel non viene avviato.  Il messaggio scomparirà non appena il kernel è pronto.

    ![Attendere l'avvio del kernel](./media/quickstart-run-cloud-notebook/wait-for-kernel.png)

1. Dopo l'avvio del kernel eseguire le celle una alla volta con **MAIUSC+INVIO**. In alternativa, selezionare **Celle** > **Esegui tutto** per eseguire l'intero notebook. Quando viene visualizzato un asterisco, __*__, accanto a una cella, significa che questa è ancora in esecuzione. Al termine dell'esecuzione del codice per la cella, compare un numero. 

1. Seguire le istruzioni nel notebook per autenticare la sottoscrizione di Azure.

Dopo che l'esecuzione di tutte le celle del notebook è completata, è possibile visualizzare i valori registrati nell'area di lavoro.

## <a name="view-logged-values"></a>Visualizzare i valori registrati

1. L'output della cella `run` contiene un collegamento al portale di Azure per visualizzare i risultati dell'esperimento nell'area di lavoro. 

    ![Visualizzare gli esperimenti](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Fare cli sul **collegamento al portale di Azure** per visualizzare le informazioni sull'esecuzione nell'area di lavoro.  Questo collegamento apre l'area di lavoro nel portale di Azure.

1. I tracciati dei valori registrati visualizzati sono stati creati automaticamente nell'area di lavoro. Ogni volta che si registrano più valori con lo stesso parametro name, viene generato automaticamente un tracciato.

   ![Visualizzare la cronologia](./media/quickstart-run-cloud-notebook/web-results.png)

Poiché il codice per approssimare Pi usa valori casuali, i tracciati mostreranno valori diversi.  

## <a name="clean-up-resources"></a>Pulire le risorse 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

È anche possibile mantenere il gruppo di risorse ma eliminare una singola area di lavoro. Visualizzare le proprietà dell'area di lavoro e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Sono state create le risorse necessarie per provare a usare e distribuire modelli. È stato anche eseguito del codice in un notebook ed è stata esaminata la cronologia di esecuzione di quel codice nell'area di lavoro nel cloud.

Per un'esperienza approfondita del flusso di lavoro, seguire le esercitazioni di Machine Learning riguardanti il training e la distribuzione di un modello:  

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training di un modello per la classificazione di immagini](tutorial-train-models-with-aml.md)