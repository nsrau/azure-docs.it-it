---
title: "Creare un listener per i gruppi di disponibilità di SQL Server: Macchine virtuali di Azure | Microsoft Docs"
description: "Istruzioni dettagliate per creare un listener per un gruppo di disponibilità AlwaysOn per SQL Server in macchine virtuali di Azure"
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/28/2016
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 407b189af12116d633ed505facf4bcfde9be5822
ms.openlocfilehash: 6a37e9e786a4e399c49cb77758a23793790888c9


---
# <a name="configure-an-internal-load-balancer-for-an-always-on-availability-group-in-azure"></a>Configurare un servizio di bilanciamento del carico interno per un gruppo di disponibilità AlwaysOn in Azure
Questo argomento illustra come creare un servizio di bilanciamento del carico interno per un gruppo di disponibilità AlwaysOn di SQL Server in macchine virtuali di Azure in esecuzione nel modello Resource Manager. Un gruppo di disponibilità richiede un servizio di bilanciamento del carico quando le istanze di SQL Server sono in macchine virtuali di Azure. Il servizio di bilanciamento del carico archivia l'indirizzo IP per il listener del gruppo di disponibilità. Se un gruppo di disponibilità si estende su più aree, è necessario un servizio di bilanciamento del carico per ogni area.

Per completare questa attività, è necessario un gruppo di disponibilità di SQL Server distribuito su macchine virtuali di Azure nel modello di Resource Manager. Entrambe le macchine virtuali di SQL Server devono appartenere allo stesso set di disponibilità. È possibile usare il [modello Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) per creare automaticamente il gruppo di disponibilità in Azure Resource Manager. Questo modello crea automaticamente il servizio di bilanciamento del carico interno. 

Se si preferisce, è possibile [configurare manualmente un gruppo di disponibilità](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Per questo argomento è necessario che i gruppi di disponibilità siano già configurati.  

Gli argomenti correlati includono:

* [Configurare i gruppi di disponibilità AlwaysOn in una VM di Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Configurare una connessione da VNet a VNet tramite Azure Resource Manager e PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Completando questo documento si creerà e configurerà un servizio di bilanciamento del carico nel portale di Azure. Al termine, si configurerà il cluster per usare l'indirizzo IP dal servizio di bilanciamento del carico per il listener del gruppo di disponibilità.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Creare e configurare il servizio di bilanciamento del carico nel portale di Azure
In questa parte dell'attività si eseguiranno questi passaggi nel portale di Azure:

1. Creare il servizio di bilanciamento del carico e configurare l'indirizzo IP
2. Configurare il pool back-end
3. Creare il probe 
4. Impostare le regole di bilanciamento del carico

> [!NOTE]
> Se le istanze di SQL Server sono in aree e gruppi di risorse diversi, questi passaggi dovranno essere eseguiti due volte, una in ogni gruppo di risorse.
> 
> 

### <a name="1-create-the-load-balancer-and-configure-the-ip-address"></a>1. Creare il servizio di bilanciamento del carico e configurare l'indirizzo IP
Il primo passaggio consiste nel creare il servizio di bilanciamento del carico. Nel portale di Azure aprire il gruppo di risorse contenente le macchine virtuali di SQL Server. Nel gruppo di risorse fare clic su **Aggiungi**.

1. Cercare **servizio di bilanciamento del carico**. Nei risultati della ricerca selezionare **Servizio di bilanciamento del carico**, pubblicato da **Microsoft**.
1. Nel pannello **Servizio di bilanciamento del carico** fare clic su**Crea**.
1. In **Crea servizio di bilanciamento del carico** configurare il servizio di bilanciamento del carico come segue:

   | Impostazione | Valore |
   | --- | --- |
   | **Nome** |Nome che rappresenta il servizio di bilanciamento del carico. Ad esempio **sqlLB**. |
   | **Tipo** |**Interno** |
   | **Rete virtuale** |Scegliere la rete virtuale in cui si trovano le istanze di SQL Server. |
   | **Subnet** |Scegliere la subnet in cui si trovano le istanze di SQL Server. |
   | **Assegnazione indirizzi IP** |**Statico** |
   | **Indirizzo IP privato** |Specificare un indirizzo IP disponibile della subnet. che verrà usato quando si creerà un listener nel cluster. Più avanti in questo articolo si userà questo indirizzo per la variabile `$ILBIP` in uno script di PowerShell. |
   | **Sottoscrizione** |Se si hanno più sottoscrizioni, può essere visualizzato questo campo. Selezionare la sottoscrizione da associare a questa risorsa. In genere è la stessa sottoscrizione di tutte le risorse del gruppo di disponibilità. |
   | **Gruppo di risorse** |Scegliere il gruppo di risorse in cui si trovano le istanze di SQL Server. |
   | **Posizione** |Scegliere la località di Azure in cui si trovano le istanze di SQL Server. |

1. Fare clic su **Crea**. 

Azure crea il servizio di bilanciamento del carico. Il servizio di bilanciamento del carico appartiene a una rete, a una subnet, a un gruppo di risorse e a una località specifici. Quando Azure ha terminato, verificare le impostazioni del servizio di bilanciamento del carico in Azure. 

### <a name="2-configure-the-backend-pool"></a>2. Configurare il pool back-end
Il passaggio successivo consiste nel creare un pool di indirizzi back-end. In Azure il pool di indirizzi back-end è chiamato *pool back-end*. In questo caso, il pool back-end è costituito dagli indirizzi delle due istanze di SQL Server nel gruppo di disponibilità. 

1. Nel gruppo di risorse fare clic sul servizio di bilanciamento del carico creato. 
1. In **Impostazioni** fare clic su **Pool back-end**.
1. In **Pool back-end** fare clic su **Aggiungi** per creare un pool di indirizzi back-end. 
1. In **Aggiungi pool back-end** under **Nome**.
1. In **Macchine virtuali** fare clic su **+ Aggiungi una macchina virtuale**. 
1. In **Scegli macchine virtuali** fare clic su **Scegli un set di disponibilità** e specificare il set di disponibilità a cui appartengono le macchine virtuali di SQL Server.
1. Dopo avere scelto il set di disponibilità, fare clic su **Scegliere le macchine virtuali**. Fare clic sulle due macchine virtuali che ospitano le istanze di SQL Server nel gruppo di disponibilità. Fare clic su **Seleziona**. 
1. Fare clic su **OK** per chiudere i pannelli per **Scegliere le macchine virtuali** e **Aggiungi pool back-end**. 

Azure Aggiorna le impostazioni per il pool di indirizzi back-end. Il set di disponibilità ora include un pool di due istanze di SQL Server.

### <a name="3-create-a-probe"></a>3. Creare un probe
Il passaggio successivo consiste nella creazione di un probe. Il probe definisce come Azure deve verificare quale istanza di SQL Server è attualmente proprietaria del listener del gruppo di disponibilità. Azure esamina il servizio in base all'indirizzo IP su una porta definita quando si crea il probe.

1. Nel pannello **Impostazioni** del servizio di bilanciamento del carico fare clic su **Probe integrità**. 
1. Nel pannello **Probe integrità** fare clic su **Aggiungi**.
1. Configurare il probe nel pannello **Aggiungi probe** . Usare i valori seguenti per configurare il probe.

   | Impostazione | Valore |
   | --- | --- |
   | **Nome** |Nome che rappresenta il probe. Ad esempio **SQLAlwaysOnEndPointProbe**. |
   | **Protocollo** |**TCP** |
   | **Porta** |È possibile usare qualsiasi porta disponibile. Ad esempio *59999*. |
   | **Interval** |*5* |
   | **Soglia non integra** |*2* |

1.  Fare clic su **OK**. 

> [!NOTE]
> Verificare che la porta specificata sia aperta nel firewall di entrambe le istanze di SQL Server. Per entrambi i server è necessaria una regola in ingresso per la porta TCP usata. Per altre informazioni, vedere [Aggiungere o modificare una regola del firewall](http://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

Azure crea il probe. Azure usa il probe per testare quale istanza di SQL Server ha il listener per il gruppo di disponibilità.

### <a name="4-set-the-load-balancing-rules"></a>4. Impostare le regole di bilanciamento del carico
Impostare le regole di bilanciamento del carico. Le regole di bilanciamento del carico determinano come il servizio di bilanciamento del carico instrada il traffico alle istanze di SQL Server. Per questo servizio di bilanciamento del carico abilitare Direct Server Return perché solo una per volta delle due istanze di SQL Server è proprietaria della risorsa listener del gruppo di disponibilità.

1. Nel pannello **Impostazioni** del servizio di bilanciamento del carico fare clic su **Regole di bilanciamento del carico**. 
1. Nel pannello **Regole di bilanciamento del carico** fare clic su **Aggiungi**.
1. Usare il pannello **Aggiungi regola di bilanciamento del carico** per configurare la regola di bilanciamento del carico. Usare le seguenti impostazioni: 

   | Impostazione | Valore |
   | --- | --- |
   | **Nome** |Nome che rappresenta la regola di bilanciamento del carico. Ad esempio **SQLAlwaysOnEndPointListener**. |
   | **Protocollo** |**TCP** |
   | **Porta** |*1433* |
   | **Porta back-end** |*1433*. Questo valore verrà ignorato perché questa regola usa **IP mobile (Direct Server Return)**. |
   | **Probe** |Usare il nome del probe creato per questo servizio di bilanciamento del carico. |
   | **Persistenza della sessione** |**Nessuno** |
   | **Timeout di inattività (minuti)** |*4* |
   | **IP mobile (Direct Server Return)** |**Enabled** |

   > [!NOTE]
   > Potrebbe essere necessario scorrere il pannello verso il basso per visualizzare tutte le impostazioni.
   > 

1. Fare clic su **OK**. 
1. Azure configura la regola di bilanciamento del carico. Ora il servizio di bilanciamento del carico è configurato per instradare il traffico all'istanza di SQL Server che ospita il listener per il gruppo di disponibilità. 

A questo punto il gruppo di risorse ha un servizio di bilanciamento del carico, che si connette a entrambi i computer SQL Server. Il servizio di bilanciamento del carico contiene anche un indirizzo IP per il listener del gruppo di disponibilità AlwaysOn di SQL Server in modo che entrambi i computer possano rispondere alle richieste per i gruppi di disponibilità.

> [!NOTE]
> Se le istanze di SQL Server sono in due aree separate, ripetere i passaggi nell'altra area. Ogni area richiede un servizio di bilanciamento del carico. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurare il cluster per usare l'indirizzo IP del servizio di bilanciamento del carico
Il passaggio successivo consiste nel configurare il listener nel cluster e nel portare il listener online. Seguire anche questa procedura: 

1. Creare il listener del gruppo di disponibilità nel cluster di failover 
2. Portare online il listener

### <a name="5-create-the-availability-group-listener-on-the-failover-cluster"></a>5. Creare il listener del gruppo di disponibilità nel cluster di failover
In questo passaggio si creerà manualmente il listener del gruppo di disponibilità in Gestione Cluster di Failover e SQL Server Management Studio (SSMS).

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

<!---------------------------
* Use RDP to connect to the Azure virtual machine that hosts the primary replica. 
* Open Failover Cluster Manager.
* Select the **Networks** node, and note the cluster network name. This name will be used in the `$ClusterNetworkName` variable in the PowerShell script.
* Expand the cluster name, and then click **Roles**.
* In the **Roles** pane, right-click the availability group name and then select **Add Resource** > **Client Access Point**.
* In the **Name** box, create a name for this new listener, then click **Next** twice, and then click **Finish**. Do not bring the listener or resource online at this point.
  
  > [!NOTE]
  > The name for the new listener is the network name that applications will use to connect to databases in the SQL Server availability group.
  > 
  > 
* Click the **Resources** tab, then expand the Client Access Point you just created. Right-click the IP resource and click properties. Note the name of the IP address. You will use this name in the `$IPResourceName` variable in the PowerShell script.
* Under **IP Address** click **Static IP Address** and set the static IP address to the same address that you used when you set the load balancer IP address on the Azure portal. Enable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
* On the cluster node that currently hosts the primary replica, open an elevated PowerShell ISE and paste the following commands into a new script.
  
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
  
        Import-Module FailoverClusters
  
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
* Update the variables and run the PowerShell script to configure the IP address and port for the new listener.
  
  > [!NOTE]
  > If your SQL Servers are in separate regions, you need to run the PowerShell script twice. The first time use the cluster network name, cluster IP resource name, and load balancer IP address from the first resource group. The second time use the cluster network name, cluster IP resource name, and load balancer IP address from the second resource group.
  > 
  > 

Now the cluster has an availability group listener resource.

### 2. Bring the listener online
With the availability group listener resource configured, you can bring the listener online so that applications can connect to databases in the availability group with the listener.

* Navigate back to Failover Cluster Manager. Expand **Roles** and then highlight your Availability Group. On the **Resources** tab, right-click the listener name and click **Properties**.
* Click the **Dependencies** tab. If there are multiple resources listed, verify that the IP addresses have OR, not AND, dependencies. Click **OK**.
* Right-click the listener name and click **Bring Online**.
* Once the listener is online, from the **Resources** tab, right-click the availability group and click **Properties**.
* Create a dependency on the listener name resource (not the IP address resources name). Click **OK**.
* Launch SQL Server Management Studio and connect to the primary replica.
* Navigate to **AlwaysOn High Availability** | **Availability Groups** | **Availability Group Listeners**. 
* You should now see the listener name that you created in Failover Cluster Manager. Right-click the listener name and click **Properties**.
* In the **Port** box, specify the port number for the availability group listener by using the $EndpointPort you used earlier (1433 was the default), then click **OK**.

------------------------------->

### <a name="verify-the-configuration-of-the-listener"></a>Verificare la configurazione del listener

Se le risorse del cluster e le dipendenze sono configurate correttamente, dovrebbe essere visualizzato il listener in SQL Server Management Studio. Per impostare la porta di attesa, procedere come segue:

1. Avviare SQL Server Management Studio e connettersi alla replica primaria.
2. Passare a **Disponibilità elevata AlwaysOn** | **Gruppi di disponibilità** | **Listener gruppo di disponibilità**. 
1. Viene visualizzato il nome del listener creato in Gestione Cluster di Failover. Fare clic con il pulsante destro del mouse sul nome del listener e quindi su **Proprietà**.
1. Nella casella **Porta** specificare il numero di porta per il listener del gruppo di disponibilità usando il valore di $EndpointPort usato in precedenza (l'impostazione predefinita era&1433;), quindi fare clic su **OK**.

Ora si ha un gruppo di disponibilità AlwaysOn di SQL Server in macchine virtuali di Azure in esecuzione in modalità Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Testare la connessione al listener
Per testare la connessione:

1. Usare RDP per connettersi a un'istanza di SQL Server che si trova nella stessa rete virtuale, ma non è proprietaria della replica. Può trattarsi dell'altra istanza di SQL Server nel cluster.
2. Usare l'utilità **sqlcmd** per testare la connessione. Lo script seguente, ad esempio, stabilisce una connessione **sqlcmd** alla replica primaria tramite il listener con l'autenticazione di Windows:
   
        sqlcmd -S <listenerName> -E

La connessione SQLCMD si connette automaticamente a qualsiasi istanza di SQL Server ospiti la replica primaria. 

## <a name="guidelines-and-limitations"></a>Linee guida e limitazioni
Tenere presente le linee guida seguenti per il listener del gruppo di disponibilità in Azure con il servizio di bilanciamento del carico interno:

* Per ogni servizio cloud è supportato un solo listener del gruppo di disponibilità interno, perché il listener è configurato per il servizio di bilanciamento del carico e c'è un solo servizio di bilanciamento del carico interno. Tuttavia, è possibile creare più listener esterni. 
* Con un servizio di bilanciamento del carico interno è possibile accedere al listener solo dalla stessa rete virtuale.


<!--HONumber=Jan17_HO2-->


