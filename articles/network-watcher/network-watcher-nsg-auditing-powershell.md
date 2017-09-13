---
title: Automatizzare il controllo del gruppo di sicurezza di rete con la visualizzazione del gruppo di sicurezza di rete di Network Watcher di Azure | Documentazione Microsoft
description: "Questa pagina fornisce istruzioni sulle modalità di configurazione del controllo di un gruppo di sicurezza di rete"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 78a01bcf-74fe-402a-9812-285f3501f877
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: d60b1d44844c449e0f66dc0107a25531569d097b
ms.openlocfilehash: a91da330e677c85f16f6f4e506613576b6507d7c
ms.contentlocale: it-it
ms.lasthandoff: 03/31/2017

---

# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatizzare il controllo del gruppo di sicurezza di rete con la visualizzazione del gruppo di sicurezza di rete di Network Watcher di Azure

I clienti devono spesso far fronte alla sfida posta dalla verifica del comportamento di sicurezza della propria infrastruttura. Lo stesso problema si pone per le macchine virtuali in Azure. È importante disporre di un profilo di sicurezza simile in base alle regole del gruppo di sicurezza di rete (NSG) applicate. Usando la visualizzazione del gruppo di sicurezza, è possibile ottenere l'elenco delle regole applicate a una macchina virtuale all'interno di un gruppo di sicurezza di rete. È possibile definire un profilo di sicurezza NSG elevato e avviare la visualizzazione del gruppo di sicurezza con frequenza settimanale, quindi confrontare l'output con il profilo elevato e creare un report. In questo modo è possibile identificare con facilità tutte le macchine virtuali che non sono conformi al profilo di protezione previsto.

Se non si ha familiarità con i gruppi di sicurezza di rete, visitare [Controllare il flusso del traffico di rete con i gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md)

## <a name="before-you-begin"></a>Prima di iniziare

In questo scenario, si confronta una buona baseline nota con i risultati della visualizzazione del gruppo di sicurezza restituiti per una macchina virtuale.

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher. Lo scenario presuppone inoltre che esista e possa essere usato un gruppo di risorse con una macchina virtuale valida.

## <a name="scenario"></a>Scenario

Lo scenario illustrato in questo articolo ottiene la visualizzazione del gruppo di sicurezza per una macchina virtuale.

In questo scenario si apprenderà come:

- Recuperare un set di buone regole noto
- Recuperare una macchina virtuale con l'API REST
- Ottenere la visualizzazione del gruppo di sicurezza per la macchina virtuale
- Valutare la risposta

## <a name="retrieve-rule-set"></a>Recuperare il set di regole

Il primo passaggio in questo esempio consiste nell'uso di una baseline esistente. L'esempio seguente consiste nell'estrazione di alcuni json da un gruppo di sicurezza di rete esistente usando il cmdlet `Get-AzureRmNetworkSecurityGroup` come baseline per l'esempio.

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>Convertire il set di regole in oggetti di PowerShell

In questo passaggio il file json creato in precedenza viene letto con le regole previste nel gruppo di sicurezza di rete per questo esempio.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Recuperare Network Watcher

Il passaggio successivo consente di recuperare l'istanza di Network Watcher. La variabile `$networkWatcher` viene passata al cmdlet `AzureRmNetworkWatcherSecurityGroupView`.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Ottenere una macchina virtuale

È necessario che una macchina virtuale esegua il cmdlet `Get-AzureRmNetworkWatcherSecurityGroupView`. Nell'esempio seguente viene ottenuto un oggetto macchina virtuale.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Recuperare la visualizzazione del gruppo di sicurezza

Il passaggio successivo prevede il recupero del risultato della visualizzazione del gruppo di sicurezza. Questo risultato viene confrontato con il file json "baseline" illustrato in precedenza.

```powershell
$secgroup = Get-AzureRmNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analisi dei risultati

La risposta è raggruppata per interfacce di rete. I diversi tipi di regole restituite sono regole di sicurezza efficaci e predefinite. Il risultato viene ulteriormente scomposto in base alla modalità di applicazione, su una subnet o scheda di rete virtuale.

Lo script PowerShell seguente confronta i risultati della visualizzazione del gruppo di sicurezza con un output esistente di un gruppo di sicurezza di rete. L'esempio seguente mostra come confrontare i risultati con il cmdlet `Compare-Object`.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

L'esempio seguente corrisponde al risultato. È possibile visualizzare due delle regole derivanti dal primo set di regole che non erano presenti nel confronto.

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>Passaggi successivi

Se sono state modificate le impostazioni, vedere [Gestire gruppi di sicurezza di rete](../virtual-network/virtual-network-manage-nsg-arm-portal.md) per tenere traccia del gruppo di sicurezza di rete e delle regole di sicurezza in questione.














