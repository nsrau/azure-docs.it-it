---
title: Spostare uno spazio dei nomi di inoltro di Azure in un'altra area
description: Questo articolo illustra come spostare uno spazio dei nomi di inoltro di Azure dall'area corrente a un'altra area.
ms.topic: how-to
ms.date: 09/03/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 60a182764639341fcda159356dd9fe6c65cfabd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89463832"
---
# <a name="move-an-azure-relay-namespace-to-another-region"></a>Spostare uno spazio dei nomi di inoltro di Azure in un'altra area
Questo articolo illustra come spostare uno spazio dei nomi di inoltro di Azure da un'area a un'altra. Ecco i passaggi principali:

1. **Esportare** lo spazio dei nomi di inoltro in un modello di Azure Resource Manager.
1. **Aggiornare il percorso (area)** per le risorse nel modello. Inoltre, eliminare eventuali inoltri WCF **dinamici** dal modello. 

    Gli inoltri WCF hanno due modalità. Nella prima modalità, l'inoltro WCF viene creato in modo esplicito tramite il modello di portale di Azure o Azure Resource Manager. Nella pagina **inoltri WCF** della portale di Azure viene visualizzata **la proprietà IsSynchronized** impostata su **false** per un inoltro in questa modalità. 

    Nella seconda modalità, l'inoltro WCF viene generato automaticamente quando un listener (Server) si connette per un determinato indirizzo endpoint. Finché il listener è connesso al relè, il relè viene visualizzato nell'elenco degli inoltri WCF nella portale di Azure. Per un inoltro in questa modalità **, la proprietà** IsSynchronized è impostata su **true** perché viene generata dinamicamente. L'inoltro WCF dinamico scompare quando il listener si disconnette. 
1. **Distribuire** le risorse usando il modello nell'area di destinazione.

## <a name="prerequisites"></a>Prerequisiti
Verificare che il servizio di inoltro di Azure sia disponibile nell'area di destinazione. Vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=service-bus&regions=all). 
 
## <a name="prepare"></a>Preparazione
Per iniziare, esportare un modello di Gestione risorse. Questo modello contiene le impostazioni che descrivono lo spazio dei nomi di inoltro di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **tutte le risorse** e quindi selezionare lo spazio dei nomi di inoltro di Azure.
3. Selezionare **Esporta modello** in **Impostazioni** nel menu a sinistra.
4. Scegliere **download** nella pagina **Esporta modello** .

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Scarica Gestione risorse modello":::
5. Individuare il file zip scaricato dal portale e decomprimere il file in una cartella di propria scelta. Questo file zip contiene i file JSON del modello e dei parametri. 
1. Aprire il **template.js** file dalla cartella estratta in un editor di propria scelta.
1. Cercare `location` e sostituire il valore della proprietà con il nuovo nome per l'area. Per ottenere i codici di località, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/). Il codice per un'area è il nome dell'area senza spazi, ad esempio, `West US` è uguale a `westus` .
1. Rimuovere le definizioni delle risorse di **inoltro WCF dinamiche** (tipo: `Microsoft.Relay/namespaces/WcfRelays` ). Gli inoltri WCF dinamici sono quelli la **cui proprietà IsSynchronized è impostata** su **true** nella pagina **Relays** . Nell'esempio seguente **EchoService** è un inoltro WCF dinamico e la relativa definizione deve essere rimossa dal modello. 

    :::image type="content" source="./media/move-across-regions/dynamic-relays.png" alt-text="Scarica Gestione risorse modello":::

## <a name="move"></a>Spostamento
Distribuire il modello per creare uno spazio dei nomi di inoltro nell'area di destinazione. 

1. Nella portale di Azure selezionare **Crea una risorsa**.
2. In **Cerca nel Marketplace**Digitare **distribuzione modello** per il testo di ricerca, selezionare **distribuzione modelli (Distribuisci usando i modelli personalizzati)**, quindi premere **invio**.

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="Scarica Gestione risorse modello":::    
1. Nella pagina **distribuzione modelli** selezionare **Crea**.

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="Scarica Gestione risorse modello":::        
1. Nella pagina **distribuzione personalizzata** selezionare **Compila un modello personalizzato nell'editor**.

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="Scarica Gestione risorse modello":::            
1. Nella pagina **modifica modello** selezionare **Carica file** sulla barra degli strumenti e quindi seguire le istruzioni per caricare il **template.js** nel file scaricato nell'ultima sezione.

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="Scarica Gestione risorse modello":::                
1. Selezionare **Save (Salva** ) per salvare il modello. 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="Scarica Gestione risorse modello":::                    
1. Nella pagina **distribuzione personalizzata** , attenersi alla seguente procedura: 
    1. Selezionare una **sottoscrizione**di Azure. 
    2. Selezionare un **gruppo di risorse** esistente o crearne uno. 
    3. Selezionare la **località** o l'area di destinazione. Se è stato selezionato un gruppo di risorse esistente, questa impostazione è di sola lettura. 
    4. Immettere un nuovo **nome per lo spazio dei nomi**.
    1. Selezionare **Rivedi e crea**. 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="Scarica Gestione risorse modello":::
    1. Nella pagina **Verifica e crea** selezionare **Crea** nella parte inferiore della pagina. 
    
## <a name="verify"></a>Verifica
1. Una volta completata la distribuzione, selezionare **Vai al gruppo di risorse**.

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="Scarica Gestione risorse modello":::    
1. Nella pagina **gruppo di risorse** selezionare lo spazio dei nomi di inoltro di Azure. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Scarica Gestione risorse modello":::    
1. Nella pagina **spazio dei nomi di inoltro di Azure** selezionare **connessioni ibride** o **inoltri WCF** nel menu a sinistra per verificare che le connessioni ibride e gli inoltri WCF siano stati creati. Se si dimentica di eliminare le definizioni per gli inoltri WCF dinamici prima di importare il modello, eliminarli nella pagina **inoltri WCF** . Gli inoltri WCF dinamici vengono creati automaticamente quando i client si connettono allo spazio dei nomi di inoltro. 

## <a name="discard-or-clean-up"></a>Eliminare o pulire
Dopo la distribuzione, se si vuole ricominciare, è possibile eliminare lo **spazio dei nomi di inoltro di Azure di destinazione**e ripetere i passaggi descritti nelle sezioni [preparare](#prepare) e [spostare](#move) di questo articolo.

Per eseguire il commit delle modifiche e completare lo spostamento di uno spazio dei nomi, eliminare lo **spazio dei nomi di inoltro di Azure** nell'area di origine. 

Per eliminare uno spazio dei nomi di inoltro di Azure (origine o destinazione) usando il portale di Azure:

1. Nella finestra di ricerca nella parte superiore di portale di Azure digitare **Relays**e selezionare **Relays** from **Services** nei risultati della ricerca. Tutti gli spazi dei nomi di inoltro di Azure vengono visualizzati in un elenco.
2. Selezionare lo spazio dei nomi di destinazione da eliminare per aprire la pagina di **inoltro** . 
1. Nella pagina **inoltro** selezionare **Elimina** dalla barra degli strumenti. 

    ![Pulsante Elimina spazio dei nomi](./media/move-across-regions/delete-namespace-button.png)
3. Nella pagina **Elimina spazio dei nomi** Digitare il nome dello spazio dei nomi di inoltro di Azure per confermare l'eliminazione e quindi selezionare **Elimina**. 

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato spostato uno spazio dei nomi di inoltro di Azure da un'area a un'altra. Per altre informazioni sullo spostamento di risorse tra aree e sul ripristino di emergenza in Azure, vedere:

- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](../azure-resource-manager/management/move-resource-group-and-subscription.md)
