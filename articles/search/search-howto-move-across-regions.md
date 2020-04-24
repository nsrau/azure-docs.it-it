---
title: Come spostare la risorsa del servizio tra aree
titleSuffix: Azure Cognitive Search
description: Questo articolo illustra come spostare le risorse del ricerca cognitiva di Azure da un'area a un'altra nel cloud di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 03/24/2020
ms.openlocfilehash: 00f16d11f7a9cd276772eda5e91d6e117ada8c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246303"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Spostare il servizio ricerca cognitiva di Azure in un'altra area di Azure

Occasionalmente, i clienti chiedono di trasferire un servizio di ricerca in un'altra area. Attualmente, non è disponibile alcun meccanismo o strumento incorporato per aiutare questa attività, ma questo articolo può aiutare a comprendere i passaggi manuali per ottenere lo stesso risultato.

> [!NOTE]
> Nel portale di Azure tutti i servizi dispongono di un comando **Esporta modello** . Nel caso di Azure ricerca cognitiva, questo comando genera una definizione di base di un servizio (nome, posizione, livello, replica e numero di partizioni), ma non riconosce il contenuto del servizio, né esegue il riporto di chiavi, ruoli o log. Sebbene il comando esista, non è consigliabile utilizzarlo per lo trasferimento di un servizio di ricerca.

## <a name="guidance-for-moving-a-service"></a>Linee guida per lo trasferimento di un servizio

1. Identificare le dipendenze e i servizi correlati per comprendere l'effetto completo della rilocazione di un servizio, nel caso in cui sia necessario spostare più di ricerca cognitiva di Azure.

   Archiviazione di Azure viene usato per la registrazione, la creazione di un archivio informazioni ed è un'origine dati esterna comunemente usata per l'arricchimento e l'indicizzazione di intelligenza artificiale. Servizi cognitivi è una dipendenza nell'arricchimento di intelligenza artificiale. È necessario che i servizi cognitivi e il servizio di ricerca si trovino nella stessa area se si usa l'arricchimento di intelligenza artificiale.

1. Creare un inventario di tutti gli oggetti nel servizio in modo da sapere cosa spostare: indici, mappe sinonimo, indicizzatori, origini dati, skillsets. Se è stata abilitata la registrazione, creare e archiviare i report che potrebbero essere necessari per un record cronologico.

1. Controlla i prezzi e la disponibilità nella nuova area per garantire la disponibilità di Azure ricerca cognitiva più tutti i servizi correlati nella nuova area. La maggior parte delle funzionalità è disponibile in tutte le aree, ma alcune funzionalità di anteprima hanno una disponibilità limitata.

1. Creazione di un servizio nella nuova area e ripubblicazione dal codice sorgente di eventuali indici, mappe sinonime, indicizzatori, origini dati e skillsets esistenti. Tenere presente che i nomi di servizio devono essere univoci, pertanto non è possibile riutilizzare il nome esistente. Controllare ogni competenze per verificare se le connessioni ai servizi cognitivi sono ancora valide in termini di requisito della stessa area. Inoltre, se vengono creati gli archivi delle informazioni, controllare le stringhe di connessione per archiviazione di Azure se si usa un servizio diverso.

1. Ricaricare gli indici e gli archivi delle informazioni, se applicabile. Si userà il codice dell'applicazione per eseguire il push dei dati JSON in un indice o rieseguire gli indicizzatori per estrarre i documenti da origini esterne. 

1. Abilitare la registrazione e, se vengono utilizzati, creare nuovamente i ruoli di sicurezza.

1. Aggiornare le applicazioni client e i gruppi di test per usare il nuovo nome del servizio e le chiavi API e testare tutte le applicazioni.

1. Elimina il vecchio servizio dopo che il nuovo servizio è stato completamente testato e funzionante.

## <a name="next-steps"></a>Passaggi successivi

I collegamenti seguenti consentono di trovare ulteriori informazioni quando si completano i passaggi illustrati in precedenza.

+ [Prezzi e aree di Azure ricerca cognitiva](https://azure.microsoft.com/pricing/details/search/)
+ [Scegliere un livello](search-sku-tier.md)
+ [Creare un servizio di ricerca](search-create-service-portal.md)
+ [Caricare i documenti di ricerca](search-what-is-data-import.md)
+ [Abilitazione della registrazione](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is whitelisted for the target region. For more information about preview features, see [knowledge stores](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [incremental enrichment](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual), and [private endpoint](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2020-03-13",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Create a skillset](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Create a knowledge store](https://docs.microsoft.com/azure/search/knowledge-store-create-portal) -->