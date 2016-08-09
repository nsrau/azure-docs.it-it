

## Connettersi al database SQL di Azure con un accesso entità a livello di server

Usare i passaggi seguenti per connettersi al database SQL di Azure con SSMS usando un accesso entità a livello di server.

1. Digitare "Microsoft SQL Server Management Studio" nella casella di ricerca di Windows e quindi fare clic sull'applicazione desktop per avviare SSMS.

2. Nella finestra Connetti al server immettere le informazioni seguenti:

 - **Tipo di server**: il valore predefinito è il motore di database. Non modificarlo.
 - **Nome server**: immettere il nome del server che ospita il database SQL nel formato seguente: *&lt;nomeserver>*.**database.windows.net**
 - **Tipo di autenticazione**: se si sta iniziando, selezionare Autenticazione SQL. Se si è abilitato Active Directory per il server logico del database SQL, è possibile selezionare Autenticazione della password Active Directory o Autenticazione integrata di Active Directory.
 - **Nome utente**: se si è selezionato Autenticazione SQL o Autenticazione della password Active Directory, immettere il nome di un utente con accesso a un database sul server.
 - **Password**: se si è selezionato Autenticazione SQL o Autenticazione della password Active Directory, immettere la password per l'utente specificato.
   
       ![SQL Server Management Studio: Connettersi a un server di database SQL](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-1.png)

3. Fare clic su **Connect**.
 
4. Se l'indirizzo IP del client non ha accesso al server logico del database SQL, verrà chiesto di accedere a un account Azure e di creare una regola del firewall a livello di server. Se si è un amministratore della sottoscrizione di Azure, fare clic su **Accedi** per creare una regola del firewall a livello di server. In caso contrario, chiedere a un amministratore di Azure di creare una regola del firewall a livello di server.
 
      ![SQL Server Management Studio: Connettersi a un server di database SQL](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-2.png)
 
1. Se si è un amministratore della sottoscrizione di Azure ed è necessario accedere, quando viene visualizzata la pagina di accesso, fornire le credenziali per la sottoscrizione e accedere.

      ![sign in](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-3.png)
 
1. Dopo l'accesso ad Azure, rivedere la regola del firewall a livello di server proposta (è possibile modificarla per consentire un intervallo di indirizzi IP) e quindi fare clic su **OK** per creare la regola del firewall e completare la connessione al database SQL.
 
      ![Nuovo firewall a livello di server](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-4.png)
 
5. Se le credenziali concedono l'accesso, si apre Esplora oggetti e sarà possibile eseguire attività amministrative o effettuare una query dei dati.
 
     ![Nuovo firewall a livello di server](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-5.png)
 
     
## Risolvere i problemi relativi agli errori di connessione

I motivi più comuni per cui si verificano errori di connessione sono gli errori nel nome del server (si ricordi che <*nomeserver*> è il nome del server logico, non del database), gli errori nel nome utente o nella password, nonché il blocco delle connessioni da parte del server per motivi di sicurezza.

<!---HONumber=AcomDC_0803_2016-->