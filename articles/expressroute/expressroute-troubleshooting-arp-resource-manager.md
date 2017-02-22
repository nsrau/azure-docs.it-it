---
title: Recupero di tabelle ARP - Resource Manager - Risoluzione dei problemi di Azure ExpressRoute | Documentazione Microsoft
description: Questa pagina fornisce istruzioni su come ottenere tabelle ARP tabelle per un circuito ExpressRoute
documentationcenter: na
services: expressroute
author: ganesr
manager: carolz
editor: tysonn
ms.assetid: 0a6bf1d5-6baf-44dd-87d3-1ebd2fd08bdc
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: ganesr
translationtype: Human Translation
ms.sourcegitcommit: 1a4206c80bc3581034b140de0003c64556b90303
ms.openlocfilehash: 2a5a12899ea3bcc89be0244e252c552515f57150


---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Recupero di tabelle ARP nel modello di distribuzione Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell - Gestione risorse](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - Classico](expressroute-troubleshooting-arp-classic.md)
> 
> 

Questo articolo illustra i passaggi per apprendere le tabelle ARP per il circuito ExpressRoute. 

> [!IMPORTANT]
> Questo documento è progettato per aiutare l'utente a rilevare e risolvere i problemi semplici. Non sostituisce tuttavia il supporto tecnico Microsoft. Se non si riesce a risolvere il problema tramite la procedura descritta di seguito, è necessario aprire un ticket di assistenza al [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>ARP (Address Resolution Protocol) e tabelle ARP
ARP (Address Resolution Protocol) è un protocollo di livello 2 definito in [RFC 826](https://tools.ietf.org/html/rfc826). Il protocollo ARP viene usato per mappare l'indirizzo Ethernet (indirizzo MAC) con un indirizzo IP.

La tabella ARP fornisce un mapping dell'indirizzo ipv4 e dell'indirizzo MAC per un particolare peering. La tabella ARP del peering di un circuito ExpressRoute fornisce le informazioni seguenti per ogni interfaccia (primaria e secondaria):

1. Mapping dell'indirizzo IP dell'interfaccia del router locale all'indirizzo MAC
2. Mapping dell'indirizzo IP dell'interfaccia del router di ExpressRoute all'indirizzo MAC
3. Età del mapping

Le tabelle ARP consentono di convalidare la configurazione di livello 2 e risoluzione dei problemi di connettività di base di livello 2. 

Tabella ARP di esempio: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


La sezione seguente fornisce informazioni su come visualizzare le tabelle ARP visualizzate tramite i router perimetrali di ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Prerequisiti per l'apprendimento delle tabelle ARP
Prima di procedere, verificare che siano presenti gli elementi seguenti:

* Un circuito ExpressRoute valido configurato con almeno un peer. Il circuito deve essere completamente configurato dal provider di connettività. L'utente o il provider di connettività deve aver configurato almeno un peer (privato di Azure, pubblico di Azure e Microsoft) su questo circuito.
* Gli intervalli degli indirizzi IP usati per la configurazione del peer (privato di Azure, pubblico di Azure e Microsoft). Consultare gli esempi di assegnazione dell'indirizzo IP in [Requisiti per il routing di ExpressRoute](expressroute-routing.md) per ottenere informazioni sul mapping degli indirizzi IP verso le interfacce sul lato utente e sul lato ExpressRoute. È possibile ottenere informazioni sulla configurazione del peering consultando la [pagina sulla configurazione del peering di ExpressRoute](expressroute-howto-routing-arm.md).
* Informazioni dal team di rete/provider di connettività sugli indirizzi MAC delle interfacce usate con questi indirizzi IP.
* È necessario disporre del modulo PowerShell più recente per Azure (versione 1.50 o successiva).

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Ottenere tabelle ARP per il circuito ExpressRoute
Questa sezione fornisce istruzioni su come visualizzare le tabelle ARP per il peering tramite PowerShell. Prima di procedere all'elaborazione, l'utente o il provider di connettività devono aver configurato il peering. Ogni circuito ha due percorsi (primario e secondario). È possibile controllare la tabella ARP di ogni percorso in modo indipendente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelle ARP per il peering privato di Azure
Il cmdlet seguente fornisce le tabelle ARP per il peering privato di Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Di seguito è illustrato un esempio di output per uno dei percorsi

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabelle ARP per il peering pubblico di Azure
Il cmdlet seguente fornisce le tabelle ARP per il peering pubblico di Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Di seguito è illustrato un esempio di output per uno dei percorsi

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabelle ARP per il peering di Microsoft
Il cmdlet seguente fornisce le tabelle ARP per il peering di Microsoft

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Di seguito è illustrato un esempio di output per uno dei percorsi

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Procedura: Come usare queste informazioni
La tabella ARP di un peer può essere usata per determinare la connettività e la configurazione di livello 2 valide. Questa sezione offre una panoramica dell'aspetto delle tabelle ARP in scenari diversi.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabella ARP quando un circuito è in stato operativo (stato previsto)
* La tabella ARP conterrà una voce per il lato locale con un indirizzo IP e un indirizzo MAC valido e una voce simile per il lato Microsoft. 
* L'ultimo ottetto dell'indirizzo IP locale sarà sempre un numero dispari.
* L'ultimo ottetto dell'indirizzo IP Microsoft sarà sempre un numero pari.
* Lo stesso indirizzo MAC verrà visualizzato sul lato Microsoft per tutti i 3 peer (principale/secondario). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabella ARP quando il lato locale/provider di connettività presenta problemi
* Verrà visualizzata solo una voce nella tabella ARP. Viene mostrato il mapping tra gli indirizzi MAC e IP usati sul lato Microsoft. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Aprire una richiesta di supporto al provider di connettività per il debug di questi problemi. 
> 
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabella ARP quando il lato Microsoft presenta problemi
* Se sono presenti problemi sul lato Microsoft, non verrà visualizzata la tabella ARP illustrata per il peering. 
* Aprire un ticket di assistenza al [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Specificare che si è riscontrato un problema di connettività di livello 2. 

## <a name="next-steps"></a>Passaggi successivi
* Convalidare le configurazioni di livello 3 per il circuito ExpressRoute
  * Ottenere un riepilogo del routing per determinare lo stato delle sessioni BGP 
  * Ottenere la tabella del routing per stabilire i prefissi pubblicati in ExpressRoute
* Convalidare il trasferimento dei dati controllando i byte in ingresso/uscita
* Aprire un ticket di assistenza al [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se continuano a verificarsi problemi.




<!--HONumber=Jan17_HO5-->


