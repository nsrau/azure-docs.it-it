---
title: 'Collegamento di una rete virtuale a un circuito ExpressRoute: PowerShell: classico: Azure | Microsoft Docs'
description: Questo documento offre una panoramica sulle procedure di collegamento delle reti virtuali ai circuiti ExpressRoute usando il modello di distribuzione classico e PowerShell.
services: expressroute
documentationcenter: na
author: ganesr
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 9b53fd72-9b6b-4844-80b9-4e1d54fd0c17
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2017
ms.author: ganesr
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 8df8a4c6ff0897c821e13248e0494b17e1a4992d
ms.contentlocale: it-it
ms.lasthandoff: 07/27/2017

---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Collegare una rete virtuale a un circuito ExpressRoute usando PowerShell (versione classica)
> [!div class="op_single_selector"]
> * [Portale di Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfaccia della riga di comando di Azure](howto-linkvnet-cli.md)
> * [Video - Portale di Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (classico)](expressroute-howto-linkvnet-classic.md)
>

Questo articolo spiega come collegare le reti virtuali ai circuiti di Azure ExpressRoute usando il modello di distribuzione classico e PowerShell. Le reti virtuali possono trovarsi nella stessa sottoscrizione o appartenere a un'altra sottoscrizione.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Informazioni sui modelli di distribuzione di Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Prerequisiti di configurazione
1. È necessario scaricare la versione più recente dei moduli di Azure PowerShell. È possibile scaricare i moduli di PowerShell più recenti dalla sezione relativa a PowerShell della [pagina Download di Azure](https://azure.microsoft.com/downloads/). Per istruzioni dettagliate sulla configurazione del computer per l'uso dei moduli di Azure PowerShell, seguire le istruzioni contenute in [Come installare e configurare Azure PowerShell](/powershell/azure/overview) .
2. Prima di procedere con la configurazione, è necessario verificare i [prerequisiti](expressroute-prerequisites.md), i [requisiti di routing](expressroute-routing.md) e i [flussi di lavoro](expressroute-workflows.md).
3. È necessario avere un circuito ExpressRoute attivo.
   * Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md) e fare in modo che il circuito venga abilitato dal provider di connettività.
   * Assicurarsi di disporre del peering privato di Azure configurato per il circuito. Vedere l'articolo relativo alla [configurazione del routing](expressroute-howto-routing-classic.md) per istruzioni relative al routing.
   * Per abilitare la connettività end-to-end, assicurarsi che sia configurato il peering privato di Azure e sia attivo il peering BGP tra la rete e Microsoft.
   * È necessario disporre di una rete virtuale e di un gateway di rete virtuale creati e con provisioning completo. Seguire le istruzioni per [configurare una rete virtuale per ExpressRoute](expressroute-howto-vnet-portal-classic.md).

È possibile collegare fino a 10 reti virtuali a un circuito ExpressRoute. Tutte le reti virtuali devono trovarsi nella stessa area geopolitica. È possibile collegare un numero maggiore di reti virtuali al circuito ExpressRoute o collegare reti virtuali che non sono presenti in altre aree geopolitiche se è stato abilitato il componente aggiuntivo ExpressRoute Premium. Per altre informazioni sul componente aggiuntivo Premium, vedere le [domande frequenti](expressroute-faqs.md) .

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Collegare una rete virtuale della stessa sottoscrizione a un circuito
È possibile collegare una rete virtuale a un circuito ExpressRoute usando il cmdlet seguente. Prima di eseguire il cmdlet, assicurarsi che il gateway di rete virtuale sia stato creato e sia pronto per il collegamento.

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
    Provisioned

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Collegare una rete virtuale di un'altra sottoscrizione a un circuito
È possibile condividere un circuito ExpressRoute tra più sottoscrizioni. La figura seguente mostra un semplice schema del funzionamento della condivisione di circuiti ExpressRoute tra più sottoscrizioni.

Ciascuno dei cloud più piccoli nel cloud di grandi dimensioni viene usato per rappresentare le sottoscrizioni appartenenti a reparti diversi di un'organizzazione. Ciascun reparto dell'organizzazione può usare la propria sottoscrizione per distribuire i servizi, ma i reparti possono condividere un singolo circuito ExpressRoute per la connessione alla rete locale. Un singolo reparto (in questo esempio, IT) può possedere il circuito ExpressRoute. Altre sottoscrizioni all'interno dell'organizzazione possono usare il circuito ExpressRoute.

> [!NOTE]
> I costi relativi a connettività e larghezza di banda per il circuito dedicato saranno addebitati al proprietario del circuito ExpressRoute. Tutte le reti virtuali condividono la stessa larghezza di banda.
> 
> 

![Connettività tra sottoscrizioni](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Amministrazione
Il *proprietario del circuito* è l'amministratore o il coamministratore della sottoscrizione in cui viene creato il circuito ExpressRoute. Il proprietario del circuito può autorizzare gli amministratori o i coamministratori di altre sottoscrizioni, indicati come *utenti del circuito*, a usare il proprio circuito dedicato. Gli utenti del circuito autorizzati a usare il circuito ExpressRoute dell'organizzazione possono collegare la rete virtuale della propria sottoscrizione al circuito ExpressRoute dopo aver ricevuto l'autorizzazione.

Il proprietario del circuito ha la facoltà di modificare e revocare le autorizzazioni in qualsiasi momento. La revoca dell'autorizzazione comporterà l'eliminazione di tutti i collegamenti dalla sottoscrizione di cui è stato revocato l'accesso.

### <a name="circuit-owner-operations"></a>Operazioni del proprietario del circuito

**Creazione di un'autorizzazione**

Il proprietario del circuito autorizza gli amministratori di altre sottoscrizioni a usare il circuito specificato. Nell'esempio seguente l'amministratore del circuito (Contoso IT) abilita l'amministratore di un'altra sottoscrizione (sviluppo/test) per collegare fino a due reti virtuali al circuito. L'amministratore di Contoso IT esegue l'abilitazione specificando l'ID Microsoft di sviluppo/test. Il cmdlet non invia messaggi di posta elettronica all'ID di Microsoft specificato. Il proprietario del circuito deve indicare in modo esplicito al proprietario dell'altra sottoscrizione che l'autorizzazione è stata completata.

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

**Verifica delle autorizzazioni**

Il proprietario del circuito può esaminare tutte le autorizzazioni rilasciate in un particolare circuito eseguendo il cmdlet seguente:

    Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"

    Description         : EngineeringTeam
    Limit               : 3
    LinkAuthorizationId : ####################################
    MicrosoftIds        : engadmin@contoso.com
    Used                : 1

    Description         : MarketingTeam
    Limit               : 1
    LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    MicrosoftIds        : marketingadmin@contoso.com
    Used                : 0

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : salesadmin@contoso.com
    Used                : 2


**Aggiornamento delle autorizzazioni**

Il proprietario del circuito può modificare le autorizzazioni usando il cmdlet seguente:

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


**Eliminazione delle autorizzazioni**

Il proprietario del circuito può revocare o eliminare le autorizzazioni dell'utente eseguendo il cmdlet seguente:

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>Operazioni dell'utente del circuito

**Verifica delle autorizzazioni**

L'utente del circuito può esaminare le autorizzazioni usando il cmdlet seguente:

    Get-AzureAuthorizedDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : ContosoIT
    Location                         : Washington DC
    MaximumAllowedLinks              : 2
    ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled
    UsedLinks                        : 0

**Riscatto delle autorizzazioni dei collegamenti**

L'utente del circuito può eseguire il cmdlet seguente per riscattare un'autorizzazione di collegamento:

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

Eseguire questo comando nella sottoscrizione appena collegata per la rete virtuale:

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).


