---
title: Come spostare la risorsa del servizio tra aree
titleSuffix: Azure Cognitive Search
description: Questo articolo illustra come spostare le risorse del ricerca cognitiva di Azure da un'area a un'altra nel cloud di Azure.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/18/2020
ms.openlocfilehash: 392c86d8ea24e59d388926d4df581305ea2b531d
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599301"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Spostare il servizio ricerca cognitiva di Azure in un'altra area di Azure

Per spostare l'account del servizio cognitivo di Azure da un'area all'altra, si creerà un modello di esportazione per spostare le sottoscrizioni. Dopo lo spostamento della sottoscrizione, sarà necessario spostare i dati e ricreare il servizio.

In questo articolo si apprenderà come:

> [!div class="checklist"]
> * Esportare un modello.
> * Modificare il modello: aggiunta dell'area di destinazione, dei nomi degli account di archiviazione e di ricerca.
> * Distribuire il modello per creare i nuovi account di archiviazione e di ricerca.
> * Verificare lo stato del servizio nella nuova area
> * Pulire le risorse nell'area di origine.

## <a name="prerequisites"></a>Prerequisites

- Verificare che i servizi e le funzionalità usati dall'account siano supportati nell'area di destinazione.

- Per le funzionalità di anteprima, assicurarsi che la sottoscrizione sia consentita per l'area di destinazione. Per ulteriori informazioni sulle funzionalità in anteprima, vedere [Knowledge Store](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [arricchimento incrementale](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual)e [endpoint privato](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## <a name="assessment-and-planning"></a>Valutazione e pianificazione

Quando si sposta il servizio di ricerca nella nuova area, sarà necessario spostare i [dati nel nuovo servizio di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) e quindi ricompilare gli indici, skillsets e gli archivi delle informazioni. È necessario registrare le impostazioni correnti e copiare i file JSON per semplificare e velocizzare la ricompilazione del servizio.

## <a name="moving-your-search-services-resources"></a>Trasferimento delle risorse del servizio di ricerca

Per iniziare, si esporterà e quindi si modificherà un modello di Gestione risorse.

### <a name="export-a-template"></a>Esportare un modello

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Passare alla pagina del gruppo di risorse.

> [!div class="mx-imgBorder"]
> esempio di pagina del gruppo di risorse ![](./media/search-move-resource/export-template-sample.png)

3. Selezionare **Tutte le risorse**.

3. Nel menu di spostamento a sinistra selezionare **Esporta modello**.

4. Scegliere **download** nella pagina **Esporta modello** .

5. Individuare il file zip scaricato dal portale e decomprimere il file in una cartella di propria scelta.

Il file zip contiene i file con estensione JSON che comprendono il modello e gli script per distribuire il modello.

### <a name="modify-the-template"></a>Modificare il modello

Si modificherà il modello cambiando le aree e i nomi degli account di archiviazione e di ricerca. I nomi devono rispettare le regole per le convenzioni di denominazione di ogni servizio e area. 

Per ottenere i codici di posizione dell'area, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Il codice per un'area è il nome dell'area senza spazi, **Stati Uniti centrali** = **centralus**.

1. Nel portale di Azure fare clic su **Crea una risorsa**.

2. In **Cerca nel Marketplace** digitare **distribuzione modelli** e quindi premere **INVIO**.

3. Selezionare **Distribuzione modello**.

4. Selezionare **Create** (Crea).

5. Selezionare **Creare un modello personalizzato nell'editor**.

6. Selezionare **Carica file**e quindi seguire le istruzioni per caricare il file **template. JSON** scaricato e decompresso nella sezione precedente.

7. Nel file **template. JSON** , denominare gli account di archiviazione e di ricerca di destinazione impostando il valore predefinito dei nomi degli account di archiviazione e di ricerca. 

8. Modificare la proprietà **location** nel file **template. JSON** nell'area di destinazione per i servizi di ricerca e archiviazione. In questo esempio l'area di destinazione viene impostata su `centralus`.

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

### <a name="deploy-the-template"></a>Distribuire il modello

1. Salvare il file **template. JSON** .

2. Immettere o selezionare i valori delle proprietà:

- **Sottoscrizione**: selezionare una sottoscrizione di Azure.

- **Gruppo di risorse**: selezionare **Crea nuovo** e assegnare un nome al gruppo di risorse.

- **Località**: selezionare una località di Azure.

3. Fare clic sulla casella di controllo Accetto **i termini e le condizioni indicati in precedenza** , quindi fare clic sul pulsante **Seleziona acquisto** .

## <a name="verifying-your-services-status-in-new-region"></a>Verifica dello stato dei servizi in una nuova area

Per verificare lo spostamento, aprire il nuovo gruppo di risorse e i servizi verranno elencati con la nuova area.

Per spostare i dati dall'area di origine all'area di destinazione, vedere le linee guida di questo articolo per [lo spostamento dei dati nel nuovo account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## <a name="clean-up-resources-in-your-original-region"></a>Pulire le risorse nell'area di origine

Per eseguire il commit delle modifiche e completare lo spostamento dell'account del servizio, eliminare l'account del servizio di origine.

## <a name="next-steps"></a>Passaggi successivi

[Creare un indice](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Creare un set di competenze](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Creazione di un archivio informazioni](https://docs.microsoft.com/azure/search/knowledge-store-create-portal)

