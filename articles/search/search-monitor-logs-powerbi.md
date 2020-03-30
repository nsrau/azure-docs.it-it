---
title: Visualizzare i log di ricerca cognitivi di Azure e le metriche con Power BI
description: Visualizzare i log di ricerca cognitivi di Azure e le metriche con Power BI
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 618033e24236d26601fafaf7b0678ee83ec973e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650154"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Visualizzare i log di ricerca cognitivi di Azure e le metriche con Power BI
[Ricerca cognitiva di Azure](https://docs.microsoft.com/azure/search/search-what-is-azure-search) consente di archiviare i log delle operazioni e le metriche del servizio sul servizio di ricerca in un account di Archiviazione di Azure.Azure Cognitive Search allows you to store operation logs and service metrics about your search service in an Azure Storage account. In questa pagina vengono fornite istruzioni su come visualizzare tali informazioni tramite un'app modello di Power BI. L'app fornisce informazioni dettagliate sul servizio di ricerca, incluse informazioni su ricerca, indicizzazione, operazioni e metriche del servizio.

È possibile trovare ricerca cognitiva di Azure Template App di Power **BI: Analizzare log e metriche** nel [marketplace di Power BI Apps.](https://appsource.microsoft.com/marketplace/apps)

## <a name="how-to-get-started-with-the-app"></a>Come iniziare a usare l'app
1. Abilitare la registrazione diagnostica per il servizio di ricerca:Enable diagnostic logging for your search service:
    1. Creare o identificare un account di archiviazione di Azure esistente in cui è possibile archiviare i logCreate or identify an existing [Azure Storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) where you can archive the logs
    1. Passare al servizio Ricerca cognitiva di Azure nel portale di AzureNavigate to your Azure Cognitive Search service in the Azure portal
    1. Nella sezione Monitoraggio nella colonna sinistra, seleziona **Impostazioni di diagnostica**

        ![](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. Selezionare **: Aggiungi impostazione diagnostica**
    1. Controllare **Archivia in un account di archiviazione,** fornire le informazioni sull'account di archiviazione e controllare OperationLogs e **AllMetricsCheck** Archive to a storage Account , provide your Storage Account information, and check **OperationLogs** and AllMetrics

        ![](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. Selezionare **Salva**

1. Dopo aver abilitato la registrazione, utilizzare il servizio di ricerca per avviare la generazione di log e metriche. Ci vuole fino a un'ora prima che i contenitori vengano visualizzati nell'archivio BLOB con questi log. Verrà visualizzato un contenitore **insights-logs-operationlogs** per i log del traffico di ricerca e un contenitore **insights-metrics-pt1m** per le metriche.

1. Trovare l'app Power BI Ricerca cognitiva di Azure nel [marketplace di Power BI Apps](https://appsource.microsoft.com/marketplace/apps) e installarla in una nuova area di lavoro o in un'area di lavoro esistente. L'app è denominata Ricerca cognitiva di **Azure: analizza log e metriche**.

1. Dopo aver installato l'app, selezionarla dall'elenco di app in Power BI.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. Selezionare **Connetti** per connettere i dati

    ![](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. Immettere il nome dell'account di archiviazione che contiene i log e le metriche. Per impostazione predefinita, l'app esaminerà gli ultimi 10 giorni di dati, ma questo valore può essere modificato con il parametro **Days.**

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. Selezionare **Chiave** come metodo di autenticazione e fornire la chiave dell'account di archiviazione. Selezionare **Privato** come livello di privacy. Fare clic su Accedi per iniziare il processo di caricamento.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. Attendere l'aggiornamento dei dati. L'operazione potrebbe richiedere del tempo a seconda della quantità di dati disponibili. È possibile vedere se i dati sono ancora in fase di aggiornamento in base all'indicatore sottostante.

    ![](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. Al termine dell'aggiornamento dati, selezionare **Report ricerca cognitivo** di Azure per visualizzare il report.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. Assicurarsi di aggiornare la pagina dopo aver aperto il report in modo che venga aperto con i dati.

    ![](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>Come modificare i parametri dell'app
Se si desidera visualizzare i dati da un account di archiviazione diverso o modificare il numero di giorni di dati su cui eseguire una query, eseguire la procedura seguente per modificare i parametri **Days** e **StorageAccount.**

1. Passare alle app di Power BI, trovare l'app Ricerca cognitiva di Azure e selezionare il pulsante **Modifica app** per visualizzare l'area di lavoro.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. Selezionare **Impostazioni** dalle opzioni Set di dati.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. Nella scheda Set di dati modificare i valori dei parametri e selezionare **Applica**. Se si verifica un problema con la connessione, aggiornare le credenziali dell'origine dati nella stessa pagina.

1. Tornare all'area di lavoro e selezionare **Aggiorna ora** dalle opzioni Set di dati.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. Aprire il report per visualizzare i dati aggiornati. Potrebbe anche essere necessario aggiornare il report per visualizzare i dati più recenti.

## <a name="troubleshooting"></a>Risoluzione dei problemi
Se ritieni di non visualizzare i tuoi dati, segui questi passaggi per la risoluzione dei problemi:

1. Aprire il report e aggiornare la pagina per assicurarsi di visualizzare i dati più recenti. Nel report è disponibile un'opzione per aggiornare i dati. Selezionare questa opzione per ottenere i dati più recenti.

1. Verificare che il nome dell'account di archiviazione e la chiave di accesso specificati siano corretti. Il nome dell'account di archiviazione deve corrispondere all'account configurato con i log del servizio di ricerca.

1. Verificare che l'account di archiviazione contenga i contenitori **insights-logs-operationlogs** e **insights-metrics-pt1m** e ogni contenitore dispone di dati. I log e le metriche saranno all'interno di un paio di livelli di cartelle.

1. Verificare se il set di dati è ancora in fase di aggiornamento. L'indicatore di stato dell'aggiornamento viene visualizzato nel passaggio 8 precedente. Se è ancora in fase di aggiornamento, attendere il completamento dell'aggiornamento per aprire e aggiornare il report.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Ricerca cognitiva di AzureLearn more about Azure Cognitive Search](https://docs.microsoft.com/azure/search/)

[Che cos'è Power BI?](https://docs.microsoft.com/power-bi/fundamentals/power-bi-overview)

[Concetti di base del servizio Power BI](https://docs.microsoft.com/power-bi/service-basic-concepts)