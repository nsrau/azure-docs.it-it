---
title: 'Modificare un circuito ExpressRoute: PowerShell: Azure classico| Microsoft Docs'
description: Questo articolo illustra i passaggi per verificare lo stato, aggiornare o eliminare ed effettuare il deprovisioning del circuito del modello di distribuzione classica ExpressRoute.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0134d242-6459-4dec-a2f1-4657c3bc8b23
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 457bb74fa15d31fecbf668038ac880cafb8a897d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Modificare un circuito ExpressRoute mediante PowerShell (versione classica)

> [!div class="op_single_selector"]
> * [portale di Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfaccia della riga di comando di Azure](howto-circuit-cli.md)
> * [Video - Portale di Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (classic)](expressroute-howto-circuit-classic.md) (PowerShell (classico))
>

L'articolo illustra anche le procedure di controllo di stato, aggiornamento, eliminazione e deprovisioning di un circuito ExpressRoute.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Informazioni sui modelli di distribuzione di AzureAbout Azure deployment models**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Installare le versioni più recenti dei moduli di PowerShell per Gestione dei servizi di Azure. Per istruzioni dettagliate sulla configurazione del computer per l'uso con i moduli di Azure PowerShell, vedere [Getting started with Azure PowerShell cmdlets](/powershell/azure/overview) (Introduzione ai cmdlet di Azure PowerShell).

## <a name="get-the-status-of-a-circuit"></a>Ottenere lo stato di un circuito

È possibile recuperare queste informazioni in qualsiasi momento usando il cmdlet `Get-AzureCircuit` . Se si effettua la chiamata senza parametri, verranno elencati tutti i circuiti.

```powershell
Get-AzureDedicatedCircuit

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled

Bandwidth                        : 1000
CircuitName                      : MyAsiaCircuit
Location                         : Singapore
ServiceKey                       : #################################
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

È possibile ottenere informazioni su un circuito ExpressRoute specifico passando la chiave di servizio come parametro alla chiamata.

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo, ad esempio, questo comando:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Modificare un circuito

È possibile modificare determinate proprietà di un circuito ExpressRoute senza conseguenze per la connettività.

È possibile eseguire le attività seguenti senza tempi di inattività:

* Abilitare o disabilitare un componente aggiuntivo ExpressRoute Premium per il circuito ExpressRoute.
* Aumentare la larghezza di banda del circuito ExpressRoute, a condizione che sulla porta sia disponibile capacità. Il downgrade della larghezza di banda di un circuito non è supportato. 
* Modificare il piano di misurazione da Dati a consumo a Dati senza limiti. La modifica del piano di misurazione da Dati senza limiti a Dati a consumo non è supportata.
* È possibile abilitare e disabilitare l'opzione *Consenti operazioni classiche*.

Per altre informazioni su limiti e limitazioni, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md) .

### <a name="enable-the-expressroute-premium-add-on"></a>Abilitare il componente aggiuntivo ExpressRoute Premium

È possibile abilitare il componente aggiuntivo ExpressRoute Premium per il circuito esistente usando il cmdlet di PowerShell seguente:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

Il circuito avrà le funzionalità del componente aggiuntivo ExpressRoute Premium abilitate. La fatturazione della funzionalità del componente aggiuntivo Premium inizia non appena il comando viene eseguito correttamente.

### <a name="disable-the-expressroute-premium-add-on"></a>Disabilitare il componente aggiuntivo ExpressRoute Premium

> [!IMPORTANT]
> Questa operazione può avere esito negativo se si usano più risorse di quelle consentite per il circuito standard.
> 
> 

#### <a name="considerations"></a>Considerazioni

* Assicurarsi che il numero di reti virtuali collegate al circuito sia minore di 10 prima di eseguire il downgrade da premium a standard. Altrimenti, la richiesta di aggiornamento avrà esito negativo e verranno fatturate le tariffe Premium.
* È necessario scollegare tutte le reti virtuali in altre aree geopolitiche. Altrimenti, la richiesta di aggiornamento avrà esito negativo e verranno fatturate le tariffe Premium.
* La tabella di route deve includere meno di 4.000 route per il peering privato. Se la tabella di route include più di 4.000 route, la sessione BGP viene eliminata e non sarà riabilitata finché il numero di prefissi pubblicati non scenderà al di sotto di 4.000.

#### <a name="to-disable-the-premium-add-on"></a>Per disabilitare il componente aggiuntivo Premium

È possibile disabilitare il componente aggiuntivo ExpressRoute Premium per il circuito esistente usando il cmdlet di PowerShell seguente:

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>Aggiornare la larghezza di banda del circuito ExpressRoute

Per le opzioni relative alla larghezza di banda supportate per il provider, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md) . È possibile scegliere qualsiasi dimensione maggiore della dimensione del circuito esistente, a condizione che sia consentita dalla porta fisica in cui viene creato il circuito.

> [!IMPORTANT]
> Se la capacità sulla porta esistente non è sufficiente, potrebbe essere necessario ricreare il circuito ExpressRoute. Il circuito non può essere aggiornato se in tale posizione non è disponibile capacità aggiuntiva.
>
> Non è possibile ridurre la larghezza di banda di un circuito ExpressRoute senza interruzioni. Il downgrade della larghezza di banda richiede il deprovisioning del circuito ExpressRoute e quindi il provisioning di un nuovo circuito ExpressRoute.
> 
> 

#### <a name="resize-a-circuit"></a>Ridimensionare un circuito

Dopo aver stabilito le dimensioni necessarie, usare il comando seguente per ridimensionare il circuito:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Dopo che il circuito è stato ridimensionato su lato di competenza di Microsoft, sarà necessario contattare il provider di connettività perché aggiorni le configurazioni corrispondenti in base a questa modifica. La fatturazione inizierà per la larghezza di banda aggiornata da questo punto in poi.

Se quando si aumenta la larghezza di banda del circuito viene visualizzato l'errore seguente, significa che la larghezza di banda rimasta nella porta fisica in cui è stato creato il circuito esistente non è sufficiente. È necessario eliminare questo circuito e crearne uno nuovo delle dimensioni necessarie.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Effettuare il deprovisioning ed eliminare un circuito

### <a name="considerations"></a>Considerazioni

* Affinché l'operazione abbia esito positivo, è necessario scollegare tutte le reti virtuali dal circuito ExpressRoute. Se l'operazione ha esito negativo, controllare se sono presenti reti virtuali collegate al circuito.
* Se lo stato di provisioning del provider del servizio del circuito ExpressRoute è **Provisioning in corso** o **Provisioning eseguito**, è necessario collaborare con il provider di servizi per eseguire il deprovisioning del circuito sul lato del provider. Le risorse continuano a essere riservate e la fatturazione continuerà a essere applicata finché il provider di servizi non avrà completato il deprovisioning del circuito e inviato una notifica a Microsoft.
* Se il provider di servizi ha eseguito il deprovisioning del circuito, ovvero lo stato di provisioning del provider di servizi è impostato su **Senza provisioning**, è possibile eliminare il circuito. Viene così interrotta la fatturazione per il circuito.

#### <a name="delete-a-circuit"></a>Eliminare un circuito

È possibile eliminare un circuito ExpressRoute eseguendo questo comando:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```