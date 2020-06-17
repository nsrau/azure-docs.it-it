---
title: "Avvio rapido: Creare un'area di lavoro di Synapse"
description: Creare un'area di lavoro di Synapse seguendo la procedura descritta in questa guida.
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 6f72e2ee3b83b693d889e9c43fe42a4677f1666d
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84609472"
---
# <a name="quickstart-create-an-azure-synapse-analytics-workspace-preview"></a>Avvio rapido: Creare un'area di lavoro di Azure Synapse Analytics (anteprima)

Questo argomento di avvio rapido descrive la procedura per creare un'area di lavoro di Azure Synapse Analytics con il portale di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- [Account di archiviazione di Azure Data Lake Storage Gen2 ](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/)

## <a name="create-an-azure-synapse-workspace-using-the-azure-portal"></a>Creare un'area di lavoro di Azure Synapse Analytics con il portale di Azure

1. Nel riquadro di ricerca di Microsoft Azure immettere **area di lavoro Synapse** quindi selezionare questo servizio.
![Barra di ricerca del portale di Azure con le aree di lavoro di Azure Synapse immesse.](media/quickstart-create-synapse-workspace/workspace-search.png)
2. Nella pagina **Aree di lavoro Synapse** fare clic su **+ Aggiungi**.
![Comando per creare una nuova area di lavoro di Azure Synapse evidenziato.](media/quickstart-create-synapse-workspace/create-workspace-02.png)
3. Compilare il modulo **Area di lavoro Azure Synapse** con le informazioni seguenti:

    | Impostazione | Valore consigliato | Descrizione |
    | :------ | :-------------- | :---------- |
    | **Sottoscrizione** | *Sottoscrizione in uso* | Per informazioni dettagliate sulle sottoscrizioni, vedere [Sottoscrizioni](https://account.windowsazure.com/Subscriptions). |
    | **Gruppo di risorse** | *Any resource group* | Per i nomi di gruppi di risorse validi, vedere [Regole di denominazione e restrizioni](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
    | **Nome area di lavoro** | mysampleworkspace | Specifica il nome dell'area di lavoro, che verrà usato anche per gli endpoint di connessione.|
    | **Area** | Stati Uniti Orientali 2 | Specifica la località dell'area di lavoro.|
    | **Data Lake Storage Gen2** | Account: `storage account name` </br> File system: `root file system to use` | Specifica il nome dell'account di archiviazione di ADLS Gen2 da usare come archiviazione primaria e il file system da usare.|
    ||||

    ![Flusso di provisioning dell'area di lavoro - Scheda Informazioni di base.](media/quickstart-create-synapse-workspace/create-workspace-03.png)

    È possibile selezionare l'account di archiviazione tramite:
    - Un elenco di account di archiviazione di ADLS Gen2 disponibili nella sottoscrizione
    - Immissione manuale del nome dell'account

    > [!IMPORTANT]
    > L'area di lavoro di Azure Synapse deve essere in grado di leggere e scrivere nell'account di ADLS Gen2 selezionato. Inoltre, per qualsiasi account di archiviazione collegato come account di archiviazione primario, è necessario avere abilitato lo **spazio dei nomi gerarchico** durante la creazione dell'account di archiviazione.
    >
    > Sotto i campi di selezione di ADLS Gen2 è presente una nota che indica che l'identità gestita dell'area di lavoro verrà assegnata al ruolo **Collaboratore ai dati dei BLOB di archiviazione** nel file system di Data Lake Storage Gen2 selezionato, concedendo l'accesso completo ai dati.

4. (Facoltativo) Modificare le impostazioni predefinite della scheda **Sicurezza + rete**:
5. (Facoltativo) Aggiungere eventuali tag nella scheda **Tag**.
6. Nella scheda **Riepilogo** verranno eseguite le convalide necessarie per assicurare che l'area di lavoro possa essere creata correttamente. Quando la convalida ha esito positivo, fare clic su **Crea** ![Scheda di conferma del flusso di provisioning dell'area di lavoro.](media/quickstart-create-synapse-workspace/create-workspace-05.png)
7. Una volta completato il processo di provisioning delle risorse, verrà visualizzata una voce per l'area di lavoro creata nell'elenco delle aree di lavoro di Synapse. ![Elenco delle aree di lavoro di Synapse che mostra l'area di lavoro appena sottoposta a provisioning.](media/quickstart-create-synapse-workspace/create-workspace-07.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare l'area di lavoro di Azure Synapse, seguire questa procedura.
> [!WARNING]
> Con l'eliminazione di un'area di lavoro di Azure Synapse, verranno rimossi anche i motori di analisi e i dati archiviati nel database dei pool SQL contenuti e dei metadati dell'area di lavoro. Non sarà più possibile connettersi agli endpoint SQL e Apache Spark. Tutti gli artefatti del codice verranno eliminati (query, notebook, definizioni di processi e pipeline).
>
> L'eliminazione dell'area di lavoro **non** influisce sui dati dell'istanza di Data Lake Store Gen2 collegata all'area di lavoro.

Per eliminare l'area di lavoro di Azure Synapse, completare la procedura seguente:

1. Passare all'area di lavoro di Azure Synapse da eliminare.
1. Premere **Elimina** sulla barra dei comandi.
 ![Panoramica dell'area di lavoro di Azure Synapse con il comando Elimina evidenziato.](media/quickstart-create-synapse-workspace/create-workspace-10.png)
1. Confermare l'eliminazione e fare clic sul pulsante **Elimina**.
 ![Panoramica dell'area di lavoro di Azure Synapse, finestra di dialogo di conferma dell'eliminazione dell'area di lavoro.](media/quickstart-create-synapse-workspace/create-workspace-11.png)
1. Quando il processo viene completato, l'area di lavoro di Azure Synapse non sarà più presente nell'elenco di aree di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Successivamente, è possibile [creare pool SQL](quickstart-create-sql-pool-studio.md) o [pool di Apache Spark](quickstart-create-apache-spark-pool-studio.md) per iniziare ad analizzare ed esplorare i dati.
