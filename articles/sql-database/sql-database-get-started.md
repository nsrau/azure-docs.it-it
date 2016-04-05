<properties
	pageTitle="Esercitazione sul database SQL: creare un database SQL | Microsoft Azure"
	description="Esercitazione sul database SQL: Creare il primo database SQL in pochi minuti nel portale di Azure. Informazioni su come configurare un server di hosting e una regola del firewall."
	keywords="esercitazione sul database sql,creare un database sql"
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor="cgronlun"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/27/2015"
	ms.author="jeffreyg"/>

# Esercitazione sul database SQL: Creare un database SQL in pochi minuti usando il portale di Azure

**Database singolo**

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Questa esercitazione sul database SQL mostra come creare il primo database SQL in pochi minuti usando il portale di Azure. È possibile configurare un database vuoto o un database con dati di esempio.

Si apprenderà come:

- Creare un server per ospitare il nuovo database e quindi configurare una regola firewall per il server.
- Creare un database SQL vuoto o un database dall'esempio AdventureWorks che contiene dati con cui è possibile sperimentare.

Per iniziare, sono necessari un account e una sottoscrizione di Azure. Nel caso in cui non siano disponibili, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE] Questa esercitazione sul database SQL descrive come configurare un database usando il sistema di gestione di database relazionali (RDBMS) di Microsoft nel cloud, ovvero il database SQL di Azure. È anche possibile eseguire SQL Server in una macchina virtuale di Azure Per un veloce confronto, vedere [Informazioni sul database SQL di Azure e su SQL Server in Macchine virtuali di Azure](data-management-azure-sql-database-and-sql-server-iaas.md). Per iniziare a usare una macchina virtuale, vedere [Effettuare il provisioning di una macchina virtuale SQL Server](../virtual-machines/virtual-machines-windows-portal-sql-server-provision.md).

## Passaggio 1: Eseguire l'accesso e avviare la configurazione del database SQL
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Nuovo** > **Dati + Archiviazione** > **Database SQL**.

	![Esercitazione sul database SQL: creare un nuovo database SQL.](./media/sql-database-get-started/create-db.png)

	Verrà visualizzato il pannello delle impostazioni del **database SQL** in cui è possibile configurare i dettagli del server e del database.

	![Impostazioni di database e server per il database SQL](./media/sql-database-get-started/get-started-dbandserversettings.png)

## Passaggio 2: Scegliere le impostazioni del server
Un database SQL in Azure è ospitato in un server di database. Un server può ospitare più database. Durante la configurazione del database, è anche possibile creare e configurare il server che lo ospiterà oppure usarne uno creato in precedenza. In questo caso, ne verrà configurato uno nuovo.

1. Digitare un **Nome** per il database (in questo caso **AdventureWorks**). Le altre impostazioni del database verranno esaminate più avanti.
2. In **Server** fare clic su **Configurare le impostazioni necessarie** e quindi fare clic su **Crea un nuovo server**.

	![Assegnare un nome al database](./media/sql-database-get-started/name-and-newserver.png)

3. Nel pannello **Nuovo server** digitare un **Nome server** univoco in Azure e facile da ricordare. Questo nome sarà necessario in seguito per connettersi al database e usarlo.
4. Digitare un **Account di accesso amministratore server** facile da ricordare (in questo caso **AdventureAdmin**). Specificare quindi una **Password** sicura e digitarla di nuovo in **Conferma password**.

	![Impostazioni del nuovo server di database](./media/sql-database-get-started/get-started-serversettings.png)

	 Lasciare l'opzione **Crea server V12 (ultimo aggiornamento)** impostata su **Sì** per usare le funzionalità più recenti. La **Località** determina l'area del data center in cui viene creato il server.

	>[AZURE.TIP] Creare i server di database in una località vicina alle applicazioni che useranno il database. Per cambiare la località, fare clic su **Località**, selezionarne un'altra e quindi fare clic su **OK**.

5. Fare clic su **OK** per tornare al pannello **Database SQL**.

Il database e il server non sono ancora stati creati. Questo avverrà dopo il passaggio successivo, nel quale si sceglierà di creare il database e si confermeranno le impostazioni.

## Passaggio 3: Configurare e creare un database SQL

**Per configurare il nuovo database senza dati o tabelle:**
- Selezionare il pulsante **Create**.

**Per configurare il nuovo database con dati o tabelle:**

1. Nel pannello **Database SQL** fare clic su **Selezionare l'origine** e quindi su **Esempio**.

	![Creare un database SQL da un esempio](./media/sql-database-get-started/new-sample-db.png)

2. Tornare al pannello **Database SQL**. Nell'elenco **Selezionare l'esempio** sarà visualizzato il database **AdventureWorks LT [V12]**. Fare clic su **Crea** per avviare la creazione di server e database.

	![Creare un database SQL di esempio](./media/sql-database-get-started/adworks_create.png)

	>[AZURE.NOTE] In questa procedura rapida non sono state modificate le impostazioni per **Piano tariffario**, **Regole di confronto** e **Gruppo di risorse**. È possibile cambiare il piano tariffario di un database e ridimensionarlo in qualsiasi momento, senza tempi di inattività. Per altre informazioni, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/) e [Piani tariffari per il database SQL](sql-database-service-tiers.md). Non è possibile modificare le regole di confronto di un database dopo averle impostate qui. Per informazioni dettagliate sulle regole di confronto, vedere [Regole di confronto e supporto Unicode](https://msdn.microsoft.com/library/ms143726.aspx). Per informazioni dettagliate sui gruppi di risorse di Azure, vedere [Panoramica di Gestione risorse di Azure](resource-group-overview.md).

Tornare alla schermata iniziale di Azure nella quale è presente un riquadro che mostra lo stato di avanzamento fintanto che l'operazione di creazione non è completata e il database non è online. Per verificare se il database è online, è anche possibile fare clic su **Esplora tutto** e su **Database SQL**.

Congratulazioni. Il database SQL è ora in esecuzione nel cloud. La procedura è quasi terminata. Rimane solo un importante passaggio da completare. Sarà necessario creare una regola nel server di database per potersi connettere al database.

## Passaggio 4: Configurare il firewall

È necessario configurare una regola firewall nel server per consentire le connessioni dall'indirizzo IP del computer client, per poter usare il database. Questo passaggio, oltre a verificare che sia possibile stabilire la connessione, consente anche di visualizzare l'area in cui è possibile ottenere informazioni sui server SQL in Azure.

1. Fare clic su **Sfoglia**, scorrere verso il basso e quindi fare clic su **SQL Server**. Non fare clic su **Database SQL** inavvertitamente. È un errore comune.

	![Selezionare il server di database](./media/sql-database-get-started/browse_dbservers.png)

2. Dall'elenco di istanze di SQL Server fare clic su quello con il **Nome server** scelto nel passaggio 2. Nel pannello delle proprietà del database visualizzato a destra fare clic su **Impostazioni** e quindi selezionare **Firewall** dall'elenco.

	![Apertura delle impostazioni del firewall](./media/sql-database-get-started/db_settings.png)

  In **Impostazioni del firewall** verrà visualizzato l'**Indirizzo IP client** corrente.

	![Current IP address](./media/sql-database-get-started/firewall_config_client_ip.png)

3. Fare clic su **Aggiungi IP client** per consentire ad Azure di creare una regola per l'indirizzo IP specificato e quindi fare clic su **Salva**.

	![Aggiungere l'indirizzo IP](./media/sql-database-get-started/firewall_config_new_rule.png)

	>[AZURE.IMPORTANT] È possibile che l'indirizzo IP client cambi nel tempo. In questo caso, non sarà possibile accedere al server fino a quando non viene creata una nuova regola firewall. Per verificare il proprio indirizzo IP, usare [Bing](http://www.bing.com/search?q=my%20ip%20address) e quindi aggiungere un singolo indirizzo IP o un intervallo di indirizzi IP. Per altre informazioni, vedere [Procedura: Configurare le impostazioni del firewall nel database SQL](sql-database-configure-firewall-settings.md).

## Passaggi successivi
Al termine dell'esercitazione sul database SQL e dopo aver creato un database con alcuni dati di esempio, è possibile iniziare l'esplorazione con i propri strumenti preferiti.

- Se si ha familiarità con Transact-SQL e SQL Server Management Studio, vedere l'articolo che illustra come [connettersi a un database SQL ed eseguire query con SQL Server Management Studio](sql-database-connect-query-ssms.md).

- Se si ha familiarità con Excel, vedere l'articolo che illustra come [connettersi al database SQL con Excel](sql-database-connect-excel.md).

- Se si ha familiarità con la scrittura di codice, vedere [Connettersi al database SQL con C# ed eseguire query](sql-database-connect-query.md) e [Uso del database SQL da .NET (C#)](sql-database-develop-dotnet-simple.md). Vedere anche [Esempi di codice di avvio rapido per il database SQL](sql-database-develop-quick-start-client-code-samples.md) per Node.js, Python, Ruby, Java e PHP, gli esempi e le procedure per C++, oltre a quelli per C#.

- Per spostare i database SQL Server locali in Azure, vedere l'articolo relativo alla [migrazione di un database al database SQL di Azure](sql-database-cloud-migrate.md).


## Risorse aggiuntive

- [Tutti gli argomenti per il servizio database SQL di Azure](sql-database-index-all-articles.md) - indice

<!---HONumber=AcomDC_0330_2016-->