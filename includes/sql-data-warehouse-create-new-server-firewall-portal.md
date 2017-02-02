### <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Creare una regola del firewall a livello di server nel portale di Azure

1. Nel pannello di SQL Server, in Impostazioni, fare clic su **Firewall** per aprire il pannello del firewall per SQL Server.

    ![Firewall di SQL server](./media/sql-data-warehouse-server-firewall/sql-server-firewall.png)
    
2. Verificare la presenza dell'indirizzo IP del client e che questo sia l'indirizzo IP Internet che usa un browser di propria scelta (chiedere "qual è l'indirizzo IP in uso"). Talvolta non corrispondono per varie ragioni.

    ![Indirizzo IP in uso](../articles/sql-database/media/sql-database-get-started/your-ip-address.png)

3. Supponendo che gli indirizzi IP corrispondano, fare clic su **Aggiungi IP client** sulla barra degli strumenti.

    ![Aggiungi IP client](./media/sql-data-warehouse-server-firewall/add-client-ip.png)

    > [!NOTE]
    > È possibile aprire il firewall nel server SQL (server logico) a un singolo indirizzo IP o a un intero intervallo di indirizzi. L'apertura del firewall consente agli amministratori SQL e agli utenti di accedere a qualsiasi database nel server per cui hanno credenziali valide.
    >

4. Fare clic su **Salva** sulla barra degli strumenti per salvare questa regola del firewall a livello di server e quindi fare clic su **OK**.

    ![Aggiungi IP client](../articles/sql-database/media/sql-database-get-started/save-firewall-rule.png)

<!--HONumber=Jan17_HO3-->


