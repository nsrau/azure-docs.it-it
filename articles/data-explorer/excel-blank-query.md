---
title: Visualizzare i dati usando una query kusto di Azure Esplora dati importata in Microsoft Excel
description: Questo articolo illustra come importare una query Azure Esplora dati kusto in Microsoft Excel.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4999000e2084922b43b8085034f545d4b5c644a9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849089"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Visualizzare i dati usando una query kusto di Azure Esplora dati importata in Microsoft Excel

Azure Esplora dati offre due opzioni per la connessione ai dati in Excel: usare il connettore nativo o importare una query da Azure Esplora dati. Questo articolo illustra come importare una query da Azure Esplora dati in Excel per visualizzare i dati. Aggiungere una query kusto come origine dati di Excel per eseguire calcoli o visualizzazioni aggiuntive sui dati.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, prima di iniziare creare un [account Azure gratuito](https://azure.microsoft.com/free/).
* Un account di posta elettronica aziendale che è membro di Azure Active Directory, quindi è possibile connettersi al [cluster della Guida di azure Esplora dati](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>Oppure</br>
* Creare [un cluster e un database di test](create-cluster-database-portal.md) e accedere all' [applicazione dell'interfaccia utente Web di Azure Esplora dati](https://dataexplorer.azure.com/).

## <a name="define-kusto-query-as-an-excel-data-source"></a>Definire una query kusto come origine dati di Excel

1. Nell' [interfaccia utente Web di Esplora dati Azure](https://dataexplorer.azure.com/clusters/help/databases/Samples)eseguire la query e verificare i risultati.

1. Selezionare la scheda **Condividi** e selezionare **query per Power bi**.

    ![Query dell'interfaccia utente Web per Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Viene visualizzata una finestra con la seguente notifica:

    ![esporta query negli Appunti](media/excel-blank-query/query-exported-to-clipboard.png)

1. Aprire **Microsoft Excel**.

1. Nella scheda **dati** selezionare **Ottieni dati** > **da altre origini** > **query vuota**.

    ![Recuperare i dati e selezionare una query vuota](media/excel-blank-query/get-data-blank-query.png)

1. Verrà visualizzata la finestra **Editor Power query** . Nella finestra di selezionare **Editor avanzato**.

    ![Finestra dell'editor di Power query](media/excel-blank-query/power-query-editor.png)

1. Nella finestra di **Editor avanzato** incollare la query esportata negli Appunti e selezionare **fine**.

    ![Query dell'editor avanzato](media/excel-blank-query/advanced-editor-query.png)    

1. Per eseguire l'autenticazione, selezionare **Modifica credenziali**.

    ![Modificare le credenziali](media/excel-blank-query/edit-credentials.png)

1. Selezionare **account aziendale** ed **accedere**. Completare il processo di accesso e quindi selezionare **Connetti**.

    ![Completamento dell'accesso](media/excel-blank-query/complete-sign-in.png)

    Ripetere i passaggi precedenti per aggiungere altre query. È possibile rinominare le query in nomi più significativi.

1. Selezionare il pulsante **chiudi & Load** per inserire i dati in Excel.

    ![Selezionare Chiudi e carica](media/excel-blank-query/close-and-load.png)

1. Ora i dati sono in Excel. Selezionare il pulsante **Aggiorna** per aggiornare la query.

    ![Visualizzare i dati in Excel](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>Passaggi successivi

[Visualizzare i dati con Azure Esplora dati Connector per Excel](excel-connector.md)