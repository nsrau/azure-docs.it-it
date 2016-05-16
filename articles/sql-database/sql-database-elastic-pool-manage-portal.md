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
	ms.date="05/02/2016"
	ms.author="ninarn"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Monitorare e gestire un pool di database elastici con il portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


È possibile utilizzare il portale di Azure per monitorare, gestire e configurare un pool di database elastici e i database nel pool. Il database SQL è dotato di intelligenza integrata che consente di analizzare la cronologia dell'utilizzo di tutti i database, a prescindere dalla relativa presenza o meno nei pool, consigliando un pool per i database se più conveniente.

Il portale consente di modificare le impostazioni del pool e del database, visualizzare in anteprima le modifiche, quindi eseguire il commit di tutte le modifiche contemporaneamente. È possibile visualizzare in anteprima le modifiche, ad esempio l'aggiunta e la rimozione di database. Verrà visualizzato anche il possibile impatto sulle prestazioni e sui prezzi.

Per eseguire i passaggi descritti in questo articolo è necessario disporre di un pool e di alcuni database. Se si dispone di database esistenti, vedere l'articolo sulla [creazione di un pool](sql-database-elastic-pool-create-portal.md). Se non si dispone di un database, vedere l'[Esercitazione sul database SQL](sql-database-get-started.md).

## Selezionare un pool con cui lavorare

1. Nel [portale di Azure](https://portal.azure.com), fare clic su **Sfoglia**
2. Fare clic su **Pool elastici SQL**
3. Dall'elenco, fare clic sul pool con cui si desidera lavorare.

## Spostare un database in un pool elastico

È possibile aggiungere o rimuovere i database da un pool esistente. I database possono trovarsi in altri pool. Tuttavia, è possibile aggiungere solo i database che sono nello stesso server logico.

1. Nel pannello del pool, in **Database elastici** fare clic su **Configura pool**.

    ![Fare clic su Configura pool][1]

2. Nel pannello **Configura pool**, fare clic su **Aggiungi al pool**.

	![Fare clic su Aggiungi al pool](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

	
3. Nel pannello **Aggiungi database**, selezionare uno o più database da aggiungere al pool. Quindi fare clic su **Seleziona**.

	![Selezionare i database da aggiungere](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    Il pannello **Configura pool** visualizzerà il database appena aggiunto, con lo stato impostato su **In sospeso**.

    ![Aggiunte di pool in sospeso](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. Nel pannello"Configura pool", fare clic su **Salva**.

    ![Fare clic su Salva.](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## Spostare un database da un pool elastico

1. Nel pannello **Configura pool**, selezionare uno o più database da rimuovere.

    ![elenchi di database](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. Fare clic su **Rimuovi dal pool**.

    ![elenchi di database](./media/sql-database-elastic-pool-manage-portal/remove-from-pool.png)

	I database selezionati vengono visualizzati nell'interfaccia utente come "database selezionati per la rimozione".


## Monitorare l'utilizzo delle risorse di un pool


1. Selezionare un pool con cui lavorare.
2. In **Monitoraggio pool elastico** vengono visualizzati un grafico e i riquadri animati contenenti informazioni importanti per l'uso del pool.

![Monitorare un pool elastico](./media/sql-database-elastic-pool-manage-portal/monitor-elastic-pool.png)

**Per modificare il grafico e la visualizzazione:**

- Fare clic su **Modifica**.

	![Fare clic su Modifica](./media/sql-database-elastic-pool-manage-portal/edit-resource-utlization.png)

- Nel pannello **Modifica grafico**, selezionare un nuovo intervallo di tempo, ad esempio ora precedente, oggi o settimana precedente, oppure fare clic su **personalizzato** per impostare un intervallo di tempo diverso. Selezionare il tipo di grafico (a barre o a linee), quindi selezionare le risorse da monitorare.

	![Fare clic su Modifica](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- Fare quindi clic su **OK**.


## Aggiungere un avviso a una risorsa di pool

È possibile aggiungere regole a risorse che inviano messaggi di posta elettronica a persone oppure stringhe di avviso a endpoint di URL quando la risorsa raggiunge la soglia di utilizzo impostata.

**Per aggiungere un avviso a una risorsa qualsiasi:**

1. Fare clic sul grafico **Utilizzo delle risorse** per aprire il pannello **Metrica**, selezionare **Aggiungi avviso** e inserire le informazioni nel pannello **Aggiungi una regola di avviso**. La **risorsa** verrà automaticamente impostata come il pool da usare.
2. Digitare **Nome** e **Descrizione** che serviranno a identificare l'avviso per l'utente e i destinatari.
3. Scegliere una **Metrica** in base alla quale creare un avviso dall'elenco.

    Il grafico mostra in modo dinamico l'utilizzo delle risorse per la metrica selezionata in modo da scegliere una soglia.

4. Scegliere una **Condizione** (maggiore di, minore di e così via) e una **Soglia**.
5. Fare clic su **OK**.

## Modificare le impostazioni delle prestazioni di un pool

Nel monitorare l'utilizzo delle risorse di un pool, è possibile scoprire che il pool necessita di più eDTU o che i singoli database nel pool necessitano di impostazioni eDTU diverse. È possibile modificare la configurazione del pool in qualsiasi momento per ottenere il miglior compromesso tra prestazioni e costi. Vedere [Quando usare un pool di database elastici](sql-database-elastic-pool-guidance.md) per sapere di più.

**Per modificare il numero di eDTU per pool e per database:**

1. Aprire il pannello **Configura pool**.

    In **Impostazioni pool di database elastico**, modificare con i dispositivi di scorrimento le impostazioni del pool.

    ![Utilizzo delle risorse del pool elastico](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Alla modifica dell'impostazione, viene visualizzato il relativo costo mensile stimato.

    ![Aggiornamento di un pool e nuovi costi mensili](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

## Anteprima delle azioni del database

È possibile visualizzare in anteprima l'aggiunta e la rimozione dei database prima di confermare l'azione nel pannello **Configura pool**:

![anteprima aggiunta e rimozione database](./media/sql-database-elastic-pool-manage-portal/pools-tab.png).


## Creare e gestire processi elastici

I processi elastici consentono l'esecuzione di script Transact-SQL su qualsiasi numero di database nel pool. Prima di usare i processi, installare i componenti dei processi elastici e fornire le credenziali. Per ulteriori informazioni, vedere [Panoramica dei processi di database elastici](sql-database-elastic-jobs-overview.md).

Vedere l'articolo sull'[aumento del numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md): usare gli strumenti di database elastici per aumentare il numero di istanze, spostare dati, eseguire query o creare transazioni.


## Risorse aggiuntive

- [Pool elastico di database SQL](sql-database-elastic-pool.md)
- [Creare un pool di database elastici con il portale](sql-database-elastic-pool-create-csharp.md)
- [Creare un pool di database elastici con PowerShell](sql-database-elastic-pool-create-powershell.md)
- [Creare un pool di database elastici con C#](sql-database-elastic-pool-create-csharp.md)
- [Considerazioni di prezzo e prestazioni per un pool di database flessibile](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png

<!---HONumber=AcomDC_0504_2016-->