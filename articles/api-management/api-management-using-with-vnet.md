---
title: Come usare Gestione API di Azure con le reti virtuali
description: Informazioni su come configurare una connessione a una rete virtuale in Gestione API di Azure e usarla per accedere ai servizi Web.
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 64b58f7b-ca22-47dc-89c0-f6bb0af27a48
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 40d9b0ee5a24e5503de19daa030bf1e8169dec24
ms.openlocfilehash: 58be070ea5d5f4ea9f6d9453a1adcc23c4b9b2a2


---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Come usare Gestione API di Azure con le reti virtuali
Le reti virtuali di Azure (VNET) consentono di posizionare le risorse di Azure in una rete instradabile non Internet a cui si controlla l'accesso. Queste reti possono quindi essere connesse alle reti locali utilizzando diverse tecnologie VPN. Per altre informazioni sulle reti virtuali di Azure, è possibile iniziare dalla [Panoramica sulla rete virtuale di Azure](../virtual-network/virtual-networks-overview.md).

Gestione API di Azure può essere connesso a una rete virtuale (VNET) così da poter accedere a servizi di back-end all'interno della rete e affinché il portale per sviluppatori e il gateway dell'API siano accessibili all'interno della rete.

> [!NOTE]
> Gestione API di Azure supporta le reti virtuali classiche e Azure Resource Manager.
> 
> 

## <a name="enable-vpn"> </a>Attivare la connessione VNET
> [!NOTE]
> La connettività della rete virtuale è disponibile per i livelli **Premium** e **Sviluppatore**. Per spostarsi tra i livelli, aprire il servizio Gestione API nel portale di Azure e quindi la scheda **Scale and pricing** (Scalabilità e prezzi). Nella sezione **Piano tariffario** selezionare il livello Premium o Sviluppatore e fare clic su Salva.
>

Per abilitare la connettività della rete virtuale, aprire il servizio Gestione API nel portale di Azure e quindi la pagina **Rete virtuale**.

![Menu della rete virtuale di Gestione API][api-management-using-vnet-menu]

Selezionare il tipo di accesso da usare:

* **Esterno**: il gateway di Gestione API e il portale per gli sviluppatori sono accessibili dalla rete internet pubblica tramite un servizio di bilanciamento del carico esterno. Il gateway può accedere alle risorse all'interno della rete virtuale.

![Peering pubblico][api-management-vnet-public]

* **Interno**: il gateway di Gestione API e il portale per gli sviluppatori sono accessibili soltanto dalla rete virtuale tramite un servizio di bilanciamento del carico interno. Il gateway può accedere alle risorse all'interno della rete virtuale.

![Peering privato][api-management-vnet-private]

Verrà ora visualizzato un elenco di tutte le aree in cui viene eseguito il provisioning del servizio Gestione API. Selezionare una VNET e una subnet per ogni area. L'elenco viene popolato con le reti virtuali classiche e Resource Manager disponibili nelle sottoscrizioni di Azure, impostate nell'area che si sta configurando.

> [!NOTE]
> **Endpoint di servizio** nel diagramma precedente include Gateway/Proxy, Portale di pubblicazione, Portale per sviluppatori, GIT e l'endpoint di gestione diretta.
> **Endpoint di gestione** nel diagramma precedente è l'endpoint ospitato nel servizio per la gestione della configurazione tramite il portale di Azure e Powershell.
> Inoltre si noti che, anche se il diagramma mostra gli indirizzi IP per i vari endpoint, il servizio Gestione API risponde **solo** ai relativi nomi host configurati.

> [!IMPORTANT]
> Quando si distribuisce un'istanza di gestione API di Azure a una rete virtuale Resource Manager, il servizio deve essere in una subnet dedicata che non contiene altre risorse, a eccezione di istanze di gestione API di Azure. Se si tenta di distribuire un'istanza di gestione API di Azure a una subnet della rete virtuale Resource Manager contenente altre risorse, la distribuzione avrà esito negativo.
> 
> 

![Selezionare una VPN][api-management-setup-vpn-select]

Fare clic su **Salva** nella parte superiore della schermata. 

> [!NOTE]
> L'indirizzo VIP dell'istanza di Gestione API può cambiare ogni volta che la rete virtuale viene abilitata o disabilitata.  
> L'indirizzo VIP viene modificato quando Gestione API passa da **Esterna** a **Interna** o viceversa
> 


> [!IMPORTANT] 
> Se si rimuove Gestione API da una rete virtuale o si modifica quella in cui è distribuito, la rete virtuale utilizzata in precedenza può rimanere bloccata fino a 4 ore. Durante questo periodo non sarà possibile eliminare la rete virtuale o distribuirvi una nuova risorsa.

## <a name="enable-vnet-powershell"> </a>Abilitare la connessione della rete virtuale usando i cmdlet di PowerShell
È inoltre possibile abilitare la connettività della rete virtuale utilizzando i cmdlet di PowerShell

* **Creare un servizio Gestione API all'interno di una rete virtuale**: usare il cmdlet [New-AzureRmApiManagement](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.apimanagement/v3.1.0/new-azurermapimanagement) per creare un servizio Gestione API di Azure all'interno di una rete virtuale.

* **Distribuire un servizio Gestione API esistente all'interno di una rete virtuale**: usare il cmdlet [Update-AzureRmApiManagementDeployment](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.apimanagement/v3.1.0/update-azurermapimanagementdeployment) per spostare un servizio Gestione API di Azure esistente all'interno di una rete virtuale.

## <a name="connect-vnet"> </a>Connettersi a un servizio Web ospitato all'interno di una rete virtuale
Dopo che il servizio Gestione API è stato connesso alla VNET, l'accesso ai servizi di back-end all'interno della rete virtuale non è diverso dall'accesso ai servizi pubblici. È sufficiente digitare l'indirizzo locale o il nome host (se è stato configurato un server DNS per la VNET) del servizio Web nel campo **URL del servizio Web** quando si crea una nuova API o se ne modifica una esistente.

![Aggiungere un'API dalla VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Problemi comuni di configurazione di rete
Di seguito è riportato un elenco di problemi di configurazione comuni che possono verificarsi durante la distribuzione del servizio Gestione API in una rete virtuale.

* **Installazione di server DNS personalizzata**: il servizio Gestione API dipende da vari servizi di Azure. Quando Gestione API è ospitata in una rete virtuale con un server DNS personalizzato, deve risolvere i nomi host dei servizi di Azure. Vedere [queste](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) informazioni aggiuntive sulla configurazione del DNS personalizzato. Vedere la tabella delle porte e altri requisiti di rete per riferimento.

> [!IMPORTANT]
> Se si utilizza un Server DNS personalizzato per la rete virtuale, è consigliabile impostarlo **prima** di distribuirvi un servizio Gestione API. In caso contrario, è necessario riavviare il servizio cloud che ospita il servizio in modo che rilevi le nuove impostazioni del server DNS.
> 

* **Porte necessarie per il servizio Gestione API**: il traffico in ingresso e in uscita nella subnet in cui viene distribuita la gestione delle API può essere controllato usando il [gruppo di sicurezza di rete][Network Security Group]. Se una qualsiasi di queste porte non è disponibile, Gestione API potrebbe non funzionare correttamente e potrebbe diventare inaccessibile. Il blocco di una o più di tali porte è un problema di configurazione comune nell'uso di Gestione API in una rete virtuale.

Quando un'istanza del servizio Gestione API è ospitata in una rete virtuale, vengono usate le porte indicate nella tabella seguente.

| Porte di origine/destinazione | Direzione | Protocollo di trasporto | Scopo | Origine/Destinazione | Tipo di accesso |
| --- | --- | --- | --- | --- | --- |
| 80, 443/80, 443 |In ingresso |TCP |Comunicazione tra client e Gestione API |INTERNET / VIRTUAL_NETWORK |Esterno |
| */3443 |In ingresso |TCP |Endpoint di gestione per il portale di Azure e PowerShell |INTERNET / VIRTUAL_NETWORK |Esterno e interno |
| 80, 443/80, 443 |In uscita |TCP |Dipendenza da Archiviazione di Azure e dal bus di servizio di Azure |VIRTUAL_NETWORK / INTERNET |Esterno e interno |
| 1433/1433 |In uscita |TCP |Dipendenza da SQL di Azure |VIRTUAL_NETWORK / INTERNET |Esterno e interno |
| 9350 - 9354/ 9350 - 9354 |In uscita |TCP |Dipendenza dal bus di servizio |VIRTUAL_NETWORK / INTERNET |Esterno e interno |
| 5671/5671 |In uscita |AMQP |Dipendenza per il criterio Registra a Hub eventi |VIRTUAL_NETWORK / INTERNET |Esterno e interno |
| 6381 - 6383/6381 - 6383 |In ingresso e in uscita |UDP |Dipendenza dalla cache Redis |VIRTUAL_NETWORK / VIRTUAL_NETWORK |Esterno e interno |-
| * / 445 |In uscita |TCP |Dipendenza dalla condivisione file di Azure per GIT |VIRTUAL_NETWORK / INTERNET |Esterno e interno |
| * / * | In ingresso |TCP |Bilanciamento del carico di infrastruttura di Azure | VIRTUAL_NETWORK/AZURE_LOADBALANCER |Esterno e interno |

* **Funzionalità SSL**: per abilitare la creazione e la convalida della catena di certificati SSL, il servizio Gestione API richiede la connettività di rete in uscita a ocsp.msocsp.com, mscrl.microsoft.com e crl.microsoft.com.

* **Installazione di Express Route**: secondo una diffusa configurazione, i clienti definiscono la propria route predefinita (0.0.0.0/0) verso la quale viene forzato il traffico Internet in uscita invece di far passare il flusso localmente. Questo flusso di traffico interrompe sempre la connettività con Gestione API di Azure perché il traffico in uscita è bloccato in locale o convertito tramite NAT in un set non riconoscibile di indirizzi che non usano più i diversi endpoint di Azure. La soluzione consiste nel definire una o più route definite dall'utente ([UDR][UDRs], User Defined Routes) o nella subnet contenente il servizio Gestione API di Azure. Una route UDR definisce le route specifiche della subnet che verranno accettate invece della route predefinita.
  Se possibile, è consigliabile utilizzare la seguente configurazione:
 * La configurazione di ExpressRoute annuncia 0.0.0.0/0 e per impostazione predefinita esegue il tunneling forzato di tutto il traffico in uscita in un ambiente locale.
 * L'UDR applicata alla subnet contenente il servizio Gestione API di Azure definisce 0.0.0.0/0 con un tipo di hop successivo di Internet.
 L'effetto combinato di questi passaggi è che il livello di subnet UDR avrà la precedenza sul tunneling forzato di ExpressRoute, garantendo l'accesso a Internet in uscita dal servizio Gestione API di Azure.

>[!WARNING]  
>Gestione API di Azure non è supportato con le configurazioni di ExpressRoute che **annunciano erroneamente route dal percorso di peering pubblico al percorso di peering privato**. Le configurazioni di ExpressRoute che dispongono di peering pubblico configurato, riceveranno gli annunci di route da Microsoft per un elevato numero di intervalli di indirizzi IP di Microsoft Azure. Se questi intervalli di indirizzi vengono annunciati in modo non corretto nel percorso di peering privato, il risultato finale è che tutti i pacchetti di rete in uscita dalla subnet dell'istanza di Gestione API di Azure verranno erroneamente sottoposti a tunneling forzato verso l'infrastruttura di rete locale del cliente. Questo flusso di rete interromperà il servizio Gestione API di Azure. La soluzione a questo problema consiste nell'interrompere l’annuncio di più route dal percorso di peering pubblico al percorso di peering privato.

## <a name="limitations"> </a>Limitazioni
* Una subnet contenente le istanze di Gestione API non può contenere altri tipi di risorse di Azure.
* La subnet e il servizio di Gestione API devono essere nella stessa sottoscrizione.
* Una subnet contenente le istanze di gestione API non può essere spostata da una sottoscrizione all'altra.
* Quando si usa una rete virtuale interna sarà disponibile un solo indirizzo IP interno proveniente dall'intervallo indicato in [RFC 1918](https://tools.ietf.org/html/rfc1918), mentre non verrà fornito alcun indirizzo IP pubblico.
* Per le distribuzioni di Gestione API su più aree con reti virtuali interne configurate, gli utenti sono responsabili della gestione diretta del bilanciamento del carico poiché sono titolari del DNS.


## <a name="related-content"> </a>Contenuti correlati
* [Connessione di una rete virtuale back-end tramite Gateway VPN][Different topologies to connect to Vpn Gateway]
* [Connessione di una rete virtuale da modelli di distribuzione differenti](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Come usare Controllo API per tenere traccia delle chiamate in Gestione API di Azure](api-management-howto-api-inspector.md)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-private.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-public.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Different topologies to connect to Vpn Gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site-connections
[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/virtual-networks-nsg.md



<!--HONumber=Jan17_HO2-->


