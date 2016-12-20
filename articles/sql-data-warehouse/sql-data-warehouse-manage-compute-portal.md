---
title: Gestire la potenza di calcolo in Azure SQL Data Warehouse (Portale di Azure) | Microsoft Docs
description: "Attività del portale di Azure per la gestione della potenza di calcolo. Ridimensionare le risorse di calcolo cambiando il numero di DWU. In alternativa, sospendere e riprendere le risorse di calcolo per ridurre i costi."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 233b0da5-4abd-4d1d-9586-4ccc5f50b071
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: c1c23ab46d9b4e43154a62080cb8865b246489f9
ms.openlocfilehash: 2e11486203f04d671548b6132e61f804acb7f90a


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>Gestire la potenza di calcolo in Azure SQL Data Warehouse (portale di Azure)
> [!div class="op_single_selector"]
> * [Panoramica](sql-data-warehouse-manage-compute-overview.md)
> * [Portale](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

Ridimensionare le prestazioni tramite la scalabilità orizzontale delle risorse di calcolo e della memoria per soddisfare le diverse esigenze del carico di lavoro. Risparmiare sui costi ridimensionando le risorse durante le ore non di punta o sospendendo completamente il calcolo.

Questa raccolta di attività usa il portale di Azure per:

* Ridimensionare le risorse di calcolo
* Sospendere le risorse di calcolo
* Riavviare le risorse di calcolo

Per altre informazioni, vedere [Panoramica sulla gestione del calcolo][Panoramica sulla gestione del calcolo] (Panoramica sulla gestione del calcolo).

## <a name="scale-compute-power"></a>Ridimensionare la potenza di calcolo
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Per modificare le risorse di calcolo:

1. Aprire il [portale di Azure][portale di Azure], aprire il database e fare clic su **Scala**.

    ![Fare clici su Ridimensiona][1]
2. Nel pannello Scala, spostare il dispositivo di scorrimento a sinistra o a destra per modificare l'impostazione delle DWU.

    ![Spostare il dispositivo di scorrimento][2]
3. Fare clic su **Save**. Viene visualizzato un messaggio di conferma. Fare clic su **Sì** per confermare o su **No** per annullare.

    ![Fare clic su Salva.][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Sospendere il calcolo
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Per sospende un database:

1. Aprire il [portale di Azure][portale di Azure] e aprire il database. Osservare che lo stato è **Online**.

    ![Stato online][6]
2. Per sospendere il calcolo e le risorse di memoria, fare clic su **Sospendi**. Viene visualizzato un messaggio di conferma. Fare clic su **Sì** per confermare o su **No** per annullare.

    ![Conferma sospensione][7]
3. Durante l'avvio di SQL Data Warehouse lo stato del database è **Sospensione in corso**.
4. Quando lo stato diventa **Sospeso**, l'operazione di sospensione è completa e le DWU non vengono più addebitate.

    ![Stato di sospensione][4]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Riavviare le risorse di calcolo
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Per riattivare un database:

1. Aprire il [portale di Azure][portale di Azure] e aprire il database. Osservare che lo stato è **Sospeso**.

    ![Database in pausa][4]
2. Per riattivare il database fare clic su **Avvia**. Viene visualizzato un messaggio di conferma. Fare clic su **Sì** per confermare o su **No** per annullare.

    ![Conferma riattivazione][5]
3. Durante l'avvio di SQL Data Warehouse lo stato del database è "Sospensione in corso".
4. Quando lo stato diventa **Online**il database è pronto.

    ![Stato online][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere [Panoramica della gestione][Panoramica della gestione].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Panoramica della gestione]: ./sql-data-warehouse-overview-manage.md
[Panoramica sulla gestione del calcolo]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[portale di Azure]: http://portal.azure.com/



<!--HONumber=Nov16_HO3-->


