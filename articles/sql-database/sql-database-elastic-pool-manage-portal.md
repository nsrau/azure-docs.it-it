<properties
    pageTitle="Monitorare e gestire un pool di database elastici con il portale di Azure | Microsoft Azure"
    description="Informazioni su come usare il portale di Azure e l'intelligenza integrata del database SQL per gestire, monitorare e dimensionare in modo adeguato un pool scalabile di database elastici per ottimizzare le prestazioni del database e gestire i costi."
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="ninarn"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/22/2016"
    ms.author="ninarn"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="monitor-and-manage-an-elastic-database-pool-with-the-azure-portal"></a>Monitorare e gestire un pool di database elastici con il portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


È possibile usare il portale di Azure per monitorare e gestire un pool di database elastici e i database nel pool. Dal portale è possibile monitorare l'utilizzo di un pool elastico e dei database al suo interno. È anche possibile apportare un set di modifiche al pool elastico e inviare tutte le modifiche contemporaneamente. Le modifiche includono l'aggiunta o la rimozione di database, la modifica delle impostazioni del pool elastico o la modifica delle impostazioni del database.

L'immagine seguente mostra un esempio di pool elastico. La visualizzazione include:

*  Grafici per il monitoraggio dell'utilizzo delle risorse da parte del pool elastico e dei database al suo interno.
*  Il pulsante **Configura pool** per apportare modifiche al pool elastico.
*  Il pulsante **Crea database** per creare un nuovo database e aggiungerlo al pool elastico corrente.
*  Processi elastici che consentono di gestire un numero elevato di database tramite l'esecuzione di script Transact SQL in tutti i database in un elenco.

![Visualizzazione del pool][2]

Per eseguire i passaggi descritti in questo articolo è necessaria un'istanza di SQL Server in Azure con almeno un database e un pool elastico. Se non si è disponibile un pool elastico, vedere l'articolo relativo alla [creazione di un pool](sql-database-elastic-pool-create-portal.md). Se non è disponibile un database, vedere l'[esercitazione sul database SQL](sql-database-get-started.md).

## <a name="elastic-pool-monitoring"></a>Monitoraggio di pool elastici

È possibile passare a un pool specifico per visualizzarne l'utilizzo delle risorse. Per impostazione predefinita, il pool è configurato per mostrare l'utilizzo di eDTU e risorse di archiviazione relativo all'ultima ora. È possibile configurare il grafico per mostrare diverse metriche in diversi intervalli di tempo.

1. Selezionare un pool con cui lavorare.
2. In **Monitoraggio pool elastico** è presente un grafico con l'etichetta **Utilizzo risorse**. Fare clic sul grafico.

    ![Monitoraggio di pool elastici][3]

    Verrà visualizzato il pannello **Metrica** , che fornisce una visualizzazione dettagliata delle metriche specificate nell'intervallo di tempo specificato.   

    ![Blade delle metriche][9]

### <a name="to-customize-the-chart-display"></a>Per personalizzare la visualizzazione del grafico

È possibile modificare il grafico e il pannello Metrica per visualizzare altre metriche, ad esempio la percentuale di CPU, la percentuale di IO dei dati e la percentuale di IO del log usata.

2. Nel pannello Metrica fare clic su **Modifica**.

    ![Fare clic su Modifica][6]

- Nel pannello **Modifica grafico** selezionare un nuovo intervallo di tempo, ad esempio ora precedente, oggi o settimana precedente, oppure fare clic su **personalizzato** per impostare un qualsiasi intervallo di tempo nelle due settimane precedenti. Selezionare il tipo di grafico (a barre o a linee), quindi selezionare le risorse da monitorare.

> Nota: solo le metriche con la stessa unità di misura possono essere visualizzate nel grafico nello stesso momento. Se, ad esempio, si seleziona "eDTU percentage" (Percentuale eDTU), sarà possibile selezionare solo altre metriche con percentuale come unità di misura.

    ![Click edit](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- Fare quindi clic su **OK**.


## <a name="elastic-database-monitoring"></a>Monitoraggio di database elastici

È possibile monitorare anche i singoli database per potenziali problemi.

1. In **Monitoraggio database elastico**è disponibile un grafico che mostra le metriche relative a cinque database. Per impostazione predefinita, il grafico mostra i primi cinque database nel pool per utilizzo di eDTU medio nell'ora precedente. Fare clic sul grafico.

    ![Monitoraggio di pool elastici][4]

2. Verrà visualizzato il pannello **Utilizzo risorse database**, che fornisce una visualizzazione dettagliata dell'utilizzo del database nel pool. La griglia nella parte inferiore del pannello permette di selezionare fino a cinque database nel pool per visualizzarne l'utilizzo nel grafico. È anche possibile personalizzare le metriche e l'intervallo di tempo visualizzati nel grafico facendo clic su **Modifica grafico**.

    ![Pannello Utilizzo risorse database][8]

### <a name="to-customize-the-view"></a>Per personalizzare la visualizzazione

1. Nel pannello **Utilizzo risorse database** fare clic su **Modifica grafico**.

    ![Fare clic su Modifica grafico](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. Nel pannello **Modifica grafico** selezionare un nuovo intervallo di tempo, ad esempio ora precedente o ultime 24 ore, oppure fare clic su **personalizzato** per selezionare un giorno diverso nelle 2 settimane precedenti.

    ![Fare clic su personalizzato](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. Fare clic sull'elenco a discesa **Confronta database per** e selezionare una metrica diversa da usare per il confronto dei database.

    ![Modificare il grafico](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Per selezionare i database da monitorare

Nell'elenco dei database del pannello **Utilizzo risorse database** è possibile trovare database specifici scorrendo le pagine dell'elenco o digitando il nome di un database. Usare la casella di controllo per selezionare il database.

![Cercare i database da monitorare][7]


## <a name="add-an-alert-to-a-pool-resource"></a>Aggiungere un avviso a una risorsa di pool

È possibile aggiungere regole a risorse che inviano messaggi di posta elettronica a persone oppure stringhe di avviso a endpoint di URL quando la risorsa raggiunge la soglia di utilizzo impostata.

> [AZURE.IMPORTANT]Il monitoraggio dell'utilizzo delle risorse per i pool elastici viene eseguito con un ritardo di almeno 20 minuti. Al momento non è possibile impostare avvisi inferiori ai 30 minuti per i pool elastici. Tutti gli avvisi per i pool elastici impostati con una durata (parametro denominato "-WindowSize" nell'API di PowerShell) inferiore a 30 minuti non possono essere attivati. Controllare che gli avvisi definiti per i pool elastici utilizzino un periodo (WindowSize) di almeno 30 minuti.

**Per aggiungere un avviso a una risorsa qualsiasi:**

1. Fare clic sul grafico **Utilizzo risorse** per aprire il pannello **Metrica**. Fare clic su **Aggiungi avviso** e inserire le informazioni nel pannello **Aggiungi una regola di avviso**. La **risorsa** viene impostata automaticamente come il pool corrente.
2. Inserire un **Nome** e una **Descrizione** che serviranno a identificare l'avviso per l'utente e i destinatari.
3. Scegliere una **Metrica** in base alla quale creare un avviso dall'elenco.

    Il grafico mostra in modo dinamico l'utilizzo delle risorse per la metrica selezionata in modo da scegliere una soglia.

4. Scegliere una **Condizione**, ad esempio maggiore di, minore di e così via, e una **Soglia**.
5. Fare clic su **OK**.



## <a name="move-a-database-into-an-elastic-pool"></a>Spostare un database in un pool elastico

È possibile aggiungere o rimuovere i database da un pool esistente. I database possono trovarsi in altri pool. Tuttavia, è possibile aggiungere solo i database che sono nello stesso server logico.

1. Nel pannello del pool, in **Database elastici** fare clic su **Configura pool**.

    ![Fare clic su Configura pool][1]

2. Nel pannello **Configura pool** fare clic su **Aggiungi al pool**.

    ![Fare clic su Aggiungi al pool](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)


3. Nel pannello **Aggiungi database** selezionare uno o più database da aggiungere al pool. Quindi fare clic su **Seleziona**.

    ![Selezionare i database da aggiungere](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    Il pannello **Configura pool** mostra il database selezionato per l'aggiunta, con lo stato impostato su **In sospeso**.

    ![Aggiunte di pool in sospeso](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. Nel pannello **Configura pool** fare clic su **Salva**.

    ![Fare clic su Salva.](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="move-a-database-out-of-an-elastic-pool"></a>Spostare un database da un pool elastico

1. Nel pannello **Configura pool** selezionare uno o più database da rimuovere.

    ![elenchi di database](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. Fare clic su **Rimuovi dal pool**.

    ![elenchi di database](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    Il pannello **Configura pool** mostra il database selezionato per la rimozione, con lo stato impostato su **In sospeso**.

    ![anteprima aggiunta e rimozione database](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. Nel pannello **Configura pool** fare clic su **Salva**.

    ![Fare clic su Salva.](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="change-performance-settings-of-a-pool"></a>Modificare le impostazioni delle prestazioni di un pool

Durante il monitoraggio dell'utilizzo delle risorse di un pool possono rendersi necessarie alcune modifiche, ad esempio dei limiti di archiviazione o di prestazioni. Si potrebbe voler modificare le impostazioni del database nel pool. È possibile modificare la configurazione del pool in qualsiasi momento per ottenere il miglior compromesso tra prestazioni e costi. Per altre informazioni, vedere [Quando usare un pool di database elastici](sql-database-elastic-pool-guidance.md).

**Per modificare i limiti di archiviazione o eDTU per il pool e il numero di eDTU per il database:**

1. Aprire il pannello **Configura pool** .

    In **Impostazioni pool di database elastico**usare i dispositivi di scorrimento per modificare le impostazioni del pool.

    ![Utilizzo delle risorse del pool elastico](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Alla modifica dell'impostazione, viene visualizzato il relativo costo mensile stimato.

    ![Aggiornamento di un pool e nuovi costi mensili](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)


## <a name="create-and-manage-elastic-jobs"></a>Creare e gestire processi elastici

I processi elastici consentono l'esecuzione di script Transact-SQL su qualsiasi numero di database nel pool. È possibile creare nuovi processi o gestire quelli esistenti tramite il portale.

![Creare e gestire processi elastici][5]


Prima di usare i processi, installare i componenti dei processi elastici e fornire le credenziali. Per ulteriori informazioni, vedere [Panoramica dei processi di database elastici](sql-database-elastic-jobs-overview.md).

Vedere l'articolo sull' [aumento del numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md): usare gli strumenti di database elastici per aumentare il numero di istanze, spostare dati, eseguire query o creare transazioni.



## <a name="additional-resources"></a>Risorse aggiuntive

- [Pool elastico di database SQL](sql-database-elastic-pool.md)
- [Creare un pool di database elastici con il portale](sql-database-elastic-pool-create-csharp.md)
- [Creare un pool di database elastici con PowerShell](sql-database-elastic-pool-create-powershell.md)
- [Creare un pool di database elastici con C#](sql-database-elastic-pool-create-csharp.md)
- [Considerazioni di prezzo e prestazioni per un pool di database flessibile](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png



<!--HONumber=Oct16_HO2-->


