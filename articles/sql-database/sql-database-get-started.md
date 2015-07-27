<properties
	pageTitle="Introduzione al database SQL"
	description="Creare il primo database cloud in pochi minuti con database SQL di Azure, servizio Microsoft di gestione di database relazionali (RDBMS) nel cloud, utilizzando il portale di Azure e il database di esempio AdventureWorks."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/15/2015"
	ms.author="genemi"/>


# Creare il primo database SQL di Azure


In questo articolo viene illustrato come creare un database SQL di Azure di esempio in meno di cinque minuti. Contenuto dell’esercitazione:


- Effettuare il provisioning di un server logico mediante l’utilizzo del [portale di Azure](http://portal.azure.com/).
- Creare un database popolato con dati di esempio.
- Impostare una regola firewall per il database per configurare gli indirizzi IP che possono accedere al database.


In questa esercitazione si presuppone di disporre di una sottoscrizione ad Azure. Se non si dispone di una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).


## Passaggio 1: Eseguire l'accesso


1. Accedere al [portale di Azure](http://portal.azure.com/).
2. Fare clic su **Nuovo** > **Dati + Archiviazione** > **Database SQL**.


![Nuovo database SQL][1]


## Passaggio 2: Creare il server logico



1. Nel pannello di database SQL, scegliere un **Nome** per il database, in questo esempio, **AdventureWorks**.
2. Per creare il server logico per il database, fare clic su **Server**, quindi su **Crea nuovo server**.


## Passaggio 3: Configurare il server


1. Nel pannello **Server**, immettere il **Nome server** come nome univoco semplice da ricordare.
2. Immettere l’**Account di accesso amministratore server** come **AdventureAdmin**.
3. Immettere il valore corretto per **Password** e **Conferma password**.
4. Selezionare la **Località** geografica preferita. In genere la località dovrebbe essere vicina al punto in cui viene eseguita l'applicazione.
5. Fare clic su **OK**.


![Creazione del server][2]


## Passaggio 4: Creare il database


1. Nel pannello di database SQL, specificare l'origine del database facendo clic su **Seleziona origine**.
 - Se si ignora questo passaggio, viene creato un database vuoto.
2. Selezionare **Esempio**.
 - In tal modo viene creato un database che è una copia del database di esempio standard denominato **AdventureWorks**.
 - In database SQL di Azure, viene utilizzata l’edizione *schema leggero* di AdventureWorks.
3. Fare clic su **Crea** nella parte inferiore del pannello.


## Passaggio 5: Configurare il firewall


Nei passaggi seguenti viene illustrato come specificare gli intervalli di indirizzi IP cui è consentito accedere al database.


![Ricerca del server][3]


1. Nella barra multifunzione sul lato sinistro della schermata, fare clic su **Sfoglia**, quindi su **SQL Server**.
2. Dalle opzioni disponibili, fare clic sul server SQL creato in precedenza.
3. Fare clic su **Impostazioni**, quindi su **Firewall**.
4. Fare clic su questo collegamento per ottenere l'indirizzo IP corrente da [Bing](http://www.bing.com/search?q=my%20ip%20address).
5. In Impostazioni firewall, immettere un **Nome regola**, e incollare l'indirizzo IP pubblico del passaggio precedente nei campi**IP iniziale** e **IP finale**.
6. Al termine, fare clic su **Salva** nella parte superiore della pagina.


![Firewall][4]


## Passaggi successivi


A questo punto si è pronti a scrivere un piccolo programma client in grado di connettersi al database. Per un esempio di codice di avvio rapido, fare clic su uno dei seguenti collegamenti:


- [Connettersi al database SQL con C# ed eseguire query](sql-database-connect-query.md)
- *Disponibile a breve:* sviluppo client ed esempi di avvio rapido al database SQL


<!-- Media references. -->
[1]: ./media/sql-database-get-started/GettingStarted_NewDB.PNG
[2]: ./media/sql-database-get-started/GettingStarted_CreateServer.png
[3]: ./media/sql-database-get-started/GettingStarted_BrowseServer.png
[4]: ./media/sql-database-get-started/GettingStarted_FireWall.png
 

<!---HONumber=July15_HO3-->