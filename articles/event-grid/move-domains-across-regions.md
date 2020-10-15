---
title: Spostare i domini di griglia di eventi di Azure in un'altra area
description: Questo articolo illustra come spostare i domini di griglia di eventi di Azure da un'area a un'altra.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: fff8638a819511f84f15c52ad0695cdd5759f971
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89086215"
---
# <a name="move-azure-event-grid-domains-to-another-region"></a>Spostare i domini di griglia di eventi di Azure in un'altra area
Potrebbe essere necessario spostare le risorse in un'altra area per diversi motivi. Ad esempio, per sfruttare i vantaggi di una nuova area di Azure, per soddisfare i requisiti di governance e criteri interni oppure in risposta ai requisiti della pianificazione della capacità. 

Di seguito sono riportati i passaggi di alto livello trattati in questo articolo: 

- **Esportare la** risorsa di dominio in un modello di Azure Resource Manager. 

    > [!IMPORTANT]
    > La risorsa di dominio e gli argomenti del dominio vengono esportati nel modello. Le sottoscrizioni di argomenti di dominio non vengono esportate 
- **Usare il modello per distribuire il dominio** nell'area di destinazione. 
- **Creare manualmente sottoscrizioni per gli argomenti del dominio** nell'area di destinazione. Quando il dominio è stato esportato in un modello nell'area corrente, le sottoscrizioni per gli argomenti del dominio non vengono esportate. Quindi, crearli dopo aver creato gli argomenti dominio e dominio nell'area di destinazione. 
- **Verificare la distribuzione**. Inviare un evento a un argomento di dominio nel dominio e verificare che venga richiamato il gestore eventi associato alla sottoscrizione. 
- Per **completare lo spostamento**, eliminare il dominio dall'area di origine. 

## <a name="prerequisites"></a>Prerequisiti
- Verificare che il servizio griglia di eventi sia disponibile nell'area di destinazione. Vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Preparazione
Per iniziare, esportare un modello di Gestione risorse per il dominio. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra di ricerca digitare **domini griglia di eventi**e selezionare **domini griglia di eventi** dall'elenco risultati. 

    :::image type="content" source="./media/move-domains-across-regions/search-domains.png" alt-text="Cercare e selezionare i domini di griglia di eventi":::
3. Selezionare il **dominio** che si vuole esportare in un modello di gestione risorse. 

    :::image type="content" source="./media/move-domains-across-regions/select-domain.png" alt-text="Cercare e selezionare i domini di griglia di eventi":::   
4. Nella pagina **dominio griglia di eventi** selezionare **Esporta modello** in **Impostazioni** nel menu a sinistra e quindi fare clic su **Scarica** sulla barra degli strumenti. 

    :::image type="content" source="./media/move-domains-across-regions/export-template-download.png" alt-text="Cercare e selezionare i domini di griglia di eventi" lightbox="./media/move-domains-across-regions/export-template-download.png":::   

    > [!IMPORTANT]
    > Vengono esportati gli argomenti dominio e dominio. Le sottoscrizioni per argomenti di dominio non vengono esportate È quindi necessario creare sottoscrizioni per gli argomenti di dominio dopo aver spostato gli argomenti del dominio. 
5. Individuare il file **zip** scaricato dal portale e decomprimere il file in una cartella di propria scelta. Questo file zip contiene i file JSON del modello e dei parametri. 
1. Aprire il **template.js** in un editor di propria scelta. 
8. Aggiornamento della `location` risorsa di **dominio** all'area o alla posizione di destinazione. Per ottenere i codici di località, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/). Il codice per un'area è il nome dell'area senza spazi, ad esempio, `West US` è uguale a `westus` .

    ```json
    "type": "Microsoft.EventGrid/domains",
    "apiVersion": "2020-06-01",
    "name": "[parameters('domains_spegriddomain_name')]",
    "location": "westus",
    ```
1. **Salvare** il modello. 

## <a name="recreate"></a>Ricreare 
Distribuire il modello per creare gli argomenti dominio e dominio nell'area di destinazione. 

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
    1. Per **nome dominio**immettere un nuovo nome per il dominio. 
    1. Selezionare **Rivedi e crea**. 
    
        :::image type="content" source="./media/move-domains-across-regions/deploy-template.png" alt-text="Cercare e selezionare i domini di griglia di eventi":::        
    1. Dopo aver completato la convalida del modello, selezionare **Crea** nella parte inferiore della pagina per distribuire la risorsa. 
    1. Una volta completata la distribuzione, selezionare **Vai al gruppo di risorse** per passare alla pagina del gruppo di risorse. Verificare che sia presente un dominio nel gruppo di risorse. Selezionare il dominio. Verificare che siano presenti argomenti di dominio nel dominio. 

## <a name="discard-or-clean-up"></a>Eliminare o pulire
Per completare lo spostamento, eliminare il dominio nell'area di origine.  

Se si desidera ricominciare, eliminare il dominio nell'area di destinazione e ripetere i passaggi nelle sezioni [prepara](#prepare) e [ricrea](#recreate) di questo articolo.

Per eliminare un dominio utilizzando la portale di Azure:

1. Nella finestra di ricerca nella parte superiore di portale di Azure digitare **domini di griglia di eventi**e selezionare **domini di griglia di eventi** nei risultati della ricerca. 
2. Selezionare il dominio da eliminare e selezionare **Elimina** dalla barra degli strumenti. 
3. Nella pagina Conferma immettere il nome del gruppo di risorse e selezionare **Elimina**.  

Per eliminare il gruppo di risorse che contiene il dominio usando il portale di Azure:

1. Nella finestra di ricerca nella parte superiore di portale di Azure digitare **gruppi di risorse**e selezionare **gruppi di risorse** dai risultati della ricerca. 
2. Selezionare il gruppo di risorse da eliminare e selezionare **Elimina** dalla barra degli strumenti. 
3. Nella pagina Conferma immettere il nome del gruppo di risorse e selezionare **Elimina**.  

## <a name="next-steps"></a>Passaggi successivi
Si è appreso come spostare un dominio di griglia di eventi da un'area a un'altra. Per gli argomenti di sistema, gli argomenti personalizzati e gli spazi dei nomi dei partner tra le aree, vedere gli articoli seguenti.

- [Spostare gli argomenti di sistema tra le aree](move-system-topics-across-regions.md). 
- [Spostare gli argomenti personalizzati tra le aree](move-custom-topics-across-regions.md). 
- [Spostare gli spazi dei nomi partner tra le aree](move-partner-namespaces-across-regions.md).

Per altre informazioni sullo spostamento di risorse tra aree e ripristino di emergenza in Azure, vedere l'articolo seguente: [spostare le risorse in un nuovo gruppo di risorse o sottoscrizione](../azure-resource-manager/management/move-resource-group-and-subscription.md).