<properties 
	pageTitle="Come creare e configurare un database SQL di Azure - Esercitazione di Azure" 
	description="Informazioni su come creare e configurare un database SQL di Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="sidneyh" 
	manager="jhubbard" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2014" 
	ms.author="sidneyh"/>

# Come creare e configurare un database SQL di Azure

In questo argomento verrà creato e configurato un nuovo database SQL di Azure usando l'opzione **CREAZIONE RAPIDA** del portale di gestione di Azure.. Questo processo illustra come creare un database SQL usando un server esistente e, se necessario, come creare un nuovo server.

> [AZURE.NOTE] Tramite la creazione di un database SQL con **CREAZIONE RAPIDA** viene eseguito il provisioning di un database Standard (S0). Per creare un database SQL con un livello di prestazioni e di servizio diverso da Standard (S0), usare **CREAZIONE PERSONALIZZATA**. Per informazioni dettagliate sulla creazione di un database SQL di Azure mediante **CREAZIONE PERSONALIZZATA**, vedere [Introduzione al database SQL di Microsoft Azure](http://azure.microsoft.com/documentation/articles/sql-database-get-started/).

## Procedura: Creare un database SQL di Azure

1. Accedere al [portale di gestione](https://portal.azure.com/).

2. Nella parte inferiore della pagina fare clic su **NUOVO**.

	![Click SQL Databases][1]

3. Fare clic su **SERVIZI DATI** e **DATABASE SQL**, quindi su **CREAZIONE RAPIDA**.

	![Click New, Data Services, and Quick Create][2]

5. In **CREAZIONE RAPIDA**, immettere un nome per il nuovo database, selezionare una sottoscrizione, quindi scegliere un server dall'elenco **SERVER** oppure andare al passaggio successivo per creare un nuovo server.

	![Create a new SQL Database in an existing server][7]

	Facoltativamente, è possibile creare un nuovo server selezionando **Nuovo server di database SQL**.
    ![Create a new SQL Database and a new server][8]

	1. Scegliere un'area, che determina la posizione geografica del server. Poiché le aree non possono essere cambiate facilmente, sceglierne una significativa per questo server. Selezionare la posizione più vicina. Per ridurre i costi relativi alla larghezza di banda in uscita e la latenza dei dati, è consigliabile mantenere il database e l'applicazione Azure nella stessa area.
	2. Immettere un nome di accesso composto da una sola parola senza spazi. 

		Nel database SQL viene usata l'autenticazione SQL tramite una connessione crittografata. Con il nome specificato verrà creato un nuovo account di accesso con autenticazione di SQL Server assegnato al ruolo predefinito del server sysadmin. 

		L'account di accesso non può essere un indirizzo e-mail, un account utente Windows o un Windows Live ID. In un database SQL non sono supportate né l'autenticazione basata sulle attestazioni né l'autenticazione di Windows. 
	3. Impostare una password complessa composta da più di otto caratteri, usando una combinazione di caratteri in minuscolo e maiuscolo e da un numero o un simbolo.

	


9. Al termine, fare clic sul segno di spunta **CREA DATABASE SQL** nella parte inferiore della pagina.

### Nome del server generato automaticamente

Si noti che non è stato specificato un nome per il nuovo server. Il database SQL genera automaticamente il nome del server per garantire che non vi siano voci DNS duplicate. Il nome del server è una stringa alfanumerica di dieci caratteri. Il nome del server di database SQL non è modificabile.

Nel passaggio successivo si configurerà il firewall in modo da consentire l'accesso alle connessioni da applicazioni in esecuzione nella rete.

<a id="configFWLogical"></a>

## Procedura: Configurare il firewall per il server logico

1. Nel [portale di gestione](http://manage.windowsazure.com) fare clic su **Database SQL**, quindi su **Server**.

	![Click Servers][4]

2. Nell'elenco fare clic sul server appena creato.

2. Fare clic su **Configura**.

	![Click Configure][5]

3. Nella sezione **indirizzi IP consentiti** fare clic su **AGGIUNGERE AGLI INDIRIZZI IP CONSENTITI**. Questo è l'indirizzo IP su cui è attualmente in ascolto il router o il server proxy. Il database SQL rileva l'indirizzo IP usato dalla connessione corrente e crea una regola firewall per accettare le richieste di connessione da questo dispositivo. 

	![Click Add to the allowed IP addresses][6]

	Viene generato un nome predefinito per la regola. Modificare il nome come desiderato. 
	

4. Quando ci si connette al database da un altro computer, è necessario creare una nuova regola per consentire il relativo indirizzo IP per la connessione. Usare le caselle di inizio e fine per creare un intervallo di indirizzi IP.

	Se i computer client usano indirizzi IP assegnati dinamicamente, è possibile specificare un intervallo sufficientemente ampio da includere gli indirizzi IP assegnati ai computer della rete. Iniziare con un intervallo ristretto, quindi ampliarlo solo se è necessario.

7. Per completare il passaggio, fare clic su **Salva** nella parte inferiore della pagina. 

A questo punto sono stati configurati un database SQL, un server logico, una regola del firewall che consente le connessioni in ingresso dall'indirizzo IP e un account di accesso dell'amministratore. 

**Nota:** il server logico appena creato è virtuale e verrà ospitato dinamicamente nei server fisici di un data center. L'eliminazione del server è un'azione irreversibile. Assicurarsi di eseguire il backup di tutti i database successivamente caricati nel server. 


<!--Image references-->
[1]: ./media/sql-database-create-configure/click-new.png
[2]: ./media/sql-database-create-configure/new-data-services-sql-storage-quick-create.png
[3]: ./media/sql-database-create-configure/server-settings.png
[4]: ./media/sql-database-create-configure/click-servers.png
[5]: ./media/sql-database-create-configure/click-configure.png
[6]: ./media/sql-database-create-configure/allowed-ip-addresses.png
[7]: ./media/sql-database-create-configure/quick-create-existing-server.png
[8]: ./media/sql-database-create-configure/quick-create-new-server.png




<!--HONumber=47-->
