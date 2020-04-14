---
title: Creare aree di lavoro di Azure Machine Learning nel portaleCreate Azure Machine Learning workspaces in the portal
titleSuffix: Azure Machine Learning
description: Informazioni su come creare, visualizzare ed eliminare aree di lavoro di Azure Machine Learning nel portale di Azure.Learn how to create, view, and delete Azure Machine Learning workspaces in the Azure portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: 4b4b64bcca57e1dc98cdba10626597532ae1461c
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269730"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Creare e gestire aree di lavoro di Azure Machine Learning nel portale di AzureCreate and manage Azure Machine Learning workspaces in the Azure portal
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo verranno create, visualizzate ed eliminate [**le aree**](concept-workspace.md) di lavoro di Azure Machine Learning nel portale di Azure per [Azure Machine Learning.](overview-what-is-azure-ml.md)  Il portale è il modo più semplice per iniziare a utilizzare le aree di lavoro, ma man mano che le esigenze cambiano o aumentano i requisiti per l'automazione è anche possibile creare ed eliminare aree di lavoro [utilizzando la CLI](reference-azure-machine-learning-cli.md), con codice [Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) o [tramite l'estensione del codice VS](tutorial-setup-vscode-extension.md).

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Per creare un'area di lavoro, è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

1. Accedere al [portale di Azure](https://portal.azure.com/) usando le credenziali della sottoscrizione di Azure. 

1. Nell'angolo in alto a sinistra del portale di Azure selezionare **+ Crea una risorsa**.

      ![Creare una nuova risorsa](./media/how-to-manage-workspace/create-workspace.gif)

1. Usare la barra di ricerca per trovare **Machine Learning**.

1. Selezionare **Machine Learning**.

1. Nel riquadro **Machine Learning** selezionare **Crea** per iniziare.

1. Specificare le informazioni seguenti per configurare la nuova area di lavoro:

   Campo|Descrizione 
   ---|---
   Nome dell'area di lavoro |Immettere un nome univoco che identifichi l'area di lavoro. In questo esempio si usa **docs-ws**. I nomi devono essere univoci all'interno del gruppo di risorse. Usare un nome facile da ricordare e da distinguere dai nomi delle aree di lavoro create da altri utenti. Il nome dell'area di lavoro non fa distinzione tra maiuscole e minuscole.
   Subscription |Selezionare la sottoscrizione di Azure da usare.
   Resource group | Usare un gruppo di risorse esistente nella sottoscrizione oppure immettere un nome per creare un nuovo gruppo di risorse. Un gruppo di risorse include risorse correlate per una soluzione Azure. In questo esempio si usa **docs-aml**. 
   Location | Selezionare la località più vicina agli utenti e alle risorse di dati per creare l'area di lavoro.
   Edizione dell'area di lavoro | Selezionare **Basic** o **Enterprise**.  Questa edizione dell'area di lavoro determina le funzionalità a cui si avrà accesso e prezzi. Ulteriori informazioni sulle [offerte basic ed Enterprise Edition](overview-what-is-azure-ml.md#sku). 

    ![Configurare l'area di lavoro](./media/how-to-manage-workspace/select-edition.png)

1. Al termine della configurazione dell'area di lavoro, selezionare **Rivedi e crea**.
2. Rivedere le impostazioni e apportare eventuali modifiche o correzioni aggiuntive. Quando si è soddisfatti delle impostazioni, selezionare **Crea**.

   > [!Warning] 
   > La creazione dell'area di lavoro nel cloud può richiedere diversi minuti.

   Al termine del processo verrà visualizzato un messaggio di conferma del completamento della distribuzione, 
 
 1. Per visualizzare la nuova area di lavoro, selezionare **Vai alla risorsa**.

### <a name="download-a-configuration-file"></a>Scaricare un file di configurazione

1. Se si creerà [un'istanza di calcolo,](tutorial-1st-experiment-sdk-setup.md#azure)ignorare questo passaggio.

1. Se si prevede di usare codice nell'ambiente locale che fa riferimento a questa area di lavoro, selezionare **Scarica config json** dalla sezione **Panoramica** dell'area di lavoro.  

   ![Scarica config.json](./media/how-to-manage-workspace/configure.png)
   
   Inserire il file nella struttura di directory che contiene gli script Python o i notebook di Jupyter. Può trattarsi della stessa directory, in una sottodirectory denominata *.azureml* o in una directory padre. Quando si crea un'istanza di calcolo, questo file viene aggiunto alla directory corretta nella macchina virtuale.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Eseguire l'aggiornamento all'edizione Enterprise

È possibile aggiornare l'area di lavoro dall'edizione Basic all'edizione Enterprise per sfruttare le funzionalità avanzate, ad esempio le esperienze con basso codice e le funzionalità di sicurezza avanzate.

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com).

1. Selezionare l'area di lavoro che si desidera aggiornare.

1. Seleziona **Ulteriori informazioni** in alto a destra nella pagina.

   [![Aggiornare](./media/how-to-manage-workspace/upgrade.png) un'area di lavoro](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. Selezionare **Aggiorna** nella finestra visualizzata.


> [!IMPORTANT]
> Non è possibile eseguire il downgrade di un'area di lavoro Enterprise Edition a un'area di lavoro edizione Basic. 

## <a name="find-a-workspace"></a><a name="view"></a>Trovare un'area di lavoro

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Nel campo di ricerca superiore digitare **Machine Learning**.  

1. Selezionare **Machine Learning**.

   ![Cercare l'area di lavoro di Azure Machine LearningSearch for Azure Machine Learning workspace](./media/how-to-manage-workspace/find-workspaces.png)

1. Esaminare l'elenco delle aree di lavoro trovate. È possibile filtrarle in base alla sottoscrizione, ai gruppi di risorse e alle posizioni.  

1. Selezionare un'area di lavoro per visualizzarne le proprietà.

## <a name="delete-a-workspace"></a>Eliminazione di un'area di lavoro

Nel [portale di Azure](https://portal.azure.com/)selezionare **Elimina** nella parte superiore dell'area di lavoro che si vuole eliminare.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Eliminare l'area di lavoro":::

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="resource-provider-errors"></a>Errori del provider di risorse

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Spostamento dell'area di lavoro

> [!WARNING]
> Lo spostamento dell'area di lavoro di Azure Machine Learning in una sottoscrizione diversa o lo spostamento della sottoscrizione proprietaria in un nuovo tenant non sono supportati. Questa operazione può causare errori.

### <a name="deleting-the-azure-container-registry"></a>Eliminazione del Registro di sistema del contenitore di AzureDeleting the Azure Container Registry

L'area di lavoro di Azure Machine Learning usa il Registro di sistema del contenitore di Azure (ACR) per alcune operazioni. Verrà creata automaticamente un'istanza ACR quando ne ha bisogno per la prima volta.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Passaggi successivi

Seguire l'esercitazione completa per informazioni su come usare un'area di lavoro per compilare, eseguire il training e distribuire modelli con Azure Machine Learning.Follow the full-length tutorial to learn how to use a workspace to build, train, and deploy models with Azure Machine Learning.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training dei modelli](tutorial-train-models-with-aml.md)
