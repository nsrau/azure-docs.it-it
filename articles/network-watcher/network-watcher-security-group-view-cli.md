---
title: Analizzare la protezione di rete con la visualizzazione del gruppo di sicurezza di rete di Network Watcher di Azure - Interfaccia della riga di comando di Azure | Documentazione Microsoft
description: Questo articolo descrive come usare l&quot;interfaccia della riga di comando di Azure per analizzare la protezione di macchine virtuali con la visualizzazione di un gruppo di sicurezza.
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1d0136b044f6049e59fa09d824cf244cac703c45
ms.openlocfilehash: 024d51df18405e0657b493976674fd23a99060a9
ms.lasthandoff: 02/23/2017

---

# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Analizzare la protezione della macchina virtuale visualizzando un gruppo di sicurezza con l'interfaccia della riga di comando di Azure

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI](network-watcher-security-group-view-cli.md)
> - [API REST](network-watcher-security-group-view-rest.md)

La visualizzazione di un gruppo di sicurezza consente di recuperare le regole di sicurezza di rete configurate ed effettive applicate a una macchina virtuale. Questa funzionalità è utile per controllare e diagnosticare i gruppi di sicurezza di rete e le regole configurate in una macchina virtuale per verificare che il traffico viene consentito o negato in modo corretto. Questo articolo illustra come recuperare le regole di sicurezza configurate ed effettive applicate a una macchina virtuale tramite l'interfaccia della riga di comando di Azure

## <a name="before-you-begin"></a>Prima di iniziare

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher.

## <a name="scenario"></a>Scenario

Lo scenario illustrato in questo articolo recupera le regole di sicurezza configurate ed effettive applicate a una determinata macchina virtuale.

## <a name="get-a-vm"></a>Ottenere una macchina virtuale

È necessario che una macchina virtuale esegua il cmdlet `vm list`. Il comando seguente elenca le macchine virtuali in un gruppo di risorse:

```azurecli
azure vm list -g resourceGroupName
```

Se la macchina virtuale è nota, è possibile usare il cmdlet `vm show` per ottenere il relativo ID della risorsa:

```azurecli
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Recuperare la visualizzazione del gruppo di sicurezza

Il passaggio successivo prevede il recupero del risultato della visualizzazione del gruppo di sicurezza. L'aggiunta del flag "-json" restituisce un risultato in formato JSON.

```azurecli
azure network watcher security-group-view -g resourceGroupName -n networkWatcherName -t targetResourceId --json
```

## <a name="viewing-the-results"></a>Visualizzazione dei risultati

L'esempio seguente è una risposta abbreviata dei risultati restituiti. I risultati mostrano tutte le regole di sicurezza effettive e applicate alla macchina virtuale, suddivise nei gruppi **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** e **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic",
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testvm",
          "securityRules": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/test-nsg/securityRules/default-allow-rdp",
              "protocol": "TCP",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 1000,
              "direction": "Inbound",
              "provisioningState": "Succeeded",
              "name": "default-allow-rdp",
              "etag": "W/\"00000000-0000-0000-0000-000000000000\""
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/networkSecurityGroups//defaultSecurityRules/",
            "description": "Allow inbound traffic from all VMs in VNET",
            "protocol": "*",
            "sourcePortRange": "*",
            "destinationPortRange": "*",
            "sourceAddressPrefix": "VirtualNetwork",
            "destinationAddressPrefix": "VirtualNetwork",
            "access": "Allow",
            "priority": 65000,
            "direction": "Inbound",
            "provisioningState": "Succeeded",
            "name": "AllowVnetInBound"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Consultare [Auditing Network Security Groups (NSG) with Network Watcher](network-watcher-nsg-auditing-powershell.md) (Verifica dei gruppi di sicurezza di rete con Network Watcher) per informazioni su come automatizzare la verifica dei gruppi di sicurezza di rete.

Per altre informazioni sulle regole di sicurezza applicate alle risorse di rete, leggere la [panoramica sulla visualizzazione di gruppo di sicurezza](network-watcher-security-group-view-overview.md).

