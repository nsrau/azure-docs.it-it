---
title: 'Registrare una connessione al servizio di peering - Azure PowerShell '
description: In questa esercitazione viene illustrato come registrare una connessione al servizio di peering con PowerShell.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service .
ms.openlocfilehash: a12bfa042c8741814cedc70f1dcb67dedbfd331e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400435"
---
# <a name="tutorial-register-a-peering-service-connection-using-azure-powershell"></a>Esercitazione: Registrare una connessione al servizio di peering con Azure PowerShell

In questa esercitazione viene illustrato come registrare il servizio di peering con Azure PowerShell.

Il servizio di peering di Azure è un servizio di rete che migliora la connettività dei clienti ai servizi cloud Microsoft, come Microsoft 365, Dynamics 365, i servizi SaaS (Software as a Service), Azure o qualsiasi servizio Microsoft accessibile tramite la rete Internet pubblica. In questa esercitazione si apprenderà come registrare una connessione al servizio di peering usando Azure PowerShell.

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si decide invece di installare e usare PowerShell in locale, questa guida introduttiva richiede l'uso del modulo Azure PowerShell versione 1.0.0 o successiva. Per trovare la versione installata, eseguire `Get-Module -ListAvailable Az`. Per informazioni relative a installazione e aggiornamento, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps).

Infine, se si esegue PowerShell in locale, sarà necessario eseguire anche `Connect-AzAccount`. Questo comando crea una connessione con Azure.

Usare il modulo Azure PowerShell per registrare e gestire il servizio di peering. È possibile registrare o gestire il servizio di peering dalla riga di comando di PowerShell o tramite script.


## <a name="prerequisites"></a>Prerequisiti  
È necessario disporre di quanto segue:

### <a name="azure-account"></a>Account Azure

Occorre avere un account Microsoft Azure valido e attivo. Questo account è necessario per configurare la connessione al servizio di peering. Il servizio di peering è una risorsa all'interno delle sottoscrizioni di Azure.

### <a name="connectivity-provider"></a>Provider di connettività

È possibile collaborare con un provider di servizi Internet o Internet Exchange per ottenere il servizio di peering in modo da connettere la propria rete alla rete Microsoft.

Assicurarsi che i provider di connettività siano partner Microsoft.

### <a name="register-a-subscription-with-the-resource-provider-and-feature-flag"></a>Registrare una sottoscrizione con il provider di risorse e il flag di funzionalità

Prima di procedere con la registrazione del servizio di peering, registrare la sottoscrizione con il provider di risorse e il flag di funzionalità usando Azure PowerShell. I comandi di Azure PowerShell sono specificati qui:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowPeeringService ProviderNamespace Microsoft.Peering 

Register-AzResourceProvider -ProviderNamespace Microsoft.Peering 

```

### <a name="fetch-the-location-and-service-provider"></a>Recuperare la località e il provider di servizi 

Eseguire i comandi seguenti in Azure PowerShell per acquisire la località e il provider di servizi in cui deve essere abilitato il servizio di peering. 

Ottenere le località del servizio di peering:

```azurepowershell-interactive
# Gets a list of available countries
Get-AzPeeringServiceCountry 
# Gets a list of metro locations serviced by country
Get-AzPeeringServiceLocation -Country "United States"
```

Ottenere i provider del servizio di peering:
              
```azurepowershell-interactive
Get-AzPeeringServiceProvider
```

### <a name="register-the-peering-service-connection"></a>Registrare la connessione al servizio di peering

Registrare la connessione al servizio di peering usando il set di comandi seguente in Azure PowerShell. Questo esempio registra il servizio di peering denominato myPeeringService.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
```

### <a name="register-the-peering-service-prefix"></a>Registrare il prefisso del servizio di peering

Registrare il prefisso fornito dal provider di connettività eseguendo i comandi seguenti tramite Azure PowerShell. Questo esempio registra il prefisso denominato myPrefix.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
$prefixName = "myPrefix"
$prefix = “192.168.1.0/24”
$serviceKey = "6f48cdd6-2c2e-4722-af89-47e27b2513af"
$prefixService = $peeringService | New-AzPeeringServicePrefix -Name $prefixName -Prefix $prefix -ServiceKey $serviceKey
```

### <a name="list-all-the-peering-services-connections"></a>Elencare tutte le connessioni ai servizi di peering

Per visualizzare l'elenco di tutti i servizi di peering, eseguire il comando seguente:

```azurepowershell-interactive
$peeringService = Get-AzPeeringService
```

### <a name="list-all-the-peering-service-prefixes"></a>Elencare tutti i prefissi dei servizi di peering

Per visualizzare l'elenco di tutti i prefissi dei servizi di peering, eseguire il comando seguente:

```azurepowershell-interactive
 $prefixName = "myPrefix"
```

```azurepowershell-interactive
$prefix = Get-AzPeeringServicePrefix -PeeringServiceName "myPeeringService" -ResourceGroupName "MyResourceGroup" -Name "myPrefix"
```

### <a name="remove-the-peering-service-prefix"></a>Rimuovere il prefisso del servizio di peering

Per rimuovere il prefisso del servizio di peering, eseguire il comando seguente:

```azurepowershell-interactive
Remove-AzPeeringServicePrefix -ResourceGroupName  "MyResourceGroup" -Name "myPrefix" -PeeringServiceName "myPeeringService"
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla connessione al servizio di peering, vedere [Connessione al servizio di peering](connection.md).
- Per informazioni sui dati di telemetria delle connessioni al servizio di peering, vedere [Telemetria delle connessioni al servizio di peering](connection-telemetry.md).
- Per registrare una connessione al servizio di peering usando il portale di Azure, vedere [Registrare una connessione al servizio di peering - Portale di Azure](azure-portal.md).
- Per registrare una connessione al servizio di peering usando l'interfaccia della riga di comando di Azure, vedere [Registrare una connessione al servizio di peering - Interfaccia della riga di comando di Azure](cli.md).
