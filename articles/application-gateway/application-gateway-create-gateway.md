<properties
   pageTitle="Creare, avviare o eliminare un gateway applicazione | Microsoft Azure"
   description="Questa pagina fornisce istruzioni per la creazione, la configurazione, l'avvio e l'eliminazione di un gateway applicazione di Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="joaoma"/>

# Creare, avviare o eliminare un gateway applicazione

Il gateway applicazione di Azure è un dispositivo di bilanciamento del carico di livello 7. Fornisce richieste HTTP con routing delle prestazioni e failover tra server diversi, sia nel cloud che in locale. Il gateway applicazione offre le seguenti funzionalità di distribuzione delle applicazioni: bilanciamento del carico HTTP, affinità delle sessioni basata sui cookie e offload SSL (Secure Sockets Layer).

> [AZURE.SELECTOR]
- [PowerShell per Azure classico](application-gateway-create-gateway.md)
- [PowerShell per Azure Resource Manager](application-gateway-create-gateway-arm.md)
- [Modello di Azure Resource Manager](application-gateway-create-gateway-arm-template.md)


<BR>

Questo articolo illustra in dettaglio i passaggi necessari per creare e configurare, avviare ed eliminare un gateway applicazione.


## Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell usando l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [pagina Download](https://azure.microsoft.com/downloads/).
2. Se si ha una rete virtuale esistente, selezionare una subnet vuota esistente oppure creare una nuova subnet nella rete virtuale esclusivamente per l'uso da parte del gateway applicazione. Non è possibile distribuire il gateway applicazione in una rete virtuale diversa da quella delle risorse da distribuire dietro il gateway applicazione.
3. Assicurarsi di avere una rete virtuale funzionante con una subnet valida. Assicurarsi che nessuna macchina virtuale o distribuzione cloud stia usando la subnet. Il gateway applicazione deve essere da solo in una subnet di rete virtuale.
3. I server che verranno configurati per l'uso del gateway applicazione devono esistere. In alternativa, è necessario creare i relativi endpoint nella rete virtuale o assegnato loro un indirizzo IP/VIP pubblico.

## Elementi necessari per creare un gateway applicazione


Quando si usa il comando **New-AzureApplicationGateway** per creare il gateway applicazione, non è ancora stata impostata alcuna configurazione e la risorsa appena creata deve essere configurata usando XML o un oggetto di configurazione.


I valori possibili sono:

- **Pool di server back-end:** elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP/VIP pubblici.
- **Impostazioni del pool di server back-end:** ogni pool ha impostazioni quali porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
- **Porta front-end:** porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
- **Listener**: ha una porta front-end, un protocollo (HTTP o HTTPS che fa distinzione tra maiuscole e minuscole) e il nome del certificato SSL, se si configura l'offload SSL.
- **Regola**: associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico.


## Creare un nuovo gateway applicazione

Per creare un gateway applicazione:

1. Creare una risorsa del gateway applicazione.
2. Creare un file XML di configurazione o un oggetto di configurazione.
3. Eseguire il commit della configurazione nella risorsa del gateway applicazione appena creata.

>[AZURE.NOTE] Se è necessario configurare un probe personalizzato per il gateway applicazione, vedere [Creare un probe personalizzato per il gateway applicazione di Azure (classico) con PowerShell](application-gateway-create-probe-classic-ps.md). Per altre informazioni, vedere l'articolo relativo a [probe personalizzati e monitoraggio dell'integrità](application-gateway-probe-overview.md).


### Creare una risorsa del gateway applicazione

Per creare il gateway, usare il cmdlet **New-AzureApplicationGateway**, sostituendo i valori esistenti con quelli personalizzati. Si noti che la fatturazione per il gateway non viene applicata a partire da questo punto. La fatturazione viene applicata a partire da un passaggio successivo, dopo l'avvio corretto del gateway.

L'esempio seguente mostra come creare un nuovo gateway applicazione usando una rete virtuale denominata "testvnet1" e una subnet denominata "subnet-1".


	New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description*, *InstanceCount* e *GatewaySize* sono parametri facoltativi.


Per convalidare la creazione del gateway è possibile usare il cmdlet **Get-AzureApplicationGateway**.




	Get-AzureApplicationGateway AppGwTest
	Name          : AppGwTest
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Stopped
	VirtualIPs    : {}
	DnsName       :

>[AZURE.NOTE]  Il valore predefinito per *InstanceCount* è 2, con un valore massimo pari a 10. Il valore predefinito per *GatewaySize* è Medium. È possibile scegliere tra Small, Medium e Large.


 *VirtualIPs* e *DnsName* vengono visualizzati vuoti perché il gateway non è stato ancora avviato. Questi valori verranno creati quando il gateway sarà in esecuzione.

## Configurare il gateway applicazione

È possibile configurare il gateway applicazione usando XML o un oggetto di configurazione.

## Configurare il gateway applicazione usando XML

Nell'esempio seguente viene usato un file XML per configurare tutte le impostazioni del gateway applicazione ed eseguirne il commit nella risorsa del gateway applicazione.

### Passaggio 1  

Copiare il testo seguente in Blocco note.

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>(name-of-your-frontend-port)</Name>
	            <Port>(port number)</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>(name-of-your-backend-pool)</Name>
	            <IPAddresses>
	                <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
	                <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>(backend-setting-name-to-configure-rule)</Name>
	            <Port>80</Port>
	            <Protocol>[Http|Https]</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>(name-of-the-listener)</Name>
	            <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
	            <Protocol>[Http|Https]</Protocol>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>(name-of-load-balancing-rule)</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
	            <Listener>(name-of-the-listener)</Listener>
	            <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>

Modificare i valori tra parentesi per gli elementi di configurazione. Salvare il file con estensione XML.

>[AZURE.IMPORTANT] L'elemento del protocollo HTTP o HTTPS deve rispettare la distinzione tra maiuscole e minuscole.

L'esempio seguente mostra come usare un file di configurazione per impostare il gateway applicazione per il bilanciamento del carico del traffico HTTP sulla porta pubblica 80 e inviare il traffico di rete alla porta back-end 80 tra due indirizzi IP.

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>FrontendPort1</Name>
	            <Port>80</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>BackendPool1</Name>
	            <IPAddresses>
	                <IPAddress>10.0.0.1</IPAddress>
	                <IPAddress>10.0.0.2</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>BackendSetting1</Name>
	            <Port>80</Port>
	            <Protocol>Http</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>HTTPListener1</Name>
	            <FrontendPort>FrontendPort1</FrontendPort>
	            <Protocol>Http</Protocol>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>HttpLBRule1</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
	            <Listener>HTTPListener1</Listener>
	            <BackendAddressPool>BackendPool1</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>


### Passaggio 2

Configurare ora il gateway applicazione. Usare il cmdlet **Set-AzureApplicationGatewayConfig** con un file di configurazione XML.


	Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Configurare il gateway applicazione usando un oggetto di configurazione

L'esempio seguente mostra come configurare il gateway applicazione usando oggetti di configurazione. Tutti gli elementi di configurazione devono essere configurati singolarmente e quindi aggiunti a un oggetto di configurazione del gateway applicazione. Dopo avere creato l'oggetto di configurazione, usare il comando **Set-AzureApplicationGateway** per eseguire il commit della configurazione nella risorsa del gateway applicazione creata in precedenza.

>[AZURE.NOTE] Prima di assegnare un valore a ogni oggetto di configurazione, è necessario dichiarare quale tipologia di oggetto verrà usato da PowerShell per l'archiviazione. La prima riga per creare i singoli elementi definisce quale Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model(nome oggetto) verrà usato.

### Passaggio 1

Creare tutti i singoli elementi di configurazione.

Creare l'IP front-end, come illustrato nell'esempio seguente.

	PS C:\> $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
	PS C:\> $fip.Name = "fip1"
	PS C:\> $fip.Type = "Private"
	PS C:\> $fip.StaticIPAddress = "10.0.0.5"

Creare la porta front-end, come illustrato nell'esempio seguente.

	PS C:\> $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
	PS C:\> $fep.Name = "fep1"
	PS C:\> $fep.Port = 80

Creare il pool di server back-end.

 Definire gli indirizzi IP che verranno aggiunti al pool di server back-end, come illustrato nell'esempio seguente.


	PS C:\> $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
	PS C:\> $servers.Add("10.0.0.1")
	PS C:\> $servers.Add("10.0.0.2")

 Usare l'oggetto $server per aggiungere i valori all'oggetto pool back-end ($pool).

	PS C:\> $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
	PS C:\> $pool.BackendServers = $servers
	PS C:\> $pool.Name = "pool1"

Creare l'impostazione del pool di server back-end.

	PS C:\> $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
	PS C:\> $setting.Name = "setting1"
	PS C:\> $setting.CookieBasedAffinity = "enabled"
	PS C:\> $setting.Port = 80
	PS C:\> $setting.Protocol = "http"

Creare il listener.

	PS C:\> $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
	PS C:\> $listener.Name = "listener1"
	PS C:\> $listener.FrontendPort = "fep1"
	PS C:\> $listener.FrontendIP = "fip1"
	PS C:\> $listener.Protocol = "http"
	PS C:\> $listener.SslCert = ""

Creare la regola.

	PS C:\> $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
	PS C:\> $rule.Name = "rule1"
	PS C:\> $rule.Type = "basic"
	PS C:\> $rule.BackendHttpSettings = "setting1"
	PS C:\> $rule.Listener = "listener1"
	PS C:\> $rule.BackendAddressPool = "pool1"

### Passaggio 2

Assegnare tutti i singoli elementi di configurazione a un oggetto di configurazione del gateway applicazione ($appgwconfig).

Aggiungere l'IP front-end alla configurazione.

	PS C:\> $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
	PS C:\> $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
	PS C:\> $appgwconfig.FrontendIPConfigurations.Add($fip)

Aggiungere la porta front-end alla configurazione.

	PS C:\> $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
	PS C:\> $appgwconfig.FrontendPorts.Add($fep)

Aggiungere il pool di server back-end alla configurazione.

	PS C:\> $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
	PS C:\> $appgwconfig.BackendAddressPools.Add($pool)  

Aggiungere l'impostazione del pool back-end alla configurazione.

	PS C:\> $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
	PS C:\> $appgwconfig.BackendHttpSettingsList.Add($setting)

Aggiungere il listener alla configurazione.

	PS C:\> $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
	PS C:\> $appgwconfig.HttpListeners.Add($listener)

Aggiungere la regola alla configurazione.

	PS C:\> $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
	PS C:\> $appgwconfig.HttpLoadBalancingRules.Add($rule)

### Passaggio 3

Eseguire il commit dell'oggetto di configurazione nella risorsa del gateway applicazione usando **Set-AzureApplicationGatewayConfig**.

	Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## Avviare il gateway

Dopo aver configurato il gateway, usare il cmdlet **Start-AzureApplicationGateway** per avviarlo. La fatturazione per un gateway applicazione verrà applicata a partire dall'avvio corretto del gateway.


> [AZURE.NOTE] Il cmdlet **Start-AzureApplicationGateway** potrebbe impiegare fino a 15-20 minuti.



	Start-AzureApplicationGateway AppGwTest

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Verificare lo stato del gateway

Usare il cmdlet **Get-AzureApplicationGateway** per controllare lo stato del gateway. Se nel passaggio precedente l'operazione **Start-AzureApplicationGateway** è riuscita, lo *stato* risulterà In esecuzione e le voci per l'indirizzo *VIP* e *DnsName* saranno valide.

L'esempio seguente illustra un gateway applicazione attivo, in esecuzione e pronto per accettare il traffico destinato a `http://<generated-dns-name>.cloudapp.net`.

	Get-AzureApplicationGateway AppGwTest

	VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
	VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
	Name          : AppGwTest
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Running
	Vip           : 138.91.170.26
	DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## Eliminare un gateway applicazione

Per eliminare un gateway applicazione:

1. Usare il cmdlet **Stop-AzureApplicationGateway** per arrestare il gateway.
2. Usare il cmdlet **Remove-AzureApplicationGateway** per rimuovere il gateway.
3. Verificare che il gateway sia stato rimosso usando il cmdlet **Get-AzureApplicationGateway**.

L'esempio seguente mostra il cmdlet **Stop-AzureApplicationGateway** nella prima riga, seguito dall'output.

	Stop-AzureApplicationGateway AppGwTest

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Quando lo stato del gateway applicazione è Arrestato, usare il cmdlet **Remove-AzureApplicationGateway** per rimuovere il servizio.


	Remove-AzureApplicationGateway AppGwTest

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

Per verificare che il servizio sia stato rimosso è possibile usare il cmdlet **Get-AzureApplicationGateway**. Questo passaggio non è obbligatorio.


	Get-AzureApplicationGateway AppGwTest

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
	.....

## Passaggi successivi

Per configurare l'offload SSL, vedere [Configurare un gateway applicazione per l'offload SSL tramite il modello di distribuzione classica](application-gateway-ssl.md).

Per configurare un gateway applicazione da usare con un servizio di bilanciamento del carico interno, vedere [Creare un gateway applicazione con un servizio di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

- [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0629_2016-->