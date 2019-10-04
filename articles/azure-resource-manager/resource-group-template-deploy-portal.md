---
title: Uso del portale di Azure per distribuire le risorse di Azure | Microsoft Docs
description: Utilizzare il portale di Azure e Azure Resource Manager per distribuire le risorse.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: tomfitz
ms.openlocfilehash: a171d9b4f054c942eebb08e7e11dd1164545f408
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460399"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure

Informazioni su come usare il [portale di Azure](https://portal.azure.com) con [Azure Resource Manager](resource-group-overview.md) per distribuire le risorse di Azure. Per altre informazioni sulla gestione delle risorse, vedere [la gestione di Azure Resource Manager usando il portale di Azure](manage-resources-portal.md).

Distribuzione di risorse di Azure tramite il portale di Azure in genere prevede due passaggi:

- Creare un gruppo di risorse.
- Distribuire le risorse al gruppo di risorse.

Inoltre, è anche possibile distribuire un modello di Azure Resource Manager per creare le risorse di Azure.

Questo articolo illustra entrambi i metodi.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

1. Per creare un nuovo gruppo di risorse, selezionare **gruppi di risorse** dalle [portale di Azure](https://portal.azure.com).

   ![Selezionare i gruppi di risorse](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. In Gruppi di risorse selezionare **Aggiungi**.

   ![Aggiungere il gruppo di risorse](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Selezionare o immettere i valori delle proprietà seguenti:

    - **Sottoscrizione** Selezionare una sottoscrizione di Azure.
    - **Gruppo di risorse**: Assegnare un nome del gruppo di risorse.
    - **Area**: Specificare una località di Azure. Si tratta in cui il gruppo di risorse archivia i metadati sulle risorse. Per motivi di conformità può essere opportuno specificare dove vengono archiviati i metadati. In generale è consigliabile specificare un percorso in cui risiederà la maggior parte delle risorse. Usando lo stesso percorso è possibile semplificare il modello.

   ![Impostare i valori del gruppo](./media/resource-group-template-deploy-portal/set-group-properties.png)

1. Selezionare **Rivedi e crea**.
1. Esaminare i valori e quindi selezionare **Create**.
1. Selezionare **Aggiorna** prima di poter visualizzare il nuovo gruppo di risorse nell'elenco.

## <a name="deploy-resources-to-a-resource-group"></a>Distribuire risorse in un gruppo di risorse

Dopo aver creato un gruppo di risorse, è possibile distribuire le risorse al gruppo da Marketplace. Marketplace fornisce soluzioni predefinite per scenari comuni.

1. Per avviare una distribuzione, selezionare **crea una risorsa** dalle [portale di Azure](https://portal.azure.com).

   ![Nuova risorsa](./media/resource-group-template-deploy-portal/new-resources.png)

1. Trovare il tipo di risorsa che si vuole distribuire. Le risorse sono organizzate in categorie. Se la specifica soluzione che si desidera distribuire non è visualizzata, è possibile cercarla nel Marketplace. Lo screenshot seguente mostra che sia selezionato Ubuntu Server.

   ![Selezionare il tipo di risorsa](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. A seconda del tipo di risorsa selezionato, sarà necessario impostare una serie di proprietà pertinenti prima della distribuzione. Per tutti i tipi è necessario selezionare un gruppo di risorse di destinazione. La figura seguente illustra come creare una macchina virtuale Linux e distribuirla al gruppo di risorse creato.

   ![Creare un gruppo di risorse](./media/resource-group-template-deploy-portal/select-existing-group.png)

   In alternativa, è possibile decidere di creare un gruppo di risorse durante la distribuzione delle risorse. Selezionare **Crea nuovo** e assegnare un nome al gruppo di risorse.

1. La distribuzione ha inizio. La distribuzione potrebbe richiedere alcuni minuti. Alcune risorse richiedono tempi più lunghi rispetto ad altre risorse. Al termine della distribuzione verrà visualizzata una notifica. Selezionare **Vai alla risorsa** aprire

   ![Visualizzare la notifica](./media/resource-group-template-deploy-portal/view-notification.png)

1. Dopo avere distribuito le risorse, è possibile aggiungerne altre al gruppo di risorse selezionando il comando **Aggiungi**.

   ![Aggiungere una risorsa](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Distribuire risorse da un modello personalizzato

Se si desidera eseguire una distribuzione ma non usare i modelli in Marketplace, è possibile creare un modello personalizzato che definisce l'infrastruttura per la soluzione. Per informazioni sulla creazione di modelli, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](resource-group-authoring-templates.md).

> [!NOTE]
> L'interfaccia del portale non supporta alcun riferimento a un [segreto dell'insieme di credenziali delle chiavi](resource-manager-keyvault-parameter.md). Usare invece [PowerShell](resource-group-template-deploy.md) oppure l'[interfaccia della riga di comando di Azure](resource-group-template-deploy-cli.md) per distribuire il modello in locale o da un URI esterno.

1. Per distribuire un modello personalizzato tramite il portale, selezionare **crea una risorsa**, cercare **modello**. e quindi selezionare **distribuzione modello**.

   ![Cercare la distribuzione del modello](./media/resource-group-template-deploy-portal/search-template.png)

1. Selezionare **Create**.
1. Verranno visualizzate diverse opzioni per la creazione di un modello:

    - **Compilare un modello personalizzato nell'editor**: creare un modello usando il modello del portale dell'editor.  L'editor è in grado di aggiungere uno schema di modello di risorsa.
    - **Modelli comuni**: Esistono quattro templatess comuni per la creazione di una macchina virtuale Linux, macchina virtuale Windows, un'applicazione web e un database SQL di Azure.
    - **Caricare un modello di avvio rapido di GitHub**: usare un oggetto esistente [modelli di avvio rapido](https://azure.microsoft.com/resources/templates/).

   ![Visualizzare le opzioni](./media/resource-group-template-deploy-portal/see-options.png)

    Questa esercitazione vengono fornite le istruzioni per il caricamento di un modello di avvio rapido.

1. Sotto **caricare un modello di avvio rapido di GitHub**digitare o selezionare **101-storage-account-create**.

    Sono disponibili due opzioni:

    - **Selezionare il modello**: distribuire il modello.
    - **Modifica modello**: modificare il modello di Guida introduttiva prima di distribuirlo.

1. Selezionare **modifica modello** per esplorare l'editor dei modelli del portale. Il modello viene caricato nell'editor. Si noti che sono presenti due parametri: **storageAccountType** e **posizione**.

   ![Creare il modello](./media/resource-group-template-deploy-portal/show-json.png)

1. Apportare una piccola modifica al modello. Ad esempio, aggiornare il **storageAccountName** variabile:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Selezionare **Salva**. È ora possibile visualizzare l'interfaccia di distribuzione del modello del portale. Si noti che i due parametri definiti nel modello.
1. Immettere o selezionare i valori delle proprietà:

    - **Sottoscrizione** Selezionare una sottoscrizione di Azure.
    - **Gruppo di risorse**: Selezionare **Crea nuovo** e assegnare un nome.
    - **Località**: Selezionare una località di Azure.
    - **Tipo di Account di archiviazione**: Usare il valore predefinito.
    - **Località**: Usare il valore predefinito.
    - **Accetto le condizioni riportate sopra**: selezionare.

1. Selezionare **Acquisto**.

## <a name="next-steps"></a>Passaggi successivi

- Per visualizzare i log di controllo, vedere [Operazioni di controllo con Resource Manager](./resource-group-audit.md).
- Per risolvere gli errori di distribuzione, vedere [View deployment operations](./resource-manager-deployment-operations.md) (Visualizzare le operazioni di distribuzione).
- Per esportare un modello da un gruppo di risorse o di distribuzione, vedere [esportazione di Azure Resource Manager templates](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Per distribuire in modo sicuro al servizio in più aree, vedere [Azure Deployment Manager](./deployment-manager-overview.md).
