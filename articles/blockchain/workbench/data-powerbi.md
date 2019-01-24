---
title: Usare i dati di Azure Blockchain Workbench in Microsoft Power BI
description: Informazioni su come caricare e visualizzare i dati del database SQL di Azure Blockchain Workbench in Microsoft Power BI.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: e50a82f0f141fec266a8348c0a1a8b5fbdb38b45
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329855"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Usare i dati di Azure Blockchain Workbench in Microsoft Power BI

Microsoft Power BI offre la possibilità di generare in modo semplice report avanzati dai database del database SQL con Power BI Desktop e quindi pubblicarli all'indirizzo [https://www.powerbi.com](http://www.powerbi.com).

Questo articolo contiene una procedura dettagliata su come connettersi al database SQL di Azure Blockchain Workbench da Power BI Desktop, creare un report e distribuirlo in powerbi.com.

## <a name="prerequisites"></a>Prerequisiti

* Scaricare [Power BI Desktop](https://aka.ms/pbidesktopstore).

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Connessione di Power BI ai dati in Azure Blockchain Workbench

1.  Aprire Power BI Desktop.
2.  Selezionare **Recupera dati**.

    ![Ottenere i dati](./media/data-powerbi/get-data.png)
3.  Selezionare **SQL Server** nell'elenco dei tipi di origine dati.

4.  Specificare il nome del server e del database nella finestra di dialogo. Specificare se si desidera importare i dati o eseguire una **DirectQuery**. Selezionare **OK**.

    ![Selezionare SQL Server](./media/data-powerbi/select-sql.png)

5.  Indicare le credenziali del database per accedere ad Azure Blockchain Workbench. Selezionare **Database** e immettere le credenziali.

    Se si usano le credenziali create durante il processo di distribuzione di Azure Blockchain Workbench, il nome utente è **dbadmin** e la password è quella indicata durante la distribuzione.

    ![Impostazioni del database SQL](./media/data-powerbi/db-settings.png)

6.  Dopo aver eseguito la connessione al database, la finestra di dialogo **Strumento di spostamento** mostra le tabelle e le viste disponibili nel database. Le viste sono progettate per creare report e hanno il prefisso **vw**.

    ![Strumento di spostamento](./media/data-powerbi/navigator.png)

7.  Selezionare le viste che si desidera includere. A scopo dimostrativo, si include **vwContractAction**, che offre informazioni dettagliate su tutte le azioni eseguite su un contratto.

    ![Selezionare le viste](./media/data-powerbi/select-views.png)

È ora possibile creare e pubblicare i report come di consueto in Power BI.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Database views in Azure Blockchain Workbench](database-views.md) (Viste di database in Azure Blockchain Workbench)