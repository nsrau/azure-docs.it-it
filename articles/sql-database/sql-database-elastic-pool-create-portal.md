<properties
	pageTitle="Creare pool di database elastici scalabili | Microsoft Azure"
	description="Come aggiungere un pool di database elastici scalabile alla configurazione del database SQL per semplificare l'amministrazione e la condivisione delle risorse tra più database."
	keywords="database scalabile,configurazione del database"
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/24/2016"
	ms.author="jeffreyg"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Creare un pool di database elastici scalabile per database SQL nel portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)

Questo articolo illustra come creare un [pool di database elastici](sql-database-elastic-pool.md) scalabile con il [portale di Azure](https://portal.azure.com/). Esistono due modi per creare un pool. È possibile farlo da zero se si conosce la configurazione del pool da usare oppure iniziare con una raccomandazione fornita dal servizio. Il database SQL integra informazioni in base alle quali viene suggerita una configurazione del pool nel caso risulti più conveniente secondo i dati di telemetria relativi all'utilizzo precedente dei database.

È possibile aggiungere più pool a un server, ma non aggiungere database da diversi server nello stesso pool. Per creare un pool, è necessario almeno un database in un server V12. Se non ne è disponibile uno, vedere [Creare il primo database SQL di Azure](sql-database-get-started.md). È possibile creare un pool con un solo database, ma i pool sono convenienti solo con più database. Vedere [Considerazioni su prezzi e prestazioni per un pool di database elastici](sql-database-elastic-pool-guidance.md).

> [AZURE.NOTE] I pool sono disponibili solo con i server di database SQL V12. Se sono disponibili database in un server V11, è possibile [usare uno script di PowerShell per identificarli come candidati per un pool](sql-database-elastic-pool-database-assessment-powershell.md) in un server V12 e quindi [usare PowerShell per eseguire l'aggiornamento a V12 e creare un pool](sql-database-upgrade-server-powershell.md) in un unico passaggio.

##Creare un nuovo pool
1. Nel [portale di Azure](http://portal.azure.com/) fare clic su **SQL Server**, selezionare il server che contiene i database da aggiungere a un pool.
2. Fare clic su **Nuovo pool**.

    ![Aggiungere un pool a un server](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **-OPPURE-**

    Fare clic sul messaggio per visualizzare i pool consigliati in base ai dati di telemetria cronologici relativi all'utilizzo del database e quindi fare clic sul livello per visualizzare altri dettagli e personalizzare il pool. Per informazioni su come vengono create le raccomandazioni, Vedere [Informazioni sulle raccomandazioni per i pool](#understand-pool-recommendations) più avanti in questo argomento.

    ![pool consigliato](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

    Viene visualizzato il pannello **Pool di database elastici** dove si configurerà il pool. Se si è fatto clic su **Nuovo pool** nel passaggio precedente, il portale sceglie un **pool Standard** in **Piano tariffario**, un **Nome** univoco e una configurazione predefinita per il pool. Se si sceglie un pool consigliato, la configurazione e il piano del pool consigliati predefiniti sono già impostati, ma è comunque possibile modificarli.

    ![Configurare un pool elastico](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

3. Per modificare il piano tariffario per il pool, fare clic su **Piano tariffario**, scegliere il piano desiderato quindi fare clic su **Seleziona**.

    > [AZURE.IMPORTANT] Dopo aver scelto il piano tariffario e aver eseguito il commit delle modifiche facendo clic su **OK** nell'ultimo passaggio, non sarà più possibile modificare il piano tariffario del pool.

4. Fare clic su **Configura pool** per aggiungere i database e scegliere le impostazioni delle risorse per il pool.
5. Per aggiungere i database, fare clic su **Aggiungi database**, fare clic su database da aggiungere e quindi scegliere il pulsante **Seleziona**.

    ![Aggiungere database](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    Se i dati di telemetria cronologici relativi all'utilizzo disponibili per i database in uso sono sufficienti, il grafico **Utilizzo di eDTU e GB** e il grafico a barre **Utilizzo di eDTU effettivo** si aggiornano per aiutare l'utente a prendere le decisioni di configurazione. Il servizio potrebbe anche visualizzare un messaggio di raccomandazione per facilitare la scelta delle dimensioni corrette per il pool. Vedere [Informazioni sulle raccomandazioni per i pool](#understand-pool-recommendations).

    ![indicazioni dinamiche](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

6. Usare i controlli nella pagina **Configura pool** per esaminare le impostazioni e configurare il pool in base alle linee guida seguenti:

    ![Configurare un pool elastico](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

    | Impostazione delle prestazioni | Descrizione |
    | :--- | :--- |
    | **eDTU POOL** e **GB POOL** per l'impostazione del pool| Numero massimo di eDTU disponibili e condivise da tutti i database nel pool. Il numero massimo di eDTU disponibili in un pool dipende dal piano tariffario (livello di servizio). Il valore di **eDTU pool** è correlato allo spazio di archiviazione disponibile per il pool. Per ogni eDTU allocata al pool si ottiene una quantità fissa di spazio archiviazione del database e viceversa. |
    | **MIN eDTU** per l'impostazione del database| Numero minimo di eDTU dal pool garantito per tutti i database nel pool in qualsiasi momento. In genere, **MIN eDTU** è impostato su un valore compreso tra 0 e l'utilizzo cronologico medio di eDTU per ogni database. Si tratta di un'impostazione globale che si applica a tutti i database nel pool. |
    | **MAX eDTU** per l'impostazione del database | Numero massimo di eDTU che può essere usato da un database singolo nel pool. È possibile impostare questo limite fino a quello di **eDTU POOL**. Impostare **MAX eDTU** per ogni database su un valore sufficientemente elevato per gestire burst massimi o picchi di utilizzo del database. È previsto un certo grado di overcommit del gruppo, perché il pool in genere presuppone schemi di utilizzo a freddo e a caldo per i database, in cui tutti i database non raggiungono il picco contemporaneamente. **Esempio**: si supponga che il picco di utilizzo per ogni database sia 50 DTU e che solo il 20% dei 100 database nel gruppo raggiunga il picco contemporaneamente. Se il limite di utilizzo delle eDTU per ogni database è impostato su 50 eDTU, è ragionevole eseguire l'overcommit del pool moltiplicando per 5 e impostare il valore di **eDTU POOL** su 1.000. **MAX eDTU** non è una garanzia di risorse per un database, ma un limite massimo di eDTU che è possibile raggiungere, se disponibili. Si tratta di un'impostazione globale che si applica a tutti i database nel pool. |

    Vedere [Riferimento al pool di database elastici](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases) per altri dettagli sui limiti per ogni livello di servizio e [Considerazioni su prezzo e prestazioni per un pool di database elastici](sql-database-elastic-pool-guidance.md) per istruzioni dettagliate sul ridimensionamento di un pool.

7. Al termine, fare clic su **Seleziona** e quindi su **OK** per creare il pool.

##Informazioni sulle raccomandazioni per i pool
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
- [Gestire un pool di database elastici SQL con C#](sql-database-client-library.md)
- [Riferimento ai database elastici](sql-database-elastic-pool-reference.md)

<!---HONumber=AcomDC_0330_2016-->