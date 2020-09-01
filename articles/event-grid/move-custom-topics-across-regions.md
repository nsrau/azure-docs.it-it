---
title: Spostare gli argomenti personalizzati di griglia di eventi di Azure in un'altra area
description: Questo articolo illustra come spostare gli argomenti personalizzati di griglia di eventi di Azure da un'area a un'altra.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: d0656a4f6ec1c7431cf7111f786b0f1d779166e3
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89145341"
---
# <a name="move-azure-event-grid-custom-topics-to-another-region"></a>Spostare gli argomenti personalizzati di griglia di eventi di Azure in un'altra area
Potrebbe essere necessario spostare le risorse in un'altra area per diversi motivi. Ad esempio, per sfruttare i vantaggi di una nuova area di Azure, per soddisfare i requisiti di governance e criteri interni oppure in risposta ai requisiti della pianificazione della capacità. 

Di seguito sono riportati i passaggi di alto livello trattati in questo articolo: 

- **Esportare la risorsa dell'argomento personalizzato** in un modello di Azure Resource Manager. 

    > [!IMPORTANT]
    > Solo l'argomento personalizzato viene esportato nel modello. Le sottoscrizioni per l'argomento non vengono esportate.
- **Usare il modello per distribuire l'argomento personalizzato** nell'area di destinazione. 
- **Creare sottoscrizioni manualmente** nell'area di destinazione. Quando l'argomento personalizzato è stato esportato in un modello nell'area corrente, viene esportato solo l'argomento. Le sottoscrizioni non sono incluse nel modello, quindi è necessario crearle manualmente dopo che l'argomento personalizzato è stato creato nell'area di destinazione. 
- **Verificare la distribuzione**. Verificare che l'argomento personalizzato venga creato nell'area di destinazione. 
- Per **completare l'operazione di spostamento**, eliminare l'argomento personalizzato dall'area di origine. 

## <a name="prerequisites"></a>Prerequisiti
- Completare la [Guida introduttiva: indirizzare gli eventi personalizzati all'endpoint Web](custom-event-quickstart-portal.md) nell'area di origine. Eseguire questo passaggio per eseguire il test delle procedure descritte in questo articolo. 
- Verificare che il servizio griglia di eventi sia disponibile nell'area di destinazione. Vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Preparazione
Per iniziare, esportare un modello di Gestione risorse per l'argomento personalizzato. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra di ricerca digitare **argomenti di griglia di eventi**e selezionare gli argomenti di griglia di **eventi** nell'elenco dei risultati. 

    :::image type="content" source="./media/move-custom-topics-across-regions/search-topics.png" alt-text="Cercare e selezionare gli argomenti di griglia di eventi":::
3. Selezionare l' **argomento** che si vuole esportare in un modello di gestione risorse. 

    :::image type="content" source="./media/move-custom-topics-across-regions/select-custom-topic.png" alt-text="Selezionare l'argomento personalizzato":::   
4. Nella pagina **argomento della griglia di eventi** selezionare **Esporta modello** in **Impostazioni** nel menu a sinistra e quindi fare clic su **Scarica** sulla barra degli strumenti. 

    :::image type="content" source="./media/move-custom-topics-across-regions/export-template-download.png" alt-text="Esporta modello-download >":::   

    > [!IMPORTANT]
    > Solo l'argomento viene esportato nel modello. Le sottoscrizioni per l'argomento non vengono esportate. È quindi necessario creare sottoscrizioni per l'argomento dopo lo spostamento dell'argomento nell'area di destinazione. 
5. Individuare il file con **estensione zip** scaricato dal portale e decomprimere il file in una cartella di propria scelta. Questo file zip contiene i file JSON del modello e dei parametri. 
1. Aprire il **template.js** in un editor di propria scelta. 
8. Aggiornamento `location` per la risorsa dell' **argomento** nell'area o nella località di destinazione. Per ottenere i codici di località, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/). Il codice per un'area è il nome dell'area senza spazi, ad esempio, `West US` è uguale a `westus` .

    ```json
    "type": "Microsoft.EventGrid/topics",
    "apiVersion": "2020-06-01",
    "name": "[parameters('topics_mytopic0130_name')]",
    "location": "westus"
    ```
1. **Salvare** il modello. 

## <a name="recreate"></a>Ricreare 
Distribuire il modello per creare un argomento personalizzato nell'area di destinazione. 

1. Nella portale di Azure selezionare **Crea una risorsa**.
2. In **Cerca nel Marketplace**Digitare **distribuzione modello**, quindi premere **invio**.
3. Selezionare **distribuzione modelli**.
4. Selezionare **Crea**.
5. Selezionare **Creare un modello personalizzato nell'editor**.
6. Selezionare **Carica file**e quindi seguire le istruzioni per caricare il **template.js** nel file scaricato nell'ultima sezione.
7. Selezionare **Save (Salva** ) per salvare il modello. 
8. Nella pagina **distribuzione personalizzata** , attenersi alla seguente procedura: 
    1. Selezionare una **sottoscrizione**di Azure. 
    1. Selezionare un **gruppo di risorse** esistente nell'area di destinazione o crearne uno. 
    1. Per **Region (area**) selezionare l'area di destinazione. Se è stato selezionato un gruppo di risorse esistente, questa impostazione è di sola lettura. 
    1. Per **nome argomento**immettere un nuovo nome per l'argomento. 
    1. Selezionare **Rivedi e crea** nella parte inferiore della pagina. 
    
        :::image type="content" source="./media/move-custom-topics-across-regions/deploy-template.png" alt-text="Distribuzione personalizzata":::
    1. Nella pagina **Verifica e crea** verificare le impostazioni e selezionare **Crea**. 

## <a name="verify"></a>Verifica

1. Una volta completata la distribuzione, selezionare **Vai alla risorsa**. 

    :::image type="content" source="./media/move-custom-topics-across-regions/navigate-custom-topic.png" alt-text="Vai alla risorsa":::
1. Verificare che venga visualizzata la pagina dell' **argomento griglia di eventi** per l'argomento personalizzato.   
1. Seguire la procedura descritta in [indirizzare gli eventi personalizzati a un endpoint Web](custom-event-quickstart-portal.md#send-an-event-to-your-topic) per inviare eventi all'argomento. Verificare che venga richiamato il gestore eventi del webhook. 

## <a name="discard-or-clean-up"></a>Eliminare o pulire
Per completare lo spostamento, eliminare l'argomento personalizzato nell'area di origine.  

Se si vuole ricominciare, eliminare l'argomento nell'area di destinazione e ripetere i passaggi nelle sezioni [preparare](#prepare) e [ricreare](#recreate) di questo articolo.

Per eliminare un argomento personalizzato utilizzando la portale di Azure:

1. Nella finestra di ricerca nella parte superiore di portale di Azure digitare **argomenti di griglia di eventi**e selezionare gli argomenti di griglia di **eventi** nei risultati della ricerca. 
2. Selezionare l'argomento da eliminare e selezionare **Elimina** dalla barra degli strumenti. 
3. Nella pagina Conferma immettere il nome del gruppo di risorse e selezionare **Elimina**.  

Per eliminare il gruppo di risorse che contiene l'argomento personalizzato usando il portale di Azure:

1. Nella finestra di ricerca nella parte superiore di portale di Azure digitare **gruppi di risorse**e selezionare **gruppi di risorse** dai risultati della ricerca. 
2. Selezionare il gruppo di risorse da eliminare e selezionare **Elimina** dalla barra degli strumenti. 
3. Nella pagina Conferma immettere il nome del gruppo di risorse e selezionare **Elimina**.  

## <a name="next-steps"></a>Passaggi successivi
Si è appreso come spostare un argomento personalizzato di griglia di eventi da un'area a un'altra. Vedere gli articoli seguenti per lo stato di argomenti di sistema, domini e spazi dei nomi partner tra le aree.

- [Spostare gli argomenti di sistema tra le aree](move-system-topics-across-regions.md). 
- [Spostare i domini tra le aree](move-domains-across-regions.md). 
- [Spostare gli spazi dei nomi partner tra le aree](move-partner-namespaces-across-regions.md).

Per altre informazioni sullo spostamento di risorse tra aree e ripristino di emergenza in Azure, vedere l'articolo seguente: [spostare le risorse in un nuovo gruppo di risorse o sottoscrizione](../azure-resource-manager/management/move-resource-group-and-subscription.md).