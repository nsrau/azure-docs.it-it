---
title: 'Verificare il traffico con la verifica del flusso IP di Azure Network Watcher: REST | Microsoft Docs'
description: "Questo articolo descrive come verificare se il traffico da o verso una macchina virtuale è consentito o negato"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 3307a79f-03be-46a0-aaaf-b2079cb5f3b2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 3ccef9ef521b86ffc1eb6047174f4f9e5d9e4296
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="check-if-traffic-is-allowed-or-denied-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Controllare se il traffico è consentito o negato con la verifica del flusso IP, una funzionalità di Azure Network Watcher

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [Interfaccia della riga di comando 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [Interfaccia della riga di comando 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [API REST di Azure](network-watcher-check-ip-flow-verify-rest.md)


La verifica del flusso IP è una funzionalità di Network Watcher che consente di verificare se il traffico da o verso una macchina virtuale è consentito o negato. La convalida può essere eseguita per il traffico in ingresso o in uscita. Questo scenario è utile per stabilire se una macchina virtuale può comunicare con una risorsa esterna o back-end. La funzionalità può essere usata per verificare se le regole del gruppo di sicurezza di rete sono configurate correttamente e per risolvere i problemi dei flussi bloccati da tali regole. La verifica del flusso IP consente inoltre di verificare che il traffico che si vuole bloccare sia correttamente bloccato dal gruppo di sicurezza di rete.

## <a name="before-you-begin"></a>Prima di iniziare

ARMclient viene usato per chiamare l'API REST con PowerShell. ARMClient è reperibile in Chocolatey in [ARMClient on Chocolatey](https://chocolatey.org/packages/ARMClient) (ARMClient in Chocolatey)

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher.

## <a name="scenario"></a>Scenario

Questo scenario usa la verifica del flusso IP per verificare se una macchina virtuale può comunicare con un altro computer sulla porta 443. Se il traffico viene negato, restituisce la regola di sicurezza che nega il traffico. Per altre informazioni sulla verifica del flusso IP, leggere la [panoramica sulla verifica del flusso IP](network-watcher-ip-flow-verify-overview.md)

In questo scenario:

* Recuperare una macchina virtuale
* Chiamare la verifica del flusso IP
* Verificare i risultati

## <a name="log-in-with-armclient"></a>Accedere con ARMClient

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Recuperare una macchina virtuale

Eseguire lo script seguente per restituire la macchina virtuale. Il codice seguente richiede valori per le variabili:

* **subscriptionId**: l'ID sottoscrizione da usare.
* **resourceGroupName**: il nome di un gruppo di risorse contenente le macchine virtuali.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

L'informazione necessaria è l'ID, disponibile nel tipo `Microsoft.Compute/virtualMachines`. I risultati dovrebbero essere simili all'esempio di codice seguente:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft
.Network/networkInterfaces/contosovm842"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Com
pute/virtualMachines/ContosoVM/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="call-ip-flow-verify"></a>Chiamare la verifica del flusso IP

L'esempio seguente crea una richiesta per verificare il traffico per una macchina virtuale specificata. La risposta restituita indica se il traffico è consentito o negato. Se il traffico viene negato, restituisce anche la regola che blocca il traffico.

> [!NOTE]
> La verifica del flusso IP richiede che la risorsa VM sia allocata.

Lo script richiede l'ID risorsa di una macchina virtuale e di una scheda di interfaccia di rete nella macchina virtuale. Questi valori sono disponibili nell'output precedente.

> [!Important]
> Per tutte le chiamate REST di Network Watcher, è il nome del gruppo di risorse nell'URI della richiesta che contiene l'istanza di Network Watcher, non le risorse su cui si eseguono le azioni di diagnostica.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$vmName = "<vm name>"
$vmNICName = "<vm NIC name>"
$direction = "<direction of traffic>" # Examples are: Inbound or Outbound
$localIP = "<source IP>"
$localPort = "<source Port>"
$remoteIP = "<destination IP>"
$remotePort = "<destination Port>" # Examples are: 80, or 80-120
$protocol = "<UDP, TCP or *>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.compute/virtualMachine/${vmName}
$targetNic = "<uri of target nic resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkInterfaces/${vmNICName}

$requestBody = @"
{
    'targetResourceId':  '$targetUri',
    'direction':  '$direction',
    'protocol':  '$protocol',
    'localPort':  '$localPort',
    'remotePort':  '$remotePort',
    'localIPAddress':  '$localIP',
    'remoteIPAddress':  '$remoteIP',
    'targetNICResourceId':  '$targetNic'
}
"@
        
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/ipFlowVerify?api-version=2016-12-01" $requestBody -verbose
```

## <a name="understanding-the-results"></a>Informazioni sui risultati

La risposta che viene restituita indica se il traffico è consentito o negato. La risposta ha un aspetto simile a uno degli esempi seguenti:

**Consentito**

```json
{
  "access": "Allow",
  "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

**Negato**

```json
{
  "access": "Deny",
  "ruleName": "defaultSecurityRules/DefaultInboundDenyAll"
}
```

## <a name="next-steps"></a>Passaggi successivi

Se il traffico risulta bloccato e non dovrebbe esserlo, vedere [Gestire gruppi di sicurezza di rete](../virtual-network/virtual-network-manage-nsg-arm-portal.md) per informazioni al riguardo.












