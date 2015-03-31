<properties 
	authors="danielceckert" 
	documentationCenter="dev-center-name" 
	editor=""
	manager="jefco" 
	pageTitle="Gestione: modalità di distribuzione del servizio di bilanciamento del carico (affinità IP di origine)" 
	description="Funzionalità di gestione per la modalità di distribuzione del servizio di bilanciamento del carico di Azure" 
	services="virtual-network" 
 />

<tags
	ms.author="danecke"
	ms.date="02/20/2015"
	ms.devlang="na"
	ms.service="virtual-network"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
<<<<<<< HEAD:articles/virtual-networks-load-balancer-manage-distribution-mode.md
 />
 
# Gestione della rete virtuale: modalità di distribuzione del servizio di bilanciamento del carico (affinità IP di origine)
=======
 /> 
 
# Gestione della rete virtuale: modalità di distribuzione del servizio di bilanciamento del carico (affinità IP di origine)
>>>>>>> 5781a6382194f50134d2a16bd9d72a6cca290f3d:articles/virtual-networks-load-balancer-manage-distribution-mode-source-ip.md

**Affinità IP di origine** (nota anche come **affinità sessione** o **affinità del client IP**) è una modalità di distribuzione del servizio di bilanciamento del carico di Azure che consente di associare connessioni da un singolo client a un singolo server ospitato di Azure, anziché distribuire ogni connessione client in modo dinamico ai diversi server ospitati di Azure (comportamento predefinito del servizio di bilanciamento carico).

Utilizzando l'affinità IP di origine, il servizio di bilanciamento del carico di Azure può essere configurato per utilizzare una combinazione di 2 tuple (IP di origine, IP di destinazione) o una combinazione di 3 tuple (IP di origine, IP di destinazione, protocollo) per eseguire il mapping del traffico al pool di server ospitati di Azure disponibili. Quando si utilizza l'affinità IP di origine, le connessioni avviate dallo stesso computer client sono gestite da un singolo endpoint DIP (un singolo server ospitato di Azure).

## Origine del servizio

L'affinità IP di origine risolve una precedente [incompatibilità tra il Gateway Desktop remoto (DOC) e il servizio di bilanciamento del carico di Azure](http://go.microsoft.com/fwlink/p/?LinkId=517389).

## Implementazione

L'affinità IP di origine può essere configurata per: 

* [Endpoint di macchina virtuale](http://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/)
* [Set di endpoint con carico bilanciato](http://msdn.microsoft.com/library/azure/dn655055.aspx)
* [Ruoli Web](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [Ruoli di lavoro](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## Scenari
1. Cluster di Gateway Desktop remoto utilizzando un singolo servizio cloud
2. Caricamento di contenuti multimediali (ad esempio, UDP per i dati, TCP per il controllo)
 * Il client avvia una sessione TCP all'indirizzo IP pubblico con carico bilanciato ospitato di Azure
 * La richiesta del client viene indirizzata a un DIP dal bilanciamento del carico. Questo canale rimane attivo per monitorare l'integrità della connessione
 * Il client avvia una sessione UDP allo stesso indirizzo IP pubblico con carico bilanciato ospitato di Azure
 * Il servizio di bilanciamento del carico di Azure indirizza la richiesta allo stesso endpoint DIP della connessione TCP
 * Il client carica i contenuti multimediali con una velocità effettiva UDP più elevata mantenendo il canale di controllo su TCP per l'affidabilità
 
## Avvertenze
* Se il set con carico bilanciato viene modificato (ad esempio aggiungendo o rimuovendo una macchina virtuale), la distribuzione del canale client viene ricalcolata e le nuove connessioni dai client esistenti possono essere gestite da un server diverso da quello utilizzato originariamente
* L'utilizzo dell'affinità IP di origine può comportare una distribuzione diversa del traffico tra i server ospitati di Azure
* I client che instradano il traffico attraverso un proxy possono essere considerati come un singolo client dal servizio di bilanciamento del carico di Azure

<<<<<<< HEAD:articles/virtual-networks-load-balancer-manage-distribution-mode.md
=======
## Passaggi successivi
* Da definire
 
>>>>>>> 5781a6382194f50134d2a16bd9d72a6cca290f3d:articles/virtual-networks-load-balancer-manage-distribution-mode-source-ip.md
## Esempi di PowerShell
Scaricare [la versione più recente di Azure PowerShell](https://github.com/Azure/azure-sdk-tools/releases) per ottenere i risultati ottimali.

### Aggiungere un endpoint di Azure a una macchina virtuale e impostare la modalità di distribuzione del servizio di bilanciamento del carico

<<<<<<< HEAD:articles/virtual-networks-load-balancer-manage-distribution-mode.md
 Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -LoadBalancerDistribution "sourceIP"| Update-AzureVM 
=======
 Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 â€"LoadBalancerDistribution â€œsourceIPâ€�| Update-AzureVM 
>>>>>>> 5781a6382194f50134d2a16bd9d72a6cca290f3d:articles/virtual-networks-load-balancer-manage-distribution-mode-source-ip.md

LoadBalancerDistribution può essere impostato su sourceIP per il bilanciamento del carico a 2 tuple (IP di origine, IP di destinazione), su sourceIPProtocol per il bilanciamento del carico a 3 tuple (IP di origine, IP di destinazione, protocollo) o su Nessuno per il comportamento predefinito (bilanciamento del carico a 5 tuple). 

### Recuperare una configurazione di modalità di distribuzione del bilanciamento del carico con endpoint

<<<<<<< HEAD:articles/virtual-networks-load-balancer-manage-distribution-mode.md
 PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
=======
 PS C:\> Get-AzureVM â€"ServiceName â€œMyServiceâ€� â€"Name â€œMyVMâ€� | Get-AzureEndpoint
>>>>>>> 5781a6382194f50134d2a16bd9d72a6cca290f3d:articles/virtual-networks-load-balancer-manage-distribution-mode-source-ip.md
 
 VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
 LBSetName : MyLoadBalancedSet
 LocalPort : 80
 Name : HTTP
 Port : 80
 Protocol : tcp
 Vip : 65.52.xxx.xxx
 ProbePath :
 ProbePort : 80
 ProbeProtocol : tcp
 ProbeIntervalInSeconds : 15
 ProbeTimeoutInSeconds : 31
 EnableDirectServerReturn : False
 Acl : {}
 InternalLoadBalancerName :
 IdleTimeoutInMinutes : 15
 LoadBalancerDistribution : sourceIP

Se l'elemento LoadBalancerDistribution non viene specificato, il bilanciamento del carico di Azure utilizza l'algoritmo di 5 tuple predefinito

### Impostare la modalità di distribuzione su un set di endpoint con carico bilanciato

<<<<<<< HEAD:articles/virtual-networks-load-balancer-manage-distribution-mode.md
 Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -LoadBalancerDistribution "sourceIP"
=======
 Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 â€"LoadBalancerDistribution "sourceIP"
>>>>>>> 5781a6382194f50134d2a16bd9d72a6cca290f3d:articles/virtual-networks-load-balancer-manage-distribution-mode-source-ip.md
 
Se gli endpoint fanno parte di un set di endpoint con carico bilanciato, è necessario impostare la modalità di distribuzione sul set di endpoint con carico bilanciato.

## Esempi di servizi cloud

È possibile utilizzare Azure SDK per .NET per aggiornare il servizio cloud

Le impostazioni degli endpoint per i servizi cloud vengono effettuate nel file con estensione csdef. Per aggiornare la modalità di distribuzione del servizio di bilanciamento del carico per una distribuzione di servizi cloud, è necessario un aggiornamento della distribuzione.

Di seguito è riportato un esempio di modifiche del file con estensione csdef alle impostazioni degli endpoint:

 <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
 <Endpoints>
 <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
 </Endpoints>
 </WorkerRole>
 <NetworkConfiguration>
 <VirtualNetworkSite name="VNet"/>
 <AddressAssignments>
 <InstanceAddress roleName="VMRolePersisted">
 <PublicIPs>
 <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
 </PublicIPs>
 </InstanceAddress>
 </AddressAssignments>
 </NetworkConfiguration>
 
## Esempi di API

Gli sviluppatori possono configurare la distribuzione del servizio di bilanciamento del carico utilizzando l'API di gestione del servizio. Assicurarsi di aggiungere l'intestazione x-ms-version impostata sulla versione 2014-09-01 o successiva.

### Aggiornare la configurazione del set con carico bilanciato specificato in una distribuzione

#### Richiesta

 POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet 
 
 x-ms-version: 2014-09-01 
 
 Content-Type: application/xml 
 
 <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"> 
 <InputEndpoint> 
 <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName> 
 <LocalPort> local-port-number </LocalPort> 
 <Port> external-port-number </Port> 
 <LoadBalancerProbe> 
 <Port> port-assigned-to-probe </Port> 
 <Protocol> probe-protocol </Protocol> 
 <IntervalInSeconds> interval-of-probe </IntervalInSeconds> 
 <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds> 
 </LoadBalancerProbe> 
 <Protocol> endpoint-protocol </Protocol> 
 <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn> 
 <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes> 
 <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution> 
 </InputEndpoint> 
 </LoadBalancedEndpointList>

Il valore di LoadBalancerDistribution può essere sourceIP per l'affinità a 2 tuple, sourceIPProtocol per l'affinità a 3 tuple o Nessuno (per nessuna affinità, ad esempio 5 tuple)

#### Risposta

 HTTP/1.1 202 Accepted 
 Cache-Control: no-cache 
 Content-Length: 0 
 Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0 
 x-ms-servedbyregion: ussouth2 
 x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af 
 Date: Thu, 16 Oct 2014 22:49:21 GMT

<!--HONumber=47-->
