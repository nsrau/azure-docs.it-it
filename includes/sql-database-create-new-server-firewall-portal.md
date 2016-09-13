
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-08-01 , rickbyh.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md
articles/sql-database/sql-database-configure-firewall-settings

-->
## Creare un nuovo firewall a livello di server per SQL Azure

Usare la procedura seguente nel portale di Azure per creare una regola del firewall a livello di server che consente le connessioni da un singolo indirizzo IP, il computer client, o un intero intervallo di indirizzi IP a un server logico di database SQL.

1. Se non è ancora stato fatto, connettersi al [portale di Azure](http://portal.azure.com).
2. Nel pannello predefinito fare clic su **SQL Server**.

  	![Nuovo firewall del server](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-1.png)  

3. Nel pannello **SQL Server** fare clic sul server in cui creare la regola firewall.

 	![Nuovo firewall del server](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-2.png)  

4. Esaminare le proprietà del server.

 	![Nuovo firewall del server](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-3.png)  

5. Nel pannello **Impostazioni** fare clic su **Firewall**.

 	![Nuovo firewall del server](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-4.png)  

 	> [AZURE.NOTE] È anche possibile accedere al pannello **Impostazioni del firewall** a livello di server dalla barra degli strumenti del pannello **Database**.

6. Fare clic su **Aggiungi IP client** per consentire ad Azure di creare una regola per l'indirizzo IP client specificato.

      ![Nuovo firewall del server](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-5.png)  

7. Facoltativamente, per consentire l'accesso a un intervallo di indirizzi IP fare clic sull'indirizzo IP aggiunto per modificare l'indirizzo del firewall.

      ![Nuovo firewall del server](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-6.png)  

8. Fare clic su **Salva** per creare la regola firewall a livello di server.

     ![Nuovo firewall del server](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-7.png)  

	>[AZURE.IMPORTANT] L'indirizzo IP client potrebbe cambiare nel tempo. In questo caso, non sarà possibile accedere al server fino a quando non viene creata una nuova regola firewall. Per controllare l'indirizzo IP, è possibile usare [Bing](http://www.bing.com/search?q=my%20ip%20address). Aggiungere quindi un indirizzo IP singolo o un intervallo di indirizzi IP. Per altre informazioni, vedere la sezione relativa alla [gestione delle impostazioni del firewall](sql-database-configure-firewall-settings.md#manage-existing-server-level-firewall-rules-through-the-azure-portal).

<!---HONumber=AcomDC_0907_2016-->