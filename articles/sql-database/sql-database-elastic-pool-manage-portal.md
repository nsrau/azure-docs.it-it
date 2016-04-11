<properties
	pageTitle="Monitorare, gestire e dimensionare un pool di database elastici"
	description="Informazioni su come usare il portale di Azure e l'intelligenza integrata del database SQL per gestire, monitorare e dimensionare in modo adeguato un pool scalabile di database elastici per ottimizzare le prestazioni del database e gestire i costi."
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/18/2016"
	ms.author="jeffreyg"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Monitorare, gestire e dimensionare un pool di database elastici con il portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-pool-manage-portal.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)


Questo articolo descrive come usare il portale di Azure per monitorare, gestire e dimensionare in modo adeguato un pool di database elastici e i database nel pool. Un database SQL è dotato di un'intelligenza integrata che analizza i dati di telemetria cronologici relativi all'uso e suggerisce in modo proattivo un pool per i database nel momento più conveniente. È inoltre possibile aggiungere e rimuovere database prima di eseguire il commit delle modifiche e controllare l'impatto sulle prestazioni del pool e sull'archiviazione.

Per eseguire i passaggi descritti in questo articolo è necessario un pool e i database. Vedere [creare un pool](sql-database-elastic-pool-create-portal.md) se si dispone già di database, oppure l'[esercitazione sul database SQL](sql-database-get-started) in caso contrario.

**Per scegliere un pool da usare:**

- Nel [portale di Azure](https://portal.azure.com) selezionare **Sfoglia**, poi **Pool elastici SQL**, quindi selezionare il pool che si desidera usare tra quelli indicati nell'elenco.

##Monitorare l'utilizzo delle risorse di un pool
Dopo aver selezionato un pool da usare, in **Monitoraggio pool elastico**, riquadri animati e un grafico mostrano informazioni importanti relative all'utilizzo del pool.

![Monitorare un pool elastico](./media/sql-database-elastic-pool-manage-portal/monitor-elastic-pool.png)

**Per modificare l'intervallo di date, il tipo di grafico (barra o riga) o le risorse visualizzate:**

- Fare clic su **Modifica**, selezionare le impostazioni desiderate, quindi fare clic su **Salva** per aggiornare il grafico.

**Per modificare i riquadri animati:**

- Fare clic su **Aggiungi riquadri**, quindi selezionare i riquadri desiderati dalla raccolta dei riquadri che viene visualizzata a sinistra.

##Aggiungere un avviso a una risorsa di pool
È possibile aggiungere regole a risorse che inviano messaggi di posta elettronica a persone oppure stringhe di avviso a endpoint di URL quando la risorsa raggiunge la soglia di utilizzo impostata.

**Per aggiungere un avviso a una risorsa qualsiasi:**

1. Fare clic sul grafico **Utilizzo delle risorse** per aprire il pannello **Metrica**, selezionare **Aggiungi avviso** e inserire le informazioni nel pannello **Aggiungi una regola di avviso** (**Risorsa** viene automaticamente impostato come il pool da usare).
2. Inserire un **Nome** e una **Descrizione** che serviranno a identificare l'avviso per l'utente e i destinatari.
3. Scegliere una **Metrica** in base alla quale creare un avviso dall'elenco.

    Il grafico mostra in modo dinamico l'utilizzo delle risorse per la metrica selezionata in modo da scegliere una soglia.

4. Scegliere una **Condizione** (Maggiore di, Minore di, ecc.) e una **Soglia**.
5. Fare clic su **OK**.

##Modificare le eDTU in base al pool e le eDTU database
Quando viene visualizzato l'utilizzo delle risorse di un pool, si potrebbe scoprire che per il pool è necessaria un'impostazione eDTU diversa, oppure che per i singoli database nel pool sono necessarie impostazioni eDTU diverse. È possibile modificare la configurazione del pool in qualsiasi momento per ottenere il miglior compromesso tra prestazioni e costi. Per altre informazioni, vedere [Considerazioni su prezzi e prestazioni](sql-database-elastic-pool-guidance.md).

**Per modificare la eDTU per il pool e la eDTU in base al database:**

1. Fare clic su **Configura pool** per aprire il pannello **Configurazione prestazioni**.

    In **Impostazioni pool di database elastici**, un grafico mostra le tendenze recenti relative alla eDTU e la percentuale di utilizzo di archiviazione della capacità per il pool.

    ![Utilizzo delle risorse del pool elastico](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Facendo clic su una **eDTU pool** diversa, verrà visualizzato il costo stimato mensile della modifica che si desidera apportare, e il grafico viene aggiornato per mostrare i valori di utilizzo stimati con il nuovo numero massimo di eDTU selezionato.

    ![Aggiornamento di un pool e nuovi costi mensili](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

3. In **Impostazione database elastico**, un grafico a barre mostra l'utilizzo di eDTU di ogni database nel pool.

4. Fare clic su **eDTU max** per impostare il numero massimo e su **eDTU min** per impostare il numero minimo di eDTU per i database nel pool.

    ![Aggiornamento del numero massimo e minimo di eDTU per il database elastico](./media/sql-database-elastic-pool-manage-portal/change-db-edtuminmax.png)

##Aggiungere e rimuovere database

Dopo aver creato un pool, è possibile aggiungere database al pool o rimuoverli. È possibile aggiungere database solo nello stesso server SQL.

**Per aggiungere database:**

1. Nel pannello per il pool, in **Database elastici** fare clic sul collegamento che mostra il numero di database nel pool.

    ![elenchi di database](./media/sql-database-elastic-pool-manage-portal/db-listing.png)

2. Nel pannello **Database elastici**, fare clic su **Aggiungi database**, selezionare il database da aggiungere, quindi fare clic sul pulsante **Seleziona**.

    Nel pannello **Database elastici** ora viene elencato il database appena aggiunto, con **MEDIA DTU** e **DIM.(GB)** per l'utilizzo di archiviazione, insieme allo stato **In sospeso**. I valori di utilizzo del pool ora mostrano quelli che saranno i **Nuovi** valori se si salvano le modifiche.

    ![pool consigliato](./media/sql-database-elastic-pool-manage-portal/add-remove-databases.png)

3. Fare clic su **Salva** e quindi su **OK** quando il portale indica che la richiesta è stata inviata. Il numero di database nel pool viene visualizzato nel pannello per il pool al termine dell'operazione.

**Per rimuovere database:**

1. Nel pannello per il pool, in **Database elastici** fare clic sul collegamento che mostra il numero di database nel pool.

    ![elenchi di database](./media/sql-database-elastic-pool-manage-portal/db-listing.png)

2. Nel pannello **Database elastici**, dall'elenco dei database nel pool selezionare i database che si desidera rimuovere, quindi fare clic su **Rimuovi database**.

    I valori di utilizzo del pool ora mostrano quelli che saranno i **Nuovi** valori se si salvano le modifiche.

3. Fare clic su **Salva** e quindi su **OK** quando il portale indica che la richiesta è stata inviata. Il numero di database nel pool viene visualizzato nel pannello per il pool al termine dell'operazione.

## Creare un nuovo database in un pool

Nel pannello per il database, fare clic su **Crea database**. Il database SQL è già configurato per il pool e il server corretti. Fare clic per selezionare altre impostazioni, quindi fare clic su **OK** per creare un nuovo database nel pool.

   ![creare database elastici](./media/sql-database-elastic-pool-portal/create-database.png)

##Creare e gestire processi elastici

I processi elastici consentono l'esecuzione di script Transact-SQL su qualsiasi numero di database nel pool. Prima di usare i processi, installare i componenti dei processi elastici e fornire le credenziali. Per ulteriori informazioni, vedere [Panoramica dei processi di database elastici](sql-database-elastic-jobs-overview.md).

## Risorse aggiuntive

- [Riferimento ai database elastici](sql-database-elastic-pool-reference.md)
- [Pool elastico di database SQL](sql-database-elastic-pool.md)
- [Creare un pool di database elastici con il portale](sql-database-elastic-pool-create-csharp.md)
- [Creare un pool di database elastici con PowerShell](sql-database-elastic-pool-create-powershell.md)
- [Creare un pool di database elastici con C#](sql-database-elastic-pool-create-csharp.md)
- [Considerazioni di prezzo e prestazioni per un pool di database flessibile](sql-database-elastic-pool-guidance.md)

<!---HONumber=AcomDC_0330_2016-->