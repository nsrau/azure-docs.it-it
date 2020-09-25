---
title: Supporto della mappa di origine per applicazioni JavaScript-monitoraggio di Azure Application Insights
description: Informazioni su come caricare i mapping di origine nel contenitore BLOB dell'account di archiviazione usando Application Insights.
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5b15492a36032a53ac81929eb55bce0bc70e040c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335132"
---
# <a name="source-map-support-for-javascript-applications"></a>Supporto della mappa di origine per le applicazioni JavaScript

Application Insights supporta il caricamento delle mappe di origine nel contenitore BLOB dell'account di archiviazione.
È possibile usare le mappe di origine per unminify gli stack di chiamate trovati nella pagina dei dettagli della transazione end-to-end. Eventuali eccezioni inviate da [JavaScript SDK][ApplicationInsights-JS] o [ SDKNode.js][ApplicationInsights-Node.js] possono essere unminified con le mappe di origine.

![Unminify di uno stack di chiamate mediante collegamento a un account di archiviazione](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Creare un nuovo account di archiviazione e un contenitore BLOB

Se si ha già un account di archiviazione o un contenitore BLOB esistente, è possibile ignorare questo passaggio.

1. [Creare un nuovo account di archiviazione][create storage account]
2. [Creare un contenitore BLOB][create blob container] nell'account di archiviazione. Assicurarsi di impostare il "livello di accesso pubblico" su per `Private` assicurarsi che le mappe di origine non siano accessibili pubblicamente.

> [!div class="mx-imgBorder"]
>![Il livello di accesso del contenitore deve essere impostato su privato](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Eseguire il push delle mappe di origine al contenitore BLOB

È necessario integrare la pipeline di distribuzione continua con l'account di archiviazione configurando il codice per caricare automaticamente i mapping di origine nel contenitore BLOB configurato.

Le mappe di origine possono essere caricate nel contenitore di archiviazione BLOB con la stessa struttura di cartelle in cui sono state compilate & distribuite con. Un caso di utilizzo comune consiste nel prefisso di una cartella di distribuzione con la relativa versione, ad esempio `1.2.3/static/js/main.js` . Quando si unminifying tramite un contenitore BLOB di Azure denominato `sourcemaps` , si tenterà di recuperare una mappa di origine che si trova in `sourcemaps/1.2.3/static/js/main.js.map` .

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Caricare i mapping di origine tramite Azure Pipelines (scelta consigliata)

Se si usa Azure Pipelines per compilare e distribuire continuamente l'applicazione, aggiungere un'attività di [copia file di Azure][azure file copy] alla pipeline per caricare automaticamente le mappe di origine.

> [!div class="mx-imgBorder"]
> ![Aggiungere un'attività di copia file di Azure alla pipeline per caricare i mapping di origine nell'archiviazione BLOB di Azure](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>Configurare la risorsa di Application Insights con un account di archiviazione della mappa di origine

### <a name="from-the-end-to-end-transaction-details-page"></a>Dalla pagina dettagli transazione end-to-end

Dalla scheda Dettagli transazione end-to-end, è possibile fare clic su *Unminify* e verrà visualizzata una richiesta per configurare se la risorsa non è configurata.

1. Nel portale visualizzare i dettagli di un'eccezione minimizzati.
2. Fare clic su *Unminify*
3. Se la risorsa non è stata configurata, verrà visualizzato un messaggio che richiede di configurare.

### <a name="from-the-properties-page"></a>Dalla pagina proprietà

Per configurare o modificare l'account di archiviazione o il contenitore BLOB collegato alla risorsa di Application Insights, è possibile eseguire questa operazione visualizzando la scheda *proprietà* Application Insights della risorsa.

1. Passare alla scheda *Proprietà* della risorsa Application Insights.
2. Fare clic su *modifica il contenitore BLOB della mappa di origine*.
3. Selezionare un contenitore BLOB diverso come contenitore Maps di origine.
4. Fare clic su `Apply`.

> [!div class="mx-imgBorder"]
> ![Riconfigurare il contenitore BLOB di Azure selezionato passando al pannello delle proprietà](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>Impostazioni del controllo degli accessi in base al ruolo (RBAC) necessarie per il contenitore BLOB

Qualsiasi utente nel portale che usa questa funzionalità deve essere almeno assegnato come [lettore dati BLOB di archiviazione][storage blob data reader] al contenitore BLOB. È necessario assegnare questo ruolo a tutti gli altri utenti che utilizzeranno i mapping di origine tramite questa funzionalità.

> [!NOTE]
> A seconda di come è stato creato il contenitore, questo potrebbe non essere stato assegnato automaticamente all'utente o al team.

### <a name="source-map-not-found"></a>Mappa di origine non trovata

1. Verificare che la mappa di origine corrispondente venga caricata nel contenitore BLOB corretto
2. Verificare che il file di mapping di origine sia denominato dopo il file JavaScript a cui viene eseguito il mapping, con suffisso `.map` .
    - Ad esempio, `/static/js/main.4e2ca5fa.chunk.js` eseguirà la ricerca del BLOB denominato `main.4e2ca5fa.chunk.js.map`
3. Controllare la console del browser per verificare se sono stati registrati errori. Includere questo in qualsiasi ticket di supporto.

## <a name="next-steps"></a>Passaggi successivi

* [Attività copia file di Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: ../../storage/common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: ../../storage/blobs/storage-quickstart-blobs-portal.md
[storage blob data reader]: ../../role-based-access-control/built-in-roles.md#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme
