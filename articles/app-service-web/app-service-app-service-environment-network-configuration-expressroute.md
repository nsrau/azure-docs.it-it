<properties 
	pageTitle="Dettagli di configurazione di rete per l'uso di Express Route" 
	description="Dettagli di configurazione di rete per eseguire ambienti del servizio app in reti virtuali connesse a un circuito ExpressRoute." 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/11/2015" 
	ms.author="stefsch"/>

# Dettagli della configurazione di rete per gli ambienti del servizio app con ExpressRoute 

## Panoramica ##
I clienti possono connettere un circuito [Azure ExpressRoute][ExpressRoute] all'infrastruttura di rete virtuale per estendere la rete locale ad Azure. Un ambiente del servizio app può essere creato in una subnet di questa infrastruttura di [rete virtuale][virtualnetwork]. Le app in esecuzione nell'ambiente del servizio app possono quindi stabilire connessioni sicure a risorse back-end accessibili solo tramite la connessione ExpressRoute.

**Nota:** non è possibile creare un ambiente del servizio app in una rete virtuale "v2". Attualmente gli ambienti del servizio app sono supportati solo in reti virtuali classiche "v1".

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Requisiti della connettività di rete ##
Esistono requisiti di connettività di rete per gli ambienti del servizio app che potrebbero non essere inizialmente soddisfatti in una rete virtuale connessa a ExpressRoute.

I requisiti per il corretto funzionamento degli ambienti del servizio app sono i seguenti:


-  Connettività di rete in uscita alle risorse di archiviazione di Azure a livello mondiale e alle risorse del database SQL che si trovano nella stessa area dell'ambiente del servizio app. Questo percorso di rete non può attraversare i proxy aziendali interni perché è probabile che verrebbe modificato l'indirizzo NAT effettivo del traffico di rete in uscita. La modifica dell'indirizzo NAT del traffico di rete in uscita di un ambiente del servizio app diretto agli endpoint di archiviazione di Azure e del database SQL causerà errori di connettività.
-  La configurazione DNS per la rete virtuale deve essere in grado di risolvere gli endpoint all'interno dei seguenti domini controllati da Azure: **.file.core.windows.net*, **.blob.core.windows.net*, **.database.windows.net*.
-  La configurazione DNS per la rete virtuale deve rimanere stabile quando vengono creati ambienti del servizio app, come anche durante le riconfigurazioni e il ridimensionamento delle modifiche apportate agli ambienti del servizio app.   
-  Se è presente un server DNS personalizzato nell’altra estremità di un gateway VPN, il server DNS deve essere raggiungibile e disponibile. 
-  L'accesso di rete in ingresso alle porte necessarie per gli ambienti del servizio app deve essere consentito come spiegato in questo [articolo][requiredports].

Per soddisfare i requisiti DNS, assicurare una configurazione DNS valida per la rete virtuale.

Per soddisfare i requisiti di accesso alla rete in ingresso, configurare un [gruppo di sicurezza di rete][NetworkSecurityGroups] nella subnet dell'ambiente del servizio app per consentire l'accesso richiesto, come spiegato in questo [articolo][requiredports].

## Abilitazione della connettività di rete in uscita per un ambiente del servizio app##
Per impostazione predefinita, un circuito ExpressRoute appena creato annuncia una route predefinita che consente la connettività Internet in uscita. Con questa configurazione, un ambiente del servizio app sarà in grado di connettersi ad altri endpoint di Azure.

Tuttavia, secondo una diffusa configurazione, i clienti definiscono la propria route predefinita verso la quale viene forzato il traffico Internet in uscita invece di far passare il flusso localmente attraverso l'infrastruttura proxy/firewall del cliente. Questo flusso di traffico interrompe sempre gli ambienti del servizio app perché il traffico in uscita è bloccato in locale o convertito tramite NAT in un set non riconoscibile di indirizzi che non usano più i diversi endpoint di Azure.

La soluzione consiste nel definire una o più route definite dall'utente (UDR, User Defined Route) nella subnet contenente l'ambiente del servizio app. Una route UDR definisce le route specifiche della subnet che verranno accettate invece della route predefinita.

Informazioni di base sulle route definite dall'utente sono disponibili in questa [panoramica][UDROverview].

Informazioni dettagliate sulla creazione e la configurazione di route definite dall'utente sono disponibili in questa [Guida introduttiva][UDRHowTo].

## Esempio di configurazione UDR per un ambiente del servizio app ##

**Prerequisiti**

1. Installare la versione più recente di Azure PowerShell, dalla [pagina di download di Azure][AzureDownloads] (con data giugno 2015 o successiva). In "Strumenti da riga di comando" è presente un collegamento "Installa" in "Windows Powershell" che installerà i cmdlet di Powershell più recenti.

2. È consigliabile creare una subnet univoca da usare esclusivamente in un ambiente del servizio app. In questo modo le route UDR applicate alla subnet apriranno solo il traffico in uscita per l'ambiente del servizio app.
3. **Importante**: distribuire l'ambiente del servizio app solo **dopo** avere eseguito i seguenti passaggi di configurazione. Questo assicura che la connettività di rete in uscita sia disponibile prima di tentare di distribuire un ambiente del servizio app.

**Passaggio 1: Creare una tabella di route denominata**

Il frammento seguente crea una tabella di route denominata "DirectInternetRouteTable" nell'area di Azure Stati Uniti occidentali:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Passaggio 2: Creare una o più route nella tabella di route**

Sarà necessario aggiungere una o più route alla tabella di route per consentire l'accesso Internet in uscita. Il seguente esempio aggiunge route sufficienti a coprire tutti i possibili indirizzi di Azure usati nell'area Stati Uniti occidentali.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 1' -AddressPrefix 23.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 2' -AddressPrefix 40.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 3' -AddressPrefix 65.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 4' -AddressPrefix 104.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 5' -AddressPrefix 137.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 6' -AddressPrefix 138.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 7' -AddressPrefix 157.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 8' -AddressPrefix 168.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 9' -AddressPrefix 191.0.0.0/8 -NextHopType Internet


Per un elenco completo e aggiornato di intervalli CIDR usati da Azure, è possibile scaricare un file XML contenente tutti gli intervalli dall'[Area download Microsoft][DownloadCenterAddressRanges].

**Nota:** in futuro sarà disponibile una forma CIDR abbreviata 0.0.0.0/0 da usare nel parametro *AddressPrefix*. Questa forma abbreviata equivale a "tutti gli indirizzi Internet". Per ora gli sviluppatori devono invece usare un ampio set di intervalli CIDR sufficienti a coprire tutti i possibili intervalli di indirizzi di Azure.

**Passaggio 3: Associare la tabella di route alla subnet contenente l'ambiente del servizio app**

Nell'ultimo passaggio della configurazione si associa la tabella di route alla subnet in cui verrà distribuito l'ambiente del servizio app. Il comando seguente associa "DirectInternetRouteTable" ad "ASESubnet" che alla fine conterrà un ambiente del servizio app.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Passaggio 4: Procedura finale**

Una volta associata la tabella di route alla subnet, si consiglia di testare e verificare l'effetto desiderato. Ad esempio, distribuire una macchina virtuale nella subnet e verificare che:


- Il traffico in uscita agli endpoint di Azure non scorra lungo il circuito ExpressRoute.
- Le ricerche DNS degli endpoint di Azure vengano risolte correttamente. 

Una volta verificati i passaggi precedenti, è possibile eliminare la macchina virtuale e procedere con la creazione di un ambiente del servizio app.

## Introduzione

Per iniziare a usare gli ambienti del servizio app, vedere [Introduzione all'ambiente del servizio app][IntroToAppServiceEnvironment].

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/downloads/
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
 

<!-- IMAGES -->

<!---HONumber=Oct15_HO1-->