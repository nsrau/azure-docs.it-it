---
title: Individuare le proprietà delle risorse
description: Viene descritto come cercare le proprietà delle risorse.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 75c089ac9d0ca08c788fea1ac9011f018ef0195e
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057076"
---
# <a name="discover-resource-properties"></a>Individuare le proprietà delle risorse

Prima di creare Gestione risorse modelli, è necessario comprendere quali tipi di risorse sono disponibili e quali valori usare nel modello. Questo articolo illustra alcuni modi in cui è possibile trovare le proprietà da includere nel modello.

## <a name="find-resource-provider-namespaces"></a>Trovare gli spazi dei nomi del provider di risorse

Le risorse in un modello ARM sono definite con uno spazio dei nomi del provider di risorse e un tipo di risorsa. Ad esempio, Microsoft. storage/storageAccounts è il nome completo del tipo di risorsa dell'account di archiviazione. Microsoft. storage è lo spazio dei nomi. Se non si conoscono già gli spazi dei nomi per i tipi di risorsa che si vuole usare, vedere [provider di risorse per i servizi di Azure](../management/azure-services-resource-providers.md).

![Mapping dello spazio dei nomi del provider di risorse Gestione risorse](./media/view-resources/resource-provider-namespace-and-azure-service-mapping.png)

## <a name="export-templates"></a>Esporta modelli

Il modo più semplice per ottenere le proprietà del modello per le risorse esistenti consiste nell'esportare il modello. Per altre informazioni, vedere [esportazione a più risorse e a più risorse in un modello nel portale di Azure](./export-template-portal.md).

## <a name="use-resource-manager-tools-extension"></a>Usa estensione strumenti Gestione risorse

Visual Studio Code e l'estensione strumenti di Azure Resource Manager consentono di vedere esattamente quali proprietà sono necessarie per ogni tipo di risorsa. Forniscono IntelliSense e frammenti di codice che semplificano la definizione di una risorsa nel modello. Per altre informazioni, vedere [Guida introduttiva: creare modelli di Azure Resource Manager con Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md#add-an-azure-resource).

La schermata seguente mostra una risorsa dell'account di archiviazione aggiunta a un modello:

![Frammenti di estensioni degli strumenti di Gestione risorse](./media/view-resources/resource-manager-tools-extension-snippets.png)

L'estensione fornisce anche un elenco di opzioni per le proprietà di configurazione.

![Valori configurabili dell'estensione strumenti di Gestione risorse](./media/view-resources/resource-manager-tools-extension-configurable-properties.png)

## <a name="use-template-reference"></a>Informazioni di riferimento sui modelli

Il riferimento al modello di Azure Resource Manager è la risorsa più completa per lo schema del modello. È possibile trovare le versioni dell'API, il formato del modello e le informazioni sulle proprietà.

1. Passare a [Azure Resource Manager riferimento al modello](/azure/templates/).
1. Nel percorso di spostamento a sinistra selezionare **archiviazione**e quindi selezionare **tutte le risorse**. Nella pagina tutte le risorse vengono riepilogati i tipi di risorse e le versioni.

    ![versioni delle risorse di riferimento del modello](./media/view-resources/resource-manager-template-reference-resource-versions.png)

    Se si conosce il tipo di risorsa, è possibile passare direttamente a questa pagina con il seguente formato di URL: `https://docs.microsoft.com/azure/templates/{provider-namespace}/{resource-type}` .

1. Selezionare la versione più recente. Si consiglia di usare la versione più recente dell'API.

    Nella sezione **formato modello** sono elencate tutte le proprietà per l'account di archiviazione. lo **SKU** è presente nell'elenco:

    ![formato dell'account di archiviazione di riferimento del modello](./media/view-resources/resource-manager-template-reference-storage-account-sku.png)

    Scorrere verso il basso fino a visualizzare l' **oggetto SKU** nella sezione **valori proprietà** . L'articolo mostra i valori consentiti per il nome dello SKU:

    ![riferimento ai modelli valori SKU dell'account di archiviazione](./media/view-resources/resource-manager-template-reference-storage-account-sku-values.png)

    Alla fine della pagina, nella sezione **modelli di avvio rapido** sono elencati alcuni modelli di avvio rapido di Azure che contengono il tipo di risorsa:

    ![modelli di avvio rapido dell'account di archiviazione di riferimento del modello](./media/view-resources/resource-manager-template-reference-quickstart-templates.png)

Il riferimento del modello è collegato da ognuno dei siti di documentazione del servizio di Azure.  Ad esempio, il [sito della documentazione Key Vault](../../key-vault/general/overview.md):

![Riferimento al modello di Gestione risorse Key Vault](./media/view-resources/resource-manager-template-reference-key-vault.png)

## <a name="use-resource-explorer"></a>USA Esplora inventario risorse

Esplora inventario risorse è incorporato nel portale di Azure. Prima di usare questo metodo, è necessario un account di archiviazione. Se non è presente, selezionare il pulsante seguente per crearne uno:

[![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella casella di ricerca immettere **Esplora inventario risorse**e quindi selezionare **Esplora inventario risorse**.

    ![portale di Azure Esplora inventario risorse](./media/view-resources/azure-portal-resource-explorer.png)

1. Da sinistra espandere **sottoscrizioni**, quindi espandere la sottoscrizione di Azure. L'account di archiviazione può essere trovato in **provider** o **ResourceGroups**.

    ![portale di Azure Esplora inventario risorse](./media/view-resources/azure-portal-resource-explorer-home.png)

    - **Provider**: espandere **provider**  ->  **Microsoft. storage**  ->  **storageAccounts**, quindi selezionare l'account di archiviazione.
    - **ResourceGroups**: selezionare il gruppo di risorse che contiene l'account di archiviazione, selezionare **risorse**e quindi selezionare l'account di archiviazione.

    Sulla destra viene visualizzata la configurazione dello SKU per l'account di archiviazione esistente, simile a:

    ![SKU dell'account di archiviazione portale di Azure Esplora inventario risorse](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="use-resourcesazurecom"></a>Usare Resources.azure.com

Resources.azure.com è un sito Web pubblico a cui è possibile accedere da qualsiasi utente con una sottoscrizione di Azure. È in anteprima.  In alternativa, provare a usare [Esplora inventario risorse](#use-resource-explorer) . Questo strumento offre le funzionalità seguenti:

- Individuare le API di Azure Resource Manager.
- Ottenere informazioni sullo schema e sulla documentazione dell'API.
- Effettuare chiamate API direttamente nelle sottoscrizioni personali.

Per illustrare come recuperare le informazioni sullo schema usando questo strumento, è necessario un account di archiviazione. Se non è presente, selezionare il pulsante seguente per crearne uno:

[![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. Passare a [Resources.Azure.com](https://resources.azure.com/). Sono necessari alcuni istanti per lo strumento per popolare il riquadro sinistro.
1. Selezionare **sottoscrizioni**.

    ![mapping dell'API resource.azure.com](./media/view-resources/resources-azure-com-api-mapping.png)

    Il nodo a sinistra corrisponde alla chiamata API a destra. È possibile effettuare la chiamata all'API selezionando il pulsante **Get** .
1. Da sinistra espandere **sottoscrizioni**, quindi espandere la sottoscrizione di Azure. L'account di archiviazione può essere trovato in **provider** o **ResourceGroups**.

    - **Provider**: espandere **provider**  ->  **Microsoft. storage**  ->  **storageAccounts**, quindi passare all'account di archiviazione.
    - **ResourceGroups**: selezionare il gruppo di risorse che contiene l'account di archiviazione e quindi selezionare **risorse**.

    Sulla destra viene visualizzata la configurazione dello SKU per l'account di archiviazione esistente, simile a:

    ![SKU dell'account di archiviazione portale di Azure Esplora inventario risorse](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come trovare le informazioni sullo schema del modello. Per altre informazioni sulla creazione di modelli di Gestione risorse, vedere [comprendere la struttura e la sintassi dei modelli ARM](./template-syntax.md).
