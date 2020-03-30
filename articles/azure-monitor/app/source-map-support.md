---
title: Supporto della mappa di origine per le applicazioni JavaScript - Azure Monitor Application InsightsSource map support for JavaScript applications - Azure Monitor Application Insights
description: Informazioni su come caricare le mappe di origine nel contenitore BLOB dell'account di archiviazione usando Application Insights.Learn how to upload source maps to your own storage account Blob container using Application Insights.
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 03/04/2020
ms.openlocfilehash: 4b452b31338760a8f53eed54420319101836bc00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474884"
---
# <a name="source-map-support-for-javascript-applications"></a>Supporto della mappa di origine per le applicazioni JavaScriptSource map support for JavaScript applications

Application Insights supporta il caricamento di mappe di origine nel proprio contenitore BLOB dell'account di archiviazione.
Le mappe di origine possono essere utilizzate per annullare l'invogliamento degli stack di chiamate disponibili nella pagina dei dettagli delle transazioni end-to-end. Qualsiasi eccezione inviata da [JavaScript SDK][ApplicationInsights-JS] o [dall'SDK di Node.js][ApplicationInsights-Node.js] può essere annullata con le mappe di origine.

![Annullare l'elesia uno stack di chiamate tramite il collegamento a un account di archiviazioneUnminify a Call Stack by linking with a Storage Account](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Creare un nuovo account di archiviazione e un nuovo contenitore BLOBCreate a new storage account and Blob container

Se si dispone già di un account di archiviazione o di un contenitore BLOB esistente, è possibile ignorare questo passaggio.

1. [Creare un nuovo account di archiviazione.][create storage account]
2. [Creare un contenitore BLOB][create blob container] all'interno dell'account di archiviazione. Assicurarsi di impostare il "Livello di accesso pubblico" su `Private`, per assicurarsi che le mappe di origine non siano accessibili pubblicamente.

> [!div class="mx-imgBorder"]
>![Il livello di accesso al contenitore deve essere impostato su Private](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Eseguire il push delle mappe di origine nel contenitore BLOB

È necessario integrare la pipeline di distribuzione continua con l'account di archiviazione configurandolo per caricare automaticamente le mappe di origine nel contenitore BLOB configurato. È consigliabile non caricare le mappe di origine in una sottocartella nel contenitore BLOB. attualmente la mappa di origine verrà recuperata solo dalla cartella radice.

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Caricare le mappe di origine tramite le pipeline di Azure (scelta consigliata)Upload source maps via Azure Pipelines (recommended)

Se si usa pipeline di Azure per compilare e distribuire continuamente l'applicazione, aggiungere un'attività [Copia file][azure file copy] di Azure alla pipeline per caricare automaticamente le mappe di origine.

> [!div class="mx-imgBorder"]
> ![Aggiungere un'attività Copia file di Azure alla pipeline per caricare le mappe di origine in Archiviazione BLOB di AzureAdd an Azure File Copy task to your Pipeline to upload your source maps to Azure Blob Storage](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>Configurare la risorsa di Application Insights con un account di archiviazione della mappa di origineConfigure your Application Insights resource with a Source Map storage account

### <a name="from-the-end-to-end-transaction-details-page"></a>Dalla pagina dei dettagli della transazione end-to-end

Dalla scheda Dettagli transazione end-to-end, è possibile fare clic su *Non ingrandisci* e verrà visualizzato un prompt per configurare se la risorsa non è configurata.

1. Nel portale, visualizzare i dettagli di un'eccezione che è stata reindita.
2. Clicca su *Annulla minifichezza*
3. Se la risorsa non è stata configurata, verrà visualizzato un messaggio che richiede di configurare.

### <a name="from-the-properties-page"></a>Dalla pagina delle proprietà

Se si vuole configurare o modificare l'account di archiviazione o il contenitore BLOB collegato alla risorsa di Application Insights, è possibile visualizzarla visualizzando la scheda *Proprietà* della risorsa di Application Insights.If you would like to configure or change the storage account or Blob container that is linked to your Application Insights Resource, you can do it by viewing the Application Insights resource tab.

1. Passare alla scheda *Proprietà* della risorsa Application Insights.
2. Fare clic su *Modifica contenitore BLOB della mappa di origine*.
3. Selezionare un contenitore BLOB diverso come contenitore di mappe di origine.
4. Fare clic su `Apply`.

> [!div class="mx-imgBorder"]
> ![Riconfigurare il contenitore BLOB di Azure selezionato passando al pannello ProprietàReconfigure your selected Azure Blob Container by navigating to the Properties Blade](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>Impostazioni necessarie per il controllo degli accessi in base al ruolo nel contenitore BLOBRequired role-based access control (RBAC) settings on your Blob container

Qualsiasi utente nel portale che usa questa funzionalità deve essere assegnato almeno come [lettore di dati BLOB][storage blob data reader] di archiviazione al contenitore BLOB. È necessario assegnare questo ruolo a chiunque utilizzi le mappe di origine tramite questa funzionalità.

> [!NOTE]
> A seconda di come è stato creato il contenitore, questo potrebbe non essere stato assegnato automaticamente all'utente o al team.

### <a name="source-map-not-found"></a>Mappa di origine non trovata

1. Verificare che la mappa di origine corrispondente venga caricata nel contenitore BLOB correttoVerify that the corresponding source map is uploaded to the correct blob container
2. Verificare che il file di mappa di origine sia denominato `.map`in base al file JavaScript a cui è mappato, con il suffisso .
    - Ad esempio, `/static/js/main.4e2ca5fa.chunk.js` cercherà il BLOB denominato`main.4e2ca5fa.chunk.js.map`
3. Controllare la console del browser per verificare se vengono registrati errori. Includilo in qualsiasi ticket di supporto.

## <a name="next-steps"></a>Passaggi successivi

* [Attività Copia file di AzureAzure File Copy task](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal
[storage blob data reader]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme