<properties linkid="manage-windows-common-tasks-install-mysql" urlDisplayName="Install MySQL" pageTitle="Create a virtual machine running MySQL in Azure " metaKeywords="Azure virtual machines, Azure Windows Server, Azure installing MySQL, Azure configuring MySQL, Azure databases" description="Create an Azure virtual machine running Windows Server 2008 R2, and then install and configure a MySQL database on the virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running Windows Server 2008 R2 in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Installazione di MySQL in una macchina virtuale che esegue Windows Server 2008 R2 in Azure
==========================================================================================

[MySQL](http://www.mysql.com) è un database SQL open source molto diffuso. Dalla raccolta immagini del [portale di gestione di Azure](http://manage.windowsazure.com), è possibile creare una macchina virtuale che esegue Windows Server 2008 R2. Su tale macchina virtuale sarà quindi possibile installare e configurare un database MySQL.

In questa esercitazione si apprenderà come:

-   Utilizzare il portale di gestione per creare una macchina virtuale che esegue Windows Server 2008 R2.

-   Installare ed eseguire MySQL Community Server nella macchina virtuale.

Creazione di una macchina virtuale che esegue Windows Server 2008 R2
--------------------------------------------------------------------

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

Collegamento di un disco dati
-----------------------------

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

Installazione ed esecuzione di MySQL Community Server nella macchina virtuale
-----------------------------------------------------------------------------

Per installare, configurare ed eseguire MySQL Community Server, eseguire la procedura seguente:

1.  Dopo avere eseguito la connessione alla macchina virtuale tramite Desktop remoto, aprire **Internet Explorer** dal menu **Start**.

2.  Nell'angolo superiore destro fare clic sul pulsante **Strumenti**. In **Opzioni Internet** selezionare la scheda **Sicurezza**, quindi l'icona **Siti attendibili** e infine fare clic sul pulsante **Siti**. Aggiungere *http://\*.mysql.com* all'elenco dei siti attendibili.

3.  Passare alla pagina di [download di MySQL Community Server](http://www.mysql.com/downloads/mysql/).

4.  Selezionare **Microsoft Windows** nel menu a discesa relativo alla **piattaforma** e scegliere **Select**.

5.  Trovare la versione più recente di **Windows (x86, 64-bit), MSI Installer** e fare clic su **Download**.

6.  Fare clic su **No thanks, just start my download** (in alternativa, è possibile creare un account). Se richiesto, selezionare un sito mirror per scaricare il programma di installazione di MySQL e salvarlo sul desktop.

7.  Fare doppio clic sul file di installazione sul desktop per avviare l'installazione.

8.  Fare clic su **Next**.

    ![Installazione di MySQL](./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall1.png)

9.  Accettare il contratto di licenza e fare clic su **Next**.

    ![Installazione di MySQL](./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall2.png)

10. Fare clic su **Typical** per installare le funzionalità più comuni.

    ![Installazione di MySQL](./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall3.png)

11. Fare clic su **Install**.

    ![Installazione di MySQL](./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall4.png)

12. Avviare la configurazione guidata di MySQL e fare clic su **Next**.

    ![Configurazione di MySQL](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig1.png)

13. Selezionare **Detailed Configuration** e fare clic su Next.

    ![Configurazione di MySQL](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig2.png)

14. Selezionare **Server Machine** se si prevede di eseguire MySQL insieme ad altre applicazioni nel server o l'opzione più adatta in base alle esigenze. Fare clic su **Next**.

    ![Configurazione di MySQL](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig3.png)

15. Selezionare **Multifunctional Database** o l'opzione più adatta in base alle esigenze. Fare clic su **Next**.

    ![Configurazione di MySQL](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig4.png)

16. Selezionare l'unità dati collegata nei passaggi precedenti.

    ![Configurazione di MySQL](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png)

17. Selezionare **Decision Support (DSS)/OLAP** oppure l'opzione più adatta in base alle esigenze. Fare clic su **Next**.

    ![Configurazione di MySQL](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig6.png)

18. Selezionare **Enable TCP/IP Networking** e **Add firewall exception for this port** (questo consentirà di creare una regola connessioni in entrata in Windows Firewall denominata **MySQL Server**).

    ![Configurazione di MySQL](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png)

19. Selezionare **Best Support For Multilingualism** se è necessario archiviare testo in molte lingue diverse oppure l'opzione più adatta in base alle esigenze. Fare clic su **Next**.

    ![Configurazione di MySQL](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png)

20. Selezionare **Install As Windows Service** e **Launch the MySQL Server automatically**. Selezionare anche l'opzione **Include Bin Directory in Windows PATH**. Fare clic su **Next**.

    ![Configurazione di MySQL](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png)

21. Immettere la password radice. Non selezionare l'opzione **Enable root access from remote machines** né l'opzione **Create An Anonymous Account**. Fare clic su **Next**.

    ![Configurazione di MySQL](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png)

22. Fare clic su **Execute** e attendere il completamento della configurazione.

    ![Configurazione di MySQL](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig11.png)

23. Fare clic su **Finish**.

    ![Configurazione di MySQL](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig12.png)

24. Fare clic su **Start** e selezionare **MySQL 5.x Command Line Client** per avviare il client della riga di comando.

25. Immettere la password radice al prompt (impostata in un passaggio precedente). Verrà visualizzato un prompt in cui è possibile digitare istruzioni SQL per l'interazione con il database.

26. Per creare un nuovo utente MySQL, eseguire il comando seguente al prompt **mysql\>**:

        mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

    Si osservi che il punto e virgola (;) alla fine delle righe è essenziale per la conclusione dei comandi.

27. Per creare un database e concedere le apposite autorizzazioni utente `mysqluser`, digitare i comandi seguenti:

        mysql> CREATE DATABASE testdatabase;
        mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

    Si noti che i nomi utente e le password per il database sono utilizzati solo da script per la connessione al database. I nomi di account utente per il database non rappresentano necessariamente account utente effettivi nel computer.

28. Per eseguire l'accesso da un altro computer, eseguire il comando seguente:

        mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

    dove `ip-address` è l'indirizzo IP del computer dal quale verrà effettuata la connessione a MySQL.

29. Per uscire dal client della riga di comando MySQL, eseguire il comando seguente:

        quit

30. Al termine dell'installazione di MySQL è necessario configurare un endpoint affinché sia possibile accedere a MySQL in modalità remota. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com). Nel portale di Azure fare clic su **Virtual Machines**, quindi sul nome della macchina virtuale desiderata, successivamente su **Endpoints** e infine su **Add Endpoint**.

    ![Endpoint](./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL0.png)

31. Selezionare **Add Endpoint**, quindi fare clic sulla freccia per continuare.

    ![Endpoint](./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL1.png)

32. Aggiungere un endpoint denominato "MySQL", con protocollo **TCP** e con entrambe le porte **Public** e **Private** impostate su "3306". Fare clic sul segno di spunta. Questo consentirà di accedere a MySQL in modalità remota.

    ![Endpoint](./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL.png)

33. È possibile stabilire una connessione remota a MySQL mentre è in esecuzione nella macchina virtuale in Azure. Eseguire il comando seguente da un computer locale che esegue MySQL per accedere come l'utente **mysqluser** creato nei passaggi precedenti:

        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

    Ad esempio, per la macchina virtuale creata in questa esercitazione, sarebbe necessario utilizzare il comando seguente:

        mysql -u mysqluser -p -h testwinvm.cloudapp.net

Riepilogo
---------

In questa esercitazione è stato illustrato come creare una macchina virtuale Windows 2008 R2 e come stabilire una connessione remota a quest'ultima. È stato inoltre illustrato come installare e configurare MySQL nella macchina virtuale, come creare un database e un nuovo utente MySQL. Per ulteriori informazioni su MySQL, vedere la [Documentazione di MySQL](http://dev.mysql.com/doc/).

