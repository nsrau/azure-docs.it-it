---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 276ddf0a70fa450451cd3ddc78c7610c4ab1edc1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165809"
---
Il listener del gruppo di disponibilità è un nome di rete e indirizzo IP sul quale è in ascolto il gruppo di disponibilità di SQL Server. Per creare il listener del gruppo di disponibilità, seguire questa procedura:

1. <a name="getnet"></a>Ottenere il nome della risorsa della rete del cluster.

    a. Usare RDP per connettersi alla macchina virtuale di Azure che ospita la replica primaria. 

    b. Aprire Gestione cluster di failover.

    c. Selezionare il nodo **Reti** e annotare il nome di rete del cluster. Usare questo nome nella variabile `$ClusterNetworkName` nello script di PowerShell. Nell'immagine seguente il nome della rete di cluster è **Cluster Network 1**:

   ![Nome rete di cluster](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Aggiungere il punto di accesso client.  
    Il punto di accesso client è il nome della rete che le applicazioni useranno per connettersi ai database nel gruppo di disponibilità. Creare il punto di accesso client in Gestione cluster di failover.

    a. Espandere il nome di cluster, quindi fare clic su **Ruoli**.

    b. Nel pannello **Ruoli** fare clic con il pulsante destro del mouse sul nome del gruppo di disponibilità e quindi scegliere **Aggiungi risorsa** > **Punto di accesso client**.

   ![Punto di accesso client](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. Nella casella **Nome** creare un nome per il nuovo listener. 
   Il nome del nuovo listener è il nome della rete che le applicazioni useranno per connettersi ai database nel gruppo di disponibilità di SQL Server.

    d. Per completare la creazione del listener, fare clic su **Avanti** due volte e quindi su **Fine**. Non portare il listener o la risorsa in linea a questo punto.

1. Portare offline il ruolo del cluster del gruppo di disponibilità. In **Gestione cluster di failover** in **Ruoli** fare clic con il pulsante destro del mouse sul ruolo e scegliere **Arresta ruolo**.

1. <a name="congroup"></a>Configurare la risorsa IP per il gruppo di disponibilità.

    a. Scegliere la scheda **Risorse** e quindi espandere il punto di accesso client creato.  
    Il punto di accesso client è offline.

   ![Punto di accesso client](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Fare clic con il pulsante destro del mouse sulla risorsa IP e quindi scegliere Proprietà. Annotare il nome dell'indirizzo IP e usarlo nella variabile `$IPResourceName` nello script di PowerShell.

    c. In **Indirizzo IP** fare clic su **Indirizzo IP statico**. Impostare l'indirizzo IP sullo stesso indirizzo usato quando è stato impostato l'indirizzo del servizio di bilanciamento del carico nel portale di Azure.

   ![Risorsa IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Rendere la risorsa del gruppo di disponibilità di SQL Server dipendente dal punto di accesso client.

    a. In Gestione cluster di failover fare clic su **Ruoli** e quindi sul gruppo di disponibilità.

    b. Nella scheda **Risorse** fare clic con il pulsante destro del mouse sul gruppo di disponibilità in **Altre risorse** e quindi scegliere **Proprietà**. 

    c. Nella scheda relativa alle dipendenze aggiungere il nome della risorsa del punto di accesso client (listener).

   ![Risorsa IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Fare clic su **OK**.

1. <a name="listname"></a>Rendere la risorsa del punto di accesso client dipendente dall'indirizzo IP.

    a. In Gestione cluster di failover fare clic su **Ruoli** e quindi sul gruppo di disponibilità. 

    b. Nella scheda **Risorse** fare clic con il pulsante destro del mouse sulla risorsa del punto di accesso client in **Nome server** e quindi scegliere **Proprietà**. 

   ![Risorsa IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Selezionare la scheda **Dipendenze** . Verificare che l'indirizzo IP sia una dipendenza. In caso contrario, impostare una dipendenza sull'indirizzo IP. Se sono presenti più risorse elencate, verificare che gli indirizzi abbiano le dipendenze OR, e non quelle AND. Fare clic su **OK**. 

   ![Risorsa IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >È possibile confermare che le dipendenze sono state configurate correttamente. In Gestione cluster di failover passare a Ruoli, fare clic con il pulsante destro del mouse sul gruppo di disponibilità, scegliere **Altre azioni** e infine fare clic su **Visualizza rapporto dipendenze**. Quando le dipendenze sono configurate correttamente, il gruppo di disponibilità dipende dal nome della rete e il nome della rete dipende dall'indirizzo IP. 


1. <a name="setparam"></a>Impostare i parametri del cluster in PowerShell.

   a. Copiare lo script di PowerShell seguente in una delle istanze di SQL Server. Aggiornare le variabili per l'ambiente.

   - `$ListenerILBIP` è l'indirizzo IP creato nel bilanciamento del carico di Azure per il listener del gruppo di disponibilità.
    
   - `$ListenerProbePort` è la porta configurata nel bilanciamento del carico di Azure per il listener del gruppo di disponibilità.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Impostare i parametri del cluster eseguendo lo script di PowerShell in uno dei nodi del cluster.  

   > [!NOTE]
   > Se le istanze di SQL Server sono in aree separate, è necessario eseguire lo script di PowerShell due volte. La prima volta usare i parametri `$ListenerILBIP` e `$ListenerProbePort` della prima area. La seconda volta usare i parametri `$ListenerILBIP` e `$ListenerProbePort` della seconda area. Il nome della rete e il nome della risorsa IP del cluster sono inoltre diversi per ciascuna regione.

1. Portare online il ruolo del cluster del gruppo di disponibilità. In **Gestione cluster di failover** in **Ruoli** fare clic con il pulsante destro del mouse sul ruolo e scegliere **Avvia ruolo**.

Se necessario, ripetere i passaggi precedenti per impostare i parametri del cluster per l'indirizzo IP del cluster WSFC.

1. Ottenere il nome dell'indirizzo IP del cluster WSFC. In **Gestione cluster di failover** in **Risorse principali del cluster** individuare **Nome server**.

1. Fare clic con il pulsante destro del mouse su **Indirizzo IP** e scegliere **Proprietà**.

1. Copiare il **nome** dell'indirizzo IP. Potrebbe essere `Cluster IP Address`. 

1. <a name="setwsfcparam"></a>Impostare i parametri del cluster in PowerShell.
  
   a. Copiare lo script di PowerShell seguente in una delle istanze di SQL Server. Aggiornare le variabili per l'ambiente.

   - `$ClusterCoreIP` è l'indirizzo IP creato nel bilanciamento del carico di Azure per la risorsa cluster principale WSFC. È differente dall'indirizzo IP per il listener del gruppo di disponibilità.

   - `$ClusterProbePort` è la porta configurata nel bilanciamento del carico di Azure per il probe di integrità WSFC. È differente dall'indirizzo IP per il probe del gruppo di disponibilità.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Impostare i parametri del cluster eseguendo lo script di PowerShell in uno dei nodi del cluster.  

>[!WARNING]
>La porta del probe di integrità del listener del gruppo di disponibilità deve essere diversa dalla porta del probe di integrità dell'indirizzo IP principale del cluster. In questi esempi, la porta del listener è 59999 e l'indirizzo IP principale del cluster è 58888. Entrambe le porte richiedono una regola di assenso del traffico in ingresso del firewall.