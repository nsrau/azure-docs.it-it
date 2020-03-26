---
title: Ridimensionare le risorse di calcolo per il pool SQL di Synapse (portale di Azure)
description: È possibile ridimensionare le risorse di calcolo per il pool SQL di Synapse (data warehouse) usando il portale di Azure.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: e7e16615df464358fa22e3d03488866022b87d9a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79539437"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Guida introduttiva: Ridimensionare le risorse di calcolo per il pool SQL di Synapse con il portale di Azure

È possibile ridimensionare le risorse di calcolo per il pool SQL di Synapse (data warehouse) usando il portale di Azure. [aumentandone il numero](sql-data-warehouse-manage-compute-overview.md) per ottenere prestazioni migliori o riducendolo per diminuire i costi. 

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Prima di iniziare

È possibile ridimensionare un pool SQL già disponibile oppure fare riferimento a [Avvio rapido: Creare e connettere - portale](create-data-warehouse-portal.md) per creare un pool SQL denominato **mySampleDataWarehouse**. In questa Guida introduttiva viene ridimensionato **mySampleDataWarehouse**.

>[!IMPORTANT] 
>Il pool SQL da ridimensionare deve essere online. 

## <a name="scale-compute"></a>Ridimensionare le risorse di calcolo

È possibile ridimensionare le risorse di calcolo del pool SQL aumentando o diminuendo le unità di data warehouse. Nella guida introduttiva [Creare e connettere - portale] (create-data-warehouse-portal.md) **mySampleDataWarehouse** è stato creato e inizializzato con 400 unità Data Warehouse. La procedura seguente modifica le unità Data Warehouse per **mySampleDataWarehouse**.

Per modificare le unità Data Warehouse:

1. Fare clic su **Azure Synapse Analytics (in precedenza SQL DW)** nella pagina sinistra del portale di Azure.
2. Selezionare **mySampleDataWarehouse** nella pagina **Azure Synapse Analytics (in precedenza SQL DW)** . Verrà aperto il pool SQL.
3. Fare clic su **Scale**.

    ![Fare clici su Ridimensiona](./media/quickstart-scale-compute-portal/click-scale.png)

2. Nel pannello Ridimensiona spostare il dispositivo di scorrimento a sinistra o a destra per modificare l'impostazione delle unità Data Warehouse. Quindi selezionare Ridimensiona.

    ![Spostare il dispositivo di scorrimento](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul pool SQL, continuare con l'esercitazione [Caricare i dati in un pool SQL](load-data-from-azure-blob-storage-using-polybase.md). 
