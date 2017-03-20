---
title: Verificare il traffico con la verifica del flusso IP di Azure Network Watcher - PowerShell | Microsoft Docs
description: "Questo articolo descrive come verificare se il traffico da o verso una macchina virtuale è consentito o negato usando PowerShell"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: e1dad757-8c5d-467f-812e-7cc751143207
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 68860006266a60bf8e87f72d8669fb26ed3a5486
ms.lasthandoff: 03/04/2017


---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Controllare se il traffico da o verso una macchina virtuale è consentito o negato con la verifica del flusso IP, una funzionalità di Azure Network Watcher

> [!div class="op_single_selector"]
> - [Portale di Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI](network-watcher-check-ip-flow-verify-cli.md)
> - [API REST di Azure](network-watcher-check-ip-flow-verify-rest.md)

La verifica del flusso IP è una funzionalità di Network Watcher che consente di verificare se il traffico da o verso una macchina virtuale è consentito o negato. Questo scenario è utile per stabilire se una macchina virtuale può comunicare con una risorsa esterna o back-end. La funzionalità può essere usata per verificare se le regole del gruppo di sicurezza di rete sono configurate correttamente e per risolvere i problemi dei flussi bloccati da tali regole. La verifica del flusso IP consente inoltre di verificare che il traffico che si vuole bloccare sia correttamente bloccato dal gruppo di sicurezza di rete.

## <a name="before-you-begin"></a>Prima di iniziare

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher o aprire un'istanza esistente di Network Watcher. Lo scenario presuppone inoltre che esista e possa essere usato un gruppo di risorse con una macchina virtuale valida.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Scenario

Questo scenario usa la verifica del flusso IP per verificare se una macchina virtuale può comunicare con un indirizzo IP Bing noto. Se il traffico viene negato, restituisce la regola di sicurezza che nega il traffico. Per altre informazioni sulla verifica del flusso IP, leggere la [panoramica sulla verifica del flusso IP](network-watcher-ip-flow-verify-overview.md)

## <a name="retrieve-network-watcher"></a>Recuperare Network Watcher

Il primo passaggio consente di recuperare l'istanza di Network Watcher. La variabile `$networkWatcher` viene passata al cmdlet di verifica del flusso IP.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Ottenere una macchina virtuale

La verifica del flusso IP esegue il test del traffico da o verso un indirizzo IP su una macchina virtuale da o verso una destinazione remota. Il cmdlet richiede un ID di macchina virtuale. Se l'ID della macchina virtuale da usare è già noto, è possibile ignorare questo passaggio.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="get-the-nics"></a>Ottenere le schede NIC

È necessario l'indirizzo IP di una scheda di rete nella macchina virtuale. In questo esempio vengono recuperate le schede NIC in una macchina virtuale. Se l'indirizzo IP da testare nella macchina virtuale è già noto, è possibile ignorare questo passaggio.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkInterfaceIDs.ForEach({$_})}
```

## <a name="run-ip-flow-verify"></a>Eseguire la verifica del flusso IP

Dopo aver ottenuto le informazioni necessarie per eseguire il cmdlet, eseguire il cmdlet `Test-AzureRmNetworkWatcherIPFlow` per testare il traffico. In questo esempio, viene usato il primo indirizzo IP nella prima scheda NIC.

```powershell
Test-AzureRmNetworkWatcherIPFlow -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id `
-Direction Outbound -Protocol TCP `
-LocalIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress -LocalPort 6895 -RemoteIPAddress 204.79.197.200 -RemotePort 80
```

> [!NOTE]
> Per eseguire la verifica del flusso IP è necessario che la risorsa della macchina virtuale sia allocata.

## <a name="review-results"></a>Esaminare i risultati

Dopo aver eseguito `Test-AzureRmNetworkWatcherIPFlow` vengono restituiti i risultati. L'esempio seguente mostra i risultati restituiti nel passaggio precedente.

```
Access RuleName                                  
------ --------                                  
Allow  defaultSecurityRules/AllowInternetOutBound
```

## <a name="next-steps"></a>Passaggi successivi

Se il traffico risulta bloccato e non dovrebbe esserlo, vedere [Gestire i gruppi di sicurezza di rete](../virtual-network/virtual-network-manage-nsg-arm-portal.md) per individuare il gruppo di sicurezza di rete e le relative regole di sicurezza definite.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png














