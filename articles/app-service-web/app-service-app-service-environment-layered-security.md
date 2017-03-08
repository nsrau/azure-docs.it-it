---
title: "Architettura di sicurezza su più livelli con ambienti del servizio app"
description: "Implementazione di un&quot;architettura di sicurezza su più livelli con ambienti del servizio app."
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 0fb02c13f99a8f4a46e0142c20da3b152c809b6b
ms.lasthandoff: 03/01/2017


---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementazione di un'architettura di sicurezza su più livelli con ambienti del servizio app
## <a name="overview"></a>Overview
Dato che gli ambienti del servizio app forniscono un ambiente di runtime isolato distribuito in una rete virtuale, gli sviluppatori possono creare un'architettura di sicurezza su più livelli offrendo livelli diversi di accesso alla rete per ogni livello applicazione fisico.

Un'esigenza comune è quella di nascondere i back-end delle API all'accesso a Internet generale e consentire alle API di essere chiamate solo dalle app Web upstream.  I [gruppi di sicurezza di rete (NSG)][NetworkSecurityGroups] possono essere usati nelle subnet contenenti ambienti del servizio app per limitare l'accesso pubblico alle applicazioni API.

Il diagramma seguente mostra un'architettura di esempio con un elemento WebAPI basato sull'app distribuita in un ambiente del servizio app.  Tre diverse istanze di app Web, distribuite in tre diversi ambienti del servizio app, eseguono chiamate back-end alla stessa app WebAPI.

![Architettura concettuale][ConceptualArchitecture] 

I segni più verdi indicano che il gruppo di sicurezza di rete nella subnet contenente "apiase" consente le chiamate in ingresso dalle app Web upstream, oltre che le chiamate da se stesso.  Lo stesso gruppo di sicurezza di rete, tuttavia, nega esplicitamente l'accesso al traffico in ingresso generale da Internet. 

Il resto di questo argomento illustra in dettaglio la procedura necessaria per configurare il gruppo di sicurezza di rete nella subnet contenente "apiase".

## <a name="determining-the-network-behavior"></a>Determinazione del comportamento della rete
Per conoscere le regole per la sicurezza della rete necessarie, si deve determinare a quali client di rete sarà consentito raggiungere l'ambiente del servizio app contenente l'app per le API e quali client verranno bloccati.

Poiché i [gruppi di sicurezza di rete][NetworkSecurityGroups] vengono applicati alle subnet e gli ambienti del servizio app vengono distribuiti nelle subnet, le regole contenute in un gruppo di sicurezza di rete si applicano a **tutte** le app in esecuzione in un ambiente del servizio app.  Usando l'architettura di esempio di questo articolo, una volta applicato un gruppo di sicurezza di rete alla subnet contenente "apiase", tutte le app in esecuzione nell'ambiente del servizio app "apiase" verranno protette dallo stesso set di regole di sicurezza. 

* **Determinare l'indirizzo IP in uscita dei chiamanti upstream:** quali sono gli indirizzi IP dei chiamanti upstream?  Sarà necessario consentire esplicitamente a questi indirizzi l'accesso nel gruppo di sicurezza di rete.  Poiché le chiamate tra gli ambienti del servizio app sono considerate chiamate "Internet", all'indirizzo IP in uscita assegnato a ciascuno dei tre ambienti del servizio app upstream deve essere consentito l'accesso nel gruppo di sicurezza di rete per la subnet "apiase".   Per altri dettagli su come determinare l'indirizzo IP in uscita per le app in esecuzione in un ambiente del servizio app, vedere l'articolo [Panoramica dell'architettura di rete][NetworkArchitecture].
* **L'app per le API back-end dovrà chiamare se stessa?**  Un aspetto delicato e a volte trascurato è lo scenario in cui l'applicazione back-end deve chiamare se stessa.  Se un'applicazione API back-end in un ambiente del servizio app deve chiamare se stessa, anche questa chiamata viene considerata una chiamata "Internet".  Nell'architettura di esempio è necessario consentire l'accesso anche dall'indirizzo IP in uscita dell'ambiente del servizio app "apiase".

## <a name="setting-up-the-network-security-group"></a>Configurazione del gruppo di sicurezza di rete
Una volta noto il set di indirizzi IP in uscita, il passaggio successivo consiste nel creare un gruppo di sicurezza di rete.  I gruppi di sicurezza di rete possono essere creati sia per le reti virtuali basate su Resource Manager che per le reti virtuali classiche.  Gli esempi seguenti illustrano la creazione e configurazione di un gruppo di sicurezza di rete in una rete virtuale classica usando Powershell.

Per l'architettura di esempio, gli ambienti si trovano negli Stati Uniti centro-meridionali e quindi viene creato un gruppo di sicurezza di rete vuoto in tale area:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Prima viene aggiunta una regola di consenso esplicita per l'infrastruttura di gestione di Azure, come indicato nell'articolo sul [traffico in ingresso][InboundTraffic] per gli ambienti del servizio app.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Quindi vengono aggiunte due regole per consentire le chiamate HTTP e HTTPS dal primo ambiente del servizio app upstream ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Cancellare e ripetere per il secondo e il terzo ambiente del servizio app upstream ("fe2ase"e "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Infine concedere l'accesso all'indirizzo IP in uscita dell'ambiente del servizio app dell'API back-end in modo che possa richiamare se stesso.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Non è necessario configurare nessuna altra regola di sicurezza di rete perché ogni gruppo di sicurezza di rete ha un set di regole predefinite che bloccano l'accesso in ingresso da Internet per impostazione predefinita.

L'elenco completo di regole nel gruppo di sicurezza di rete è mostrato di seguito.  Si noti che l'ultima regola, che è evidenziata, impedisce l'accesso in ingresso a tutti i chiamanti a cui non è stato esplicitamente concesso.

![Configurazione del gruppo di sicurezza di rete][NSGConfiguration] 

Il passaggio finale consiste nell'applicare il gruppo di sicurezza di rete alla subnet contenente l'ambiente del servizio app "apiase".  

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Con il gruppo di sicurezza di rete applicato alla subnet, solo ai tre ambienti del servizio app upstream e all'ambiente del servizio app contenente il back-end dell'API è consentito chiamare l'ambiente "apiase".

## <a name="additional-links-and-information"></a>Informazioni e collegamenti aggiuntivi
Tutti gli articoli e le procedure sugli ambienti del servizio app sono disponibili nel [file LEGGIMI per gli ambienti di servizio dell'applicazione](../app-service/app-service-app-service-environments-readme.md).

Informazioni sui [gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md). 

Informazioni sugli [indirizzi IP in uscita][NetworkArchitecture] e sugli ambienti del servizio app.

[Porte di rete][InboundTraffic] usate dagli ambienti del servizio app.

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[InboundTraffic]:  https://azure.microsoft.com/en-us/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png

