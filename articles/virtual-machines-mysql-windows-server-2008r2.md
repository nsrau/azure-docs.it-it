<properties urlDisplayName="Install MySQL" pageTitle="Creare una macchina virtuale che esegue MySQL in Azure " metaKeywords="Azure virtual machines, Azure Windows Server, Azure installing MySQL, Azure configuring MySQL, Azure databases" description="Create an Azure virtual machine running Windows Server 2008 R2, and then install and configure a MySQL database on the virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running Windows Server 2008 R2 in Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="12/15/2013" ms.author="kathydav" />


#Installare MySQL in una macchina virtuale che esegue Windows Server 2008 R2 in Azure

[MySQL](http://www.mysql.com) è un database SQL open source molto diffuso. Dalla raccolta immagini del [portale di gestione di Azure][AzurePreviewPortal], è possibile creare una macchina virtuale che esegue Windows Server 2008 R2 dalla Raccolta immagini.  Su tale macchina virtuale sarà quindi possibile installare e configurare un database MySQL.

In questa esercitazione si apprenderà come:

- Usare il portale di gestione per creare una macchina virtuale che esegue Windows Server 2008 R2.

- Installare ed eseguire MySQL Community Server nella macchina virtuale.

##Creazione di una macchina virtuale che esegue Windows Server 2008 R2

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

##Collegamento di un disco dati

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

##Installare ed eseguire MySQL Community Server nella macchina virtuale
Per installare, configurare ed eseguire MySQL Community Server, eseguire la procedura seguente:

1. Dopo avere eseguito la connessione alla macchina virtuale tramite Desktop remoto, aprire **Internet Explorer** dal menu **Start**. 

2. Nell'angolo superiore destro fare clic sul pulsante **Strumenti**. In **Opzioni Internet** selezionare la scheda **Sicurezza**, quindi l'icona **Siti attendibili** e infine fare clic sul pulsante **Siti**. Aggiungere *http://\*.mysql.com* all'elenco dei siti attendibili.

3. Passare alla pagina di [download di MySQL Community Server][MySQLDownloads]..

4. Selezionare **Microsoft Windows** nel menu a discesa **Piattaforma** e scegliere **Seleziona**.

5. Trovare la versione più recente di **Windows (x86, 64 bit), MSI Installer** e fare clic su **Download**. 

6. Fare clic su **No thanks, just start my download!** (in alternativa, è possibile creare un account).  Se richiesto, selezionare un sito mirror per scaricare il programma di installazione di MySQL e salvarlo sul desktop.

7. Fare doppio clic sul file di installazione sul desktop per avviare l'installazione.

8. Fare clic su **Avanti**.

	![MySQL Setup][MySQLInstall1]

9. Accettare il contratto di licenza e fare clic su **Next**.

	![MySQL Setup][MySQLInstall2]

10. Fare clic su **Typical** per installare le funzionalità più comuni.

	![MySQL Setup][MySQLInstall3]

11. Fare clic su **Install**.

	![MySQL Setup][MySQLInstall4]

12. Avviare la configurazione guidata di MySQL e fare clic su **Next**.

	![Configure MySQL][MySQLConfig1]

13. Selezionare **Detailed Configuration** e fare clic su Next.

	![Configure MySQL][MySQLConfig2]

14. Selezionare **Server Machine** se si prevede di eseguire MySQL insieme ad altre applicazioni nel server o l'opzione più adatta in base alle esigenze.  Fare clic su **Next**.

	![Configure MySQL][MySQLConfig3]

15. Selezionare **Multifunctional Database** o l'opzione più adatta in base alle esigenze.  Fare clic su **Next**.

	![Configure MySQL][MySQLConfig4]

16. Selezionare l'unità dati collegata nei passaggi precedenti.

	![Configure MySQL][MySQLConfig5]

17. Selezionare **Decision Support (DSS)/OLAP** oppure l'opzione più adatta in base alle esigenze.  Fare clic su **Next**.

	![Configure MySQL][MySQLConfig6]

18. Selezionare **Enable TCP/IP Networking** e **Add firewall exception for this port** (questo consentirà di creare una regola connessioni in entrata in Windows Firewall denominata **MySQL Server**).

	![Configure MySQL][MySQLConfig7]

19. Selezionare **Best Support For Multilingualism** se è necessario archiviare testo in molte lingue diverse oppure l'opzione più adatta in base alle esigenze.  Fare clic su **Next**.

	![Configure MySQL][MySQLConfig8]

20. Selezionare **Install As Windows Service** e **Launch the MySQL Server automatically**.  Selezionare anche l'opzione **Include Bin Directory in Windows PATH**. Fare clic su **Avanti**.

	![Configure MySQL][MySQLConfig9]

21. Immettere la password radice. Non selezionare l'opzione **Enable root access from remote machines** né l'opzione **Create An Anonymous Account**.  Fare clic su **Next**.

	![Configure MySQL][MySQLConfig10]

22. Fare clic su **Execute** e attendere il completamento della configurazione.

	![Configure MySQL][MySQLConfig11]

23. Fare clic su **Finish**.

	![Configure MySQL][MySQLConfig12]

24. Fare clic su **Start** e selezionare **MySQL 5.x Command Line Client** per avviare il client della riga di comando.

25.  Immettere la password radice al prompt (impostata in un passaggio precedente). Verrà visualizzato un prompt in cui è possibile digitare istruzioni SQL per l'interazione con il database.

26. Per creare un nuovo utente MySQL, alla richiesta di **mysql>** eseguire il comando seguente:

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Si osservi che il punto e virgola (;) alla fine delle righe è essenziale per la conclusione dei comandi.

27. Per creare un database e concedere le apposite autorizzazioni utente `mysqluser`, digitare i comandi seguenti:

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Si noti che i nomi utente e le password per il database sono usati solo da script per la connessione al database.  I nomi di account utente per il database non rappresentano necessariamente account utente effettivi nel computer.

28. Per eseguire l'accesso da un altro computer, eseguire il comando seguente:

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	dove `ip-address` è l'indirizzo IP del computer da quale verrà effettuata la connessione a MySQL.
	
29. Per uscire dal client della riga di comando MySQL, eseguire il comando seguente:

		quit

30. Al termine dell'installazione di MySQL è necessario configurare un endpoint affinché sia possibile accedere a MySQL in modalità remota. Accedere al [portale di gestione di Azure][AzurePreviewPortal]. Nel portale di Azure fare clic su **Macchine virtuali**, quindi sul nome della macchina virtuale desiderata, successivamente su **Endpoint** e infine su  **Aggiungi endpoint**.

	![Endpoints][AddEndPoint]

31. Selezionare **Aggiungi endpoint**, quindi fare clic sulla freccia per continuare.
	
	![Endpoints][AddEndPoint2]

32. Aggiungere un endpoint denominato "MySQL", con protocollo **TCP** e con entrambe le porte **Public** e **Private** impostate su "3306". Fare clic sul segno di spunta. Questo consentirà di accedere a MySQL in modalità remota.
	
	![Endpoints][AddEndPoint3]

33. È possibile stabilire una connessione remota a MySQL mentre è in esecuzione nella macchina virtuale in Azure.  Eseguire il comando seguente da un computer locale che esegue MySQL per accedere come l'utente **mysqluser** creato nei passaggi precedenti:

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	Ad esempio, per la macchina virtuale creata in questa esercitazione, sarebbe necessario usare il comando seguente:

		mysql -u mysqluser -p -h testwinvm.cloudapp.net

##Riepilogo

In questa esercitazione è stato illustrato come creare una macchina virtuale Windows 2008 R2 e come stabilire una connessione remota a quest'ultima. È stato inoltre illustrato come installare e configurare MySQL nella macchina virtuale, come creare un database e un nuovo utente MySQL. Per altre informazioni su MySQL, vedere la [Documentazione di MySQL](http://dev.mysql.com/doc/).

[AzurePreviewPortal]: http://manage.windowsazure.com
[MySQLDownloads]: http://www.mysql.com/downloads/mysql/


[MySQLInstall1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall1.png
[MySQLInstall2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall2.png
[MySQLInstall3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall3.png
[MySQLInstall4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall4.png
[MySQLConfig1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig1.png
[MySQLConfig2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig2.png
[MySQLConfig3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig3.png
[MySQLConfig4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig4.png
[MySQLConfig5]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png
[MySQLConfig6]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig6.png
[MySQLConfig7]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png
[MySQLConfig8]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png
[MySQLConfig9]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png
[MySQLConfig10]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png
[MySQLConfig11]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig11.png
[MySQLConfig12]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig12.png
[AddEndPoint]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL0.png
[AddEndPoint2]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL1.png
[AddEndPoint3]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL.png
