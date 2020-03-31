---
title: Distribuire risorse con il portale di AzureDeploy resources with Azure portal
description: Usare il portale di Azure e Gestione risorse di Azure per distribuire le risorse in un gruppo di risorse nella sottoscrizione.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: ea2faf810b3a5d1b8fa46575201022a501cc7d58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153438"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>Distribuire risorse con modelli ARM e portale di AzureDeploy resources with ARM templates and Azure portal

Informazioni su come usare il portale di Azure con i modelli di Azure Resource Manager (ARM) per distribuire le risorse di Azure.Learn how to use the [Azure portal](https://portal.azure.com) with Azure Resource Manager [(ARM) templates](overview.md) to deploy your Azure resources. Per altre informazioni sulla gestione delle risorse, vedere Gestire le risorse di Azure tramite il portale di Azure.To learn about managing your resources, see [Manage Azure resources by using the Azure portal.](../management/manage-resources-portal.md)

La distribuzione di risorse di Azure tramite il portale di Azure prevede in genere due passaggi:Deploying Azure resources by using the Azure portal usually involves two steps:

- Creare un gruppo di risorse.
- Distribuire le risorse nel gruppo di risorse.

Inoltre, è anche possibile distribuire un modello ARM per creare risorse di Azure.In addition, you can also deploy an ARM template to create Azure resources.

Questo articolo illustra entrambi i metodi.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

1. Per creare un nuovo gruppo di risorse, selezionare Gruppi di risorse dal portale di Azure.To create a new resource **group,** select Resource groups from the [Azure portal](https://portal.azure.com).

   ![Selezionare i gruppi di risorse](./media/deploy-portal/select-resource-groups.png)

1. In Gruppi di risorse selezionare **Aggiungi**.

   ![Aggiungere il gruppo di risorse](./media/deploy-portal/add-resource-group.png)

1. Selezionare o immettere i seguenti valori di proprietà:

    - **Sottoscrizione:** selezionare una sottoscrizione di Azure.Subscription : Select an Azure subscription.
    - **Gruppo di risorse:** assegnare un nome al gruppo di risorse.
    - **Area**: Specificare un percorso di Azure.Region : Specify an Azure location. Si tratta in cui il gruppo di risorse archivia i metadati relativi alle risorse. Per motivi di conformità può essere opportuno specificare dove vengono archiviati i metadati. In generale è consigliabile specificare un percorso in cui risiederà la maggior parte delle risorse. Usando lo stesso percorso è possibile semplificare il modello.

   ![Impostare i valori del gruppo](./media/deploy-portal/set-group-properties.png)

1. Selezionare **Revisione e creazione**.
1. esaminare i valori e quindi selezionare **Crea**.
1. Selezionare **Aggiorna** prima di visualizzare il nuovo gruppo di risorse nell'elenco.

## <a name="deploy-resources-to-a-resource-group"></a>Distribuire risorse in un gruppo di risorseDeploy resources to a resource group

Dopo aver creato un gruppo di risorse, è possibile distribuire le risorse al gruppo dal Marketplace.After you create a resource group, you can deploy resources to the group from the Marketplace. Marketplace fornisce soluzioni predefinite per scenari comuni.

1. Per avviare una distribuzione, selezionare Crea una risorsa dal portale di Azure.To start a **deployment,** select Create a resource from the [Azure portal](https://portal.azure.com).

   ![Nuova risorsa](./media/deploy-portal/new-resources.png)

1. Trovare il tipo di risorsa che si vuole distribuire. Le risorse sono organizzate in categorie. Se la specifica soluzione che si desidera distribuire non è visualizzata, è possibile cercarla nel Marketplace. La schermata seguente mostra che Ubuntu Server è selezionato.

   ![Selezionare il tipo di risorsa](./media/deploy-portal/select-resource-type.png)

1. A seconda del tipo di risorsa selezionato, sarà necessario impostare una serie di proprietà pertinenti prima della distribuzione. Per tutti i tipi è necessario selezionare un gruppo di risorse di destinazione. L'immagine seguente mostra come creare una macchina virtuale Linux e distribuirla al gruppo di risorse creato.

   ![Creare un gruppo di risorse](./media/deploy-portal/select-existing-group.png)

   In alternativa, è possibile decidere di creare un gruppo di risorse durante la distribuzione delle risorse. Selezionare **Crea nuovo** e assegnare un nome al gruppo di risorse.

1. La distribuzione ha inizio. La distribuzione potrebbe richiedere alcuni minuti. Alcune risorse richiedono più tempo rispetto ad altre risorse. Al termine della distribuzione verrà visualizzata una notifica. Selezionare **Vai alla risorsa** per aprire

   ![Visualizzare la notifica](./media/deploy-portal/view-notification.png)

1. Dopo avere distribuito le risorse, è possibile aggiungerne altre al gruppo di risorse selezionando il comando **Aggiungi**.

   ![Aggiungere una risorsa](./media/deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Distribuire risorse da un modello personalizzato

Se si desidera eseguire una distribuzione ma non usare i modelli in Marketplace, è possibile creare un modello personalizzato che definisce l'infrastruttura per la soluzione. Per informazioni sulla creazione di modelli, vedere Informazioni sulla struttura e la [sintassi dei modelli ARM](template-syntax.md).

> [!NOTE]
> L'interfaccia del portale non supporta alcun riferimento a un [segreto dell'insieme di credenziali delle chiavi](key-vault-parameter.md). Usare invece [PowerShell](deploy-powershell.md) oppure l'[interfaccia della riga di comando di Azure](deploy-cli.md) per distribuire il modello in locale o da un URI esterno.

1. Per distribuire un modello personalizzato tramite il portale, selezionare **Crea una risorsa**, cercare **modello**. , quindi selezionare **Distribuzione modello**.

   ![Cercare la distribuzione del modello](./media/deploy-portal/search-template.png)

1. Selezionare **Crea**.
1. Vengono visualizzate diverse opzioni per la creazione di un modello:

    - **Creare un modello personalizzato nell'editor:** creare un modello usando l'editor di modelli del portale.  L'editor è in grado di aggiungere uno schema del modello di risorsa.
    - **Modelli comuni:** sono disponibili quattro modelli comuni per la creazione di una macchina virtuale Linux, una macchina virtuale Windows, un'applicazione Web e un database SQL di Azure.Common templates : There are four common templates for creating a Linux virtual machine, Windows virtual machine, a web application, and an Azure SQL database.
    - Caricare un modello di **avvio rapido GitHub:** usare un modello di [avvio rapido](https://azure.microsoft.com/resources/templates/)esistente.

   ![Visualizzare le opzioni](./media/deploy-portal/see-options.png)

    Questa esercitazione fornisce le istruzioni per il caricamento di un modello di avvio rapido.

1. In Carica un modello di **avvio rapido GitHub**digitare o selezionare **101-storage-account-create**.

    Sono disponibili due opzioni:

    - **Seleziona modello**: distribuire il modello.
    - **Modifica modello**: modificare il modello di avvio rapido prima di distribuirlo.

1. Selezionare **Modifica modello** per esplorare l'editor dei modelli del portale. Il modello viene caricato nell'editor. Si noti che sono presenti due parametri: **storageAccountType** e **location**.

   ![Creare il modello](./media/deploy-portal/show-json.png)

1. Apportare una modifica minore al modello. Ad esempio, aggiornare la variabile **storageAccountName** a:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Selezionare **Salva**. Viene ora visualizzata l'interfaccia di distribuzione del modello del portale. Si notino i due parametri definiti nel modello.
1. Immettere o selezionare i valori delle proprietà:

    - **Sottoscrizione:** selezionare una sottoscrizione di Azure.Subscription : Select an Azure subscription.
    - **Gruppo di risorse:** selezionare **Crea nuovo** e assegnare un nome.
    - **Posizione**: selezionare un percorso di Azure.Location : Select an Azure location.
    - **Storage Account Type (Tipo di account di archiviazione):** usare il valore predefinito.
    - **Posizione**: utilizzare il valore predefinito.
    - **Accetto le condizioni riportate sopra**: selezionare.

1. Selezionare **Acquista**.

## <a name="next-steps"></a>Passaggi successivi

- Per visualizzare i log di controllo, vedere [Operazioni di controllo con Resource Manager](../management/view-activity-logs.md).
- Per risolvere gli errori di distribuzione, vedere [View deployment operations](deployment-history.md) (Visualizzare le operazioni di distribuzione).
- Per esportare un modello da una distribuzione o da un gruppo di risorse, vedere [Esportare modelli ARM](export-template-portal.md).
- Per distribuire in modo sicuro il servizio in più aree, vedere Gestione distribuzione di Azure.To safely roll out your service across multiple regions, see [Azure Deployment Manager.](deployment-manager-overview.md)
