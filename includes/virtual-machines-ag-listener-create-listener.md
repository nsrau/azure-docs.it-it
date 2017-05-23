In questo passaggio si creerà manualmente il listener del gruppo di disponibilità in Gestione cluster di failover e SQL Server Management Studio.

1. Aprire Gestione cluster di failover dal nodo che ospita la replica primaria.

2. Selezionare il nodo **Reti** e quindi annotare il nome di rete del cluster. Questo nome viene usato nella variabile $ClusterNetworkName nello script di PowerShell.

3. Espandere il nome di cluster, quindi fare clic su **Ruoli**.

4. Nel pannello **Ruoli** fare clic con il pulsante destro del mouse sul nome del gruppo di disponibilità e quindi scegliere **Aggiungi risorsa** > **Punto di accesso client**.
   
    ![Aggiungere il punto di accesso client per il gruppo di disponibilità](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. Nella casella **Nome** creare un nome per il nuovo listener, fare clic su **Avanti** due volte, quindi fare clic su **Fine**.  
    Non portare il listener o la risorsa in linea a questo punto.

6. Scegliere la scheda **Risorse**, e quindi espandere il punto di accesso client appena creato. 
    Viene visualizzata la risorsa indirizzo IP per ogni rete di cluster nel cluster. Se si tratta di una soluzione solo per Azure, viene visualizzata solo una risorsa indirizzo IP.

7. Eseguire una di queste operazioni:
   
   * Per configurare una soluzione ibrida:
     
        a. Fare clic con il pulsante destro del mouse sulla risorsa indirizzo IP che corrisponde alla subnet locale e quindi scegliere **Proprietà**. Annotare il nome dell'indirizzo IP e il nome di rete.
   
        b. Selezionare **Indirizzo IP statico**, assegnare un indirizzo IP non usato e quindi fare clic su **OK**.
 
   * Per configurare una soluzione solo per Azure:

        a. Fare clic con il pulsante destro del mouse sulla risorsa indirizzo IP corrispondente alla subnet di Azure e quindi scegliere **Proprietà**.
       
       > [!NOTE]
       > Se il listener non riesce in un secondo momento a portarsi in linea a causa di un indirizzo IP in conflitto selezionato da DHCP, è possibile configurare un indirizzo IP statico valido in questa finestra Proprietà.
       > 
       > 

       b. Nella stessa finestra delle proprietà dell'**indirizzo IP** modificare il valore di **Nome indirizzo IP**.  
        Questo nome viene usato nella variabile $IPResourceName dello script di PowerShell. Se la soluzione si estende in più reti virtuali di Azure, ripetere questo passaggio per ogni risorsa IP.

