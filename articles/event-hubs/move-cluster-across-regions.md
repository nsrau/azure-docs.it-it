---
title: Spostare un cluster dedicato di hub eventi di Azure in un'altra area | Microsoft Docs
description: Questo articolo illustra come spostare un cluster dedicato di hub eventi di Azure dall'area corrente a un'altra area.
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 94be44ee8f9442a3a65e899d7a58524b2570f194
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89380833"
---
# <a name="move-an-azure-event-hubs-dedicated-cluster-to-another-region"></a>Spostare un cluster dedicato di hub eventi di Azure in un'altra area
Questo articolo illustra come esportare un modello di Azure Resource Manager per un cluster dedicato di hub eventi esistente e quindi usare il modello per creare un cluster con le stesse impostazioni di configurazione in un'altra area. 

Se si dispone di altre risorse, ad esempio spazi dei nomi e hub eventi, nel gruppo di risorse di Azure che contiene il cluster di hub eventi, è possibile esportare il modello a livello di gruppo di risorse in modo che tutte le risorse correlate possano essere spostate nella nuova area in un unico passaggio. I passaggi descritti in questo articolo illustrano come esportare un **cluster di hub eventi** nel modello. I passaggi per l'esportazione di un **gruppo di risorse** nel modello sono simili. 

## <a name="prerequisites"></a>Prerequisiti
Verificare che il cluster dedicato possa essere creato nell'area di destinazione. Il modo più semplice per scoprirlo consiste nell'usare il portale di Azure per provare a [creare un cluster dedicato di hub eventi](event-hubs-dedicated-cluster-create-portal.md). Viene visualizzato l'elenco delle aree supportate in quel momento per la creazione del cluster. 

## <a name="prepare"></a>Preparazione
Per iniziare, esportare un modello di Gestione risorse. Questo modello contiene le impostazioni che descrivono il cluster dedicato di hub eventi.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **tutte le risorse** e quindi selezionare il cluster dedicato di hub eventi.
3. Selezionare > **Impostazioni**  >  **Esporta modello**.
4. Scegliere **download** nella pagina **Esporta modello** .

    :::image type="content" source="./media/move-cluster-across-regions/download-template.png" alt-text="Scarica Gestione risorse modello" lightbox="./media/move-cluster-across-regions/download-template.png":::
5. Individuare il file zip scaricato dal portale e decomprimere il file in una cartella di propria scelta.

   Questo file zip contiene i file con estensione JSON che includono il modello e gli script per distribuire il modello.


## <a name="move"></a>Spostamento

Distribuire il modello per creare un cluster dedicato di hub eventi nell'area di destinazione. 


1. Nella portale di Azure selezionare **Crea una risorsa**.
2. In **Cerca nel Marketplace**Digitare **distribuzione modello**e selezionare **distribuzione modelli (Distribuisci usando modelli personalizzati)**.
5. Selezionare **Creare un modello personalizzato nell'editor**.
6. Selezionare **Carica file**e quindi seguire le istruzioni per caricare il **template.js** nel file scaricato nell'ultima sezione.
1. Aggiornare il valore della `location` Proprietà in modo che punti alla nuova area. Per ottenere i codici di località, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/). Il codice per un'area è il nome dell'area senza spazi, ad esempio, `West US` è uguale a `westus` .
1. Selezionare **Save (Salva** ) per salvare il modello. 
1. Nella pagina **distribuzione personalizzata** , attenersi alla seguente procedura: 
    1. Selezionare una **sottoscrizione**di Azure. 
    2. Selezionare un **gruppo di risorse** esistente o crearne uno. 
    3. Selezionare la **località** o l'area di destinazione. Se è stato selezionato un gruppo di risorse esistente, questa impostazione è di sola lettura. 
    4. Nella sezione **Impostazioni** seguire questa procedura:    
        1. Immettere il nome del nuovo **cluster**. 

            :::image type="content" source="./media/move-cluster-across-regions/deploy-template.png" alt-text="Scarica Gestione risorse modello":::
    5. Selezionare **Rivedi e crea** nella parte inferiore della pagina. 
    1. Nella pagina **Verifica e crea** verificare le impostazioni e quindi selezionare **Crea**.  

## <a name="discard-or-clean-up"></a>Eliminare o pulire
Dopo la distribuzione, se si vuole ricominciare, è possibile eliminare il **cluster dedicato di hub eventi di destinazione**e ripetere i passaggi descritti nelle sezioni [preparare](#prepare) e [spostare](#move) di questo articolo.

Per eseguire il commit delle modifiche e completare lo spostamento di un cluster di hub eventi, eliminare il **cluster di hub eventi** nell'area originale. 

Per eliminare un cluster di hub eventi (origine o destinazione) usando il portale di Azure:

1. Nella finestra di ricerca nella parte superiore di portale di Azure digitare **cluster di hub eventi**e selezionare cluster di **Hub eventi** dai risultati della ricerca. Il cluster di hub eventi viene visualizzato in un elenco.
2. Selezionare il cluster da eliminare e selezionare **Elimina** dalla barra degli strumenti. 
3. Nella pagina **Elimina cluster** , confermare l'eliminazione digitando il **nome del cluster**e quindi selezionare **Elimina**. 

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come spostare un cluster dedicato di hub eventi da un'area a un'altra. 

Per istruzioni sullo spostamento di uno spazio dei nomi da un'area a un'altra, vedere l'articolo [spostare gli spazi dei nomi di hub eventi in più aree geografiche](move-across-regions.md) . 

Per altre informazioni sullo spostamento di risorse tra aree e sul ripristino di emergenza in Azure, vedere:

- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Spostare macchine virtuali di Azure in un'altra area](../site-recovery/azure-to-azure-tutorial-migrate.md)
