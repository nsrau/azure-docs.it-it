<properties urlDisplayName="How to create and configure an Azure SQL DB" pageTitle="Come creare e configurare un database SQL di Azure - Esercitazione di Azure" metaKeywords="Create Azure SQL Database, Configure Azure SQL Database" description="Informazioni su come creare e configurare un database SQL di Azure." metaCanonical="" services="sql-database" documentationCenter="" title="How to Create and Configure an Azure SQL Database" authors="sidneyh" solutions="" manager="jhubbard" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sidneyh" />

<h1><a id="configLogical"></a>Come creare e configurare un database SQL di Azure</h1>

In questo argomento verrà creato e configurato un database SQL di Azure usando il portale di gestione di Azure. In questo flusso di lavoro verrà creato prima il server. Questo approccio può essere preferibile se si dispone già di alcuni database di SQL Server da caricare.

##Sommario##
* [Procedura: Creare un server logico](#createLogical)
* [Procedura: Configurare il firewall per il server logico](#configFWLogical)

<h2><a id="createLogical"></a>Procedura: Creare un server logico</h2>

1. Accedere al portale di gestione[](http://manage.windowsazure.com).

2. Nella parte inferiore della pagina fare clic su **NUOVO**.

	![Click SQL Databases][1]

3. Fare clic su **Servizi dati**, e **Database SQL**, quindi su **Creazione rapida**.

	![Click New, Data Services, and Quick Create][2]
	 
5. Nelle **impostazioni del server di database SQL**, selezionare una sottoscrizione.

	![Select a subscription][3]

6. Nelle impostazioni del server di database SQL****, immettere un nome di accesso composto da una sola parola senza spazi. 

Nel database SQL viene usata l'autenticazione SQL tramite una connessione crittografata. Con il nome specificato verrà creato un nuovo account di accesso con autenticazione di SQL Server assegnato al ruolo predefinito del server sysadmin. 

	L'account di accesso non può essere un indirizzo di posta elettronica, un account utente Windows o un Windows Live ID. In un database SQL non sono supportate né l'autenticazione basata sulle attestazioni né l'autenticazione di Windows.
	
7. Impostare una password complessa composta da più di otto caratteri, usando una combinazione di caratteri in minuscolo e maiuscolo e da un numero o un simbolo.

7. Scegliere un'area, che determina la posizione geografica del server. Poiché le aree non possono essere cambiate facilmente, sceglierne una significativa per questo server. Selezionare la posizione più vicina. Per ridurre i costi relativi alla larghezza di banda in uscita e la latenza dei dati, è consigliabile mantenere il database e l'applicazione Azure nella stessa area.

8. Assicurarsi che la casella di controllo **Consentire ai servizi di Microsoft Azure di accedere al server** sia selezionata in modo da consentire la connessione al database tramite il portale di gestione per il database SQL, i servizi di archiviazione e altri servizi di Azure. 

9. Al termine, fare clic sul segno di spunta nella parte inferiore della pagina.

###Nome del server generato automaticamente

Si noti che non è stato specificato un nome di server. Il database SQL genera automaticamente il nome del server per garantire che non vi siano voci DNS duplicate. Il nome del server è una stringa alfanumerica di dieci caratteri. Il nome del server di database SQL non è modificabile.

Nel passaggio successivo si configurerà il firewall in modo da consentire l'accesso alle connessioni da applicazioni in esecuzione nella rete.

##Procedura: Configurare il firewall per il server logico

1. Nel [portale di gestione](http://manage.windowsazure.com), scegliere **Database SQL**, quindi **Server**

	![Click Servers][4]
2. Nell'elenco, fare clic sul server appena creato.

2. Fare clic su **Configura**.

	![Click Configure][5]

3. Nella sezione **indirizzi IP consentiti** fare clic su **AGGIUNGERE AGLI INDIRIZZI IP CONSENTITI**. ovvero l'indirizzo IP su cui è attualmente in ascolto il router o il server proxy. Il database SQL rileva l'indirizzo IP usato dalla connessione corrente e crea una regola firewall per accettare le richieste di connessione da questo dispositivo. 
	![Click Add to the allowed IP addresses][6]

	Viene generato un nome predefinito per la regola. Modificare il nome come desiderato. 
	

4. Quando ci si connette al database da un altro computer, è necessario creare una nuova regola per consentire il relativo indirizzo IP per la connessione. Usare le caselle di inizio e fine per creare un intervallo di indirizzi IP.

	Se i computer client usano indirizzi IP assegnati dinamicamente, è possibile specificare un intervallo sufficientemente ampio da includere gli indirizzi IP assegnati ai computer della rete. Iniziare con un intervallo ristretto, quindi ampliarlo solo se è necessario.

7. Per completare il passaggio, fare clic su **Salva** nella parte inferiore della pagina. 

A questo punto sono stati configurati un server logico, una regola del firewall che consente le connessioni in ingresso dall'indirizzo IP e un account di accesso dell'amministratore. 

**Nota:** il server logico appena creato è temporaneo e verrà ospitato dinamicamente nei server fisici di un data center. L'eliminazione del server è un'operazione irreversibile. Assicurarsi di eseguire il backup di tutti i database caricati in seguito nel server. 


<!--Image references-->
[1]: ./media/sql-database-create-configure/click-new.png
[2]: ./media/sql-database-create-configure/new-data-services-sql-storage-quick-create.png
[3]: ./media/sql-database-create-configure/server-settings.png
[4]: ./media/sql-database-create-configure/click-servers.png
[5]: ./media/sql-database-create-configure/click-configure.png
[6]: ./media/sql-database-create-configure/allowed-ip-addresses.png

