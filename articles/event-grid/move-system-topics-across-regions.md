---
title: Spostare gli argomenti del sistema di griglia di eventi di Azure in un'altra area
description: Questo articolo illustra come spostare gli argomenti del sistema di griglia di eventi di Azure da un'area a un'altra.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: eb6029b206e7d47789371ee81e75c4e05c69ee65
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89086185"
---
# <a name="move-azure-event-grid-system-topics-to-another-region"></a>Spostare gli argomenti del sistema di griglia di eventi di Azure in un'altra area
Potrebbe essere necessario spostare le risorse in un'altra area per diversi motivi. Ad esempio, per sfruttare i vantaggi di una nuova area di Azure, per soddisfare i requisiti di governance e criteri interni oppure in risposta ai requisiti della pianificazione della capacità. 

Di seguito sono riportati i passaggi di alto livello trattati in questo articolo: 

- **Esportare il gruppo di risorse** che contiene l'account di archiviazione di Azure e il relativo argomento di sistema associato in un modello di gestione risorse. È anche possibile esportare un modello solo per l'argomento di sistema. Se si passa a questa route, ricordarsi di spostare l'origine evento di Azure (in questo esempio un account di archiviazione di Azure) nell'altra area prima di spostare l'argomento di sistema. Quindi, nel modello esportato per l'argomento di sistema, aggiornare l'ID esterno per l'account di archiviazione nell'area di destinazione. 
- **Modificare il modello** per aggiungere la `endpointUrl` Proprietà in modo che punti a un webhook che sottoscrive l'argomento di sistema. Quando l'argomento di sistema viene esportato, la relativa sottoscrizione (in questo caso, si tratta di un webhook) viene anche esportata nel modello, ma la `endpointUrl` proprietà non è inclusa. Quindi, è necessario aggiornarlo in modo che punti all'endpoint che sottoscrive l'argomento. Aggiornare anche il valore della `location` proprietà alla nuova posizione o all'area geografica. Per altri tipi di gestori eventi, è necessario aggiornare solo il percorso. 
- **Usare il modello per distribuire le risorse** nell'area di destinazione. Verranno specificati i nomi per l'account di archiviazione e l'argomento di sistema da creare nell'area di destinazione. 
- **Verificare la distribuzione**. Verificare che il webhook venga richiamato quando si carica un file nell'archiviazione BLOB nell'area di destinazione. 
- Per **completare l'operazione di spostamento**, eliminare le risorse (origine evento e argomento di sistema) dall'area di origine. 

## <a name="prerequisites"></a>Prerequisiti
- Completare la [Guida introduttiva: indirizzare gli eventi di archiviazione BLOB all'endpoint Web con il portale di Azure](blob-event-quickstart-portal.md) nell'area di origine. Questo passaggio è **facoltativo**. Eseguire questa operazione per testare i passaggi di questo articolo. Conservare l'account di archiviazione in un gruppo di risorse distinto dal servizio app e dal piano di servizio app. 
- Verificare che il servizio griglia di eventi sia disponibile nell'area di destinazione. Vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Preparazione
Per iniziare, esportare un modello di Gestione risorse per il gruppo di risorse che contiene l'origine evento di sistema (account di archiviazione di Azure) e il relativo argomento di sistema associato. 

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Scegliere **gruppi di risorse** dal menu a sinistra. Selezionare quindi il gruppo di risorse contenente l'origine evento per cui è stato creato l'argomento di sistema. Nell'esempio seguente si tratta dell'account di **archiviazione di Azure** . Il gruppo di risorse contiene l'account di archiviazione e il relativo argomento di sistema associato. 

    :::image type="content" source="./media/move-system-topics-across-regions/resource-group-page.png" alt-text="Pagina gruppo di risorse":::        
3. Nel menu a sinistra selezionare **Esporta modello** in **Impostazioni**e quindi fare clic su **Scarica** sulla barra degli strumenti. 

    :::image type="content" source="./media/move-system-topics-across-regions/export-template-menu.png" alt-text="Account limite archiviazione-pagina Esporta modello":::        
5. Individuare il file **zip** scaricato dal portale e decomprimere il file in una cartella di propria scelta. Questo file zip contiene i file JSON del modello e dei parametri. 
1. Aprire il **template.js** in un editor di propria scelta. 
1. L'URL per il webhook non viene esportato nel modello. Quindi, attenersi alla procedura seguente:
    1. Nel file modello cercare **webhook**. 
    1. Nella sezione **Proprietà** aggiungere un carattere virgola ( `,` ) alla fine dell'ultima riga. In questo esempio è `"preferredBatchSizeInKilobytes": 64`. 
    1. Aggiungere la `endpointUrl` proprietà con il valore impostato sull'URL del webhook, come illustrato nell'esempio seguente. 

        ```json
        "destination": {
            "properties": {
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64,
                "endpointUrl": "https://mysite.azurewebsites.net/api/updates"
            },
            "endpointType": "WebHook"
        }
        ```

        > [!NOTE]
        > Per gli altri tipi di gestori eventi, tutte le proprietà vengono esportate nel modello. È sufficiente aggiornare la proprietà nell' `location` area di destinazione, come illustrato nel passaggio successivo. 
7. Aggiornare `location` per la risorsa dell' **account di archiviazione** nell'area o nel percorso di destinazione. Per ottenere i codici di località, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/). Il codice per un'area è il nome dell'area senza spazi, ad esempio, `West US` è uguale a `westus` .

    ```json
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccounts_spegridstorage080420_name')]",
    "location": "westus",
    ```
8. Ripetere il passaggio per eseguire l'aggiornamento `location` per la risorsa **argomento di sistema** nel modello. 

    ```json
    "type": "Microsoft.EventGrid/systemTopics",
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('systemTopics_spegridsystopic080420_name')]",
    "location": "westus",
    ```
1. **Salvare** il modello. 

## <a name="recreate"></a>Ricreare 
Distribuire il modello per creare un account di archiviazione e un argomento di sistema per l'account di archiviazione nell'area di destinazione. 

1. Nella portale di Azure selezionare **Crea una risorsa**.
2. In **Cerca nel Marketplace**Digitare **distribuzione modello**, quindi premere **invio**.
3. Selezionare **distribuzione modelli**.
4. Selezionare **Create** (Crea).
5. Selezionare **Creare un modello personalizzato nell'editor**.
6. Selezionare **Carica file**e quindi seguire le istruzioni per caricare il **template.js** nel file scaricato nell'ultima sezione.
7. Selezionare **Save (Salva** ) per salvare il modello. 
8. Nella pagina **distribuzione personalizzata** attenersi alla seguente procedura. 
    1. Selezionare una **sottoscrizione**di Azure. 
    1. Selezionare un **gruppo di risorse** esistente nell'area di destinazione o crearne uno. 
    1. Per **Region (area**) selezionare l'area di destinazione. Se è stato selezionato un gruppo di risorse esistente, questa impostazione è di sola lettura.
    1. Per il **nome dell'argomento di sistema**, immettere un nome per l'argomento di sistema che verrà associato all'account di archiviazione.  
    1. Per **nome account di archiviazione**immettere un nome per l'account di archiviazione da creare nell'area di destinazione. 

        :::image type="content" source="./media/move-system-topics-across-regions/deploy-template.png" alt-text="Distribuisci modello di Gestione risorse":::
    5. Selezionare **Rivedi e crea** nella parte inferiore della pagina. 
    1. Nella pagina **Verifica e crea** verificare le impostazioni e selezionare **Crea**. 

## <a name="verify"></a>Verifica
1. Una volta completata la distribuzione, selezionare **Vai al gruppo di risorse**. 
1. Nella pagina **gruppo di risorse** verificare che l'origine evento (in questo esempio, l'account di archiviazione di Azure) e l'argomento di sistema siano state create. 
1. Caricare un file in un contenitore nell'archivio BLOB di Azure e verificare che il webhook abbia ricevuto l'evento. Per altre informazioni, vedere [inviare un evento all'endpoint](blob-event-quickstart-portal.md#send-an-event-to-your-endpoint).

## <a name="discard-or-clean-up"></a>Eliminare o pulire
Per completare lo spostamento, eliminare il gruppo di risorse che contiene l'account di archiviazione e il relativo argomento di sistema associato nell'area di origine.  

Se si vuole ricominciare, eliminare il gruppo di risorse nell'area di destinazione e ripetere i passaggi nelle sezioni [prepara](#prepare) e [ricrea](#recreate) di questo articolo.

Per eliminare un gruppo di risorse (origine o destinazione) usando il portale di Azure:

1. Nella finestra di ricerca nella parte superiore di portale di Azure digitare **gruppi di risorse**e selezionare **gruppi di risorse** dai risultati della ricerca. 
2. Selezionare il gruppo di risorse da eliminare e selezionare **Elimina** dalla barra degli strumenti. 

    :::image type="content" source="./media/move-system-topics-across-regions/delete-resource-group-button.png" alt-text="Eliminare un gruppo di risorse":::
3. Nella pagina Conferma immettere il nome del gruppo di risorse e selezionare **Elimina**.  

## <a name="next-steps"></a>Passaggi successivi
Si è appreso come spostare un'origine evento di Azure e il relativo argomento di sistema associato da un'area a un'altra. Vedere gli articoli seguenti per lo stato di argomenti personalizzati, domini e spazi dei nomi dei partner tra le aree.

- [Spostare gli argomenti personalizzati tra le aree](move-custom-topics-across-regions.md). 
- [Spostare i domini tra le aree](move-domains-across-regions.md). 
- [Spostare gli spazi dei nomi partner tra le aree](move-partner-namespaces-across-regions.md). 

Per altre informazioni sullo spostamento di risorse tra aree e ripristino di emergenza in Azure, vedere l'articolo seguente: [spostare le risorse in un nuovo gruppo di risorse o sottoscrizione](../azure-resource-manager/management/move-resource-group-and-subscription.md).
