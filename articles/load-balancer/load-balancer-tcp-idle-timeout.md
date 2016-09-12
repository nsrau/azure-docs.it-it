<properties
   pageTitle="Configurare il timeout di inattività TCP del bilanciamento del carico | Microsoft Azure"
   description="Configurare il timeout di inattività TCP del bilanciamento del carico"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/03/2016"
   ms.author="sewhee" />  

# Come modificare le impostazioni del timeout di inattività TCP per il bilanciamento del carico

La configurazione predefinita del bilanciamento del carico di Azure prevede un'impostazione per il "timeout di inattività" di 4 minuti.

Ciò significa che se il periodo di inattività nelle sessioni tcp o http supera il valore di timeout, non vi è alcuna garanzia che venga mantenuta la connessione tra il client e il servizio.

Quando la connessione viene chiusa, l'applicazione client riceve un messaggio di errore che indica che la connessione sottostante è stata chiusa e che una connessione che doveva restare attiva è stata chiusa dal server.

Una metodo tipico per mantenere attiva la connessione per un periodo più lungo consiste nell'usare l'impostazione keep-alive TCP (è possibile trovare esempi di .NET [qui](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)).

Vengono inviati pacchetti quando non viene rilevata alcuna attività nella connessione. Garantendo la continuità dell'attività di rete, il valore del timeout di inattività non viene mai raggiunto e la connessione viene mantenuta per un lungo periodo.

L'idea è quella di configurare l'impostazione keep-alive TCP con un intervallo più breve rispetto all'impostazione di timeout predefinita per evitare l'interruzione della connessione oppure di aumentare il valore del timeout di inattività per mantenere attiva la sessione di connessione TCP.

Sebbene l'impostazione keep-alive TCP funzioni bene per gli scenari in cui la batteria non rappresenta un vincolo, in genere non è un'opzione valida per le applicazioni per dispositivi mobili. L'uso dell'impostazione keep-alive TCP da un'applicazione per dispositivi mobile fa scaricare più velocemente la batteria del dispositivo.

Per supportare tali scenari, è stato aggiunto il supporto per un timeout di inattività configurabile. È ora possibile impostare questo valore su una durata compresa tra 4 e 30 minuti. Questa impostazione funziona solo per le connessioni in entrata.

![tcptimeout](./media/load-balancer-tcp-idle-timeout/image1.png)


## Come modificare le impostazioni del timeout di inattività in macchine virtuali e servizi cloud

>[AZURE.NOTE] Tenere presente che alcuni comandi sono disponibili solo nel pacchetto più recente di Azure PowerShell. Se il comando PowerShell non è disponibile, scaricare un pacchetto di PowerShell più recente.


### Configurare il timeout TCP per l'IP pubblico a livello di istanza su 15 minuti

    Set-AzurePublicIP –PublicIPName webip –VM MyVM -IdleTimeoutInMinutes 15

IdleTimeoutInMinutes è facoltativo. Se non impostato, il timeout predefinito è 4 minuti.

>[AZURE.NOTE] L'intervallo di timeout accettabile è compreso tra 4 e 30 minuti.

### Impostare il timeout di inattività durante la creazione di un endpoint di Azure in una macchina virtuale

Per modificare l'impostazione di timeout per un endpoint

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

Recuperare la configurazione del timeout di inattività

    PS C:\> Get-AzureVM –ServiceName “MyService” –Name “MyVM” | Get-AzureEndpoint
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

### Modifica delle impostazioni di timeout per i servizi cloud

È possibile usare Azure SDK per .NET 2.4 per aggiornare il servizio cloud.

Le impostazioni degli endpoint per i servizi cloud vengono effettuate nel file con estensione csdef. Per aggiornare il timeout TCP per una distribuzione di servizi cloud, è necessario l'aggiornamento della distribuzione. Fa eccezione il caso in cui il timeout TCP viene specificato solo per un indirizzo IP pubblico. Le impostazioni degli indirizzi IP pubblici si trovano nel file con estensione cscfg e possono essere aggiornate tramite l'aggiornamento della distribuzione.

Le modifiche del file con estensione csdef per le impostazioni di endpoint sono:

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>

Le modifiche al file con estensione cscfg per l'impostazione di timeout negli indirizzi IP pubblici sono le seguenti:

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

## Esempio di API REST

È possibile configurare il timeout di inattività TCP usando l'API di gestione servizi. Assicurarsi di aggiungere l'intestazione x-ms-version, impostandola sulla versione 2014-06-01 o successiva.

Aggiornare la configurazione di specificati endpoint di input con carico bilanciato in tutte le macchine virtuali di una distribuzione

    Request

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>
<BR>

    Response

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

## Passaggi successivi

[Panoramica del bilanciamento del carico interno](load-balancer-internal-overview.md)

[Introduzione alla configurazione del bilanciamento del carico Internet](load-balancer-get-started-internet-arm-ps.md)

[Configurare una modalità di distribuzione del bilanciamento del carico](load-balancer-distribution-mode.md)

<!---HONumber=AcomDC_0831_2016-->