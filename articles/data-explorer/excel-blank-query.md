---
title: Visualizzare i dati usando una query Kusto di Azure Data Explorer importata in Microsoft Excel
description: In this article, you learn how to import an Azure Data Explorer Kusto query into Microsoft Excel.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4999000e2084922b43b8085034f545d4b5c644a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849089"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Visualizzare i dati usando una query Kusto di Azure Data Explorer importata in Microsoft Excel

Azure Data Explorer provides two options for connecting to data in Excel: use the native connector or import a query from Azure Data Explorer. Questo articolo illustra come importare una query da Azure Data Explorer in Excel per visualizzare i dati. Aggiungere una query Kusto come origine dati Excel per eseguire ulteriori calcoli o visualizzazioni sui dati.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Un account di posta elettronica dell'organizzazione membro di Azure Active Directory, pertanto è possibile connettersi al cluster della Guida di [Azure Data ExplorerAn](https://dataexplorer.azure.com/clusters/help/databases/Samples) organizational email account that is a member of Azure Active directory, so you can connect to the Azure Data Explorer help cluster 
<br>o</br>
* Creare [un cluster e un database](create-cluster-database-portal.md) di test e accedere [all'applicazione dell'interfaccia utente Web](https://dataexplorer.azure.com/)di Azure Data Explorer.

## <a name="define-kusto-query-as-an-excel-data-source"></a>Definire la query Kusto come origine dati di ExcelDefine Kusto query as an Excel data source

1. [Nell'interfaccia utente Web](https://dataexplorer.azure.com/clusters/help/databases/Samples)di Azure Data Explorer eseguire la query e controllare i risultati.

1. Selezionare la scheda **Condividi** e selezionare **Query a Power BI.**

    ![Query dell'interfaccia utente Web a Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Viene visualizzata una finestra con la seguente notifica:

    ![esportare query negli Appunti](media/excel-blank-query/query-exported-to-clipboard.png)

1. Aprire **Microsoft Excel**.

1. Nella scheda **Dati** selezionare **Ottieni dati** > **da altre origini** > **Query vuota**.

    ![Ottenere dati e selezionare una query vuotaGet data and select blank query](media/excel-blank-query/get-data-blank-query.png)

1. Verrà visualizzata la finestra **Editor di Power Query.** Nella finestra selezionare **Editor avanzato**.

    ![Finestra dell'editor di Power Query](media/excel-blank-query/power-query-editor.png)

1. Nella finestra **Editor avanzato** incollare la query esportata negli Appunti e selezionare **Fatto**.

    ![Query editor avanzata](media/excel-blank-query/advanced-editor-query.png)    

1. Per eseguire l'autenticazione, selezionare **Modifica credenziali**.

    ![Modificare le credenziali](media/excel-blank-query/edit-credentials.png)

1. Selezionare **Account dell'organizzazione** e **Accedi**. Completare il processo di accesso e quindi selezionare **Connetti**.

    ![Completare l'accesso](media/excel-blank-query/complete-sign-in.png)

    Ripetere i passaggi precedenti per aggiungere altre query. È possibile rinominare le query con nomi più significativi.

1. Selezionare il pulsante **Chiudi & carico** per ottenere i dati in Excel.

    ![Selezionare Chiudi e carica](media/excel-blank-query/close-and-load.png)

1. Ora i tuoi dati sono in Excel. Selezionare il pulsante **Aggiorna** per aggiornare la query.

    ![Visualizzare i dati in Excel](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>Passaggi successivi

[Visualizzare i dati usando il connettore di Azure Data Explorer per ExcelDisplay data using the Azure Data Explorer connector for Excel](excel-connector.md)