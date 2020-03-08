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
ms.date: 03/06/2020
ms.openlocfilehash: 183a937a232dbd28962bb7d6ef42b0d78b8a81fd
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78850687"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Spostare il servizio ricerca cognitiva di Azure in un'altra area di Azure

Occasionalmente, i clienti hanno la richiesta di trasferire un servizio di ricerca esistente in un'altra area. Attualmente, non sono disponibili meccanismi o strumenti predefiniti per semplificare l'attività. Rimane un processo manuale, illustrato di seguito in questo articolo.

> [!NOTE]
> Nel portale di Azure tutti i servizi dispongono di un comando **Esporta modello** . Nel caso di Azure ricerca cognitiva, questo comando genera una definizione di base di un servizio (nome, posizione, livello, replica e numero di partizioni), ma non riconosce il contenuto del servizio, né esegue il riporto di chiavi, ruoli o log. Sebbene il comando esista, non è consigliabile utilizzarlo per lo trasferimento di un servizio di ricerca.

## <a name="steps-for-moving-a-service"></a>Passaggi per lo stato di trasferimento di un servizio

Se è necessario spostare un servizio di ricerca in un'area diversa, l'approccio dovrebbe essere simile ai passaggi seguenti:

1. Identificare i servizi correlati per comprendere l'effetto completo della rilocazione di un servizio. È possibile usare archiviazione di Azure per la registrazione, l'archivio delle informazioni o come origine dati esterna. È possibile usare servizi cognitivi per l'arricchimento di intelligenza artificiale. L'accesso ai servizi in altre aree è comune, ma viene addebitato un costo aggiuntivo per la larghezza di banda. I servizi cognitivi e ricerca cognitiva di Azure devono trovarsi nella stessa area se si usa l'arricchimento di intelligenza artificiale.

1. Inventario del servizio esistente per un elenco completo di oggetti nel servizio. Se è stata abilitata la registrazione, creare e archiviare i report che potrebbero essere necessari per un record cronologico.

1. Controllare i prezzi e la disponibilità nella nuova area per garantire la disponibilità di Azure ricerca cognitiva più tutti i servizi correlati che è possibile creare nella stessa area. Verificare la parità di funzionalità. Alcune funzionalità di anteprima hanno una disponibilità limitata.

1. Creare un servizio nella nuova area e ripubblicarlo dal codice sorgente di eventuali indici, indicizzatori, origini dati, skillsets, archivi informazioni e mappe sinonimi esistenti. I nomi dei servizi devono essere univoci, pertanto non è possibile riutilizzare il nome esistente.

1. Ricaricare gli indici e gli archivi delle informazioni, se applicabile. Si userà il codice dell'applicazione per eseguire il push dei dati JSON in un indice o rieseguire gli indicizzatori per estrarre i documenti da origini esterne. 

1. Abilitare la registrazione e, se vengono utilizzati, creare nuovamente i ruoli di sicurezza.

1. Aggiornare le applicazioni client e i gruppi di test per usare il nuovo nome del servizio e le chiavi API e testare tutte le applicazioni.

1. Elimina il vecchio servizio dopo che il nuovo servizio è stato completamente testato e funzionante.

## <a name="next-steps"></a>Passaggi successivi

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
            "apiVersion": "2015-08-19",
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