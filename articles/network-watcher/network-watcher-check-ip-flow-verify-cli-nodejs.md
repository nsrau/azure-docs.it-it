---
title: Verificare il traffico con la verifica del flusso IP di Network Watcher di Azure - Interfaccia della riga di comando di Azure | Documentazione Microsoft
description: "Questo articolo descrive come verificare se il traffico da o verso una macchina virtuale è consentito o negato usando l'interfaccia della riga di comando di Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f1355cd861722848211277250155c434da1e774d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Controllare se il traffico da o verso una macchina virtuale è consentito o negato con la verifica del flusso IP, una funzionalità di Network Watcher di Azure

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [Interfaccia della riga di comando 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [Interfaccia della riga di comando 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [API REST di Azure](network-watcher-check-ip-flow-verify-rest.md)


La verifica del flusso IP è una funzionalità di Network Watcher che consente di verificare se il traffico da o verso una macchina virtuale è consentito o negato. Questo scenario è utile per stabilire se una macchina virtuale può comunicare con una risorsa esterna o back-end. La funzionalità può essere usata per verificare se le regole del gruppo di sicurezza di rete sono configurate correttamente e per risolvere i problemi dei flussi bloccati da tali regole. La verifica del flusso IP consente inoltre di verificare che il traffico che si vuole bloccare sia correttamente bloccato dal gruppo di sicurezza di rete.

Questo articolo usa l'interfaccia della riga di comando di Azure 1.0 multipiattaforma, disponibile per Windows, Mac e Linux.

## <a name="before-you-begin"></a>Prima di iniziare

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher o aprire un'istanza esistente di Network Watcher. Lo scenario presuppone inoltre che esista e possa essere usato un gruppo di risorse con una macchina virtuale valida.

## <a name="scenario"></a>Scenario

Questo scenario usa la verifica del flusso IP per verificare se una macchina virtuale può comunicare con un indirizzo IP Bing noto. Se il traffico viene negato, restituisce la regola di sicurezza che nega il traffico. Per altre informazioni sulla verifica del flusso IP, leggere la [panoramica sulla verifica del flusso IP](network-watcher-ip-flow-verify-overview.md)


## <a name="get-a-vm"></a>Ottenere una macchina virtuale

La verifica del flusso IP esegue il test del traffico da o verso un indirizzo IP su una macchina virtuale da o verso una destinazione remota. Il cmdlet richiede un ID di macchina virtuale. Se l'ID della macchina virtuale da usare è già noto, è possibile ignorare questo passaggio.

```
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="get-the-nics"></a>Ottenere le schede NIC

È necessario l'indirizzo IP di una scheda di rete nella macchina virtuale. In questo esempio vengono recuperate le schede NIC in una macchina virtuale. Se l'indirizzo IP da testare nella macchina virtuale è già noto, è possibile ignorare questo passaggio.

```
azure network nic show -g resourceGroupName -n nicName
```

## <a name="run-ip-flow-verify"></a>Eseguire la verifica del flusso IP

Dopo aver ottenuto le informazioni necessarie per eseguire il cmdlet, eseguire il cmdlet `network watcher ip-flow-verify` per testare il traffico. In questo esempio, viene usato il primo indirizzo IP nella prima scheda NIC.

```
azure network watcher ip-flow-verify -g resourceGroupName -n networkWatcherName -t targetResourceId -d directionInboundorOutbound -p protocolTCPorUDP -o localPort -m remotePort -l localIpAddr -r remoteIpAddr
```

> [!NOTE]
> Per eseguire la verifica del flusso IP è necessario che la risorsa della macchina virtuale sia allocata.

## <a name="review-results"></a>Esaminare i risultati

Dopo aver eseguito `network watcher ip-flow-verify` vengono restituiti i risultati. L'esempio seguente mostra i risultati restituiti nel passaggio precedente.

```
data:    Access                          : Deny
data:    Rule Name                       : defaultSecurityRules/DefaultInboundDenyAll
info:    network watcher ip-flow-verify command OK
```

## <a name="next-steps"></a>Passaggi successivi

Se il traffico risulta bloccato e non dovrebbe esserlo, vedere [Gestire i gruppi di sicurezza di rete](../virtual-network/virtual-network-manage-nsg-arm-portal.md) per individuare il gruppo di sicurezza di rete e le relative regole di sicurezza definite.

Informazioni su come controllare le impostazioni del gruppo sicurezza di rete sono disponibili in [Auditing Network Security Groups (NSG) with Network Watcher](network-watcher-nsg-auditing-powershell.md) (Verifica dei gruppi di sicurezza di rete con Network Watcher).

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png
