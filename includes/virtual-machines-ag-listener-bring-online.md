1. Tornare a gestione Cluster di Failover. Espandere**Ruoli**ed evidenziare il gruppo di disponibilità. Nella scheda**Risorse**, fare clic con il pulsante destro del mouse sul nome del listener e scegliere Proprietà.

1. Selezionare la scheda **Dipendenze**. Se sono presenti più risorse elencate, verificare che gli indirizzi abbiano le dipendenze OR, e non quelle AND. Fare clic su **OK**.

1. Fare clic con il pulsante destro del mouse sul nome del listener e scegliere**Porta in linea**.

1. Una volta che il listener è online, dalla scheda**Risorse**, fare clic con il tasto destro del mouse sul gruppo di disponibilità e fare clic su**Proprietà**.

	![Configurare la risorsa del gruppo di disponibilità](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

1. Creare una dipendenza sulla risorsa nome del listener (non nome risorse indirizzo IP). Fare clic su **OK**.

	![Aggiungere dipendenza al nome del Listener](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

1. Aprire **SQL Server Management Studio** e connettersi alla replica primaria.

1. Passare a **Disponibilità elevata AlwaysOn** | **Gruppi di disponibilità** | **<AvailabilityGroupName>** | **Listener del gruppo di disponibilità**.

3. Viene visualizzato il nome del listener creato in Gestione Cluster di Failover. Fare clic con il pulsante destro del mouse sul nome del listener e quindi su **Proprietà**.

1. Nella casella **Porta** specificare il numero di porta per il listener del gruppo di disponibilità usando il valore di $EndpointPort usato in precedenza (in questa esercitazione, 1433 era l'impostazione predefinita), quindi fare clic su **OK**.

<!---HONumber=Oct15_HO3-->