---
title: Creare un'istanza di Azure Data Factory usando un modello di Azure Resource Manager (ARM)
description: Creare una pipeline di Azure Data Factory di esempio usando un modello di Azure Resource Manager (ARM).
services: data-factory
ms.service: data-factory
tags: azure-resource-manager
ms.workload: data-services
author: dcstwh
ms.author: weetok
ms.reviewer: maghan, jingwang
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 07/16/2020
ms.openlocfilehash: 0a9c4f6d9e98e721370f54dfbe261025cf186b6c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497487"
---
# <a name="quickstart-create-an-azure-data-factory-using-arm-template"></a>Avvio rapido: Creare un'istanza di Azure Data Factory con un modello di Azure Resource Manager

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Versione corrente](quickstart-create-data-factory-resource-manager-template.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo argomento di avvio rapido descrive come usare un modello di Azure Resource Manager (ARM) per creare un'istanza di Azure Data Factory. La pipeline creata in questa data factory **copia** dati da una cartella a un'altra in un archivio BLOB di Azure. Per un'esercitazione su come **trasformare** dati usando Azure Data Factory, vedere [Esercitazione: Trasformare i dati con Spark](transform-data-using-spark.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

> [!NOTE]
> Questo articolo non offre una presentazione dettagliata del servizio Data Factory. Per un'introduzione al servizio Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-subscription"></a>Sottoscrizione di Azure

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

### <a name="create-a-file"></a>Creare un file

Aprire un editor di testo come il **Blocco note** e creare un file denominato **emp.txt** con il contenuto seguente:

```emp.txt
John, Doe
Jane, Doe
```

Salvare il file nella cartella **C:\ADFv2QuickStartPSH**. Se la cartella non esiste, crearla.

## <a name="review-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-data-factory-v2-blob-to-blob-copy/).

:::code language="json" source="~/quickstart-templates/101-data-factory-v2-blob-to-blob-copy/azuredeploy.json":::

Nel modello sono definite risorse di Azure:

- [Microsoft.Storage/storageAccounts](/azure/templates/Microsoft.Storage/storageAccounts): definisce un account di archiviazione.
- [Microsoft.DataFactory/factories](/azure/templates/microsoft.datafactory/factories): crea un'istanza di Azure Data Factory.
- [Microsoft.DataFactory/factories/linkedServices](/azure/templates/microsoft.datafactory/factories/linkedservices): crea un servizio collegato Azure Data Factory.
- [Microsoft.DataFactory/factories/datasets](/azure/templates/microsoft.datafactory/factories/datasets): crea un'istanza di Azure Data Factory.
- [Microsoft.DataFactory/factories/pipelines](/azure/templates/microsoft.datafactory/factories/pipelines): crea una pipeline di Azure Data Factory.

Altri esempi di modelli di Azure Data Factory sono disponibili nella [raccolta di modelli di avvio rapido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea un account Azure Data Factory, un account di archiviazione e un contenitore BLOB.

    [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

2. Selezionare o immettere i valori seguenti.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-deploy-arm-template.png" alt-text="Distribuire un modello di Azure Resource Manager per Azure Data Factory":::

    Se non specificati, usare i valori predefiniti per creare le risorse di Azure Data Factory:

    - **Sottoscrizione** Selezionare una sottoscrizione di Azure.
    - **Gruppo di risorse**: selezionare **Crea nuovo**, immettere un nome univoco per il gruppo di risorse e quindi scegliere **OK**.
    - **Area**: Selezionare una località.  Ad esempio *Stati Uniti orientali*.
    - **Nome data factory**: usare il valore predefinito.
    - **Località**: usare il valore predefinito.
    - **Nome dell'account di archiviazione**: usare il valore predefinito.
    - **Contenitore BLOB**: usare il valore predefinito.

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

1. Selezionare **Vai al gruppo di risorse**.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-go-to-resource-group.png" alt-text="Gruppo di risorse":::

2.  Verificare che venga creata l'istanza di Azure Data Factory.
    1. Il nome dell'istanza di Azure Data Factory è nel formato - datafactory\<uniqueid\>.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-sample.png" alt-text="Data factory di esempio":::

2. Verificare che venga creato un account di archiviazione.
    1. Il nome dell'account di archiviazione è nel formato - storage\<uniqueid\>.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-storage-account.png" alt-text="Storage Account":::

3. Selezionare l'account di archiviazione creato e quindi selezionare **Contenitori**.
    1. Nella pagina **Contenitori** selezionare il contenitore BLOB creato.
        1. Il nome del contenitore BLOB è nel formato - blob\<uniqueid\>.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-blob-container.png" alt-text="Contenitore BLOB":::

### <a name="upload-a-file"></a>Caricare un file

1. Nella pagina **Contenitori** selezionare **Carica**.

2. Nel riquadro destro selezionare la casella **File** e quindi individuare e selezionare il file **emp.txt** creato in precedenza.

3. Espandere l'intestazione **Avanzate**.

4. Nella casella **Carica nella cartella** immettere *input*.

5. Selezionare il pulsante **Carica**. Verranno visualizzati il file **emp.txt** e lo stato del caricamento nell'elenco.

6. Selezionare l'icona **Chiudi** (una **X**) per chiudere la pagina **Carica BLOB**.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-upload-blob-file.png" alt-text="Caricare il file nella cartella di input":::

Lasciare aperta la pagina del contenitore perché è possibile usarla per verificare l'output alla fine di questo argomento di avvio rapido.

### <a name="start-trigger"></a>Avviare il trigger

1. Passare alla pagina **Data factory** e selezionare la data factory creata. 

2. Selezionare il riquadro **Crea e monitora**. 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author-monitor-tile.png" alt-text="Crea e monitora":::

2. Selezionare la scheda **Autore** :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author.png" border="false":::.

3. Selezionare la pipeline creata: ArmtemplateSampleCopyPipeline.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-pipelines.png" alt-text="Pipeline del modello di Azure Resource Manager":::

4. Selezionare **Aggiungi trigger** > **Trigger Now** (Attiva adesso).

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-trigger-now.png" alt-text="Trigger":::

5. Nel riquadro destro in **Pipeline run** (Esecuzione pipeline) scegliere **OK**.

### <a name="monitor-the-pipeline"></a>Monitorare la pipeline

1. Selezionare la scheda **Monitoraggio** :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-monitor.png" border="false":::.

2. Vengono visualizzate le esecuzioni di attività associate all'esecuzione della pipeline. In questa guida introduttiva la pipeline ha solo un'attività di tipo copia. Viene pertanto visualizzata un'esecuzione per tale attività.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-successful-run.png" alt-text="Esecuzione riuscita":::

### <a name="verify-the-output-file"></a>Verificare il file di output

La pipeline crea automaticamente una cartella di output nel contenitore BLOB. quindi copia il file emp.txt dalla cartella di input a quella di output. 

1. Nella pagina **Contenitori** nel portale di Azure selezionare **Aggiorna** per visualizzare la cartella di output. 

2. Nell'elenco delle cartelle selezionare **output**.

3. Verificare che **emp.txt** venga copiato nella cartella di output. 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-output.png" alt-text="Output":::

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile eseguire la pulizia delle risorse create nel corso della guida introduttiva in due modi. È possibile [eliminare il gruppo di risorse di Azure](../azure-resource-manager/management/delete-resource-group.md), che include tutte le risorse del gruppo. Se invece si vogliono mantenere intatte le altre risorse, eliminare solo la data factory creata in questa esercitazione.

Se si elimina un gruppo di risorse, vengono eliminate tutte le risorse in esso contenute, incluse le data factory. Eseguire il comando seguente per eliminare l'intero gruppo di risorse: 

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Per eliminare solo la data factory e non l'intero gruppo di risorse, eseguire questo comando: 

```azurepowershell-interactive
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata un'istanza di Azure Data Factory usando un modello di Azure Resource Manager ed è stata convalidata la distribuzione. Per altre informazioni su Azure Data Factory e Azure Resource Manager, continuare con gli articoli seguenti.

- [Documentazione di Azure Data Factory](index.yml)
- Vedere altre informazioni su [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Ottenere altri [modelli di Azure Resource Manager per Azure Data Factory](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular)