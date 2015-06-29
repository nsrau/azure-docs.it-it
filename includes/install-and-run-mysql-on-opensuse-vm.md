
1. Per l'escalation dei privilegi, eseguire:

		sudo -s
	
	Immettere la password.

2. Eseguire il comando seguente per installare MySQL Community Server Edition:

		# zypper install mysql-community-server

	Attendere fino al termine del download e dell'installazione di MySQL.
3. Per fare in modo che MySQL venga avviato all'avvio del sistema, eseguire il comando seguente:

		# insserv mysql
4. Ora è possibile avviare manualmente il daemon MySQL \(mysqld\) usando il comando seguente:

		# rcmysql start

	Per controllare lo stato del daemon MySQL, eseguire:

		# rcmysql status

	Per arrestare il daemon MySQL, eseguire:

		# rcmysql stop

5. Avviso Dopo l'installazione, la password radice di MySQL è vuota per impostazione predefinita. È consigliabile eseguire **mysql\_secure\_installation**, uno script per la protezione di MySQL. Durante l'esecuzione di **mysql\_secure\_installation**, verrà richiesto di modificare la password radice di MySQL, rimuovere gli account utente anonimi, disabilitare gli account di accesso radice remoti, rimuovere i database di test e ricaricare la tabella dei privilegi. È consigliabile rispondere sì a tutte queste opzioni e modificare la password radice. Digitare il comando seguente per eseguire lo script:

		$ mysql_secure_installation

6. Al termine dell'esecuzione, sarà possibile eseguire l'accesso a MySQL:

		$ mysql -u root -p

	Immettere la password radice di MySQL \(modificata nel passaggio precedente\). Verrà visualizzato un prompt in cui è possibile digitare istruzioni SQL per l'interazione con il database.

7. Per creare un nuovo utente MySQL, alla richiesta di **mysql\>** eseguire il comando seguente:

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Si osservi che il punto e virgola \(;\) alla fine delle righe è essenziale per la conclusione dei comandi.

8. Per creare un database e concedere le apposite autorizzazioni utente `mysqluser`, digitare i comandi seguenti:

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Si noti che i nomi utente e le password per il database sono usati solo da script per la connessione al database. I nomi di account utente per il database non rappresentano necessariamente account utente effettivi nel sistema.

9. Per eseguire l'accesso da un altro computer, eseguire il comando seguente:

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	dove `ip-address` è l'indirizzo IP del computer dal quale verrà effettuata la connessione a MySQL.
	
10. Per uscire dall'utilità di amministrazione database MySQL, eseguire il comando seguente:

		quit

11. Al termine dell'installazione di MySQL è necessario configurare un endpoint affinché sia possibile accedere a MySQL in modalità remota. Accedere al [portale di gestione di Azure][AzurePreviewPortal]. Nel portale di Azure fare clic su **Macchine virtuali**, quindi sul nome della nuova macchina virtuale e infine su **Endpoint**.

	!\[Endpoints\]\[Image7\]

12. Fare clic su **Aggiungi** nella parte inferiore della pagina. !\[Endpoints\]\[Image8\]

13. Aggiungere un endpoint denominato "MySQL", con protocollo **TCP** e con entrambe le porte **pubblica** e **privata** impostate su "3306". In questo modo sarà possibile accedere a MySQL in remoto. ![Endpoint][Image9]

14. Per eseguire la connessione remota a MySQL in esecuzione nella macchina virtuale OpenSUSE Azure, nel computer locale eseguire il comando seguente:

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	Ad esempio, per la macchina virtuale creata in questa esercitazione, sarebbe necessario usare il comando seguente:

		mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

15. In questa esercitazione è stato illustrato come configurare MySQL, creare un database e un nuovo utente. Per ulteriori informazioni su MySQL, vedere la [Documentazione di MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePreviewPortal]: http://manage.windowsazure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png

<!---HONumber=58_postMigration-->