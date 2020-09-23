---
title: Creare aree di lavoro nel portale
titleSuffix: Azure Machine Learning
description: Informazioni su come creare, visualizzare ed eliminare aree di lavoro Azure Machine Learning nel portale di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 38784b006acac4c3ff70b2aa3c38648e939eddeb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90889922"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Creare e gestire le aree di lavoro di Azure Machine Learning nel portale di Azure


Questo articolo illustra come creare, visualizzare ed eliminare [**Azure Machine Learning aree di lavoro**](concept-workspace.md) nel portale di Azure per [Azure Machine Learning](overview-what-is-azure-ml.md).  Il portale è il modo più semplice per iniziare a usare le aree di lavoro, ma in base alle esigenze modificate o ai requisiti per l'aumento dell'automazione è anche possibile creare ed eliminare aree di lavoro [tramite l'interfaccia della](reference-azure-machine-learning-cli.md)riga di comando, [con codice Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) o [tramite l'estensione vs code](tutorial-setup-vscode-extension.md).

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
   Nome dell'area di lavoro |Immettere un nome univoco che identifichi l'area di lavoro. In questo esempio si usa **docs-ws**. I nomi devono essere univoci all'interno del gruppo di risorse. Usare un nome facile da ricordare e da distinguere dai nomi delle aree di lavoro create da altri utenti. Il nome dell'area di lavoro non rileva la distinzione tra maiuscole e minuscole.
   Subscription |Selezionare la sottoscrizione di Azure da usare.
   Resource group | Usare un gruppo di risorse esistente nella sottoscrizione oppure immettere un nome per creare un nuovo gruppo di risorse. Un gruppo di risorse include risorse correlate per una soluzione Azure. In questo esempio si usa **docs-aml**. Per usare un gruppo di risorse esistente, è necessario un ruolo di *collaboratore* o *proprietario* .  Per ulteriori informazioni sull'accesso, vedere [gestire l'accesso a un'area di lavoro Azure Machine Learning](how-to-assign-roles.md).
   Location | Selezionare la località più vicina agli utenti e alle risorse di dati per creare l'area di lavoro.
   Location | Selezionare la località più vicina agli utenti e alle risorse di dati per creare l'area di lavoro.

    ![Configurare l'area di lavoro](./media/how-to-manage-workspace/select-edition.png)

1. Al termine della configurazione dell'area di lavoro, selezionare **Verifica + crea**.
2. Rivedere le impostazioni e apportare eventuali modifiche o correzioni aggiuntive. Quando si è soddisfatti delle impostazioni, selezionare **Crea**.

   > [!Warning] 
   > La creazione dell'area di lavoro nel cloud può richiedere diversi minuti.

   Al termine del processo verrà visualizzato un messaggio di conferma del completamento della distribuzione, 
 
 1. Per visualizzare la nuova area di lavoro, selezionare **Vai alla risorsa**.

### <a name="download-a-configuration-file"></a>Scaricare un file di configurazione

1. Se si vuole creare un' [istanza di calcolo](tutorial-1st-experiment-sdk-setup.md#azure), ignorare questo passaggio.

1. Se si prevede di usare codice nell'ambiente locale che fa riferimento a questa area di lavoro, selezionare **Scarica config json** dalla sezione **Panoramica** dell'area di lavoro.  

   ![Scarica config.json](./media/how-to-manage-workspace/configure.png)
   
   Inserire il file nella struttura di directory che contiene gli script Python o i notebook di Jupyter. Può trattarsi della stessa directory, in una sottodirectory denominata *.azureml* o in una directory padre. Quando si crea un'istanza di calcolo, questo file viene aggiunto alla directory corretta nella macchina virtuale.
## <a name="find-a-workspace"></a><a name="view"></a>Trovare un'area di lavoro

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Nel campo di ricerca superiore digitare **Machine Learning**.  

1. Selezionare **Machine Learning**.

   ![Cerca area di lavoro Azure Machine Learning](./media/how-to-manage-workspace/find-workspaces.png)

1. Esaminare l'elenco delle aree di lavoro trovate. È possibile filtrarle in base alla sottoscrizione, ai gruppi di risorse e alle posizioni.  

1. Selezionare un'area di lavoro per visualizzarne le proprietà.

## <a name="delete-a-workspace"></a>Eliminare un'area di lavoro

Nella [portale di Azure](https://portal.azure.com/)selezionare **Elimina**  nella parte superiore dell'area di lavoro che si vuole eliminare.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Elimina area di lavoro":::

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="resource-provider-errors"></a>Errori del provider di risorse

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Spostamento dell'area di lavoro

> [!WARNING]
> Lo spostamento dell’area di lavoro di Azure Machine Learning in una diversa sottoscrizione o della sottoscrizione proprietaria su un nuovo tenant non è supportato in quanto ciò può provocare errori.

### <a name="deleting-the-azure-container-registry"></a>Eliminazione del Registro Azure Container

L'area di lavoro di Azure Machine Learning usa il Registro Azure Container (ACR) per alcune operazioni. Verrà creata automaticamente un'istanza di ACR quando è necessaria per la prima volta.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Passaggi successivi

Seguire l'esercitazione completa per apprendere come usare un'area di lavoro per creare, eseguire il training e distribuire modelli con Azure Machine Learning.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training dei modelli](tutorial-train-models-with-aml.md)
