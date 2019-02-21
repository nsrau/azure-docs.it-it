---
title: Risolvere i problemi delle distribuzioni Resource Manager | Microsoft Docs
description: Informazioni su come monitorare le distribuzioni Resource Manager e risolverne i problemi.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 1a7a0604117dde55300a779bb1b7167c8da4e056
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56266765"
---
# <a name="tutorial-troubleshoot-resource-manager-template-deployments"></a>Esercitazione: Risolvere i problemi delle distribuzioni dei modelli di Resource Manager

Questa esercitazione illustra come risolvere gli errori nelle distribuzioni dei modelli di Resource Manager. Nel corso dell'esercitazione si configurano due errori in un modello e si apprende come usare i log attività e la cronologia di distribuzione per risolvere i problemi.

Alla distribuzione dei modelli sono correlati due tipi di errori:

- **Errori di convalida** causati da scenari che possono essere determinati prima della distribuzione. Tra di essi figurano gli errori di sintassi presenti in un modello oppure il tentativo di distribuire risorse superiori alle quote di sottoscrizione. 
- **Errori di distribuzione** causati da condizioni che si verificano durante il processo di distribuzione. Tra essi configurano i tentativi di accedere a una risorsa che viene distribuita in parallelo.

Entrambi i tipi di errore restituiscono un codice necessario per risolvere i problemi della distribuzione. Entrambi i tipi di errori compaiono nel registro attività. Gli errori di convalida non sono invece visualizzati nella cronologia della distribuzione, poiché quest'ultima non è mai stata avviata.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Creare un modello con errori
> * Risolvere gli errori di convalida
> * Risolvere gli errori relativi alla distribuzione
> * Pulire le risorse

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

- [Visual Studio Code](https://code.visualstudio.com/) con l'[estensione Strumenti di Azure Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="create-a-problematic-template"></a>Creare un modello con errori

Aprire un modello denominato [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Creare un account di archiviazione Standard) da [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/) e configurare due problemi nel modello.

1. In Visual Studio Code selezionare **File**>**Apri file**.
2. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Selezionare **Apri** per aprire il file.
4. Sostituire la riga **apiVersion** con la riga seguente:

    ```json
    "apiVersion1": "2018-07-02",
    ```
    - **apiVersion1** non è un nome di elemento valido. È un errore di convalida.
    - La versione dell'API deve essere "2018-07-01".  È un errore di distribuzione.

5. Selezionare **File**>**Salva con nome** per salvare il file con il nome **azuredeploy.json** nel computer locale.

## <a name="troubleshoot-the-validation-error"></a>Risolvere l'errore di convalida

Per distribuire il modello, vedere la sezione [Distribuire il modello](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template).

La shell restituirà un errore simile al seguente:

```
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

Il messaggio di errore indica che il problema riguarda **apiVersion1**.

Usare Visual Studio Code per risolvere il problema sostituendo **apiVersion1** con **apiVersion** e quindi salvare il modello.

## <a name="troubleshoot-the-deployment-error"></a>Risolvere l'errore di distribuzione

Per distribuire il modello, vedere la sezione [Distribuire il modello](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template).

La shell restituirà un errore simile al seguente:

```
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

È possibile trovare l'errore di distribuzione nel portale di Azure seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il gruppo di risorse selezionando **Gruppi di risorse** e quindi il nome del gruppo di risorse. Sotto **Distribuzioni** verrà visualizzato **1 operazioni non riuscite**.

    ![Esercitazione su Resource Manager: risoluzione dei problemi](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Selezionare **Dettagli errore**.

    ![Esercitazione su Resource Manager: risoluzione dei problemi](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    Il messaggio di errore è lo stesso visualizzato in precedenza:

    ![Esercitazione su Resource Manager: risoluzione dei problemi](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

L'errore si trova anche nei log attività:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Monitoraggio** > **Log attività**.
3. Usare i filtri per trovare il log.

    ![Esercitazione su Resource Manager: risoluzione dei problemi](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Usare Visual Studio Code per risolvere il problema e quindi distribuire di nuovo il modello.

Per un elenco degli errori comuni, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](./resource-manager-common-deployment-errors.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.  Nel gruppo di risorse verranno visualizzate in totale sei risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come risolvere gli errori nelle distribuzioni dei modelli di Resource Manager.  Per altre informazioni, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](./resource-manager-common-deployment-errors.md).
