---
title: Usare i blocchi appunti di Azure per analizzare i dati in Azure Data ExplorerUse Azure Notebooks to analyze data in Azure Data Explorer
description: Questo argomento illustra come creare una query usando un blocco appunti di AzureThis topic shows you how to create a query using an Azure Notebook
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 0f99e11be99f22feec73b72397b27522b90dbf49
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522407"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>Usare i blocchi appunti di Azure per analizzare i dati in Azure Data ExplorerUse Azure Notebooks to analyze data in Azure Data Explorer

[Blocchi appunti](https://notebooks.azure.com/) di Azure è un servizio cloud di Azure che semplifica la creazione e la condivisione di [blocchi appunti jupyter.](https://jupyter.org/) Blocchi appunti di Azure semplifica la combinazione di documentazione, codice e risultati dell'esecuzione del codice.

> [!Note]
> * The following procedure uses the Python client in the Azure Notebooks environment to query Azure Data Explorer. Tuttavia, è anche possibile usare KQLmagic per eseguire query su Azure Data Explorer.However, you can also use [KQLmagic](https://docs.microsoft.com/azure/data-explorer/kqlmagic) to query Azure Data Explorer.
> * Alcuni utenti hanno segnalato problemi di autenticazione tramite Edge; fino a quando tali problemi sono risolti, utilizzare un browser diverso.

## <a name="create-a-project"></a>Creare un progetto

1. Aprire [Blocchi appunti](https://notebooks.azure.com/) di Azure nel browser e accedere.

1. Selezionare la scheda **Progetti personali** nell'intestazione. 

    [![](media/azurenotebooks/an-myprojects.png "My projects")](media/azurenotebooks/an-myprojects.png#lightbox)

1. Selezionare **: Nuovi progetti**.
    
1. Nella finestra di dialogo **Crea nuovo progetto:**
    1. Immettere il **nome del progetto**.
    1. Deselezionare la casella di controllo **Pubblico.**
        >[!Important]
        > Se non si deseleziona la casella di controllo Pubblico, il progetto verrà esposto su Internet aperto.
    1. Selezionare **Create** (Crea).
    
    ![Creare un nuovo progetto](media/azurenotebooks/an-create-new-project-blank.png)

    L'ID progetto viene creato automaticamente.

## <a name="create-a-notebook"></a>Creare un notebook

1. Nel nuovo progetto creare un blocco appunti. Il blocco appunti deve utilizzare una [lingua supportata.](https://github.com/Azure/azure-kusto-python#minimum-requirements)
Per creare un blocco appunti, selezionare **Nuovo** e selezionare **Blocco appunti**.

    ![Creare un nuovo blocco appunti](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. Nella finestra di dialogo **Crea nuovo blocco appunti** immettere il nome del blocco appunti.

1. Selezionare **Python 3.6** e selezionare **Nuovo**.
    
    ![Creare un nuovo blocco appunti](media/azurenotebooks/an-create-new-notebook.png) 
    
1. Nel progetto selezionare il nuovo blocco appunti.

    ![Selezionare un nuovo blocco appunti](media/azurenotebooks/an-select-notebook.png)

    Attendere l'inizializzazione del kernel Python. Quando l'icona del cerchio pieno diventa un'icona a forma di cerchio vuoto, è possibile procedere.

    ![Inizializzazioni del kernel](media/azurenotebooks/an-python-init-icon.png)

1. Per importare il modulo di sistema, immettere i seguenti comandi e selezionare **Esegui**:
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > Per eseguire una cella, è anche possibile premere Maiusc-Invio.

1.  Per importare le classi SDK, immettere i seguenti comandi e selezionare **Esegui:**
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  Per compilare la stringa di connessione e avviare il client Kusto, immettere i comandi seguenti e selezionare **Esegui:**  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. Quando richiesto, aprire una nuova [https://aka.ms/devicelogin](https://aka.ms/devicelogin)scheda del browser per . 
   
1. Immettere il codice di autorizzazione e@microsoft.comaccedere all'account AAD ( ). Il client `kc` Kusto può ora eseguire l'autenticazione in Azure Data Explorer usando la tua identità.

1. Tornare al blocco appunti per visualizzare il risultato dell'autenticazione. 

[![](media/azurenotebooks/an-python-commands.png "Python commands")](media/azurenotebooks/an-python-commands.png#lightbox)

## <a name="execute-a-kusto-query"></a>Eseguire una query Kusto

1. Inserisci la tua query Kusto e seleziona **Esegui**. Ad esempio:

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

[![](media/azurenotebooks/an-commands.png "Python commands")](media/azurenotebooks/an-commands.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* [Kusto-python GitHub repo](https://github.com/Azure/azure-kusto-python)
