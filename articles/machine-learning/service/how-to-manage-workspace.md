---
title: Creare aree di lavoro Azure Machine Learning nel portale
titleSuffix: Azure Machine Learning
description: Informazioni su come creare, visualizzare ed eliminare aree di lavoro Azure Machine Learning nel portale di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 80b554b7dd4f37e1a215892962d8ec5622d8ae5c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974037"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Creare e gestire aree di lavoro Azure Machine Learning nel portale di Azure
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come creare, visualizzare ed eliminare [**Azure Machine Learning aree di lavoro**](concept-workspace.md) nel portale di Azure per [Azure Machine Learning](overview-what-is-azure-ml.md).  Il portale è il modo più semplice per iniziare a usare le aree di lavoro, ma in base alle esigenze modificate o ai requisiti per l'aumento dell'automazione è anche possibile creare ed eliminare aree di lavoro [tramite l'interfaccia della](reference-azure-machine-learning-cli.md)riga di comando, [con codice Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) o [tramite l'estensione vs code](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Per creare un'area di lavoro, è necessaria una sottoscrizione di Azure. Se non si dispone di una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

1. Accedere al [portale di Azure](https://portal.azure.com/) usando le credenziali della sottoscrizione di Azure. 

1. Nell'angolo in alto a sinistra del portale di Azure selezionare **+ Crea una risorsa**.

      ![Creare una nuova risorsa](../../../includes/media/aml-create-in-portal/create-workspace.gif)

1. Usare la barra di ricerca per trovare **Machine Learning**.

1. Selezionare **Machine Learning**.

1. Nel riquadro **Machine Learning** selezionare **Crea** per iniziare.

1. Specificare le informazioni seguenti per configurare la nuova area di lavoro:

   Campo|Description 
   ---|---
   Nome dell'area di lavoro |Immettere un nome univoco che identifichi l'area di lavoro. In questo esempio si usa **docs-ws**. I nomi devono essere univoci all'interno del gruppo di risorse. Usare un nome facile da ricordare e da distinguere dai nomi delle aree di lavoro create da altri utenti.  
   Sottoscrizione |Selezionare la sottoscrizione di Azure da usare.
   Gruppo di risorse | Usare un gruppo di risorse esistente nella sottoscrizione oppure immettere un nome per creare un nuovo gruppo di risorse. Un gruppo di risorse include risorse correlate per una soluzione Azure. In questo esempio si usa **docs-aml**. 
   Località | Selezionare la località più vicina agli utenti e alle risorse di dati per creare l'area di lavoro.
   Edizione dell'area di lavoro | Selezionare **Basic** o **Enterprise**.  Questa edizione dell'area di lavoro determina le funzionalità a cui si avrà accesso e i prezzi. Scopri di più sulle [offerte Basic ed Enterprise Edition](overview-what-is-azure-ml.md#sku). 

    ![Configurare l'area di lavoro](media/how-to-manage-workspace/select-edition.png)

1. Dopo aver completato la configurazione dell'area di lavoro, selezionare **Crea**. 

   > [!Warning] 
   > La creazione dell'area di lavoro nel cloud può richiedere diversi minuti.

   Al termine del processo verrà visualizzato un messaggio di conferma del completamento della distribuzione, 
 
 1. Per visualizzare la nuova area di lavoro, selezionare **Vai alla risorsa**.

### <a name="download-a-configuration-file"></a>Scaricare un file di configurazione

1. Se si vuole creare una [VM notebook](tutorial-1st-experiment-sdk-setup.md#azure), ignorare questo passaggio.

1. Se si prevede di usare codice nell'ambiente locale che fa riferimento a questa area di lavoro, selezionare **Scarica config json** dalla sezione **Panoramica** dell'area di lavoro.  

   ![Scarica config.json](./media/how-to-manage-workspace/configure.png)
   
   Inserire il file nella struttura di directory che contiene gli script Python o i notebook di Jupyter. Può trattarsi della stessa directory, in una sottodirectory denominata *.azureml* o in una directory padre. Quando si crea una macchina virtuale notebook, questo file viene aggiunto alla directory corretta nella macchina virtuale.

## <a name="upgrade"></a>Eseguire l'aggiornamento a Enterprise Edition

È possibile aggiornare l'area di lavoro da Basic Edition a Enterprise Edition per sfruttare le funzionalità avanzate, ad esempio le esperienze di basso codice e le funzionalità di sicurezza avanzate.

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com).

1. Selezionare l'area di lavoro che si desidera aggiornare.

1. Selezionare **altre informazioni** nella parte superiore destra della pagina.

   [![aggiornare un'area di lavoro](media/how-to-manage-workspace/upgrade.png)](media/how-to-manage-workspace/upgrade.png#lightbox)

1. Selezionare **Aggiorna** nella finestra visualizzata.


> [!IMPORTANT]
> Non è possibile effettuare il downgrade di un'area di lavoro Enterprise Edition a un'area di lavoro Basic Edition. 

## <a name="view"></a>Trovare un'area di lavoro

1. Nel campo di ricerca superiore digitare **Machine Learning**.  

1. Selezionare **Machine Learning**.

   ![Cerca area di lavoro Azure Machine Learning](media/how-to-manage-workspace/find-workspaces.png)

1. Esaminare l'elenco delle aree di lavoro trovate. È possibile filtrarle in base alla sottoscrizione, ai gruppi di risorse e alle posizioni.  

1. Selezionare un'area di lavoro per visualizzarne le proprietà.

## <a name="delete-a-workspace"></a>Eliminazione di un'area di lavoro

Usare il pulsante Elimina nella parte superiore dell'area di lavoro da eliminare.

  ![Pulsante Elimina](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

Seguire l'esercitazione completa per apprendere come usare un'area di lavoro per creare, eseguire il training e distribuire modelli con Azure Machine Learning.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training dei modelli](tutorial-train-models-with-aml.md)
