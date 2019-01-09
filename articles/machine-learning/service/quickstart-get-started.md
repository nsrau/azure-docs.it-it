---
title: Avvio rapido tramite il portale di Azure
titleSuffix: Azure Machine Learning service
description: Introduzione al servizio Azure Machine Learning. Usare il portale di Azure per creare un'area di lavoro che rappresenta l'elemento fondamentale nel cloud per eseguire esperimenti, training e distribuzione di modelli di Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 14c500d77cc0e67aaade5e6be490f599f39bfad5
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807721"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning"></a>Avvio rapido: Iniziare a usare Azure Machine Learning con il portale di Azure

In questo argomento di avvio rapido si usa il portale di Azure per creare un'area di lavoro di Azure Machine Learning. Questa area di lavoro rappresenta l'elemento fondamentale nel cloud per eseguire esperimenti, training e distribuzione di modelli di Machine Learning con il servizio Machine Learning. Questo argomento di avvio rapido usa le risorse cloud e non richiede installazione. Per configurare in alternativa un proprio server di Jupyter Notebook, vedere [Avvio rapido: Iniziare a usare Azure Machine Learning con Python](quickstart-create-workspace-with-python.md).  
 
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]

In questo argomento di avvio rapido si eseguono le operazioni seguenti:

* Creare un'area di lavoro nella sottoscrizione di Azure.
* Provarla con Python in un notebook di Azure e registrare i valori per più iterazioni.
* Visualizzare i valori registrati nell'area di lavoro.

Le risorse di Azure seguenti vengono aggiunte automaticamente all'area di lavoro quando sono disponibili a livello di area:

  - [Registro contenitori di Azure](https://azure.microsoft.com/services/container-registry/)
  - [Archiviazione di Azure](https://azure.microsoft.com/services/storage/)
  - [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
  - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Le risorse create possono essere usate come prerequisiti per altre esercitazioni e procedure dettagliate per il servizio Machine Learning. Come con altri servizi di Azure, sono previsti limiti per determinate risorse associate a Machine Learning. Un esempio è dato dalla dimensione del cluster di elaborazione. Sono disponibili altre informazioni sui [limiti predefiniti e su come aumentare la quota](how-to-manage-quotas.md).

Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Accedere alla [versione gratuita o a pagamento del servizio Azure Machine Learning](http://aka.ms/AMLFree).


## <a name="create-a-workspace"></a>Creare un'area di lavoro 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Nella pagina dell'area di lavoro selezionare `Explore your Azure Machine Learning service Workspace`.

 ![Esplorare l'area di lavoro](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>Usare l'area di lavoro

Ora si vedrà in che modo un'area di lavoro consente di gestire gli script di apprendimento automatico. In questa sezione si eseguono i passaggi seguenti:

* Aprire un notebook in Azure Notebooks.
* Eseguire il codice che crea alcuni valori registrati.
* Visualizzare i valori registrati nell'area di lavoro.

Questo esempio mostra come l'area di lavoro consente di tenere traccia delle informazioni generate in uno script. 

### <a name="open-a-notebook"></a>Aprire un notebook 

Azure Notebooks offre una piattaforma cloud gratuita per i notebook di Jupyter preconfigurati con tutto il necessario per eseguire Machine Learning.  

Selezionare `Open Azure Notebooks` per provare il primo esperimento.

 ![Aprire Azure Notebooks](./media/quickstart-get-started/explore_ws.png)

L'organizzazione potrebbe richiedere [il consenso dell'amministratore](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent) per poter accedere.

Accedere a Azure Notebooks con lo stesso account usato per accedere al portale di Azure.  Dopo l'accesso, si apre una nuova scheda e viene visualizzata la richiesta `Clone Library`. Selezionare `Clone`.


### <a name="run-the-notebook"></a>Eseguire il notebook

Insieme a due notebook, viene visualizzato un file `config.json`. Questo file di configurazione contiene informazioni sull'area di lavoro creata.  

Selezionare `01.run-experiment.ipynb` per aprire il notebook.

Eseguire le celle una alla volta premendo MAIUSC+INVIO. In alternativa, selezionare `Cells` > `Run All` per eseguire l'intero notebook. Quando viene visualizzato un asterisco, __*__, accanto a una cella, significa che questa è in esecuzione. Al termine dell'esecuzione del codice per la cella, compare un numero. 

Dopo che l'esecuzione di tutte le celle del notebook è completata, è possibile visualizzare i valori registrati nell'area di lavoro.

## <a name="view-logged-values"></a>Visualizzare i valori registrati

Dopo aver eseguito tutte le celle del notebook, tornare alla pagina del portale.  

Selezionare `View Experiments`.

![Visualizzare gli esperimenti](./media/quickstart-get-started/view_exp.png)

Chiudere la finestra popup `Reports`.

Selezionare `my-first-experiment`.

Visualizzare le informazioni sull'esecuzione appena eseguita. Scorrere la pagina verso il basso per trovare la tabella delle esecuzioni. Selezionare il collegamento del numero di esecuzione.

 ![Collegamento della cronologia di esecuzione](./media/quickstart-get-started/report.png)

Vengono visualizzati tracciati dei valori registrati che sono stati creati automaticamente. Ogni volta che si registrano più valori con lo stesso parametro name, viene generato automaticamente un tracciato.

   ![Visualizzare la cronologia](./media/quickstart-get-started/plots.png)

Poiché il codice per approssimare Pi usa valori casuali, i tracciati mostreranno valori diversi.  

## <a name="clean-up-resources"></a>Pulire le risorse 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

È anche possibile mantenere il gruppo di risorse ma eliminare una singola area di lavoro. Visualizzare le proprietà dell'area di lavoro e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Sono state create le risorse necessarie per provare a usare e distribuire modelli. È stato anche eseguito del codice in un notebook ed è stata esaminata la cronologia di esecuzione di quel codice nell'area di lavoro nel cloud.

Per un'esperienza approfondita del flusso di lavoro, seguire le esercitazioni di Machine Learning riguardanti il training e la distribuzione di un modello:  

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training di un modello per la classificazione di immagini](tutorial-train-models-with-aml.md)
