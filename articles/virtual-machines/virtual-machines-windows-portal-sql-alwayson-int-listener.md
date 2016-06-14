<properties
   pageTitle="Creare un listener per un gruppo di disponibilità AlwaysOn per SQL Server in macchine virtuali di Azure"
   description="Istruzioni dettagliate per creare un listener per un gruppo di disponibilità AlwaysOn per SQL Server in macchine virtuali di Azure"
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="04/17/2016"
   ms.author="MikeRayMSFT"/>

# Configurare un servizio di bilanciamento del carico interno per un gruppo di disponibilità AlwaysOn in Azure

Questo argomento illustra come creare un servizio di bilanciamento del carico interno per un gruppo di disponibilità AlwaysOn di SQL Server in macchine virtuali di Azure in esecuzione nel modello Resource Manager. Un gruppo di disponibilità AlwaysOn richiede un servizio di bilanciamento del carico quando le istanze di SQL Server sono in macchine virtuali di Azure. Il servizio di bilanciamento del carico archivia l'indirizzo IP per il listener del gruppo di disponibilità. Se un gruppo di disponibilità si estende su più aree, è necessario un servizio di bilanciamento del carico per ogni area.

Per completare questa attività, è necessario un gruppo di disponibilità AlwaysOn di SQL Server distribuito in macchine virtuali di Azure nel modello Resource Manager. Entrambe le macchine virtuali di SQL Server devono appartenere allo stesso set di disponibilità. È possibile usare il [modello Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) per creare automaticamente il gruppo di disponibilità AlwaysOn in Azure Resource Manager. Questo modello crea automaticamente il servizio di bilanciamento del carico interno.

Se si preferisce, è possibile [configurare manualmente un gruppo di disponibilità AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Per questo argomento è necessario che i gruppi di disponibilità siano già configurati.

Gli argomenti correlati includono:

 - [Configurare i gruppi di disponibilità AlwaysOn nelle VM di Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
 
 - [Configurare una connessione da VNet a VNet tramite Azure Resource Manager e PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## Passaggi

Completando questo documento si creerà e configurerà un servizio di bilanciamento del carico nel portale di Azure. Al termine, si configurerà il cluster per usare l'indirizzo IP dal servizio di bilanciamento del carico per il listener del gruppo di disponibilità AlwaysOn.

## Creare e configurare il servizio di bilanciamento del carico nel portale di Azure

In questa parte dell'attività si eseguiranno questi passaggi nel portale di Azure:

1. Creare il servizio di bilanciamento del carico e configurare l'indirizzo IP

1. Configurare il pool back-end

1. Creare il probe

1. Impostare le regole di bilanciamento del carico

>[AZURE.NOTE] Se le istanze di SQL Server sono in aree e gruppi di risorse diversi, questi passaggi dovranno essere eseguiti due volte, una in ogni gruppo di risorse.

## 1\. Creare il servizio di bilanciamento del carico e configurare l'indirizzo IP

Il primo passaggio consiste nel creare il servizio di bilanciamento del carico. Nel portale di Azure aprire il gruppo di risorse contenente le macchine virtuali di SQL Server. Nel gruppo di risorse fare clic su **Aggiungi**.

- Cercare **servizio di bilanciamento del carico**. Nei risultati della ricerca selezionare **Servizio di bilanciamento del carico**, pubblicato da **Microsoft**.

- Nel pannello **Servizio di bilanciamento del carico** fare clic su **Crea**.

- In **Crea servizio di bilanciamento del carico** configurare il servizio di bilanciamento del carico come segue:

| Impostazione | Valore |
| ----- | ----- |
| **Nome** | Nome che rappresenta il servizio di bilanciamento del carico. Ad esempio **sqlLB**. |
| **Schema** | **Interno** |
| **Rete virtuale** | Scegliere la rete virtuale in cui si trovano le istanze di SQL Server. |
| **Subnet** | Scegliere la subnet in cui si trovano le istanze di SQL Server. |
| **Sottoscrizione** | Se si hanno più sottoscrizioni, può essere visualizzato questo campo. Selezionare la sottoscrizione da associare a questa risorsa. In genere è la stessa sottoscrizione di tutte le risorse del gruppo di disponibilità. |
| **Gruppo di risorse** | Scegliere il gruppo di risorse in cui si trovano le istanze di SQL Server. | 
| **Posizione** | Scegliere la località di Azure in cui si trovano le istanze di SQL Server. |

- Fare clic su **Create**. 

Azure crea il servizio di bilanciamento del carico configurato sopra. Il servizio di bilanciamento del carico appartiene a una rete, a una subnet, a un gruppo di risorse e a una località specifici. Quando Azure ha terminato, verificare le impostazioni del servizio di bilanciamento del carico in Azure.

Configurare ora l'indirizzo IP del servizio di bilanciamento del carico.

- Nel pannello **Impostazioni** del servizio di bilanciamento del carico fare clic su **Indirizzo IP**. Il pannello **Indirizzo IP** indica che si tratta di un servizio di bilanciamento del carico privato nella stessa rete virtuale delle istanze di SQL Server. 

- Usare le seguenti impostazioni:

| Impostazione | Valore |
| ----- | ----- |
| **Subnet** | Scegliere la subnet in cui si trovano le istanze di SQL Server. |
| **Assegnazione** | **Statico** |
| **Indirizzo IP** | Digitare un indirizzo IP virtuale non usato dalla subnet. |

- Salvare le impostazioni.

Il servizio di bilanciamento del carico ora ha un indirizzo IP. Registrare tale indirizzo IP, che verrà usato quando si creerà un listener nel cluster. Più avanti in questo articolo si userà questo indirizzo per la variabile `$ILBIP` in uno script di PowerShell.



## 2\. Configurare il pool back-end

Il passaggio successivo consiste nel creare un pool di indirizzi back-end. In Azure il pool di indirizzi back-end è chiamato *pool back-end*. In questo caso, il pool back-end è costituito dagli indirizzi delle due istanze di SQL Server nel gruppo di disponibilità.

- Nel gruppo di risorse fare clic sul servizio di bilanciamento del carico creato. 

- In **Impostazioni** fare clic su **Pool back-end**.

- In **Pool di indirizzi back-end** fare clic su **Aggiungi** per creare un pool di indirizzi back-end.

- In **Aggiungi pool back-end** digitare un nome per il pool back-end in **Nome**.

- In **Macchine virtuali** fare clic su **+ Aggiungi una macchina virtuale**.

- In **Scegliere le macchine virtuali** fare clic su **Scegliere un set di disponibilità** e specificare il set di disponibilità a cui appartengono le macchine virtuali di SQL Server.

- Dopo avere scelto il set di disponibilità, fare clic su **Scegliere le macchine virtuali**. Fare clic sulle due macchine virtuali che ospitano le istanze di SQL Server nel gruppo di disponibilità. Fare clic su **Seleziona**.

- Fare clic su **OK** per chiudere i pannelli per **Scegliere le macchine virtuali** e **Aggiungi pool back-end**.

Azure Aggiorna le impostazioni per il pool di indirizzi back-end. Il set di disponibilità ora include un pool di due istanze di SQL Server.

## 3\. Creare un probe

Il passaggio successivo consiste nella creazione di un probe. Il probe definisce come Azure deve verificare quale istanza di SQL Server è attualmente proprietaria del listener del gruppo di disponibilità. Azure esaminerà il servizio in base all'indirizzo IP su una porta definita quando si crea il probe.

- Nel pannello **Impostazioni** del servizio di bilanciamento del carico fare clic su **Probe**. 

- Nel pannello **Probe** fare clic su **Aggiungi**.

- Configurare il probe nel pannello **Aggiungi probe**. Usare i valori seguenti per configurare il probe.

| Impostazione | Valore |
| ----- | ----- |
| **Nome** | Nome che rappresenta il probe. Ad esempio **SQLAlwaysOnEndPointProbe**. |
| **Protocollo** | **TCP** |
| **Porta** | È possibile usare qualsiasi porta disponibile. Ad esempio *59999*. |
| **Interval** | *5* | 
| **Soglia non integra** | *2* | 

- Fare clic su **OK**. 

>[AZURE.NOTE] Verificare che la porta specificata sia aperta nel firewall di entrambe le istanze di SQL Server. Per entrambi i server è necessaria una regola in ingresso per la porta TCP usata. Per altre informazioni, vedere [Aggiungere o modificare una regola del firewall](http://technet.microsoft.com/library/cc753558.aspx).

Azure crea il probe. Azure userà il probe per testare quale istanza di SQL Server ha il listener per il gruppo di disponibilità.

## 4\. Impostare le regole di bilanciamento del carico

Impostare le regole di bilanciamento del carico. Le regole di bilanciamento del carico determinano come il servizio di bilanciamento del carico instrada il traffico alle istanze di SQL Server. Per questo servizio di bilanciamento del carico si abiliterà Direct Server Return perché solo una per volta delle due istanze di SQL Server sarà proprietaria della risorsa listener del gruppo di disponibilità.

- Nel pannello **Impostazioni** del servizio di bilanciamento del carico fare clic su **Regole di bilanciamento del carico**. 

- Nel pannello **Regole di bilanciamento del carico** fare clic su **Aggiungi**.

- Usare il pannello **Aggiungi regola di bilanciamento del carico** per configurare la regola di bilanciamento del carico. Usare le seguenti impostazioni:

| Impostazione | Valore |
| ----- | ----- |
| **Nome** | Nome che rappresenta la regola di bilanciamento del carico. Ad esempio **SQLAlwaysOnEndPointListener**. |
| **Protocollo** | **TCP** |
| **Porta** | *1433* |
| **Porta back-end** | *1433*. Si noti che sarà disabilitata perché questa regola usa **IP mobile (Direct Server Return)**. |
| **Probe** | Usare il nome del probe creato per questo servizio di bilanciamento del carico. |
| **Salvataggio permanente sessione** | **Nessuno** | 
| **Timeout di inattività (minuti)** | *4* | 
| **IP mobile (Direct Server Return)** | **Enabled** | 

 >[AZURE.NOTE] Potrebbe essere necessario scorrere il pannello verso il basso per visualizzare tutte le impostazioni.

- Fare clic su **OK**. 

- Azure configura la regola di bilanciamento del carico. Ora il servizio di bilanciamento del carico è configurato per instradare il traffico all'istanza di SQL Server che ospita il listener per il gruppo di disponibilità.

A questo punto il gruppo di risorse ha un servizio di bilanciamento del carico, che si connette a entrambi i computer SQL Server. Il servizio di bilanciamento del carico contiene anche un indirizzo IP per il listener del gruppo di disponibilità AlwaysOn di SQL Server in modo che entrambi i computer possano rispondere alle richieste per i gruppi di disponibilità.

>[AZURE.NOTE] Se le istanze di SQL Server sono in due aree separate, ripetere i passaggi nell'altra area. Ogni area richiede un servizio di bilanciamento del carico.

## Configurare il cluster per usare l'indirizzo IP del servizio di bilanciamento del carico 

Il passaggio successivo consiste nel configurare il listener nel cluster e nel portare il listener online. A tale scopo, eseguire queste operazioni:

1. Creare il listener del gruppo di disponibilità nel cluster di failover 

1. Portare online il listener

## 1\. Creare il listener del gruppo di disponibilità nel cluster di failover

In questo passaggio si creerà manualmente il listener del gruppo di disponibilità in Gestione Cluster di Failover e SQL Server Management Studio (SSMS).

- Usare RDP per connettersi alla macchina virtuale di Azure che ospita la replica primaria. 

- Aprire Gestione cluster di failover.

- Selezionare il nodo**Reti**e annotare il nome di rete del cluster. Questo nome verrà usato nella variabile `$ClusterNetworkName` nello script di PowerShell.

- Espandere il nome di cluster, quindi fare clic su **Ruoli**.

- Nel riquadro**Ruoli**, fare clic con il pulsante destro del mouse sul nome del gruppo di disponibilità e quindi scegliere**Aggiungi risorsa** > **Punto di accesso Client**.

- Nella casella**Nome**creare un nome per il nuovo listener, quindi fare doppio clic su **Avanti**, poi fare clic su**Fine**. Non portare il listener o la risorsa in linea a questo punto.

 >[AZURE.NOTE] Il nome del nuovo listener è il nome della rete che le applicazioni useranno per connettersi ai database nel gruppo di disponibilità di SQL Server.

- Scegliere la scheda**Risorse**, quindi espandere il punto di accesso Client appena creato. Fare clic con il pulsante destro del mouse sulla risorsa IP e scegliere Proprietà. Prendere nota del nome dell'indirizzo IP. Si userà questo nome nella variabile `$IPResourceName` nello script di PowerShell.

- In **Indirizzo IP** fare clic su **Indirizzo IP statico** e impostare l'indirizzo IP statico sullo stesso indirizzo usato quando si è impostato l'indirizzo IP del servizio di bilanciamento del carico nel portale di Azure. Abilitare NetBIOS per questo indirizzo e fare clic su **OK**. Ripetere questo passaggio per ogni risorsa IP se la soluzione si estende su più reti virtuali di Azure.

- Nel nodo del cluster che attualmente ospita la replica primaria, aprire un'istanza di PowerShell ISE con privilegi elevati e incollare i comandi seguenti in un nuovo script.

        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    
        Import-Module FailoverClusters
    
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

- Aggiornare le variabili ed eseguire lo script di PowerShell per configurare l'indirizzo IP e la porta per il nuovo listener.

 >[AZURE.NOTE] Se le istanze di SQL Server sono in aree separate, è necessario eseguire lo script di PowerShell due volte. La prima volta usare il nome della rete di cluster, il nome della risorsa IP cluster e l'indirizzo IP del servizio di bilanciamento del carico del primo gruppo di risorse. La seconda volta usare il nome della rete di cluster, il nome della risorsa IP cluster e l'indirizzo IP del servizio di bilanciamento del carico del secondo gruppo di risorse.

Ora il cluster ha una risorsa listener del gruppo di disponibilità.

## 2\. Portare online il listener

Con la risorsa listener del gruppo di disponibilità configurata, è possibile portare online il listener in modo che le applicazioni possano connettersi ai database nel gruppo di disponibilità con il listener.

- Tornare a gestione Cluster di Failover. Espandere**Ruoli**ed evidenziare il gruppo di disponibilità. Nella scheda **Risorse** fare clic con il pulsante destro del mouse sul nome del listener e scegliere **Proprietà**.

- Selezionare la scheda **Dipendenze**. Se sono presenti più risorse elencate, verificare che gli indirizzi abbiano le dipendenze OR, e non quelle AND. Fare clic su **OK**.

- Fare clic con il pulsante destro del mouse sul nome del listener e scegliere**Porta in linea**.


- Una volta che il listener è online, dalla scheda**Risorse**, fare clic con il tasto destro del mouse sul gruppo di disponibilità e fare clic su**Proprietà**.

- Creare una dipendenza sulla risorsa nome del listener (non nome risorse indirizzo IP). Fare clic su **OK**.


- Avviare SQL Server Management Studio e connettersi alla replica primaria.


- Passare a **Disponibilità elevata AlwaysOn** | **Gruppi di disponibilità** | **Listener del gruppo di disponibilità**.


- Viene visualizzato il nome del listener creato in Gestione Cluster di Failover. Fare clic con il pulsante destro del mouse sul nome del listener e quindi su **Proprietà**.


- Nella casella **Porta** specificare il numero di porta per il listener del gruppo di disponibilità usando il valore di $EndpointPort usato in precedenza (1433 era l'impostazione predefinita), quindi fare clic su **OK**.

Ora si ha un gruppo di disponibilità AlwaysOn di SQL Server in macchine virtuali di Azure in esecuzione in modalità Resource Manager.

## Testare la connessione al listener

Per testare la connessione:

1. Usare RDP per connettersi a un'istanza di SQL Server che si trova nella stessa rete virtuale, ma non è proprietaria della replica. Può trattarsi dell'altra istanza di SQL Server nel cluster.

1. Usare l'utilità **sqlcmd** per testare la connessione. Lo script seguente, ad esempio, stabilisce una connessione **sqlcmd** alla replica primaria tramite il listener con l'autenticazione di Windows:

        sqlmd -S <listenerName> -E

La connessione SQLCMD si connette automaticamente a qualsiasi istanza di SQL Server ospiti la replica primaria.

## Linee guida e limitazioni

Tenere presente le linee guida seguenti per il listener del gruppo di disponibilità in Azure con il servizio di bilanciamento del carico interno:

- Per ogni servizio cloud è supportato un solo listener del gruppo di disponibilità interno, perché il listener è configurato per il servizio di bilanciamento del carico e c'è un solo servizio di bilanciamento del carico interno. Tuttavia, è possibile creare più listener esterni. 

- Con un servizio di bilanciamento del carico interno è possibile accedere al listener solo dalla stessa rete virtuale.
 

<!---HONumber=AcomDC_0601_2016-->