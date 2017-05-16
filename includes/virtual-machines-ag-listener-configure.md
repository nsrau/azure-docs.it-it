Il listener del gruppo di disponibilità è un nome di rete e indirizzo IP sul quale è in ascolto il gruppo di disponibilità di SQL Server. Per creare il listener del gruppo di disponibilità seguire questa procedura:

1. [Ottenere il nome della risorsa di rete di cluster](#getnet).

1. [Aggiungere il punto di accesso client](#addcap).

1. [Configurare la risorsa IP per il gruppo di disponibilità](#congroup).

1. [Rendere la risorsa del gruppo di disponibilità di SQL Server dipendente dal punto di accesso client](#dependencyGroup).

1. [Rendere la risorsa del punto di accesso client dipendente dall'indirizzo IP](#listname).

1. [Impostare i parametri del cluster in PowerShell](#setparam).

Le sezioni seguenti illustrano istruzioni dettagliate per ciascuna operazione. 

#### <a name="getnet"></a>Ottenere il nome della risorsa di rete di cluster

1. Usare RDP per connettersi alla macchina virtuale di Azure che ospita la replica primaria. 

1. Aprire Gestione cluster di failover.

1. Selezionare il nodo **Reti** e annotare il nome di rete del cluster. Usare questo nome nella variabile `$ClusterNetworkName` nello script di PowerShell.

   Nell'immagine seguente, il nome della rete di cluster è **Cluster Network 1**:

   ![Nome rete di cluster](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

#### <a name="addcap"></a>Aggiungere il punto di accesso client

Il punto di accesso client è il nome della rete che le applicazioni useranno per connettersi ai database nel gruppo di disponibilità. Creare il punto di accesso client in Gestione cluster di failover. 

1. Espandere il nome di cluster, quindi fare clic su **Ruoli**.

1. Nel pannello **Ruoli** fare clic con il pulsante destro del mouse sul nome del gruppo di disponibilità e quindi scegliere **Aggiungi risorsa** > **Punto di accesso Client**.

   ![Punto di accesso client](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

1. Nella casella **Nome** creare un nome per il nuovo listener. 

   Il nome del nuovo listener è il nome della rete che le applicazioni useranno per connettersi ai database nel gruppo di disponibilità di SQL Server.
   
   Per completare la creazione del listener, fare clic su **Avanti** due volte e quindi su **Fine**. Non portare il listener o la risorsa in linea a questo punto.
   
#### <a name="congroup"></a>Configurare la risorsa IP per il gruppo di disponibilità

1. Scegliere la scheda **Risorse** , quindi espandere il punto di accesso client creato. Il punto di accesso client è offline.

   ![Punto di accesso client](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

1. Fare clic con il pulsante destro del mouse sulla risorsa IP e scegliere Proprietà. Prendere nota del nome dell'indirizzo IP. Usare questo nome nella variabile `$IPResourceName` nello script di PowerShell.

1. In **Indirizzo IP** fare clic su **Indirizzo IP statico**. Impostare l'indirizzo IP sullo stesso indirizzo usato quando è stato impostato l'indirizzo del servizio di bilanciamento del carico nel portale di Azure.

   ![Risorsa IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

<!-----------------------I don't see this option on server 2016
1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
------------------------->

#### <a name = "dependencyGroup"></a>Rendere la risorsa del gruppo di disponibilità di SQL Server dipendente dal punto di accesso client

1. In Gestione cluster di failover fare clic su **Ruoli**, quindi sul gruppo di disponibilità.

1. Nella scheda **Risorse** fare clic con il pulsante destro del mouse sul gruppo di disponibilità in **Nome server** e scegliere **Proprietà**. 

1. Nella scheda Dipendenze aggiungere la risorsa. Questa risorsa è il punto di accesso client. 

   ![Risorsa IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

1. Fare clic su **OK**.

#### <a name="listname"></a>Rendere la risorsa del punto di accesso client dipendente dall'indirizzo IP

1. In Gestione cluster di failover fare clic su **Ruoli**, quindi sul gruppo di disponibilità. 

1. Nella scheda **Risorse** fare clic con il pulsante destro del mouse sulla risorsa del punto di accesso client in **Nome server** e scegliere **Proprietà**. 

   ![Risorsa IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

1. Selezionare la scheda **Dipendenze** . Impostare una dipendenza sul nome della risorsa del listener. Se sono presenti più risorse elencate, verificare che gli indirizzi abbiano le dipendenze OR, e non quelle AND. Fare clic su **OK**. 

   ![Risorsa IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

1. Fare clic con il pulsante destro del mouse sul nome del listener e scegliere **Porta in linea**. 

#### <a name="setparam"></a>Impostare i parametri del cluster in PowerShell

1. Copiare lo script di PowerShell seguente in uno dei server SQL. Aggiornare le variabili per l'ambiente.     
   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

2. Impostare i parametri del cluster eseguendo lo script di PowerShell in uno dei nodi del cluster.  

> [!NOTE]
> Se le istanze di SQL Server sono in aree separate, è necessario eseguire lo script di PowerShell due volte. La prima volta usare i parametri `$ILBIP` e `$ProbePort` della prima area. La seconda volta usare i parametri `$ILBIP` e `$ProbePort` della seconda area. Il nome della rete di cluster e il nome della risorsa IP del cluster coincidono. 


