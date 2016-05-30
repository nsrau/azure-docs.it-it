<properties
   pageTitle="Gestire la potenza di calcolo in Azure SQL Data Warehouse (portale di Azure) | Microsoft Azure"
   description="Attività del portale di Azure per la gestione della potenza di calcolo. Ridimensionare le risorse di calcolo cambiando il numero di DWU. In alternativa, sospendere e riprendere le risorse di calcolo per ridurre i costi."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/06/2016"
   ms.author="barbkess;sonyama"/>

# Gestire la potenza di calcolo in Azure SQL Data Warehouse (portale di Azure)

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-overview-manage-compute.md)
- [Portale](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Ridimensionare le prestazioni tramite la scalabilità orizzontale delle risorse di calcolo e della memoria per soddisfare le diverse esigenze del carico di lavoro. Risparmiare sui costi ridimensionando le risorse durante le ore non di punta o sospendendo completamente il calcolo.

Questa raccolta di attività usa il portale di Azure per:

- Ridimensionare le risorse di calcolo
- Sospendere le risorse di calcolo
- Riavviare le risorse di calcolo

Per altre informazioni, vedere [Manage compute power overview][] (Panoramica sulla gestione della potenza di calcolo).

<a name="scale-performance-bk"></a> <a name="scale-compute-bk"></a>

## Ridimensionare la potenza di calcolo

[AZURE.INCLUDE [Descrizione della scalatura di DWU di SQL Data Warehouse](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Per modificare le risorse di calcolo:

1. Aprire il [portale di Azure][], aprire il database e fare clic su **Ridimensiona**.

    ![Fare clici su Ridimensiona][1]

1. Nel pannello Scala, spostare il dispositivo di scorrimento a sinistra o a destra per modificare l'impostazione delle DWU.

    ![Spostare il dispositivo di scorrimento][2]

1. Fare clic su **Save**. Verrà visualizzato un messaggio di conferma. Fare clic su **Sì** per confermare o su **No** per annullare.

    ![Fare clic su Salva.][3]

<a name="pause-compute-bk"></a>

## Sospendere le risorse di calcolo

[AZURE.INCLUDE [Descrizione della sospensione di SQL Data Warehouse](../../includes/sql-data-warehouse-pause-description.md)]

Per sospende un database:

1. Aprire il [portale di Azure][] e aprire il database. Si noti che lo stato è **Online**. 

    ![Stato online][6]

1. Per sospendere il calcolo e le risorse di memoria, fare clic su **Sospendi**. Viene visualizzato un messaggio di conferma. Fare clic su **Sì** per confermare o su **No** per annullare.

    ![Conferma sospensione][7]

1. Durante l'avvio di SQL Data Warehouse lo stato del database sarà Sospensione in corso.
2. Quando lo stato diventa **Sospeso**, l'operazione di sospensione è completa e le DWU non vengono più addebitate.

    ![Stato di sospensione][4]

<a name="resume-compute-bk"></a>

## Riavviare le risorse di calcolo

[AZURE.INCLUDE [Descrizione della riattivazione di SQL Data Warehouse](../../includes/sql-data-warehouse-resume-description.md)]Per riattivare un database:

1. Aprire il [portale di Azure][] e aprire il database. Si noti che lo stato è **Sospeso**. 

    ![Database in pausa][4]

1. Per riattivare il database fare clic su **Avvia**. Verrà visualizzato un messaggio di conferma. Fare clic su **Sì** per confermare o su **No** per annullare.

    ![Conferma riattivazione][5]

1. Durante l'avvio di SQL Data Warehouse lo stato del database sarà Ripresa in corso.
2. Quando lo stato diventa **Online** il database è pronto.

    ![Stato online][6]

<a name="next-steps-bk"></a>

## Passaggi successivi
Per altre informazioni, vedere [Panoramica della gestione][].

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
[Manage compute power overview]: ./sql-data-warehouse-overview-manage-compute.md

<!--MSDN references-->


<!--Other Web references-->

[portale di Azure]: http://portal.azure.com/

<!---HONumber=AcomDC_0518_2016-->