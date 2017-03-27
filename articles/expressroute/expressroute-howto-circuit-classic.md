---
title: 'Creare e modificare un circuito ExpressRoute: PowerShell: Azure classico| Microsoft Docs'
description: Questo articolo illustra i passaggi per la creazione e il provisioning di un circuito ExpressRoute. Questo articolo descrive anche come controllare lo stato, eseguire l&quot;aggiornamento o effettuare l&quot;eliminazione e il deprovisioning di un circuito.
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
ms.date: 02/08/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 62ecd4cc2eed8623cab75777605d621e16b99977
ms.lasthandoff: 03/14/2017


---
# <a name="create-and-modify-an-expressroute-circuit"></a>Creare e modificare un circuito ExpressRoute
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-circuit-arm.md)
> * [Classica: PowerShell](expressroute-howto-circuit-classic.md)
> * [Video - Portale di Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> 
>

Questo articolo illustra i passaggi per creare un circuito di Azure ExpressRoute tramite i cmdlet di PowerShell e il modello di distribuzione classica. L'articolo illustra anche le procedure di controllo dello stato, aggiornamento, eliminazione e deprovisioning di un circuito ExpressRoute.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]


**Informazioni sui modelli di distribuzione di Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Prima di iniziare
### <a name="step-1-review-the-prerequisites-and-workflow-articles"></a>Passaggio 1. Consultare gli articoli sui prerequisiti e sul flusso di lavoro
Prima di procedere con la configurazione, assicurarsi di avere verificato i [prerequisiti](expressroute-prerequisites.md) e i [flussi di lavoro](expressroute-workflows.md).  

### <a name="step-2-install-the-latest-versions-of-the-azure-powershell-modules"></a>Passaggio 2. Installare la versione più recente dei moduli di Azure PowerShell
Per istruzioni dettagliate sulla configurazione del computer per l'uso dei moduli di Azure PowerShell, seguire le istruzioni contenute in [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) .

### <a name="step-3-log-in-to-your-azure-account-and-select-a-subscription"></a>Passaggio 3. Accedere all'account Azure e selezionare una sottoscrizione
1. Eseguire i cmdlet seguenti in un prompt di Windows PowerShell con privilegi elevati:
   
        Add-AzureAccount
2. Nella schermata di accesso visualizzata accedere al proprio account.
3. Ottenere un elenco delle sottoscrizioni.
   
        Get-AzureSubscription
4. Selezionare la sottoscrizione da usare.
   
        Select-AzureSubscription -SubscriptionName "mysubscriptionname"

## <a name="create-and-provision-an-expressroute-circuit"></a>Creare un circuito ExpressRoute ed eseguirne il provisioning
### <a name="step-1-import-the-powershell-modules-for-expressroute"></a>Passaggio 1. Importare i moduli di PowerShell per ExpressRoute
 Se ancora non è stato fatto, per iniziare a usare i cmdlet per ExpressRoute, è necessario importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell. Importare i moduli dal percorso in cui sono stati installati sul computer locale. A seconda del metodo usato per installare i moduli, il percorso potrebbe essere diverso da quello illustrato nell'esempio seguente. Se necessario, modificare l'esempio.  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="step-2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>Passaggio 2. Ottenere l'elenco dei provider, delle località e delle larghezze di banda supportate
Prima di creare un circuito ExpressRoute, è necessario avere l'elenco delle località, delle opzioni di larghezza di banda e dei provider di connettività supportati.

Il cmdlet `Get-AzureDedicatedCircuitServiceProvider` di PowerShell restituisce queste informazioni, che verranno usate nei passaggi successivi:

    Get-AzureDedicatedCircuitServiceProvider

Verificare se sia presente anche il proprio provider di connettività. Prendere nota delle informazioni seguenti, perché saranno necessarie al momento della creazione di un circuito:

* Nome
* PeeringLocations
* BandwidthsOffered

È ora possibile creare un circuito ExpressRoute.         

### <a name="step-3-create-an-expressroute-circuit"></a>Passaggio 3. Creare un circuito ExpressRoute
L'esempio seguente illustra come creare un circuito ExpressRoute a 200 Mbps tramite Equinix nella Silicon Valley. Se si usa un altro provider e impostazioni diverse, sostituire tali informazioni al momento della richiesta.

> [!IMPORTANT]
> Il circuito ExpressRoute viene addebitato dal momento in cui emessa una chiave di servizio. Verificare che l'operazione venga eseguita quando il provider di connettività è pronto a effettuare il provisioning del circuito.
> 
> 

Di seguito è riportato un esempio di richiesta di una nuova chiave di servizio:

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

Se invece si vuole creare un circuito ExpressRoute con il componente aggiuntivo Premium, usare l'esempio seguente. Per altri dettagli sul componente aggiuntivo Premium, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md) .

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


La risposta conterrà la chiave di servizio. È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo questo comando:

    get-help new-azurededicatedcircuit -detailed

### <a name="step-4-list-all-the-expressroute-circuits"></a>Passaggio 4. Elencare tutti i circuiti ExpressRoute
Per ottenere un elenco di tutti i circuiti ExpressRoute creati, eseguire il comando `Get-AzureDedicatedCircuit`:

    Get-AzureDedicatedCircuit

La risposta sarà simile all'esempio seguente:

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

È possibile recuperare queste informazioni in qualsiasi momento usando il cmdlet `Get-AzureDedicatedCircuit` . Se si effettua la chiamata senza parametri, verranno elencati tutti i circuiti. La chiave di servizio verrà elencata nel campo *ServiceKey* .

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo questo comando:

    get-help get-azurededicatedcircuit -detailed

### <a name="step-5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Passaggio 5. Inviare la chiave di servizio al provider di connettività per il provisioning
*ServiceProviderProvisioningState* offre informazioni sullo stato di provisioning corrente sul lato provider del servizio. *Status* indica lo stato sul lato Microsoft. Per altre informazioni sullo stato di provisioning dei circuiti, vedere l'articolo relativo ai [flussi di lavoro](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando si crea un nuovo circuito ExpressRoute, il circuito ha lo stato seguente:

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Il circuito passa allo stato seguente quando è in corso l'abilitazione da parte del provider di connettività:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Un circuito ExpressRoute può essere usato solo se è impostato sullo stato seguente:

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="step-6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Passaggio 6. Controllare periodicamente lo stato e la condizione della chiave del circuito
In questo modo è possibile sapere quando il provider ha abilitato il circuito. Dopo la configurazione del circuito, *ServiceProviderProvisioningState* verrà visualizzato come *Provisioning eseguito*, come illustrato nell'esempio seguente:

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="step-7-create-your-routing-configuration"></a>Passaggio 7. Creare la configurazione di routing
Per istruzioni dettagliate, vedere l'articolo relativo alla [configurazione del routing per un circuito ExpressRoute (creazione e modifica del peering del circuito)](expressroute-howto-routing-classic.md) .

> [!IMPORTANT]
> Queste istruzioni si applicano solo ai circuiti creati con provider di servizi che offrono servizi di connettività di livello 2. Se si usa un provider di servizi che offre servizi gestiti di livello 3 (di solito un IP VPN, come MPLS), il provider di connettività configurerà e gestirà il routing per conto dell'utente.
> 
> 

### <a name="step-8-link-a-virtual-network-to-an-expressroute-circuit"></a>Passaggio 8. Collegare una rete virtuale a un circuito ExpressRoute
Collegare quindi una rete virtuale al circuito ExpressRoute. Per istruzioni dettagliate, vedere [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md) . Se è necessario creare una rete virtuale usando il modello di distribuzione classica per ExpressRoute, vedere le istruzioni contenute nell'articolo relativo alla [creazione di una rete virtuale per ExpressRoute](expressroute-howto-vnet-portal-classic.md) .

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Ottenere lo stato di un circuito ExpressRoute
È possibile recuperare queste informazioni in qualsiasi momento usando il cmdlet `Get-AzureCircuit` . Se si effettua la chiamata senza parametri, verranno elencati tutti i circuiti.

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

È possibile ottenere informazioni su un circuito ExpressRoute specifico passando la chiave di servizio come parametro alla chiamata.

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo, ad esempio, questo comando:

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>Modifica di un circuito ExpressRoute
È possibile modificare determinate proprietà di un circuito ExpressRoute senza conseguenze per la connettività.

È possibile eseguire le operazioni seguenti senza tempi di inattività:

* Abilitare o disabilitare un componente aggiuntivo ExpressRoute Premium per il circuito ExpressRoute.
* Aumentare la larghezza di banda del circuito ExpressRoute, a condizione che sulla porta sia disponibile capacità. Si noti che il downgrade della larghezza di banda di un circuito non è supportato. 
* Modificare il piano di misurazione da Dati a consumo a Dati senza limiti. Si noti che la modifica del piano di misurazione da Dati senza limiti a Dati a consumo non è supportata.
* È possibile abilitare e disabilitare l'opzione *Allow Classic Operations*(Consenti operazioni classiche).

Per altre informazioni su limiti e limitazioni, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md) .

### <a name="to-enable-the-expressroute-premium-add-on"></a>Per abilitare il componente aggiuntivo ExpressRoute Premium
È possibile abilitare il componente aggiuntivo ExpressRoute Premium per il circuito esistente usando il cmdlet di PowerShell seguente:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

Il circuito avrà le funzionalità del componente aggiuntivo ExpressRoute Premium abilitate. La fatturazione della funzionalità del componente aggiuntivo Premium inizierà non appena il comando verrà eseguito correttamente.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Per disabilitare il componente aggiuntivo ExpressRoute Premium
> [!IMPORTANT]
> Questa operazione può avere esito negativo se si usano più risorse di quelle consentite per il circuito standard.
> 
> 

#### <a name="considerations"></a>Considerazioni

* È necessario assicurarsi che il numero di reti virtuali collegate al circuito sia minore di 10 prima di eseguire il downgrade da premium a standard. In caso contrario, la richiesta di aggiornamento avrà esito negativo e verranno fatturate le tariffe Premium.
* È necessario scollegare tutte le reti virtuali in altre aree geopolitiche. In caso contrario, la richiesta di aggiornamento avrà esito negativo e verranno fatturate le tariffe Premium.
* La tabella di route deve includere meno di 4.000 route per il peering privato. Se la tabella di route include più di 4000 route, la sessione BGP verrà eliminata e non sarà riabilitata finché il numero di prefissi pubblicati non scenderà al di sotto di 4000.

#### <a name="disable-the-premium-add-on"></a>Disabilitare il componente aggiuntivo Premium
È possibile disabilitare il componente aggiuntivo ExpressRoute Premium per il circuito esistente usando il cmdlet di PowerShell seguente:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Per aggiornare la larghezza di banda del circuito ExpressRoute
Per le opzioni relative alla larghezza di banda supportate per il provider, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md) . È possibile scegliere qualsiasi dimensione maggiore della dimensione del circuito esistente, a condizione che sia consentita dalla porta fisica in cui viene creato il circuito.

> [!IMPORTANT]
> Se la capacità sulla porta esistente non è sufficiente, potrebbe essere necessario ricreare il circuito ExpressRoute. Il circuito non può essere aggiornato se in tale posizione non è disponibile capacità aggiuntiva.
>
> Non è possibile ridurre la larghezza di banda di un circuito ExpressRoute senza interruzioni. Il downgrade della larghezza di banda richiede il deprovisioning del circuito ExpressRoute e quindi il provisioning di un nuovo circuito ExpressRoute.
> 
> 

#### <a name="resize-a-circuit"></a>Ridimensionare un circuito

Dopo aver stabilito le dimensioni necessarie, usare il comando seguente per ridimensionare il circuito:

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Il circuito verrà ridimensionato sul lato di Microsoft. È necessario contattare il provider di connettività per aggiornare le configurazioni corrispondenti in base a questa modifica. Si noti che inizierà la fatturazione per la larghezza di banda aggiornata da questo punto in poi.

Se quando si aumenta la larghezza di banda del circuito viene visualizzato l'errore seguente, significa che la larghezza di banda rimasta nella porta fisica in cui è stato creato il circuito esistente non è sufficiente. È necessario eliminare questo circuito e crearne uno nuovo delle dimensioni necessarie. 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Deprovisioning ed eliminazione di un circuito ExpressRoute

### <a name="considerations"></a>Considerazioni

* Affinché l'operazione abbia esito positivo, è necessario scollegare tutte le reti virtuali dal circuito ExpressRoute. Se l'operazione ha esito negativo, controllare se sono presenti reti virtuali collegate al circuito.
* Se lo stato di provisioning del provider del servizio del circuito ExpressRoute è **Provisioning in corso** o **Provisioning eseguito**, è necessario collaborare con il provider di servizi per eseguire il deprovisioning del circuito sul lato del provider. Le risorse continueranno a essere riservate e la fatturazione continuerà a essere applicata finché il provider di servizi non avrà completato il deprovisioning del circuito e inviato una notifica a Microsoft.
* Se il provider di servizi ha eseguito il deprovisioning del circuito, ovvero lo stato di provisioning del provider di servizi è impostato su **Senza provisioning**, è possibile eliminare il circuito. Non verrà più applicata la fatturazione corrispondente.

#### <a name="delete-a-circuit"></a>Eliminare un circuito

È possibile eliminare un circuito ExpressRoute eseguendo questo comando:

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato il circuito, verificare di eseguire le operazioni seguenti:

* [Creare e modificare il routing per un circuito ExpressRoute](expressroute-howto-routing-classic.md)
* [Collegare la rete virtuale al circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)


