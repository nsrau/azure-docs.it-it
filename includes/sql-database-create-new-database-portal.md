
<!--
includes/sql-database-create-new-database-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md

-->
## Creare un nuovo database SQL di Azure

Usare la procedura seguente nel portale di Azure per creare un nuovo database SQL di Azure in un server logico di database SQL di Azure nuovo o esistente.

1. Se non si è già connessi, accedere al [portale di Azure](http://portal.azure.com).
2. Fare clic su **Nuovo**, digitare **Database SQL** e quindi fare clic su **Database SQL (nuovo database)**

     ![nuovo database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-1.png)

3. Fare clic su Database SQL (nuovo database).

     ![nuovo database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-2.png)
   
4. Fare clic su **Crea ** per creare un nuovo servizio database SQL.

     ![nuovo database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-3.png)

5. Specificare i valori per le proprietà del server seguenti:

 - Nome database
 - Sottoscrizione: solo se sono disponibili più sottoscrizioni
 - Gruppo di risorse: per iniziare a usare il gruppo di risorse del server logico
 - Selezionare l'origine: è possibile scegliere un database vuoto, dati di esempio o un backup del database
 - Server: un server logico nuovo o esistente
 - Password amministratore server
 - Password
 - Piano tariffario: per iniziare, usare il valore predefinito S0
 - Regole di confronto: solo se è stato scelto il database vuoto

        ![new database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-4.png)

6.  Fare clic su **Crea**. Nell'area di notifica si noterà che è stata avviata la distribuzione.

     ![nuovo database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-5.png)

7. Attendere il completamento della distribuzione prima di procedere al passaggio successivo.

     ![nuovo database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-6.png)

<!---HONumber=AcomDC_0427_2016-->