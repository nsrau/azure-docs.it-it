1. In Gestione cluster di failover espandere **Ruoli** e quindi evidenziare il gruppo di disponibilità.  

2. Nella scheda **Risorse** fare clic con il pulsante destro del mouse sul nome del listener e scegliere **Proprietà**.

3. Selezionare la scheda **Dipendenze** . Se sono presenti più risorse elencate, verificare che gli indirizzi IP abbiano dipendenze OR anziché AND.  

4. Fare clic su **OK**.

5. Fare clic con il pulsante destro del mouse sul nome del listener e quindi scegliere **Porta online**.

6. Quando il listener è online, nella scheda **Risorse** fare clic con il pulsante destro del mouse sul gruppo di disponibilità e quindi scegliere **Proprietà**.
   
    ![Configurare la risorsa del gruppo di disponibilità](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Creare una dipendenza nella risorsa nome del listener, non il nome della risorsa indirizzo IP, e quindi fare clic su **OK**.
   
    ![Aggiungere una dipendenza al nome del listener](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Avviare SQL Server Management Studio e connettersi alla replica primaria.

9. Passare a **Disponibilità elevata AlwaysOn** > **Gruppi di disponibilità** > **\<AvailabilityGroupName\>** > **Listener gruppo di disponibilità**.  
    Verrà visualizzato il nome del listener creato in Gestione cluster di failover.

10. Fare clic con il pulsante destro del mouse sul nome del listener e quindi su **Proprietà**.

11. Nella casella **Porta** specificare il numero di porta per il listener del gruppo di disponibilità usando il valore di $EndpointPort usato in precedenza e quindi fare clic su **OK**. In questa esercitazione 1433 era l'impostazione predefinita.

