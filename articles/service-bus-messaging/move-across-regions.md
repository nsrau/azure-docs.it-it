---
title: Spostare uno spazio dei nomi del bus di servizio in un'altra area | Microsoft Docs
description: Questo articolo illustra come spostare uno spazio dei nomi del bus di servizio di Azure dall'area corrente a un'altra area.
ms.topic: how-to
ms.date: 06/23/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 573ea96437cf6cb76854ffa417fd3ad3fb86138b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88861063"
---
# <a name="move-an-azure-service-bus-namespace-to-another-region"></a>Spostare uno spazio dei nomi del bus di servizio di Azure in un'altra area
Esistono diversi scenari in cui si vuole spostare lo spazio dei nomi del bus di servizio esistente da un'area a un'altra. Ad esempio, è possibile creare uno spazio dei nomi con la stessa configurazione per il test. È anche possibile creare uno spazio dei nomi secondario in un'altra area nell'ambito della [pianificazione del ripristino di emergenza](service-bus-geo-dr.md).

Ecco i passaggi principali:

1. Esportare lo spazio dei nomi del bus di servizio nell'area corrente in un modello di Azure Resource Manager. 
1. Aggiornare il percorso per le risorse nel modello. Rimuovere anche il filtro della sottoscrizione predefinito dal modello perché non è possibile creare una regola predefinita perché viene creata automaticamente. 
1. Usare il modello per distribuire lo spazio dei nomi del bus di servizio nell'area di destinazione. 
1. Verificare la distribuzione per assicurarsi che lo spazio dei nomi, le code, gli argomenti e le sottoscrizioni per gli argomenti vengano creati nell'area di destinazione. 
1. Completare lo spostamento eliminando lo spazio dei nomi dall'area di origine dopo l'elaborazione di tutti i messaggi. 

## <a name="prerequisites"></a>Prerequisiti
Verificare che le funzionalità e il bus di servizio di Azure usati dall'account siano supportati nell'area di destinazione.
 
## <a name="prepare"></a>Preparazione
Per iniziare, esportare un modello di Gestione risorse. Questo modello contiene le impostazioni che descrivono lo spazio dei nomi del bus di servizio.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **tutte le risorse** e quindi selezionare lo spazio dei nomi del bus di servizio.
3. Selezionare > **Impostazioni**  >  **Esporta modello**.
4. Scegliere **download** nella pagina **Esporta modello** .

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Scarica Gestione risorse modello":::
5. Individuare il file zip scaricato dal portale e decomprimere il file in una cartella di propria scelta. Questo file zip contiene i file JSON del modello e dei parametri. 
1. Aprire il template.jsnel file nella cartella estratta. 
1. Cercare `location` e sostituire il valore della proprietà con il nuovo nome per l'area o la località. Per ottenere i codici di località, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/). Il codice per un'area è il nome dell'area senza spazi, ad esempio, `West US` è uguale a `westus` .
1. Rimuovere le definizioni delle risorse di tipo: `Microsoft.ServiceBus/namespaces/topics/subscriptions/rules` . Non dimenticare di rimuovere il carattere virgola ( `,` ) che precede questa sezione per tenere JSON valido.  

    > [!NOTE]
    > Non è possibile creare una regola predefinita per una sottoscrizione usando un modello di Gestione risorse. La regola predefinita viene creata automaticamente quando la sottoscrizione viene creata nell'area di destinazione. 

## <a name="move"></a>Spostamento
Distribuire il modello per creare uno spazio dei nomi del bus di servizio nell'area di destinazione. 

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
1. Nella pagina **gruppo di risorse** selezionare lo spazio dei nomi del bus di servizio. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Scarica Gestione risorse modello":::    
1. Nella pagina **spazio dei nomi del bus di servizio** verificare che siano visualizzate le code, gli argomenti e le sottoscrizioni dell'area di origine. 
    1. Nella parte inferiore del riquadro di destra vengono visualizzate le **Code** nello spazio dei nomi.         
    
        :::image type="content" source="./media/move-across-regions/queue-namespace.png" alt-text="Scarica Gestione risorse modello":::
    2. Passare alla scheda **argomenti** per visualizzare gli argomenti nello spazio dei nomi
    
        :::image type="content" source="./media/move-across-regions/topics-namespace.png" alt-text="Scarica Gestione risorse modello":::
    3. Selezionare l'argomento per verificare che vengano create le sottoscrizioni. 

        :::image type="content" source="./media/move-across-regions/topic-subscriptions.png" alt-text="Scarica Gestione risorse modello":::      
    
    

## <a name="discard-or-clean-up"></a>Eliminare o pulire
Dopo la distribuzione, se si desidera ricominciare, è possibile eliminare lo **spazio dei nomi del bus di servizio di destinazione**e ripetere i passaggi descritti nelle sezioni [preparare](#prepare) e [spostare](#move) di questo articolo.

Per eseguire il commit delle modifiche e completare lo spostamento dello spazio dei nomi del bus di servizio, eliminare lo **spazio dei nomi del bus di servizio di origine**. Assicurarsi di elaborare tutti i messaggi prima di eliminare lo spazio dei nomi. 

Per eliminare uno spazio dei nomi del bus di servizio (origine o destinazione) usando il portale di Azure:

1. Nella finestra di ricerca nella parte superiore di portale di Azure digitare **bus di servizio**e selezionare **bus di servizio** dai risultati della ricerca. Gli spazi dei nomi del bus di servizio vengono visualizzati in un elenco.
2. Selezionare lo spazio dei nomi di destinazione da eliminare e selezionare **Elimina** dalla barra degli strumenti. 

    ![Pulsante Elimina spazio dei nomi](./media/move-across-regions/delete-namespace-button.png)
3. Nella pagina **Elimina risorse** verificare le risorse selezionate e confermare l'eliminazione digitando **Sì**, quindi selezionare **Elimina**. 

    Un'altra opzione consiste nell'eliminare il gruppo di risorse con lo spazio dei nomi del bus di servizio. Nella pagina **gruppo di risorse** selezionare **Elimina gruppo di risorse** sulla barra degli strumenti e quindi confermare l'eliminazione. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato uno spazio dei nomi del bus di servizio di Azure da un'area a un'altra ed è stata eseguita la pulizia delle risorse di origine.  Per altre informazioni sullo spostamento di risorse tra aree e sul ripristino di emergenza in Azure, vedere:

- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](../azure-resource-manager/management/move-resource-group-and-subscription.md)
