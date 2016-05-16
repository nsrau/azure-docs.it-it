<properties
   pageTitle="Gestire le operazioni di scalabilità per Azure SQL Data Warehouse (portale di Azure) | Microsoft Azure"
   description="Attività del portale di Azure per scalare orizzontalmente le prestazioni per Azure SQL Data Warehouse. Modificare le risorse di calcolo cambiando il numero di DWU. In alternativa, sospendere e riprendere le risorse di calcolo per ridurre i costi."
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
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Gestire le operazioni di scalabilità per Azure SQL Data Warehouse (portale di Azure)

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-overview-scalability.md)
- [Portale](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)


La scalatura orizzontale elastica delle risorse di calcolo e di memoria consente di soddisfare le variazioni di domanda del carico di lavoro e di contenere i costi, riducendo le risorse durante i periodi non di picco.

Questa raccolta di attività usa il portale di Azure per:

- Scalare le prestazioni modificando le DWU
- Sospendere l'attività delle risorse di calcolo
- Ripristinare l'attività delle risorse di calcolo

Per altre informazioni, vedere [Panoramica sulla scalabilità delle prestazioni][].

<a name="scale-performance-bk"></a>

## Scalare le prestazioni

[AZURE.INCLUDE [Descrizione della scalatura di DWU di SQL Data Warehouse](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Per modificare le risorse di calcolo:

1. Aprire il [portale di Azure][], aprire il database e fare clic su **Scala**.

    ![Fare clici su Ridimensiona][1]

1. Nel pannello Scala, spostare il dispositivo di scorrimento a sinistra o a destra per modificare l'impostazione delle DWU.

    ![Spostare il dispositivo di scorrimento][2]

1. Fare clic su **Save**. Verrà visualizzato un messaggio di conferma. Fare clic su **Sì** per confermare o su **No** per annullare.

    ![Fare clic su Salva.][3]

<a name="pause-compute-bk"></a>

## Sospendere il calcolo

[AZURE.INCLUDE [Descrizione della sospensione di SQL Data Warehouse](../../includes/sql-data-warehouse-pause-description.md)]

Per sospende un database:

1. Aprire il [portale di Azure][] e aprire il database. Osservare che lo stato è **Online**. 

    ![Stato online][6]

1. Per sospendere il calcolo e le risorse di memoria, fare clic su **Sospendi**. Viene visualizzato un messaggio di conferma. Fare clic su **Sì** per confermare o su **No** per annullare.

    ![Conferma sospensione][7]

1. Durante l'avvio di SQL Data Warehouse lo stato del database sarà Sospensione in corso.
2. Quando lo stato diventa **Sospeso**, l'operazione di sospensione è completa e le DWU non vengono più addebitate.

    ![Stato di sospensione][4]

<a name="resume-compute-bk"></a>

## Riprendere il calcolo

[AZURE.INCLUDE [Descrizione della riattivazione di SQL Data Warehouse](../../includes/sql-data-warehouse-resume-description.md)]Per riattivare un database:

1. Aprire il [portale di Azure][] e aprire il database. Osservare che lo stato è **Sospeso**. 

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
[1]: ./media/sql-data-warehouse-manage-scale-out-tasks/click-scale.png
[2]: ./media/sql-data-warehouse-manage-scale-out-tasks/move-slider.png
[3]: ./media/sql-data-warehouse-manage-scale-out-tasks/click-save.png
[4]: ./media/sql-data-warehouse-manage-scale-out-tasks/resume-database.png
[5]: ./media/sql-data-warehouse-manage-scale-out-tasks/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-scale-out-tasks/pause-database.png
[7]: ./media/sql-data-warehouse-manage-scale-out-tasks/pause-confirm.png

<!--Article references-->
[Panoramica della gestione]: ./sql-data-warehouse-overview-manage.md
[Panoramica sulla scalabilità delle prestazioni]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->


<!--Other Web references-->

[portale di Azure]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->