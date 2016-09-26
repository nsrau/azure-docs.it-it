<properties
   pageTitle="Configurazione del timeout di inattività TCP di Load Balancer | Microsoft Azure"
   description="Configurazione del timeout di inattività TCP di Load Balancer"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/03/2016"
   ms.author="sewhee" />

# Modifica delle impostazioni del timeout di inattività TCP per Load Balancer

La configurazione predefinita di Azure Load Balancer prevede che il timeout di inattività sia impostato su 4 minuti.

Ciò significa che se un periodo di inattività è più lungo del valore di timeout, non ci sono garanzie che la sessione TCP o HTTP tra il client e il servizio cloud esista ancora.

Quando la connessione viene chiusa, l'applicazione client riceve un messaggio di errore simile al seguente: "Connessione sottostante chiusa: una connessione che doveva restare attiva è stata chiusa dal server in modo imprevisto".

Un metodo comune per mantenere attiva la connessione per un periodo più lungo consiste nell'usare l'impostazione keep-alive TCP (è possibile trovare esempi di .NET [qui](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)). Vengono inviati pacchetti quando non viene rilevata alcuna attività nella connessione. Questa attività di rete garantisce che il valore del timeout di inattività non venga mai raggiunto e che la connessione sia mantenuta per un lungo periodo.

Per evitare di perdere la connessione, è necessario configurare l'impostazione keep-alive TCP con un intervallo minore rispetto all'impostazione di timeout di inattività o aumentare il valore del timeout di inattività.

Sebbene l'impostazione keep-alive TCP funzioni bene per gli scenari in cui la batteria non rappresenta un vincolo, in genere non è consigliata per le applicazioni per dispositivi mobili. L'uso di un'impostazione keep-alive TCP da un'applicazione per dispositivi mobili fa scaricare più velocemente la batteria del dispositivo.

Per supportare tali scenari, è stato aggiunto il supporto per un timeout di inattività configurabile. È ora possibile impostare questo valore su una durata compresa tra 4 e 30 minuti. Questa impostazione funziona solo per le connessioni in entrata.

![Timeout TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

Nella sezione seguente è descritto come modificare le impostazioni del timeout di inattività in macchine virtuali e servizi cloud.

>[AZURE.NOTE] Per supportare la configurazione di queste impostazioni, assicurarsi di aver installato il pacchetto di Azure PowerShell più recente.

## Configurazione del timeout TCP per l'IP pubblico a livello di istanza su 15 minuti

    Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15

`IdleTimeoutInMinutes` è facoltativo. Se non impostato, il timeout predefinito è 4 minuti.

>[AZURE.NOTE] L'intervallo di timeout accettabile è compreso tra 4 e 30 minuti.

## Impostazione del timeout di inattività durante la creazione di un endpoint di Azure in una macchina virtuale

Modificare l'impostazione di timeout per un endpoint:

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

Recuperare la configurazione del timeout di inattività:

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
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

## Impostazione del timeout TCP su un set di endpoint con carico bilanciato

Se gli endpoint fanno parte di un set di endpoint con carico bilanciato, è necessario impostare il timeout TCP sul set di endpoint con carico bilanciato:

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15

## Modifica delle impostazioni di timeout per i servizi cloud

È possibile usare Azure SDK per .NET 2.4 per aggiornare il servizio cloud.

Le impostazioni degli endpoint per i servizi cloud vengono effettuate nel file .csdef. L'aggiornamento il timeout TCP per la distribuzione di un servizio cloud richiede un aggiornamento della distribuzione. Fa eccezione il caso in cui il timeout TCP sia specificato solo per un indirizzo IP pubblico. Le impostazioni degli indirizzi IP pubblici si trovano nel file .cscfg e possono essere aggiornate tramite l'aggiornamento della distribuzione.

Le modifiche del file con estensione csdef per le impostazioni di endpoint sono:

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>

Le modifiche al file .cscfg per l'impostazione di timeout negli indirizzi IP pubblici sono le seguenti:

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

È possibile configurare il timeout di inattività TCP l'API Gestione dei servizi. Assicurarsi che l'intestazione x-ms-version sia impostata sulla versione 2014-06-01 o successiva.

Aggiornare la configurazione degli endpoint di input specificati con carico bilanciato in tutte le macchine virtuali di una distribuzione.

    Request:

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>
<BR>

    Response:

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

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)

<!---HONumber=AcomDC_0914_2016-->