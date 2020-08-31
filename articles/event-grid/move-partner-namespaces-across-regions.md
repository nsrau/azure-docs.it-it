---
title: Spostare gli spazi dei nomi del partner griglia di eventi di Azure in un'altra area
description: Questo articolo illustra come spostare gli spazi dei nomi del partner di griglia di eventi di Azure da un'area a un'altra.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: 6783db6b9bb1c7d48b308234a179925d6f30e281
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89086212"
---
# <a name="move-azure-event-grid-partner-namespaces-to-another-region"></a>Spostare gli spazi dei nomi del partner griglia di eventi di Azure in un'altra area
Potrebbe essere necessario spostare le risorse in un'altra area per diversi motivi. Ad esempio, per sfruttare i vantaggi di una nuova area di Azure, per soddisfare i requisiti di governance e criteri interni oppure in risposta ai requisiti della pianificazione della capacità. 

Di seguito sono riportati i passaggi di alto livello trattati in questo articolo: 

- **Esportare la risorsa spazio dei nomi partner** in un modello di Azure Resource Manager. Elimina le definizioni per le risorse del canale eventi nel modello. Un canale di eventi può avere un riferimento all'ID Azure Resource Manager dell'argomento partner, che è di proprietà di un cliente. Non è quindi possibile crearli usando il modello nell'area di destinazione.  
- **Usare il modello per distribuire lo spazio dei nomi del partner** nell'area di destinazione. Quindi, creare i canali degli eventi nel nuovo spazio dei nomi partner nell'area di destinazione. 
- Per **completare lo spostamento**, eliminare lo spazio dei nomi partner dall'area di origine. 

    > [!NOTE]
    > - L'esportazione degli **argomenti del partner** in un modello di Azure Resource Manager non è supportata perché i clienti non possono creare direttamente un argomento partner. 
    > - Le **registrazioni dei partner** sono risorse globali (non associate ad alcuna area specifica), quindi non è possibile spostarle da un'area a un'altra. 

## <a name="prerequisites"></a>Prerequisiti
- Verificare che il servizio griglia di eventi sia disponibile nell'area di destinazione. Vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Preparazione
Per iniziare, esportare un modello di Gestione risorse per lo spazio dei nomi partner. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra di ricerca nella parte superiore digitare gli **spazi dei nomi di griglia di eventi**e selezionare gli **spazi dei nomi del partner di griglia di eventi** dall'elenco dei risultati. 
3. Selezionare lo **spazio dei nomi del partner** che si desidera esportare in un modello di gestione risorse. 
4. Nella pagina **spazio dei nomi del partner di griglia di eventi** selezionare **Esporta modello** in **Impostazioni** nel menu a sinistra e quindi fare clic su **Scarica** sulla barra degli strumenti. 

    :::image type="content" source="./media/move-partner-namespaces-across-regions/download-template.png" alt-text="Esporta modello-download >" lightbox="./media/move-partner-namespaces-across-regions/download-template.png":::   
5. Individuare il file **zip** scaricato dal portale e decomprimere il file in una cartella di propria scelta. Questo file zip contiene i file JSON del modello e dei parametri. 
1. Aprire il **template.js** in un editor di propria scelta. 
8. Aggiornamento `location` per la risorsa dell' **argomento** nell'area o nella località di destinazione. Per ottenere i codici di località, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/). Il codice per un'area è il nome dell'area senza spazi, ad esempio, `West US` è uguale a `westus` .

    ```json
    {
        "type": "Microsoft.EventGrid/partnerNamespaces",
        "apiVersion": "2020-04-01-preview",
        "name": "[parameters('partnerNamespace_name')]",
        "location": "westus",
        "properties": {
            "partnerRegistrationFullyQualifiedId": "[parameters('partnerRegistrations_ContosoCorpAccount1_externalid')]"
        }
    }
    ``` 
1. **Salvare** il modello. 

## <a name="recreate"></a>Ricreare 
Distribuire il modello per creare uno spazio dei nomi partner nell'area di destinazione. 

1. Nella portale di Azure selezionare **Crea una risorsa**.
2. In **Cerca nel Marketplace**Digitare **distribuzione modello**, quindi premere **invio**.
3. Selezionare **distribuzione modelli**.
4. Selezionare **Create** (Crea).
5. Selezionare **Creare un modello personalizzato nell'editor**.
6. Selezionare **Carica file**e quindi seguire le istruzioni per caricare il **template.js** nel file scaricato nell'ultima sezione.
7. Selezionare **Save (Salva** ) per salvare il modello. 
8. Nella pagina **distribuzione personalizzata** , attenersi alla seguente procedura: 
    1. Selezionare una **sottoscrizione**di Azure. 
    1. Selezionare un **gruppo di risorse** esistente nell'area di destinazione o crearne uno. 
    1. Per **località**selezionare l'area di destinazione. Se è stato selezionato un gruppo di risorse esistente, questa impostazione è di sola lettura. 
    1. Per **nome spazio dei nomi partner**immettere un nome per il nuovo spazio dei nomi partner. 
    1. Per l'ID esterno della registrazione partner, immettere l'ID risorsa della registrazione partner nel formato seguente: `/subscriptions/<Azure subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.EventGrid/partnerRegistrations/<Partner registration name>` .
    1. Selezionare la casella di controllo **Accetto le condizioni riportate sopra**.     
    1. Selezionare **Verifica + crea** per avviare il processo di distribuzione. 
    1. Nella pagina **Verifica e crea** verificare le impostazioni e selezionare **Crea**. 

## <a name="discard-or-clean-up"></a>Eliminare o pulire
Per completare lo spostamento, eliminare lo spazio dei nomi partner nell'area di origine.  

Se si vuole ricominciare, eliminare lo spazio dei nomi partner nell'area di destinazione e ripetere i passaggi nelle sezioni [preparare](#prepare) e [ricreare](#recreate) di questo articolo.

Per eliminare uno spazio dei nomi partner usando il portale di Azure:

1. Nella finestra di ricerca nella parte superiore di portale di Azure digitare gli **spazi dei nomi del partner di griglia di eventi**e selezionare gli **spazi dei nomi del partner di griglia di eventi** nei risultati della ricerca. 
2. Selezionare lo spazio dei nomi partner da eliminare e selezionare **Elimina** dalla barra degli strumenti. 
3. **Confermare** l'eliminazione per eliminare lo spazio dei nomi del partner. 

## <a name="next-steps"></a>Passaggi successivi
Si è appreso come spostare uno spazio dei nomi del partner di griglia di eventi da un'area a un'altra. Per gli argomenti di sistema, gli argomenti personalizzati e i domini tra le aree, vedere gli articoli seguenti.

- [Spostare gli argomenti di sistema tra le aree](move-system-topics-across-regions.md). 
- [Spostare gli argomenti personalizzati tra le aree](move-custom-topics-across-regions.md). 
- [Spostare i domini tra le aree](move-domains-across-regions.md).

Per altre informazioni sullo spostamento di risorse tra aree e ripristino di emergenza in Azure, vedere l'articolo seguente: [spostare le risorse in un nuovo gruppo di risorse o sottoscrizione](../azure-resource-manager/management/move-resource-group-and-subscription.md).