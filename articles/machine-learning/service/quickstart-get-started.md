---
title: "Guida introduttiva: Creare un'area di lavoro del servizio Machine Learning nel portale di Azure - Azure Machine Learning"
description: Usare il portale di Azure per creare un'area di lavoro di Azure Machine Learning. Questa area di lavoro rappresenta le fondamenta nel cloud per sperimentare, eseguire il training e distribuire modelli di Machine Learning con il servizio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: rastala
ms.author: roastala
ms.date: 09/24/2018
ms.openlocfilehash: 14bd85a23e2630a1cf2a8b5621d669c4c6748168
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376619"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning"></a>Guida introduttiva: Iniziare a usare Azure Machine Learning con il portale di Azure

In questa guida introduttiva si usa il portale di Azure per creare un'area di lavoro di Azure Machine Learning. Questa area di lavoro rappresenta le fondamenta nel cloud per sperimentare, eseguire il training e distribuire modelli di Machine Learning con il servizio Machine Learning. 

In questa esercitazione:

* Creare un'area di lavoro nella sottoscrizione di Azure.
* Provarla con Python in un notebook di Azure e registrare i valori per più iterazioni.
* Visualizzare i valori registrati nell'area di lavoro.

Le risorse di Azure seguenti vengono aggiunte automaticamente all'area di lavoro quando sono disponibili a livello di area:

  - [Registro contenitori di Azure](https://azure.microsoft.com/services/container-registry/)
  - [Archiviazione di Azure](https://azure.microsoft.com/services/storage/)
  - [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
  - [Insieme di credenziali chiave Azure](https://azure.microsoft.com/services/key-vault/)

Le risorse create possono essere usate come prerequisiti per altre esercitazioni e procedure dettagliate per il servizio Machine Learning. Come con altri servizi di Azure, sono previsti limiti per determinate risorse associate a Machine Learning. Un esempio è la dimensione dei cluster Azure Batch per intelligenza artificiale. Per informazioni su limiti predefiniti e su come aumentare la quota, vedere [questo articolo](how-to-manage-quotas.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


## <a name="create-a-workspace"></a>Creare un'area di lavoro 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Nella pagina dell'area di lavoro selezionare `Explore your Azure Machine Learning service workspace`.

 ![Esplorare l'area di lavoro](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>Usare l'area di lavoro

Ora si vedrà in che modo un'area di lavoro consente di gestire gli script di apprendimento automatico. In questa sezione verrà illustrato come:

* Aprire un notebook in Azure Notebooks.
* Eseguire il codice che crea alcuni valori registrati.
* Visualizzare i valori registrati nell'area di lavoro.

Questo esempio mostra come l'area di lavoro consente di tenere traccia delle informazioni generate in uno script. 

### <a name="open-a-notebook"></a>Aprire un notebook 

Azure Notebooks fornisce una piattaforma cloud gratuita per i notebook di Jupyter preconfigurati con tutto il necessario per eseguire Machine Learning.  

Selezionare `Open Azure Notebooks` per provare il primo esperimento.

 ![Aprire Azure Notebooks](./media/quickstart-get-started/explore_ws.png)

L'organizzazione potrebbe richiedere [il consenso dell'amministratore](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent) per poter accedere.

Dopo l'accesso, si apre una nuova scheda e viene visualizzata la richiesta `Clone Library`. Selezionare `Clone`.


### <a name="run-the-notebook"></a>Eseguire il notebook

Insieme a due notebook, viene visualizzato un file `config.json`. Questo file di configurazione contiene informazioni sull'area di lavoro creata.  

Selezionare `01.run-experiment.ipynb` per aprire il notebook.

Per eseguire le celle una alla volta, usare `Shift`+`Enter`. In alternativa, selezionare `Cells` > `Run All` per eseguire l'intero notebook. Quando viene visualizzato un asterisco [*] accanto a una cella, questa è in esecuzione. Al termine dell'esecuzione del codice per la cella, compare un numero.

Potrebbe essere necessario effettuare l'accesso. Copiare il codice nel messaggio. Quindi selezionare il collegamento e incollare il codice nella nuova finestra. Assicurarsi di non copiare spazi prima o dopo il codice. Accedere con lo stesso account usato nel portale di Azure.

 ![Accesso](./media/quickstart-get-started/login.png)

La seconda cella del notebook legge le informazioni da `config.json` per la connessione all'area di lavoro.
```
ws = Workspace.from_config()
```

La terza cella di codice avvia un esperimento con il nome "my-first-experiment". Usare questo nome per cercare le informazioni sull'esecuzione nell'area di lavoro.

```
experiment = Experiment(workspace_object=ws, name = "my-first-experiment")
```

Nell'ultima cella del notebook sono visibili i valori che vengono scritti nel file di log.

```
# Log final results
run.log("Final estimate: ",pi_estimate)
run.log("Final error: ",math.pi-pi_estimate)
```

È possibile visualizzare questi valori nell'area di lavoro dopo l'esecuzione del codice.

## <a name="view-logged-values"></a>Visualizzare i valori registrati

Dopo aver eseguito tutte le celle del notebook, tornare alla pagina del portale.  

Selezionare `View Experiments`.

![Visualizzare gli esperimenti](./media/quickstart-get-started/view_exp.png)

Chiudere la finestra popup `Reports`.

Selezionare `my-first-experiment`.

Visualizzare le informazioni sull'esecuzione appena eseguita. Scorrere la pagina verso il basso per trovare la tabella delle esecuzioni. Selezionare il collegamento del numero di esecuzione.

 ![Collegamento della cronologia di esecuzione](./media/quickstart-get-started/report.png)

Vengono visualizzati tracciati dei valori registrati che sono stati creati automaticamente.  

   ![Visualizzare la cronologia](./media/quickstart-get-started/plots.png)

Poiché il codice per approssimare PI usa valori casuali, i tracciati dell'utente mostreranno valori diversi.

## <a name="clean-up-resources"></a>Pulire le risorse 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

È anche possibile mantenere il gruppo di risorse ma eliminare una singola area di lavoro. Visualizzare le proprietà dell'area di lavoro, quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Sono state create le risorse necessarie per provare a usare e distribuire modelli. È stato anche eseguito del codice in un notebook ed è stata esaminata la cronologia di esecuzione di quel codice nell'area di lavoro nel cloud.

Per un'esperienza approfondita del flusso di lavoro, eseguire le esercitazioni di Machine Learning riguardanti il training e la distribuzione dei modelli.  

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training di un modello per la classificazione di immagini](tutorial-train-models-with-aml.md)
