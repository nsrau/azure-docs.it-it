Il listener del gruppo di disponibilità è un nome di rete e indirizzo IP sul quale è in ascolto il gruppo di disponibilità di SQL Server. Per creare il listener del gruppo di disponibilità, seguire questa procedura:

1. <a name="getnet"></a>Ottenere il nome della risorsa della rete del cluster.

    a. Usare RDP per connettersi alla macchina virtuale di Azure che ospita la replica primaria. 

    b. Aprire Gestione cluster di failover.

    c. Selezionare il nodo **Reti** e annotare il nome di rete del cluster. Usare questo nome nella variabile `$ClusterNetworkName` nello script di PowerShell. Nell'immagine seguente il nome della rete di cluster è **Cluster Network 1**:

   ![Nome rete di cluster](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

2. <a name="addcap"></a>Aggiungere il punto di accesso client.  
    Il punto di accesso client è il nome della rete che le applicazioni useranno per connettersi ai database nel gruppo di disponibilità. Creare il punto di accesso client in Gestione cluster di failover.

    a. Espandere il nome di cluster, quindi fare clic su **Ruoli**.

    b. Nel pannello **Ruoli** fare clic con il pulsante destro del mouse sul nome del gruppo di disponibilità e quindi scegliere **Aggiungi risorsa** > **Punto di accesso client**.

   ![Punto di accesso client](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. Nella casella **Nome** creare un nome per il nuovo listener. 
   Il nome del nuovo listener è il nome della rete che le applicazioni useranno per connettersi ai database nel gruppo di disponibilità di SQL Server.
   
    d. Per completare la creazione del listener, fare clic su **Avanti** due volte e quindi su **Fine**. Non portare il listener o la risorsa in linea a questo punto.

3. <a name="congroup"></a>Configurare la risorsa IP per il gruppo di disponibilità.

    a. Scegliere la scheda **Risorse** e quindi espandere il punto di accesso client creato.  
    Il punto di accesso client è offline.

   ![Punto di accesso client](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Fare clic con il pulsante destro del mouse sulla risorsa IP e quindi scegliere Proprietà. Annotare il nome dell'indirizzo IP e usarlo nella variabile `$IPResourceName` nello script di PowerShell.

    c. In **Indirizzo IP** fare clic su **Indirizzo IP statico**. Impostare l'indirizzo IP sullo stesso indirizzo usato quando è stato impostato l'indirizzo del servizio di bilanciamento del carico nel portale di Azure.

   ![Risorsa IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------Questa opzione non è disponibile nel server 2016
    1. Disabilitare NetBIOS per questo indirizzo e fare clic su **OK**. Ripetere questo passaggio per ogni risorsa IP se la soluzione si estende su più reti virtuali di Azure. 
    ------------------------->

4. <a name = "dependencyGroup"></a>Rendere la risorsa del gruppo di disponibilità di SQL Server dipendente dal punto di accesso client.

    a. In Gestione cluster di failover fare clic su **Ruoli** e quindi sul gruppo di disponibilità.

    b. Nella scheda **Risorse** fare clic con il pulsante destro del mouse sul gruppo di disponibilità in **Altre risorse** e quindi scegliere **Proprietà**. 

    c. Nella scheda relativa alle dipendenze aggiungere il nome della risorsa del punto di accesso client (listener).

   ![Risorsa IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Fare clic su **OK**.

5. <a name="listname"></a>Rendere la risorsa del punto di accesso client dipendente dall'indirizzo IP.

    a. In Gestione cluster di failover fare clic su **Ruoli** e quindi sul gruppo di disponibilità. 

    b. Nella scheda **Risorse** fare clic con il pulsante destro del mouse sulla risorsa del punto di accesso client in **Nome server** e quindi scegliere **Proprietà**. 

   ![Risorsa IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Selezionare la scheda **Dipendenze** . Verificare che l'indirizzo IP sia una dipendenza. In caso contrario, impostare una dipendenza sull'indirizzo IP. Se sono presenti più risorse elencate, verificare che gli indirizzi abbiano le dipendenze OR, e non quelle AND. Fare clic su **OK**. 

   ![Risorsa IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    d. Fare clic con il pulsante destro del mouse sul nome del listener e quindi scegliere **Porta online**. 

    >[!TIP]
    >È possibile confermare che le dipendenze sono state configurate correttamente. In Gestione cluster di failover passare a Ruoli, fare clic con il pulsante destro del mouse sul gruppo di disponibilità, scegliere **Altre azioni** e infine fare clic su **Visualizza rapporto dipendenze**. Quando le dipendenze sono configurate correttamente, il gruppo di disponibilità dipende dal nome della rete e il nome della rete dipende dall'indirizzo IP. 


6. <a name="setparam"></a>Impostare i parametri del cluster in PowerShell.
    
    a. Copiare lo script di PowerShell seguente in una delle istanze di SQL Server. Aggiornare le variabili per l'ambiente.     
    
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>
    
    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

    b. Impostare i parametri del cluster eseguendo lo script di PowerShell in uno dei nodi del cluster.  

    > [!NOTE]
    > Se le istanze di SQL Server sono in aree separate, è necessario eseguire lo script di PowerShell due volte. La prima volta usare i parametri `$ILBIP` e `$ProbePort` della prima area. La seconda volta usare i parametri `$ILBIP` e `$ProbePort` della seconda area. Il nome della rete del cluster e il nome della risorsa IP del cluster coincidono. 
