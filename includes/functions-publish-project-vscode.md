---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 256510f855256e648ae9203f46eb9f66c9ffaed6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77029152"
---
## <a name="publish-the-project-to-azure"></a>Pubblicare il progetto in Azure

In questa sezione verrà creata un'app per le funzioni con le risorse correlate nella sottoscrizione di Azure e quindi verrà distribuito il codice. 

1. Selezionare l'icona di Azure nella barra attività, quindi nell'area **Azure: Funzioni** scegliere il pulsante **Deploy to function app...** (Distribuisci nell'app per le funzioni...).

    ![Pubblicare il progetto in Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Quando richiesto, immettere le informazioni seguenti:

    + **Selezionare la sottoscrizione**: scegliere la sottoscrizione da usare. Questa opzione non è visibile se è disponibile una sola sottoscrizione.

    + **Selezionare l'app per le funzioni in Azure**: scegliere `+ Create new Function App` (non `Advanced`). Questo articolo non supporta il [flusso di pubblicazione avanzata](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options). 
    
    >[!IMPORTANT]
    > La pubblicazione in un'app per le funzioni esistente sovrascrive il contenuto di tale app in Azure. 
    
    + **Immettere un nome univoco a livello globale per l'app per le funzioni**: digitare un nome valido in un percorso URL. Il nome digitato viene convalidato per assicurarsi che sia univoco in Funzioni di Azure. 
    
    ::: zone pivot="programming-language-python"
    + **Selezionare un runtime**: scegliere la versione di Python in esecuzione in locale. Per verificare la versione in uso, eseguire il comando `python --version`.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Selezionare un runtime**: scegliere la versione di Node.js in esecuzione in locale. Per verificare la versione in uso, eseguire il comando `node --version`.
    ::: zone-end

    + **Selezionare una località per le nuove risorse**:  per prestazioni ottimali, scegliere un'[area](https://azure.microsoft.com/regions/) vicina. 
    
1.  Al termine, nella sottoscrizione vengono create le risorse di Azure seguenti:

    + **[Gruppo di risorse](../articles/azure-resource-manager/management/overview.md)** : contiene tutte le risorse di Azure create. Il nome si basa sul nome dell'app per le funzioni.
    + **[Account di archiviazione](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** : viene creato un account di archiviazione standard con un nome univoco basato sul nome dell'app per le funzioni.
    + **[Piano di hosting](../articles/azure-functions/functions-scale.md)** : nell'area Stati Uniti occidentali viene creato un piano a consumo per l'hosting dell'app per le funzioni.
    + **App per le funzioni**: il progetto viene distribuito ed eseguito nella nuova app per le funzioni.
    + **Application Insights**: viene creata un'istanza connessa all'app per le funzioni in base al nome della funzione.

    Dopo la creazione dell'app per le funzioni e dopo l'applicazione del pacchetto di distribuzione viene visualizzata una notifica. 
    
1. Selezionare **Visualizza output** nelle notifiche per visualizzare i risultati della creazione e della distribuzione, incluse le risorse di Azure create. Se non si riesce a visualizzare la notifica, selezionare l'icona della campana nell'angolo in basso a destra per visualizzarla di nuovo.

    ![Notifica di creazione completata](media/functions-publish-project-vscode/function-create-notifications.png)
