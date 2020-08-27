---
title: Connetti al back-end V1
description: Informazioni su come connettersi in modo sicuro alle risorse back-end da un ambiente del servizio app. Questo documento è rivolto solo ai clienti che usano l'ambiente del servizio app v1 legacy.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 9f8e288f771b9d584a0fd3430115f5fa60f68e47
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961806"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Connettersi in modo sicuro alle risorse back-end da un ambiente del servizio app
Poiché un ambiente del servizio app viene sempre creato in una **rete virtuale** di Azure Resource Manager **o** in una [rete virtuale][virtualnetwork] creata con il modello di distribuzione classica, le connessioni in uscita da un ambiente del servizio app ad altre risorse back-end possono transitare esclusivamente tramite la rete virtuale. Al 2016 giugno, gli ambienti può essere distribuito anche in reti virtuali che usano intervalli di indirizzi pubblici o spazi di indirizzi RFC1918 (indirizzi privati).  

Ad esempio, potrebbe essere in esecuzione un'istanza di SQL Server in un cluster di macchine virtuali con la porta 1433 bloccata.  In base all'elenco di controllo di accesso definito per l'endpoint, potrebbe essere consentito solo l'accesso da altre risorse nella stessa rete virtuale.  

Oppure, gli endpoint sensibili potrebbero essere eseguiti in locale ed essere connessi ad Azure tramite connessioni [da sito a sito][SiteToSite] o connessioni [Azure ExpressRoute][ExpressRoute].  Di conseguenza, solo le risorse nelle reti virtuali connesse ai tunnel da sito a sito o ExpressRoute possono accedere agli endpoint locali.

Per tutti questi scenari, le app in esecuzione in un ambiente del servizio app possono connettersi in modo sicuro ai vari server e risorse. Se il traffico in uscita dalle app viene eseguito in un ambiente del servizio app a endpoint privati nella stessa rete virtuale o connesso alla stessa rete virtuale, verrà eseguito solo il flusso attraverso la rete virtuale.  Il traffico in uscita verso gli endpoint privati non passa attraverso la rete Internet pubblica.

Un problema si applica al traffico in uscita da un ambiente del servizio app agli endpoint all'interno di una rete virtuale. Gli ambienti del servizio app non possono raggiungere endpoint di macchine virtuali che si trovano nella **stessa** subnet del ambiente del servizio app. Questa limitazione normalmente non dovrebbe essere un problema, se gli ambienti del servizio app vengono distribuiti in una subnet riservata per l'uso esclusivo da parte del ambiente del servizio app.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Requisiti per DNS e connettività in uscita
Per un corretto funzionamento dell'ambiente del servizio app, è necessario l'accesso in uscita ai vari endpoint. Un elenco completo degli endpoint esterni usati da un ambiente del servizio app è disponibile nella sezione "Requisiti della connettività di rete" dell'articolo [Configurazione di rete per ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Gli ambienti del servizio app richiedono un'infrastruttura DNS valida configurata per la rete virtuale.  Se la configurazione DNS viene modificata dopo la creazione di un ambiente del servizio app, gli sviluppatori possono forzare un ambiente del servizio app a prelevare la nuova configurazione DNS. Nella parte superiore del pannello di gestione ambiente del servizio app nel portale, selezionare l'icona **Riavvia** per attivare un riavvio dell'ambiente in sequenza, in modo che l'ambiente rilevi la nuova configurazione DNS.

È inoltre consigliabile configurare in anticipo tutti i server DNS personalizzati in VNET prima di creare un ambiente del servizio app.  Se la configurazione DNS di una rete virtuale viene modificata durante la creazione di un ambiente del servizio app, il processo di creazione del ambiente del servizio app non riesce. All'altra estremità di un gateway VPN, se è presente un server DNS personalizzato irraggiungibile o non disponibile, anche il processo di creazione ambiente del servizio app avrà esito negativo.

## <a name="connecting-to-a-sql-server"></a>Connessione a un'istanza di SQL Server
Una configurazione di SQL Server comune prevede un endpoint in ascolto sulla porta 1433:

![Endpoint di SQL Server][SqlServerEndpoint]

È possibile usare due approcci per limitare il traffico a questo endpoint:

* [Elenchi di controllo di accesso di rete][NetworkAccessControlLists] (ACL di rete)
* [Gruppi di sicurezza di rete][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Limitazione dell'accesso con un elenco di controllo di accesso di rete
È possibile proteggere la porta 1433 usando un elenco di controllo di accesso di rete.  Nell'esempio seguente vengono aggiunte le autorizzazioni di assegnazione agli indirizzi client provenienti dall'interno di una rete virtuale e viene bloccato l'accesso a tutti gli altri client.

![Esempio di elenco di controllo di accesso di rete (ACL)][NetworkAccessControlListExample]

Tutte le applicazioni eseguite in ambiente del servizio app, nella stessa rete virtuale del SQL Server, possono connettersi all'istanza di SQL Server. Usare l'indirizzo IP **interno di VNet** per la macchina virtuale SQL Server.  

La stringa di connessione di esempio seguente fa riferimento all'instanza di SQL Server usando l'indirizzo IP privato.

`Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient`

Anche se la macchina virtuale ha anche un endpoint pubblico, i tentativi di connessione di usare l'indirizzo IP pubblico verranno rifiutati a causa dell'ACL di rete. 

## <a name="restricting-access-with-a-network-security-group"></a>Limitazione dell'accesso con un gruppo di sicurezza di rete
In alternativa, per proteggere l'accesso è possibile usare un gruppo di sicurezza di rete.  I gruppi di sicurezza di rete possono essere applicati alle singole macchine virtuali o a una subnet contenente macchine virtuali.

Prima di tutto, è necessario creare un gruppo di sicurezza di rete:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

La limitazione dell'accesso al solo traffico interno VNet è semplice con un gruppo di sicurezza di rete.  Le regole predefinite in un gruppo di sicurezza di rete consentono l'accesso solo da altri client di rete nella stessa rete virtuale.

Di conseguenza, il blocco dell'accesso a SQL Server è semplice. È sufficiente applicare un gruppo di sicurezza di rete con le regole predefinite per le macchine virtuali che eseguono SQL Server o la subnet contenente le macchine virtuali.

L'esempio seguente mostra come applicare un gruppo di sicurezza di rete alla subnet contenente le macchine virtuali:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
```

Il risultato finale è un set di regole di sicurezza che bloccano l'accesso esterno, consentendo al contempo l'accesso interno VNet:

![Regole di sicurezza di rete predefinite][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Introduzione
Per iniziare a usare gli ambienti del servizio app, vedere [Introduzione all'ambiente del servizio app][IntroToAppServiceEnvironment]

Per informazioni dettagliate su come controllare il traffico in ingresso all'ambiente del servizio app, vedere [Controllo del traffico in ingresso a un ambiente del servizio app][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: /previous-versions/azure/virtual-network/virtual-networks-acl
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png