<properties 
	pageTitle="Connessione sicura alle risorse back-end da un ambiente del servizio app" 
	description="Informazioni su come connettersi in modo sicuro alle risorse back-end da un ambiente del servizio app." 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="stefsh"/>

# Connessione sicura alle risorse back-end da un ambiente del servizio app #

## Panoramica ##
Poiché un ambiente del servizio app viene sempre creato in una [rete virtuale][virtualnetwork] regionale, le connessioni in uscita da un ambiente del servizio app ad altre risorse back-end possono transitare esclusivamente tramite la rete virtuale.

Ad esempio, potrebbe essere in esecuzione un'istanza di SQL Server in un cluster di macchine virtuali con la porta 1433 bloccata. In base all'elenco di controllo di accesso definito per l'endpoint, potrebbe essere consentito solo l'accesso da altre risorse nella stessa rete virtuale.

Oppure, gli endpoint sensibili potrebbero essere eseguiti in locale ed essere connessi ad Azure tramite connessioni [da sito a sito][SiteToSite] o connessioni [Azure ExpressRoute][ExpressRoute]. In questo caso, solo le risorse nelle reti virtuali connesse ai tunnel da sito a sito o ExpressRoute potrebbero accedere agli endpoint locali.

Per tutti questi scenari, le app in esecuzione in un ambiente del servizio app potranno connettersi in modo sicuro ai server e alle risorse. Il traffico in uscita dalle app in esecuzione in un ambiente del servizio app agli endpoint privati nella stessa rete virtuale (o connessi alla stessa rete virtuale) transiterà solo attraverso la rete virtuale. Il traffico in uscita agli endpoint privati non transiterà attraverso la rete Internet pubblica.

## Requisiti per DNS e connettività in uscita ##
Si noti che per il corretto funzionamento di un ambiente del servizio app, è necessario l'accesso in uscita ad Archiviazione di Azure oltre che al database SQL nella stessa area di Azure. Se l'accesso a Internet in uscita è bloccato nella rete virtuale, gli ambienti del servizio app non potranno accedere a questi endpoint di Azure.

Il cliente potrebbe anche avere configurato server DNS personalizzati nella rete virtuale. Gli ambienti del servizio app devono poter risolvere gli endpoint di Azure in *.database.windows.net, *.file.core.windows.net e *.blob.core.windows.net. 

È anche consigliabile che i server DNS personalizzati nella rete virtuale vengano configurati prima di creare un ambiente del servizio app. Se la configurazione DNS della rete virtuale viene modificata durante la creazione di un ambiente del servizio app, il processo di creazione dell'ambiente del servizio app avrà esito negativo.

## Connessione a un'istanza di SQL Server
Una configurazione di SQL Server comune prevede un endpoint in ascolto sulla porta 1433:

![Endpoint di SQL Server][SqlServerEndpoint]

È possibile usare due approcci per limitare il traffico a questo endpoint:


- [Elenchi di controllo di accesso di rete][NetworkAccessControlLists] (ACL di rete)

- [Gruppi di sicurezza di rete][NetworkSecurityGroups]


## Limitazione dell'accesso con un elenco di controllo di accesso di rete

È possibile proteggere la porta 1433 usando un elenco di controllo di accesso di rete. L'esempio seguente illustra come consentire gli indirizzi client originati dall'interno di una rete virtuale e bloccare l'accesso a tutti gli altri client.

![Esempio di elenco di controllo di accesso di rete (ACL)][NetworkAccessControlListExample]

Tutte le applicazioni in esecuzione nell'ambiente del servizio app all'interno della stessa rete virtuale di SQL Server potranno connettersi all'istanza di SQL Server usando l'indirizzo IP **interno della rete virtuale** per la macchina virtuale SQL Server.

La stringa di connessione di esempio seguente fa riferimento all'instanza di SQL Server usando l'indirizzo IP privato.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Sebbene la macchina virtuale disponga anche di un endpoint pubblico, i tentativi di connessione usando l'indirizzo IP pubblico verranno rifiutati a causa dell'elenco di controllo di accesso di rete.

## Limitazione dell'accesso con un gruppo di sicurezza di rete
In alternativa, per proteggere l'accesso è possibile usare un gruppo di sicurezza di rete. I gruppi di sicurezza di rete possono essere applicati alle singole macchine virtuali o a una subnet contenente macchine virtuali.

È prima necessario creare un gruppo di sicurezza di rete:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

La limitazione dell'accesso al solo traffico interno della rete virtuale con un gruppo di sicurezza di rete è un'operazione estremamente semplice. Le regole predefinite in un gruppo di sicurezza di rete consentono l'accesso solo da altri client di rete nella stessa rete virtuale.

Di conseguenza, è possibile bloccare l'accesso a SQL Server semplicemente applicando un gruppo di sicurezza di rete con le relative regole predefinite alle macchine virtuali che eseguono SQL Server o alla subnet contenente le macchine virtuali.

L'esempio seguente mostra come applicare un gruppo di sicurezza di rete alla subnet contenente le macchine virtuali:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
Il risultato finale è costituito da un set di regole di sicurezza che blocca l'accesso esterno e consente l'accesso interno al traffico proveniente dalla rete virtuale:

![Regole di sicurezza di rete predefinite][DefaultNetworkSecurityRules]


## Introduzione

Per iniziare a usare gli ambienti del servizio app, vedere [Introduzione all'ambiente del servizio app][IntroToAppServiceEnvironment].

Per informazioni dettagliate su come controllare il traffico in ingresso all'ambiente del servizio app, vedere [Controllo del traffico in ingresso a un ambiente del servizio app][ControlInboundASE]

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!-- LINKS -->
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[ControlInboundTraffic]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://msdn.microsoft.com/library/azure/dn133795.aspx
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://msdn.microsoft.com/library/azure/dn376541.aspx
[NetworkSecurityGroups]: https://msdn.microsoft.com/library/azure/dn848316.aspx
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ControlInboundASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png

<!---HONumber=July15_HO4-->