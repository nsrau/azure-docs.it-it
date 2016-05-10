<properties
	pageTitle="Creare un nuovo pool di database elastici con il portale di Azure | Microsoft Azure"
	description="Come aggiungere un pool di database elastici scalabile alla configurazione del database SQL per semplificare l'amministrazione e la condivisione delle risorse tra più database."
	keywords="database scalabile,configurazione del database"
	services="sql-database"
	documentationCenter=""
	authors="ninarn"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/28/2016"
	ms.author="ninarn"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Creare un nuovo pool di database elastici con il portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)

È possibile creare un [pool](sql-database-elastic-pool.md) in due modi.

1. Crearlo da zero, soprattutto se si conosce la configurazione del pool che si vuole usare.
2. Iniziare con una raccomandazione fornita dal servizio Database SQL. Il servizio usa i dati di telemetria per esaminare l'utilizzo precedente, quindi raccomanda una configurazione conveniente per i database in uso.

È possibile aggiungere più pool a un server, ma non aggiungere database da diversi server nello stesso pool. Per creare un pool, è necessario almeno un database in un server V12. Se non è disponibile un server v12, vedere [Creare il primo database SQL di Azure](sql-database-get-started.md). È possibile creare un pool con un solo database, ma i pool sono convenienti solo con più database. Vedere [Considerazioni su prezzi e prestazioni per un pool di database elastici](sql-database-elastic-pool-guidance.md).

I pool sono disponibili solo con i server di database SQL V12. Se sono disponibili database in un server V11, è possibile [usare uno script di PowerShell per identificarli come candidati per un pool](sql-database-elastic-pool-database-assessment-powershell.md) in un server V12 e quindi [usare PowerShell per eseguire l'aggiornamento a V12 e creare un pool](sql-database-upgrade-server-powershell.md) in un unico passaggio.

## Creare un nuovo pool

1. Nel [portale di Azure](http://portal.azure.com/) fare clic su **SQL Server**, selezionare il server che contiene i database da aggiungere a un pool.
2. Fare clic su **Nuovo pool**.

    ![Aggiungere un pool a un server](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **-OPPURE-**

    Fare clic sul messaggio per visualizzare i pool consigliati in base ai dati di telemetria cronologici relativi all'utilizzo del database e quindi fare clic sul livello per visualizzare altri dettagli e personalizzare il pool. Per informazioni su come vengono create le raccomandazioni, vedere [Informazioni sulle raccomandazioni per i pool](#understand-pool-recommendations) più avanti in questo argomento.

    ![pool consigliato](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

    Viene visualizzato il pannello **Pool di database elastici** dove si configurerà il pool. Se si è fatto clic su **Nuovo pool** nel passaggio precedente, il portale sceglie un **pool Standard** in **Piano tariffario**, un **Nome** univoco e una configurazione predefinita per il pool. Se si sceglie un pool consigliato, la configurazione e il piano del pool consigliati predefiniti sono già impostati, ma è comunque possibile modificarli.

    ![Configurare un pool elastico](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

3. Per modificare il piano tariffario per il pool, fare clic su **Piano tariffario**, scegliere il piano desiderato quindi fare clic su **Seleziona**.

    > [AZURE.IMPORTANT] Dopo aver scelto il piano tariffario e avere eseguito il commit delle modifiche facendo clic su **OK** nell'ultimo passaggio, non sarà più possibile modificare il piano tariffario del pool.

4. Fare clic su **Configura pool** per aggiungere i database e scegliere le impostazioni delle risorse per il pool.
5. Per aggiungere i database, fare clic su **Aggiungi database**, fare clic sui database da aggiungere e quindi scegliere il pulsante **Seleziona**.

    ![Aggiungere database](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    Se i dati di telemetria cronologici relativi all'utilizzo disponibili per i database in uso sono sufficienti, il grafico **Utilizzo di eDTU e GB** e il grafico a barre **Utilizzo di eDTU effettivo** si aggiornano per aiutare l'utente a prendere decisioni relative alla configurazione.

    ![indicazioni dinamiche](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

6. Usare i controlli nella pagina **Configura pool** per esaminare le impostazioni e configurare il pool. Vedere la sezione [Limiti dei pool elastici e dei database elastici](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases) per altri dettagli sui limiti per ogni livello di servizio e [Considerazioni su prezzo e prestazioni per un pool di database elastici](sql-database-elastic-pool-guidance.md) per istruzioni dettagliate sul ridimensionamento di un pool.
7. Al termine, fare clic su **Seleziona** e quindi su **OK** per creare il pool.

## Informazioni sulle raccomandazioni per i pool

Il servizio di database SQL valuta la cronologia di utilizzo e suggerisce uno o più pool quando questo approccio è più conveniente rispetto all'uso di singoli database. Ogni raccomandazione viene configurata con un subset univoco di database del server che meglio si adatta al pool. La raccomandazione per il pool include:

- Piano tariffario per il pool (Basic, Standard o Premium).
- **eDTU POOL** appropriato, detto anche eDTU max per pool.
- **MAX eDTU** e **Min eDTU** per ogni database.
- Elenco di database consigliati per il pool.

Il servizio prende in considerazione la telemetria degli ultimi 30 giorni per la raccomandazione dei pool. Affinché un database possa essere considerato un candidato per un pool di database elastici, deve esistere per almeno 7 giorni. I database che sono già in un pool di database elastici non vengono considerati come candidati dai consigli relativi ai pool di database elastici.

Il servizio valuta le risorse necessarie e la convenienza dello spostamento di singoli database in ogni livello di servizio nei pool dello stesso livello. Ad esempio, vengono valutati tutti i database Standard in un server per l’utilizzo in un pool elastico Standard. Ciò significa che il servizio non effettua consigli relativi a livelli diversi, ad esempio lo spostamento di un database Standard in un pool Premium.

## Risorse aggiuntive

- [Gestire un pool di database elastici SQL con il portale](sql-database-elastic-pool-manage-portal.md)
- [Gestire un pool di database elastici SQL con PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [Gestire un pool di database elastici SQL con C#](sql-database-elastic-pool-manage-csharp.md)
- [Aumento del numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md) 

<!---HONumber=AcomDC_0504_2016-->