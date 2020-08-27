---
title: Controllare il traffico in ingresso V1
description: Informazioni su come controllare il traffico in ingresso in un ambiente del servizio app. Questo documento è rivolto solo ai clienti che usano l'ambiente del servizio app v1 legacy.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: fe9326ea9ebd5afe981b7ba6c34b1a5d51e084b0
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962061"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Come controllare il traffico in ingresso a un ambiente del servizio app
## <a name="overview"></a>Panoramica
Un ambiente del servizio app può essere creato in una rete virtuale Azure Resource Manager **o** in una [rete virtuale][virtualnetwork]del **modello di distribuzione** classica.  È possibile definire una nuova rete virtuale e una nuova subnet al momento della creazione di un ambiente del servizio app. È invece possibile creare un ambiente del servizio app in una rete virtuale preesistente e in una subnet preesistente.  Al 2016 giugno, gli ambienti può essere distribuito anche in reti virtuali che usano intervalli di indirizzi pubblici o spazi di indirizzi RFC1918 (indirizzi privati).  Per ulteriori informazioni, vedere [come creare un ambiente del servizio app][HowToCreateAnAppServiceEnvironment].

Creare sempre un ambiente del servizio app all'interno di una subnet. Una subnet fornisce un limite di rete che può essere usato per bloccare il traffico in ingresso dietro i dispositivi e i servizi upstream. Questa configurazione consente solo indirizzi IP upstream specifici per accettare il traffico HTTP e HTTPS.

Il traffico in ingresso e in uscita diretto verso e proveniente da una subnet è controllato tramite un [gruppo di sicurezza di rete][NetworkSecurityGroups]. Per controllare il traffico in ingresso, creare regole di sicurezza di rete in un gruppo di sicurezza di rete. Assegnare quindi al gruppo di sicurezza di rete la subnet contenente il ambiente del servizio app.

Quando si assegna un gruppo di sicurezza di rete a una subnet, il traffico in ingresso verso le app nel ambiente del servizio app è consentito o bloccato in base alle regole Consenti e nega definite nel gruppo di sicurezza di rete.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Porte di rete in ingresso usate in un ambiente del servizio app
Prima di bloccare il traffico di rete in ingresso con un gruppo di sicurezza di rete, è possibile stabilire il set di porte di rete obbligatorie e facoltative usate da un ambiente del servizio app.  Il blocco accidentale del traffico ad alcune porte può comportare una perdita di funzionalità nell'ambiente del servizio app.

Nell'elenco seguente sono incluse le porte utilizzate da un ambiente del servizio app. Tutte le porte sono di tipo **TCP**, se non indicato diversamente in modo chiaro:

* 454:  **porta obbligatoria** usata dall'infrastruttura di Azure per la gestione e la manutenzione degli ambienti del servizio app tramite TLS.  Non bloccare il traffico su questa porta.  Questa porta è sempre associata all'indirizzo VIP pubblico di un ambiente del servizio app.
* 455:  **porta obbligatoria** usata dall'infrastruttura di Azure per la gestione e la manutenzione degli ambienti del servizio app tramite TLS.  Non bloccare il traffico su questa porta.  Questa porta è sempre associata all'indirizzo VIP pubblico di un ambiente del servizio app.
* 80: porta predefinita per il traffico HTTP in ingresso alle app in esecuzione nei piani di servizio app in un ambiente del servizio app.  In un ambiente del servizio app abilitato al bilanciamento del carico interno, questa porta è associata all'indirizzo ILB dell'ambiente.
* 443: porta predefinita per il traffico TLS in ingresso verso le app eseguite nei piani di servizio app in un ambiente del servizio app.  In un ambiente del servizio app abilitato al bilanciamento del carico interno, questa porta è associata all'indirizzo ILB dell'ambiente.
* 21: canale di controllo per il servizio FTP.  Questa porta può essere bloccata in modo sicuro se FTP non è in uso.  In un ambiente del servizio app abilitato al bilanciamento del carico interno, questa porta è associata all'indirizzo ILB per un ambiente.
* 990: canale di controllo per il servizio FTPS.  Questa porta può essere bloccata in modo sicuro se FTPS non è in uso.  In un ambiente del servizio app abilitato al bilanciamento del carico interno, questa porta è associata all'indirizzo ILB per un ambiente.
* 10001-10020: canali di dati per il servizio FTP.  Come per il canale di controllo, queste porte possono essere bloccate senza problemi se non si utilizza FTP.  In un ambiente del servizio app abilitato al bilanciamento del carico interno, questa porta può essere associata all'indirizzo ILB dell'ambiente.
* 4016: porta usata per il debug remoto con Visual Studio 2012.  Questa porta può essere bloccata in modo sicuro se la funzionalità non è in uso.  In un ambiente del servizio app abilitato al bilanciamento del carico interno, questa porta è associata all'indirizzo ILB dell'ambiente.
* 4018: porta usata per il debug remoto con Visual Studio 2013.  Questa porta può essere bloccata in modo sicuro se la funzionalità non è in uso.  In un ambiente del servizio app abilitato al bilanciamento del carico interno, questa porta è associata all'indirizzo ILB dell'ambiente.
* 4020: porta usata per il debug remoto con Visual Studio 2015.  Questa porta può essere bloccata in modo sicuro se la funzionalità non è in uso.  In un ambiente del servizio app abilitato al bilanciamento del carico interno, questa porta è associata all'indirizzo ILB dell'ambiente.

## <a name="outbound-connectivity-and-dns-requirements"></a>Requisiti per DNS e connettività in uscita
Per un corretto funzionamento dell'ambiente del servizio app, è necessario l'accesso in uscita ai vari endpoint. Un elenco completo degli endpoint esterni usati da un ambiente del servizio app è disponibile nella sezione "Requisiti della connettività di rete" dell'articolo [Configurazione di rete per ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Gli ambienti del servizio app richiedono un'infrastruttura DNS valida configurata per la rete virtuale.  Se la configurazione DNS viene modificata dopo la creazione di un ambiente del servizio app, gli sviluppatori possono forzare un ambiente del servizio app a prelevare la nuova configurazione DNS.  Se si attiva un riavvio dell'ambiente in sequenza con l'icona **Riavvia** , l'ambiente preleva la nuova configurazione DNS. L'icona **Riavvia** si trova nella parte superiore del pannello di gestione ambiente del servizio app, nella [portale di Azure][NewPortal].

È inoltre consigliabile configurare in anticipo tutti i server DNS personalizzati in VNET prima di creare un ambiente del servizio app.  Se la configurazione DNS di una rete virtuale viene modificata durante la creazione di un ambiente del servizio app, il processo di creazione ambiente del servizio app avrà esito negativo.  Analogamente, se è presente un server DNS personalizzato irraggiungibile o non disponibile sull'altra estremità di un gateway VPN, anche il processo di creazione ambiente del servizio app avrà esito negativo.

## <a name="creating-a-network-security-group"></a>Creazione di un gruppo di sicurezza di rete
Per i dettagli sul funzionamento dei gruppi di sicurezza di rete, vedere le [informazioni][NetworkSecurityGroups] seguenti.  L'esempio di gestione dei servizi di Azure seguente illustra le caratteristiche principali dei gruppi di sicurezza di rete. Nell'esempio viene configurato e applicato un gruppo di sicurezza di rete a una subnet contenente un ambiente del servizio app.

**Nota:** I gruppi di sicurezza di rete possono essere configurati graficamente tramite il [portale di Azure](https://portal.azure.com) o tramite Azure PowerShell.

I gruppi di sicurezza di rete vengono inizialmente creati come un'entità autonoma associata a una sottoscrizione. Poiché i gruppi di sicurezza di rete vengono creati in un'area di Azure, creare il gruppo di sicurezza di rete nella stessa area del ambiente del servizio app.

Il comando seguente illustra la creazione di un gruppo di sicurezza di rete:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

Dopo aver creato il gruppo di sicurezza di rete, vengono aggiunte una o più regole di sicurezza di rete.  Poiché il set di regole può variare nel tempo, è necessario distanziare lo schema di numerazione usato per le priorità della regola. Questa procedura consente di inserire in modo semplice altre regole nel tempo.

Nell'esempio seguente una regola concede in modo esplicito l'accesso alle porte di gestione richieste dall'infrastruttura di Azure per gestire e mantenere un ambiente del servizio app.  Tutto il traffico di gestione passa attraverso TLS ed è protetto da certificati client. Anche se le porte vengono aperte, non sono accessibili da alcuna entità diversa dall'infrastruttura di gestione di Azure.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

Quando si blocca l'accesso alla porta 80 e 443 per "nascondere" un ambiente del servizio app dietro i dispositivi o i servizi upstream, ricordare l'indirizzo IP upstream.  Se, ad esempio, si usa un web application firewall (WAF), WAF avrà un proprio indirizzo IP o indirizzi IP. Il WAF li usa per l'inoltro del traffico a una ambiente del servizio app downstream.  È necessario usare questo indirizzo IP nel parametro *SourceAddressPrefix* di una regola di sicurezza di rete.

Nell'esempio seguente, il traffico in ingresso da un indirizzo IP upstream specifico è consentito in modo esplicito.  L'indirizzo *1.2.3.4* è usato come segnaposto per l'indirizzo IP di un firewall per applicazioni Web upstream.  Modificare il valore in modo che corrisponda all'indirizzo usato dal dispositivo o dal servizio upstream corrente.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Se è necessario il supporto FTP, utilizzare le seguenti regole come modello per concedere l'accesso alla porta di controllo FTP e alle porte del canale dati.  Poiché FTP è un protocollo con stato, potrebbe non essere possibile instradare il traffico FTP tramite un firewall o un dispositivo proxy HTTP/HTTPS tradizionale.  In questo caso, è necessario impostare *SourceAddressPrefix* su un valore diverso, ad esempio l'intervallo di indirizzi IP dei computer di sviluppo o di distribuzione in cui sono in esecuzione i client FTP. 

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP
```

**Nota:** l'intervallo di porte del canale dati può variare durante il periodo di anteprima.

Se si usa la funzionalità di debug remoto con Visual Studio, usare le regole seguenti per concedere l'accesso.  Esiste una regola separata per ogni versione supportata di Visual Studio poiché ogni versione USA una porta diversa per il debug remoto.  Come per l'accesso FTP, il traffico di debug remoto potrebbe non transitare correttamente attraverso un dispositivo proxy o un firewall per applicazioni Web tradizionale.  È quindi possibile impostare *SourceAddressPrefix* sull'intervallo di indirizzi IP dei computer di sviluppo che eseguono Visual Studio.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP
```

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Assegnazione di un gruppo di sicurezza di rete a una subnet
Un gruppo di sicurezza di rete ha una regola di sicurezza predefinita che nega l'accesso a tutto il traffico esterno. Quando si combina questa regola con le regole di sicurezza di rete sopra elencate, solo il traffico proveniente dagli intervalli di indirizzi di origine associati a un'azione *Consenti* sarà in grado di inviare il traffico alle app eseguite in un ambiente del servizio app.

Quando un gruppo di sicurezza di rete viene popolato con le regole di sicurezza, assegnarlo alla subnet che contiene il ambiente del servizio app.  Il comando di assegnazione fa riferimento a due nomi: il nome della rete virtuale in cui si trova il ambiente del servizio app e il nome della subnet in cui è stato creato il ambiente del servizio app.  

L'esempio seguente illustra l'assegnazione di un gruppo di sicurezza di rete a una subnet e a una rete virtuale:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

L'assegnazione è un'operazione a esecuzione prolungata che può richiedere alcuni minuti per il completamento. Quando l'assegnazione del gruppo di sicurezza di rete ha esito positivo, solo il traffico in ingresso che corrisponde alle regole di accesso *consentirà* di raggiungere le app nella ambiente del servizio app.

Per completezza, nell'esempio seguente viene illustrato come rimuovere e annullare l'associazione del gruppo di sicurezza di rete dalla subnet:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

## <a name="special-considerations-for-explicit-ip-ssl"></a>Considerazioni speciali su IP SSL esplicito
Se un'app è configurata con un indirizzo IP-SSL esplicito (applicabile *solo* a gli ambienti con un indirizzo VIP pubblico), anziché usare l'indirizzo IP predefinito del ambiente del servizio app, sia il traffico HTTP che quello https passano alla subnet sulle porte diverse dalle porte 80 e 443.

Per trovare la singola coppia di porte usata da ogni indirizzo IP-SSL, passare al portale e visualizzare il pannello UX dettagli del ambiente del servizio app.  Selezionare **tutte le impostazioni**  >  **indirizzi IP**.  Il pannello **indirizzi IP** Mostra una tabella di tutti gli indirizzi IP-SSL configurati in modo esplicito per la ambiente del servizio app. Il pannello mostra anche la coppia di porte speciale usata per indirizzare il traffico HTTP e HTTPS associato a ogni indirizzo IP-SSL.  Usare questa coppia di porte per i parametri DestinationPortRange quando si configurano le regole in un gruppo di sicurezza di rete.

Quando un'app in un ambiente del servizio app è configurata per l'uso di IP-SSL, i clienti esterni non vedranno o devono preoccuparsi del mapping speciale della coppia di porte.  Il traffico verso le app transiterà normalmente all'indirizzo IP SSL configurato.  La conversione alla coppia di porta speciale viene eseguita automaticamente internamente, durante il passaggio finale del traffico di routing alla subnet che contiene l'ambiente del servizio app. 

## <a name="getting-started"></a>Introduzione
Per iniziare a usare gli ambienti del servizio app, vedere [Introduzione a ambiente del servizio app][IntroToAppServiceEnvironment].

Per altre informazioni, vedere [connessione sicura alle risorse back-end da un ambiente del servizio app][SecurelyConnecttoBackend].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->