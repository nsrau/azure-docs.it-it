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
ms.date: 10/20/2016
ms.author: antonba
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7dfbf0e460dc8de13a4a71c1f925c93f6238df5d


---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Come usare Gestione API di Azure con le reti virtuali
Le reti virtuali di Azure (VNET) consente di posizionare le risorse di Azure in una rete instradabile non Internet a cui si controlla l'accesso. Queste reti possono quindi essere connesse alle reti locali con diverse tecnologie VPN. Per altre informazioni sulle reti virtuali di Azure, è possibile iniziare dalla [Panoramica sulla rete virtuale di Azure](../virtual-network/virtual-networks-overview.md).

Gestione API di Azure può essere connesso a una rete virtuale (VNET) così da poter accedere a servizi di back-end all'interno della rete e affinché il portale per sviluppatori e il gateway dell'API siano accessibili all'interno della rete.

## <a name="enable-vpn"> </a>Attivare la connessione VNET
> La connettività VNET è disponibile solo per i livelli **Premium** e **Sviluppatori**. Per spostarsi tra i livelli, aprire il servizio Gestione API nel portale di Azure classico e aprire la scheda **Scale and pricing** (Scalabilità e prezzi). Nella sezione **Piano tariffario** selezionare il livello Premium e fare clic su Salva.
> 
> 

Per abilitare la connettività VNET, aprire il servizio Gestione API nel portale di Azure e aprire la pagina **Rete virtuale**.

![Menu della rete virtuale di Gestione API][api-management-using-vnet-menu]

Selezionare il tipo di accesso da usare:

* **Esterno**: il gateway di Gestione API e il portale per gli sviluppatori sono accessibili dalla rete internet pubblica tramite un servizio di bilanciamento del carico esterno. Il gateway può accedere alle risorse all'interno della rete virtuale.

![Peering pubblico][api-management-vnet-public]

* **Interno**: il gateway di Gestione API e il portale per gli sviluppatori sono accessibili soltanto dalla rete virtuale tramite un servizio di bilanciamento del carico interno. Il gateway può accedere alle risorse all'interno della rete virtuale.

![Peering privato][api-management-vnet-private]

Verrà ora visualizzato un elenco di tutte le aree in cui viene eseguito il provisioning del servizio Gestione API. Selezionare una VNET e una subnet per ogni area. L'elenco viene popolato con le reti virtuali classiche e ARM disponibili nelle sottoscrizioni di Azure, impostate nell'area che si sta configurando.

![Selezionare una VPN][api-management-setup-vpn-select]

Fare clic su **Salva** nella parte superiore della schermata. 

> Non sarà possibile eseguire operazioni gestionali nel servizio Gestione API durante l'aggiornamento, ma il gateway di Gestione API e il portale per gli sviluppatori rimarranno disponibili.
> Si noti che l'indirizzo VIP dell'istanza di Gestione API può presumibilmente cambiare ogni volta che la VNET viene abilitata o disabilitata.
> 
> 

## <a name="enable-vnet-powershell"> </a>Abilitare la connessione VNET usando i cmdlet di PowerShell
È anche possibile abilitare la connettività VNET usando il cmdlet PowerShell [Set-AzureRmApiManagementVirtualNetworks](https://msdn.microsoft.com/library/mt619277.aspx).

## <a name="connect-vnet"> </a>Connettersi a un servizio Web ospitato all'interno di una rete virtuale
Dopo che il servizio Gestione API è stato connesso alla VNET, l'accesso ai servizi di back-end all'interno della rete virtuale non è diverso dall'accesso ai servizi pubblici. È sufficiente digitare l'indirizzo locale o il nome host (se è stato configurato un server DNS per la VNET) del servizio Web nel campo **URL del servizio Web** quando si crea una nuova API o se ne modifica una esistente.

![Aggiungere un'API dalla VPN][api-management-setup-vpn-add-api]

## <a name="custom-dns"> </a>Configurazione del server DNS personalizzato
Gestione API dipende da diversi servizi di Azure. Quando Gestione API è ospitata in una VNET con un server DNS personalizzato, deve poter risolvere i nomi host dei servizi di Azure. Vedere [queste](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) informazioni aggiuntive sulla configurazione del DNS personalizzato. Vedere la tabella relativa a diagrammi e porte sottostante per riferimento.

## <a name="ports-required"> </a>Porte necessarie per Gestione API
> Se una qualsiasi di queste porte non è disponibile Gestione API potrebbe non funzionare correttamente e potrebbe diventare inaccessibile. Il blocco di una o più di queste porte è il problema di configurazione più comune nell'uso di Gestione API in una rete virtuale.
> 
> 

Quando un'istanza del servizio Gestione API è ospitata in una rete virtuale, vengono usate le porte indicate nella tabella seguente.

| Porte | Direzione | Protocollo di trasporto | Scopo | Origine/Destinazione | Tipo di accesso |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |In ingresso |TCP |Comunicazione tra client e Gestione API |INTERNET / VIRTUAL_NETWORK |Esterno |
| 3443 |In ingresso |TCP |Endpoint di gestione |INTERNET / VIRTUAL_NETWORK |Esterno e interno |
| 80, 443 |In uscita |TCP |Dipendenza da Archiviazione di Azure e dal bus di servizio di Azure |VIRTUAL_NETWORK / INTERNET |Esterno e interno |
| 1433 |In uscita |TCP |Dipendenza da SQL di Azure |VIRTUAL_NETWORK / INTERNET |Esterno e interno |
| 9350, 9351, 9352, 9353, 9354 |In uscita |TCP |Dipendenza dal bus di servizio |VIRTUAL_NETWORK / INTERNET |Esterno e interno |
| 5671 |In uscita |AMQP |Dipendenza per il criterio Registra a Hub eventi |VIRTUAL_NETWORK / INTERNET |Esterno e interno |
| 6381, 6382, 6383 |In ingresso/In uscita |UDP |Dipendenza dalla cache Redis |VIRTUAL_NETWORK / VIRTUAL_NETWORK |Esterno e interno |
| 445 |In uscita |TCP |Dipendenza dalla condivisione file di Azure per GIT |VIRTUAL_NETWORK / INTERNET |Esterno e interno |

## <a name="limitations"> </a>Limitazioni
* Una subnet contenente le istanze di Gestione API non può contenere altri tipi di risorse di Azure.
* La subnet e il servizio di Gestione API devono essere nella stessa sottoscrizione.
* Una subnet contenente le istanze di gestione API non può essere spostata da una sottoscrizione all'altra.
* Quando si usa una rete virtuale interna sarà disponibile un solo indirizzo IP interno proveniente dall'intervallo indicato in [RFC 1918](https://tools.ietf.org/html/rfc1918), mentre non verrà fornito alcun indirizzo IP pubblico.
* Per le distribuzioni di Gestione API su più aree con reti virtuali interne configurate, gli utenti sono responsabili della gestione diretta del bilanciamento del carico poiché sono titolari del DNS.

## <a name="related-content"> </a>Contenuti correlati
* [Creare una rete virtuale con una connessione VPN da sito a sito nel portale di Azure][Creare una rete virtuale con una connessione VPN da sito a sito nel portale di Azure]
* [Come usare Controllo API per tenere traccia delle chiamate in Gestione API di Azure][Come usare Controllo API per tenere traccia delle chiamate in Gestione API di Azure]

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-private.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-public.png

[Abilitare connessioni VPN]: #enable-vpn
[Connettersi a un servizio Web dietro la VPN]: #connect-vpn
[Contenuti correlati]: #related-content


[Creare una rete virtuale con una connessione VPN da sito a sito nel portale di Azure]: ../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[Come usare Controllo API per tenere traccia delle chiamate in Gestione API di Azure]: api-management-howto-api-inspector.md



<!--HONumber=Nov16_HO3-->


