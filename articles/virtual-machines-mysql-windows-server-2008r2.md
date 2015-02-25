<properties pageTitle="Creazione di una macchina virtuale che esegue MySQL in Azure" description="Creare una macchina virtuale di Azure che esegue Windows Server 2008 R2, quindi installare e configurare un database MySQL nella macchina virtuale." services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor="tysonn"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="kathydav"/>


#Installare MySQL in una macchina virtuale che esegue Windows Server 2008 R2 in Azure

[MySQL](http://www.mysql.com) è un database SQL open source molto diffuso. Dalla raccolta immagini del [portale di gestione di Azure][AzurePreviewPortal], è possibile creare una macchina virtuale in esecuzione su Windows Server 2008 R2. Su tale macchina virtuale sarà quindi possibile installare e configurare un database MySQL.

Questa esercitazione illustra come:

- Usare il portale di gestione per creare una macchina virtuale che esegue Windows Server 2008 R2.

- Installare ed eseguire MySQL Community Server nella macchina virtuale.

##Creare una macchina virtuale che esegue Windows Server

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

##Collegamento di un disco dati

Dopo aver creato la macchina virtuale, collegare un disco dati. Questo disco fornisce l'archiviazione di dati necessaria quando si installa MySQL. Vedere [Come collegare un disco dati a una macchina virtuale Windows](http://azure.microsoft.com/it-it/documentation/articles/storage-windows-attach-disk/) e seguire le istruzioni per collegare un disco vuoto.

##Accesso alla macchina virtuale
Collegarsi quindi alla macchina virtuale in modo da poter installare MySQL.

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

##Installare ed eseguire MySQL Community Server nella macchina virtuale
Per installare, configurare ed eseguire MySQL Community Server, eseguire la procedura seguente:

1. Dopo avere eseguito la connessione alla macchina virtuale tramite Desktop remoto, aprire **Internet Explorer** dal menu **Start**. 

2. Nell'angolo superiore destro fare clic sul pulsante **Strumenti**. In **Opzioni Internet** selezionare la scheda **Sicurezza**, quindi l'icona **Siti attendibili** e infine fare clic sul pulsante **Siti**. Aggiungere *http://\*.mysql.com* all'elenco di siti attendibili.

3. Passare alla pagina di [download di MySQL Community Server][MySQLDownloads].

4. Selezionare **Microsoft Windows** nel menu a discesa relativo alla **piattaforma** e scegliere **Select**.

5. Trovare la versione più recente di **Windows (x86, 64-bit), MSI Installer** e fare clic su **Download**. 

6. Selezionare **No thanks, just start my download!** (in alternativa, è possibile creare un account).  Se richiesto, selezionare un sito mirror per scaricare il programma di installazione di MySQL e salvarlo sul desktop.

7. Fare doppio clic sul file di installazione sul desktop per avviare l'installazione.

8. Fare clic su **Next**.

9. Accettare il contratto di licenza e fare clic su **Next**.

10. Fare clic su **Typical** per installare le funzionalità più comuni.

11. Fare clic su **Install**.

12. Avviare la configurazione guidata di MySQL e fare clic su **Next**.

13. Selezionare **Detailed Configuration** e fare clic su Next.

14. Selezionare **Server Machine** se si prevede di eseguire MySQL insieme ad altre applicazioni nel server o l'opzione più adatta in base alle esigenze.  Fare clic su **Next**.

15. Selezionare **Multifunctional Database** o l'opzione più adatta in base alle esigenze.  Fare clic su **Next**.

16. Selezionare l'unità dati collegata nella sezione precedente.

	![Configure MySQL][MySQLConfig5]

17. Selezionare **Decision Support (DSS)/OLAP** oppure l'opzione più adatta in base alle esigenze.  Fare clic su **Next**.

18. Selezionare **Enable TCP/IP Networking** e **Add firewall exception for this port** (questo consentirà di creare una regola connessioni in entrata in Windows Firewall denominata **MySQL Server**).

	![Configure MySQL][MySQLConfig7]

19. Se è necessario archiviare testo in molte lingue diverse, selezionare **Best Support For Multilingualism**. In alternativa, scegliere una delle altre opzioni disponibili.  Fare clic su **Next**.

	![Configure MySQL][MySQLConfig8]

20. Selezionare **Install As Windows Service** e **Launch the MySQL Server automatically**.  Selezionare anche l'opzione **Include Bin Directory in Windows PATH**. Fare clic su **Next**.

	![Configure MySQL][MySQLConfig9]

21. Immettere la password radice. Non selezionare l'opzione **Enable root access from remote machines** né l'opzione **Create An Anonymous Account**.  Fare clic su **Next**.

	![Configure MySQL][MySQLConfig10]

22. Fare clic su **Execute** e attendere il completamento della configurazione.

23. Fare clic su **Finish**.

24. Fare clic su **Start** e selezionare **MySQL 5.x Command Line Client** per avviare il client della riga di comando.

25.  Immettere la password radice al prompt (impostata in un passaggio precedente). Verrà visualizzato un prompt in cui è possibile digitare istruzioni SQL per l'interazione con il database.

26. Per creare un nuovo utente MySQL, al prompt di **mysql>** eseguire il comando seguente:

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	The semi-colons (;) at the end of the lines are required for ending the commands.

27. Per creare un database e e concedere le apposite autorizzazioni utente  `mysqluser`, eseguire questi comandi:

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Si noti che i nomi utente e le password per il database sono usati solo da script per la connessione al database.  I nomi di account utente per il database non rappresentano necessariamente account utente effettivi nel computer.

28. Per eseguire l'accesso da un altro computer, eseguire il comando seguente:

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	dove `ip-address` è l'indirizzo IP del computer dal quale verrà effettuata la connessione a MySQL.
	
29. Per uscire dal client della riga di comando MySQL, eseguire il comando seguente:

		quit

30. Al termine dell'installazione di MySQL, configurare un endpoint in modo da poter accedere a MySQL in modalità remota. Accedere al [portale di gestione di Azure][AzurePreviewPortal]. Nel portale di Azure fare clic su **Macchine virtuali**, sul nome della macchina virtuale, quindi su **Endpoint** e su **Aggiungi endpoint**.

31. Selezionare **Aggiungi endpoint**, quindi fare clic sulla freccia per continuare.
	

32. Aggiungere un endpoint denominato "MySQL", con protocollo **TCP** e con entrambe le porte **pubblica** e **privata** impostate su "3306". Fare clic sul segno di spunta. Questo consentirà di accedere a MySQL in modalità remota.
	

33. Testare la connessione remota a MySQL.  Eseguire un comando simile a quello riportato di seguito da un computer locale che esegue MySQL per accedere come utente **mysqluser**:

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	For example, if you named the virtual machine "testwinvm", run this command:

		mysql -u mysqluser -p -h testwinvm.cloudapp.net

##Risorse
Per informazioni su MySQL, vedere la [documentazione di MySQL](http://dev.mysql.com/doc/).

[AzurePortal]: http://manage.windowsazure.com
[MySQLDownloads]: http://www.mysql.com/downloads/mysql/


[MySQLConfig5]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png
[MySQLConfig7]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png
[MySQLConfig8]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png
[MySQLConfig9]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png
[MySQLConfig10]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png





<!--HONumber=42-->
