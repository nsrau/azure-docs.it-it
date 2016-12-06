
1. Per l'escalation dei privilegi, digitare:
   
        sudo -s
   
    Immettere la password.
2. Per installare MySQL Community Server Edition, digitare:
   
        zypper install mysql-community-server
   
    Attendere fino al termine del download e dell'installazione di MySQL.
3. Per impostare MySQL in modo che si avvii all’avvio del sistema, digitare:
   
        insserv mysql
4. Avviare manualmente il daemon MySQL (mysqld) con questo comando:
   
        rcmysql start
   
    Per controllare lo stato del daemon MySQL, digitare:
   
        rcmysql status
   
    Per arrestare il daemon MySQL, digitare:
   
        rcmysql stop
   
   > [!IMPORTANT]
   > Dopo l'installazione, la password radice di MySQL è vuota per impostazione predefinita. È consigliabile eseguire **mysql\_secure\_installation**, uno script per la protezione di MySQL. Lo script richiederà di cambiare la password radice di MySQL, rimuovere gli account utente anonimi, disabilitare gli account di accesso radice remoti, rimuovere i database di test e ricaricare la tabella dei privilegi. È consigliabile rispondere sì a tutte queste opzioni e modificare la password radice.
   > 
   > 
5. Digitare quanto segue per eseguire lo script MySQL dello script di installazione:
   
        mysql_secure_installation
6. Accedere a MySQL:
   
        mysql -u root -p
   
    Immettere la password radice di MySQL (modificata nel passaggio precedente). Verrà visualizzato un prompt in cui è possibile digitare istruzioni SQL per l'interazione con il database.
7. Per creare un nuovo utente MySQL, al prompt **mysql>** eseguire questo comando:
   
        CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    Si osservi che il punto e virgola (;) alla fine delle righe è essenziale per la conclusione dei comandi.
8. Per creare un database e concedere le apposite autorizzazioni utente `mysqluser` , digitare i comandi seguenti:
   
        CREATE DATABASE testdatabase;
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    Si noti che i nomi utente e le password per il database sono usati solo da script per la connessione al database.  I nomi di account utente per il database non rappresentano necessariamente account utente effettivi nel sistema.
9. Per accedere da un altro computer, digitare:
   
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';
   
    dove `ip-address` è l'indirizzo IP del computer dal quale verrà effettuata la connessione a MySQL.
10. Per uscire dall'utilità di amministrazione database MySQL, digitare:
    
        quit

## <a name="add-an-endpoint"></a>Aggiungere un endpoint
1. Al termine dell'installazione di MySQL è necessario configurare un endpoint per accedere a MySQL in modalità remota. Accedere al [portale di Azure classico][AzurePortal]. Fare clic su **Macchine virtuali**, quindi sul nome della nuova macchina virtuale e infine su **Endpoint**.
2. Fare clic su **Add** nella parte inferiore della pagina.
3. Aggiungere un endpoint denominato "MySQL", con protocollo **TCP** e con entrambe le porte **pubblica** e **privata** impostate su "3306".
4. Per connettersi in remoto alla macchina virtuale dal computer, digitare:
   
        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net
   
    Ad esempio, per la macchina virtuale creata in questa esercitazione, digitare questo comando:
   
        mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePortal]: http://manage.windowsazure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png


<!--HONumber=Nov16_HO3-->


