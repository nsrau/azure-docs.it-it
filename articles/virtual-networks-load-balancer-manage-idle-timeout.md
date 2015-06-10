<properties 
   authors="danielceckert" 
   documentationCenter="dev-center-name" 
   editor=""
   manager="jefco" 
   pageTitle="Gestione: timeout di inattività del servizio di bilanciamento del carico" 
   description="Funzionalità di gestione per il timeout di inattività del servizio di bilanciamento carico di Azure" 
   services="virtual-network" 
   />

<tags
   ms.author="danecke"
   ms.date="05/27/2015"
   ms.devlang="na"
   ms.service="virtual-network"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   />
   
# Gestione della rete virtuale: timeout di inattività TCP del servizio di bilanciamento del carico

**Timeout di inattività TCP** consente allo sviluppatore di specificare una soglia di inattività certa per le sessioni client-server che coinvolgono il servizio di bilanciamento del carico di Azure. Un valore di timeout di inattività TCP pari a 4 minuti (l'impostazione predefinita per il servizio di bilanciamento del carico di Azure) comporta che se si verifica un periodo di inattività più lungo di 4 minuti durante una sessione client-server che utilizza il servizio di bilanciamento del carico di Azure, la connessione verrà chiusa.

Quando una connessione client-server viene chiusa, l'applicazione client riceve un messaggio di errore simile a "Connessione sottostante chiusa: una connessione che doveva restare attiva è stata chiusa dal server in modo imprevisto".

[TCP Keep-Alive](http://tools.ietf.org/html/rfc1122#page-101) è una procedura comune per mantenere le connessioni per un lungo periodo di inattività [(esempio MSDN)](http://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Quando si utilizza TCP Keep-Alive, vengono inviati periodicamente semplici pacchetti da un client (in genere con una frequenza inferiore alla soglia di timeout di inattività del server). Il server considera queste trasmissioni come prova dell'attività di connessione, anche in assenza di altre attività, e pertanto il valore di timeout di inattività non viene mai superato e la connessione viene mantenuta per un lungo periodo di tempo.

Sebbene TCP Keep-Alive funzioni bene, in genere non è un'opzione per le applicazioni mobili in quanto dispendiosa per le risorse di alimentazione limitate dei dispositivi mobili. Un'applicazione mobile che utilizza TCP Keep-Alive esaurisce la batteria del dispositivo molto rapidamente perché assorbe continuamente energia per l'utilizzo della rete.

Per supportare scenari di dispositivi mobili, il servizio di bilanciamento del carico di Azure supporta un timeout di inattività TCP configurabile. Gli sviluppatori possono impostare il timeout di inattività TCP su una durata compresa tra 4 e 30 minuti per le connessioni in ingresso (il timeout di inattività TCP configurabile non si applica alle connessioni in uscita). In tal modo i client possono mantenere una sessione con un server con lunghi periodi di inattività per molto più tempo. È comunque possibile impostare la tecnica TCP Keep-Alive per un'applicazione di un dispositivo mobile per mantenere le connessioni che prevedono periodi di inattività più lunghi di 30 minuti, ma questo timeout di inattività TCP più lungo consente alle applicazioni di emettere richieste TCP Keep-Alive molto meno frequentemente rispetto a prima, riducendo in modo significativo l'utilizzo delle risorse di alimentazione del dispositivo mobile.

## Implementazione

Il timeout di inattività TCP può essere configurato per:

* [Indirizzi IP pubblici a livello di istanza](http://msdn.microsoft.com/library/azure/dn690118.aspx)
* [Set di endpoint con carico bilanciato](http://msdn.microsoft.com/library/azure/dn655055.aspx)
* [Endpoint di macchina virtuale](virtual-machines-set-up-endpoints.md)
* [Ruoli Web](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [Ruoli di lavoro](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## Passaggi successivi
* Da definire

## Esempi di PowerShell
Scaricare [la versione più recente di Azure PowerShell](https://github.com/Azure/azure-sdk-tools/releases) per ottenere i risultati ottimali.

### Configurare il timeout TCP per l'IP pubblico a livello di istanza su 15 minuti

    Set-AzurePublicIP –PublicIPName webip –VM MyVM -IdleTimeoutInMinutes 15

IdleTimeoutInMinutes è facoltativo. Se non impostato, il timeout predefinito è 4 minuti. È possibile impostare un valore compreso tra 4 e 30 minuti.

### Impostare il timeout di inattività durante la creazione di un endpoint di Azure in una macchina virtuale

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

### Recuperare la configurazione del timeout di inattività

    PS C:> Get-AzureVM –ServiceName “MyService” –Name “MyVM” | Get-AzureEndpoint
    
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
    
### Impostare il timeout TCP su un set di endpoint con carico bilanciato

Se gli endpoint fanno parte di un set di endpoint con carico bilanciato, è necessario impostare il timeout TCP sul set di endpoint con carico bilanciato.

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15

## Esempi di servizi cloud

È possibile utilizzare Azure SDK per .NET per aggiornare il servizio cloud

Le impostazioni degli endpoint per i servizi cloud vengono effettuate nel file con estensione csdef. Pertanto, per aggiornare il timeout TCP per una distribuzione di servizi cloud, è necessario l'aggiornamento della distribuzione. Fa eccezione il caso in cui il timeout TCP viene specificato solo per un indirizzo IP pubblico. Le impostazioni degli indirizzi IP pubblici si trovano nel file con estensione cscfg e possono essere aggiornate tramite l'aggiornamento della distribuzione.

Le modifiche del file con estensione csdef per le impostazioni di endpoint sono:

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>The .cscfg changes for the timeout setting on Public IPs are:
    
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

Gli sviluppatori possono configurare la distribuzione del servizio di bilanciamento del carico utilizzando l'API di gestione del servizio. Assicurarsi di aggiungere l'intestazione x-ms-version impostata sulla versione 2014-06-01 o successiva.

### Aggiornare la configurazione di specificati endpoint di input con carico bilanciato in tutte le macchine virtuali di una distribuzione

#### Richiesta

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

Il valore di LoadBalancerDistribution può essere sourceIP per l'affinità a 2 tuple, sourceIPProtocol per l'affinità a 3 tuple o Nessuno (per nessuna affinità, ad esempio 5 tuple)

#### Response

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
        <LocalPort>local-port-number</LocalPort>
        <Port>external-port-number</Port>
        <LoadBalancerProbe>
          <Path>path-of-probe</Path>
          <Port>port-assigned-to-probe</Port>
          <Protocol>probe-protocol</Protocol>
          <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
          <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
        </LoadBalancerProbe>
        <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
        <Protocol>endpoint-protocol</Protocol>
        <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
        <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
        <EndpointACL>
          <Rules>
            <Rule>
              <Order>priority-of-the-rule</Order>
              <Action>permit-rule</Action>
              <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
              <Description>description-of-the-rule</Description>
            </Rule>
          </Rules>
        </EndpointACL>
      </InputEndpoint>
    </LoadBalancedEndpointList>

<!---HONumber=58-->